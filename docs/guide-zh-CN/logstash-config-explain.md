## Input Plugin

### 从文件输入

从文件读取数据，如常见的日志文件。文件读取通常要解决几个问题：


No. | 问题 | 解决办法
---|---|---
1 | 文件内容如何只被读取一次？即重启Logstash时，从上次读取的位置继续 	 | sincedb
2 | 如何即时读取到文件的新内容 | 定时检查文件是否有更新
3 | 如何发现新文件并进行读取 | 定时检查是否有新文件生成
4 | 如何文件发生了归档（rotation）操作，是否影响当前的内容读取 | 不影响，被归档的文件内容可以继续被读取

#### logstash-input-file 配置

配置：

配置项 | 说明
---|---
path | 数组类型，用于知名读取的文件路径，基于glob匹配语法。
exclude | 数组类型，排除不想监听的文件规则，基于glob匹配语法
sincedb_path | 字符串类型，记录sinceddb文件路径，默认路径是当前用户的home文件夹
start_position | 字符串类型，可以配置为beginning/end，是否从头读取文件
stat_interval | 数值类型，单位为秒，定时检查文件是否有更新，默认是1秒
discover_interval | 	数值类型，单位为秒，定时检查是否有新文件待读取，默认是15秒
ignore_older | 数值类型，单位为秒，扫描文件列表时，如果该文件上次更改时间超过设定的时长，则不做处理，但依然会监控是否有新内容，默认关闭
close_older | 数值类型，单位为秒，如果监听的文件在超过该设定时间内没有新内容，会被关闭文件句柄，释放资源，但依然会监控是否有新内容，默认3600秒，即1小时
#### path

例如：

- /var/log/*.log：匹配/var/log目录下以.log结尾的所有文件
- /var/log/**/*.log：匹配/var/log所有子目录下以.log结尾的文件
- /var/log/{app1,app2,app3}/*.log：匹配/var/log目录下app1，app2，app3子目录中以.log结尾的文件

#### sincedb

通过logstash-input-file插件导入了一些本地日志文件时，logstash会通过一个名为sincedb的独立文件中来跟踪记录每个文件中的当前位置。这使得停止和重新启动Logstash成为可能，并让它在不丢失在停止Logstashwas时添加到文件中的行数的情况下继续运行。sincedb默认保存在当前用户的Home目录下。

在调试的时候，我们可能希望取消sincedb的记录功能，使文件每次都能从头开始读取。此时，我们可以这样来做：

```
input {
    file {
        path => ["文件路径"]
        start_position => "beginning"
        sincedb_path => "/dev/null"
    }
}
```


### 从HTTP输入


```
input {
  http {
    port => 端口号
  }
}
```

### 从TCP输入

```
input {
  tcp {
    mode => "server"
    host => "0.0.0.0"
    port => 端口号
    codec => json_lines
  }
}
```


## Codec Plugin

Codec Plugin作用于Input和Output Plugin，负责将数据在原始与Logstash Event之间转换，常见的codec有：

- plain：读取原始内容
- dots：将内容简化为点进行输出
- rubydebug：将Logstash Events按照ruby格式输出，方便调试
- line：处理带有换行符的内容
- json：处理json格式的内容
- multiline：处理多行数据的内容

### multiline

当一个Event的message由多行组成时，需要使用multiline，常见的情况是处理日志信息，如下所示：

```
Exception in thread "main" java.lang.NullPointerException
	at com.example.myproject.Book.getTitle(Book.java:16)
	at com.example.myproject.Author.getBookTitles(Author.java:25)
	at com.example.myproject.Bootstrap.main(Bootstrap.java:14)
```

主要设置参数如下：

参数 | 说明
---|---
pattern | 设置行匹配的正则表达式，可以使用grok
what | 可设置为previous或next。表示当与pattern匹配成功的时候，匹配行是归属上一个事件还是下一个事件
negate | 布尔值，表示是否对pattern的结果取反

例如，匹配日志文件：


```
input {
	file {
		path => ["文件路径"]
       start_position => "beginning"
		codec => multiline {
			pattern => "^\s" #匹配以空格开头的行
			what => "previous"
		}
	}
}
```

## Filter Plugin

Filter是Logstash功能强大的主要原因，它可以对Logstash Event进行丰富的处理，比如解析数据、删除字段、类型转换等等，常见的有如下几个：
插件名称 | 说明
---|---
date | 日期解析
grok | 正则匹配解析
dissect | 分隔符解析
mutate | 对字段作处理，比如重命名、删除、替换
json | 按照json解析字段内容到指定字段中
geoip | 增加地理位置数据
ruby  | 利用ruby代码来动态修改Logstash Event

### date插件

date 插件可以将日期字符串解析为日期类型，然后替换@timestamp字段或者指定其他字段：

```
filter {
	date {
		match => ["logdate", "MM dd yyy HH:mm:ss"]
	}
}
```

通过以上配置，我们可以将

```
{
	"logdate": "Jan 01 2018 12:02:2018"
	"target": "@timestamp"
}
```


转换为：

```
{
	"@version" => "1"
	"host" => "..."
	"logdate" => Jan01 2018 12:02:08
	"@timestamp" => "2018-01-01T04:02:08.000Z"
}
```

### grok插件

grok插件用于解析日志内容，例如我们要将下面这一条日志解析成json数组。


```
83.149.9.216 [17/May/2015:10:05:03 +0000] "GET /presentations/logstash-monitorama-2013/images/kibana-search.png HTTP/1.1" 200 203023 "http://semicomplete.com/presentations/logstash-monitorama-2013/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36"
```


那么我们就可以这样来配置grok：


```
%{IPORHOST:clientip} \[%{HTTPDATE:timestamp}\] "%{WORD:verb} %{DATA:request} HTTP/%{NUMBER:httpversion}" %{NUMBER:response:int} (?:-|%{NUMBER:bytes:int}) %{QS:referrer} %{QS:agent}
```

grok语法：

例子：%{SYNTAX:SEMANTIC}
SYNTAX为grok pattern的名称，SEMANTIC为赋值字段名称。%{NUMBER:duration}可以匹配数值类型，但是grok匹配出的内容都是字符串类型，可以通过在最后指定为int或者float来强转类型：%{NUMBER:duration:int}

grok内置pattern：logstash-patterns-core

自定义grok pattern
我们通过pattern_definitions参数，以键值对的方式定义pattern名称和内容。也可以通过pattern_dir参数，以文件的形式读取pattern。


```
filter {
	grok {
		match => {
			"message" => "%{SERVICE:service}"
		}
		pattern_definitions => {
			"SERVICE" => "[a-z0-9]{10,11}"
		}
	}
}
```

### dissect插件

基于分隔符原理解析数据，解决grok解析时消耗过多cpu资源的问题。

同样的日志信息，dissect可以这样来写：

```
83.149.9.216 [17/May/2015:10:05:03 +0000] "GET /presentations/logstash-monitorama-2013/images/kibana-search.png HTTP/1.1" 200 203023 "http://semicomplete.com/presentations/logstash-monitorama-2013/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36"
```

dissect配置：

```
%{clientip} [%{timestamp}] "%{request}" %{response} %{bytes} "%{referrer}" "%{agent}"
```

但是，正因为dissect语法简单，因此它能处理的场景比较有限。它只能处理格式相似，且有分隔符的字符串。它的语法如下：

- %{}里面是字段
- 两个%{}之间是分隔符。

例子1：
有以下日志：

```
Apr 26 12:20:02 localhost systemd[1]: Starting system activity accounting tool
```

我想要把前面的日期和时间解析到同一个字段中，那么就可以这样来做：


```
filter {
    dissect {
        mapping => {
        	"message" => "%{ts} %{+ts} %{+ts} %{src} %{prog}[%{pid}]: %{msg}"
        }
    }
}
```

例子2：

如果有以下日志：


```
name=hushukang&age=28
```

我想要把它解析成：

```
{
	"name": "hushukang",
	"age": 28
}
```


那么，可以这样来写dissect:


```
filter {
    dissect {
        mapping => {
            "message" => "%{?key1}=%{&key1}&%{?key2}=%{&key2}"
        }
        convert_datatype => {
            age => "int"
        }
    }
}
```


%{?}代表忽略匹配值，但是赋予字段名，用于后续匹配用。%{&}代表将匹配值赋到指定字段中。convert_datatype可以将指定字符串转为int或者float类型。
### mutate插件

mutate是使用最频繁的插件，可以对字段进行各种操作，比如重命名、删除、替换、更新等，主要操作如下：

- convert类型转换
- gsub字符串替换
- split、join、merge字符串切割、数组合并为字符串、数组合并为数组
- rename字段重命名
- update、replace字段内容更新或替换。它们都可以更新字段的内容，区别在于update只在字段存在时生效，而replace在字段不存在时会执行新增字段的操作
- remove_field删除字段


```
mutate {
    convert => {"age" => "integer"}
    gsub => [
        "birthday", "-", "/",
        "image", "\/", "_"
    ]
    split => {"hobities" => ","}
    rename => {"hobities" => "hobityList"}
    replace => {"icon" => "%{image}"}
    remove_field => ["message"]
}
```



### json 插件

将字段内容为json格式的数据解析出来。
语法如下：


```
filter {
	json {
		source => "message"
		target => "msg_json"
	}
}
```


如果不指定target的话，那么filter会把解析出来的json数据直接放到根级别。
### geoip 插件

常用的插件，根据ip地址提供对应的地域信息，比如经纬度、城市名等，方便进行地理数据分析。

语法如下：


```
filter {
	geoip {
		source => "clientIp"
	}
}
```


## Output Plugin
### 输出到控制台

多用于调试


```
output {
	stdout {
		codec => rubydebug
	}
}
```

### 输出到文件

实现将分散在多地的文件统一到一处的需求，比如将所有web机器的web日志收集到1个文件中，从而方便查阅信息


```
output {
	file {
		path => "文件路径"
		codec => line {format => %{message}}
	}
}
```

### 输出到elasticsearch

```
output {
  elasticsearch {
    hosts => ["http://192.168.3.12:9200"]
    index => "logstash-%{+YYYY.MM.dd}"
    document_type => "_doc"
    user => "用户名"
    password => "密码"
  }
}
```
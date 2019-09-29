目录

- 基本命令
- 正则表达式匹配
- rewrite 规则

### 基本命令

```
// 优雅停止nginx，有连接时会等连接请求完成再杀死worker进程
nginx -s quit

// 优雅重启，并重新载入配置文件nginx.conf
nginx -s reload

// 重新打开日志文件，一般用于切割日志
nginx -s reopen

// 查看版本
nginx -v    

// 检查nginx的配置文件
nginx -t   

// 查看帮助信息
nginx -h

// 详细版本信息，包括编译参数 
nginx -V

// 指定配置文件
nginx -c filename
```

### 正则表达式匹配

正则匹配

符号 | 说明
---|---
 ~ | 区分大小写匹配
 ~* | 不区分大小写匹配
 !~ 和 !~*  | 分别为区分大小写不匹配及不区分大小写不匹配
 ^ | 以什么开头的匹配
 $ | 以什么结尾的匹配
 .* | .匹配任意字符，*匹配数量0到正无穷；
 \. | 斜杠用来转义，\.匹配 .    特殊使用方法，记住记性了；
 值1|值2|值3|值4）| 或匹配模式，例：（jpg|gif|png|bmp）匹配jpg或gif或png或bmp

文件及目录匹配

- -f和!-f用来判断是否存在文件
- -d和!-d用来判断是否存在目录
- -e和!-e用来判断是否存在文件或目录
- -x和!-x用来判断文件是否可执行

### rewrite 规则

```
1.break 指令
　　使用环境：server,location,if;
　　该指令的作用是完成当前的规则集，不再处理rewrite指令。

2.if 指令
　　使用环境：server,location
　　该指令用于检查一个条件是否符合，如果条件符合，则执行大括号内的语句。If指令不支持嵌套，不支持多个条件&&和||处理。

3.return 指令
　　语法：return code ;
　　使用环境：server,location,if;
　　该指令用于结束规则的执行并返回状态码给客户端。
　　示例：如果访问的URL以".sh"或".bash"结尾，则返回403状态码
　　location ~ .*\.(sh|bash)?$
　　{
　　　　return 403;
　　}

4.rewrite 指令
　　语法：rewriteregex replacement flag
　　使用环境：server,location,if
　　该指令根据表达式来重定向URI，或者修改字符串。指令根据配置文件中的顺序来执行。注意重写表达式只对相对路径有效。如果你想配对主机名，你应该使用if语句，示例如下：
　　if ($host ~* www\.(.*))
　　{
　　　　set $host_without_www $1;
　　　　rewrite  ^(.*)$   http://$host_without_www$1 permanent;
　　}

5.set 指令
　　语法：setvariable value ; 默认值:none; 使用环境：server,location,if;
　　该指令用于定义一个变量，并给变量赋值。变量的值可以为文本、变量以及文本变量的联合。
　　示例：set $varname "hello world";

6.uninitialized_variable_warn 指令
　　语法：uninitialized_variable_warnon|off
　　使用环境：http,server,location,if
　　该指令用于开启和关闭未初始化变量的警告信息，默认值为开启。
```


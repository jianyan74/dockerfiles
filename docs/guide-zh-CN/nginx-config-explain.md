目录

- URL 美化
- 反向代理
- 重定向
- 获取 header 变量
- 个性化日志
- 默认变量详解

### URL 美化

```
# 省略 index.php 入口文件
location / {
    try_files $uri $uri/ /index.php$is_args$args;
}

# 省略 backend/index.php 入口文件
location /backend {
    try_files $uri $uri/ /backend/index.php$is_args$args;
}
```

### 反向代理

> 可以代理端口、sock等，给php、java等程序使用  
> 案例可以看 `services/nginx/conf.d/demo.conf`

```
server {
    location ~ \.php$ 
    {
        # 案例为代理端口
        # 可直接代理 sock，比如你部署多个php版本的时候就可以用这种方式, docker安装一般以 ip:端口 方式连接
        # fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        # 如果用 links 的方式例如 php-fpm 可以是 proxy_pass http://php-fpm:9000;
        proxy_pass http://127.0.0.1:9000;
        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        
        # php 的 fast_cgi 案例
        fastcgi_index /index.php;
        
        include fastcgi_params;
        fastcgi_split_path_info       ^(.+\.php)(/.+)$;
        fastcgi_param PATH_INFO       $fastcgi_path_info;
        fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

### 重定向

```
server 
{
     listen 80;
     server_name test.com;
    
     index index.html;
     rewrite ^/ https://www.test.com$is_args$args;
     access_log off;
}
 ```
 
 ### 获取 header 变量
 
 > 可以配合个性化日志记录用户的信息
 
 1、例如想获取用户的ID，在PHP端输出特殊的header
 
 ```
 $user_id = Yii::$app->session['user_id'];
 if (empty($user_id)) {
     header('X-UID: 0');
 } else {
     header('X-UID: ' . $user_id);
 }
 ```
 
2、创建一种新的日志格式

log_format只能被存储在http段里，所以我们需要找到nginx.conf文件。

nginx缺省的日志格式第二部分就是用户信息，但通常什么也没有，只是一个-，这里我们它改造成我们从后端传进来的header信息。由上文我们创造的特殊header是X-UID，这里需要先做一个小的转换，把大写字母全部改为小写，把所有的-改为下划线，就变成了x_uid，然后在前面拼接上$upstream_http_，就得到了最终的结果$upstream_http_x_uid，然后把它插入到日志格式任何你想让它出现的地方：

```
log_format front '$remote_addr - $upstream_http_x_uid [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent"';
```
2、对用户隐藏id

在上面的第一步，我们用php输出了一个特殊header，本来我们这个header只是供nginx消费用的，但是这个header会被nginx原封不动地显示给前端，可能会有细心的用户感到不安。为此我们可以在nginx的server设置里再加一个小开关，隐藏掉这个头部：

```
proxy_hide_header X-UID;
```
    
### 个性化日志

- log_format: 用来定义记录日志的格式（可以定义多种日志格式，取不同名字即可）
- access_log：用来指定日至文件的路径及使用的何种日志格式记录日志

#### log_format
 
1、默认值
 
```
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                     '"$http_user_agent" "$http_x_forwarded_for"';
```

2、语法格式及参数语法

```
log_format     <NAME>      <Strin­­­g>;
关键字         格式标签      日志格式
```

变量名称 | 说明
---|---
$remote_addr | 记录访问网站的客户端地址
$remote_user | 远程客户端用户名
$time_local | 记录访问时间与时区
$request | 用户的http请求起始行信息
$status | http状态码，记录请求返回的状态码，例如：200、301、404等
$body_bytes_sent | 服务器发送给客户端的响应body字节数\
$http_referer | 记录此次请求是从哪个连接访问过来的，可以根据该参数进行防盗链设置。
$http_user_agent | 记录客户端访问信息，例如：浏览器、手机客户端等
$http_x_forwarded_for | 当前端有代理服务器时，设置web节点记录客户端地址的配置，此参数生效的前提是代理服务器也要进行相关的x_forwarded_for设置

#### access_log

1、默认值
 
```
access_log  logs/access.log  main;
```

2、语法格式及参数语法

```
 access_log    <FILE>    <NAME>;
 关键字         日志文件   格式标签
```

其他语法：

```
access_log    off;  #关闭access_log，即不记录访问日志
access_log path [format [buffer=size [flush=time]] [if=condition]];
access_log path format gzip[=level] [buffer=size] [flush=time] [if=condition];
access_log syslog:server=address[,parameter=value] [format [if=condition]];
说明：
buffer=size  #为存放访问日志的缓冲区大小
flush=time  #为缓冲区的日志刷到磁盘的时间
gzip[=level]  #表示压缩级别
[if = condition]  #表示其他条件
```

ELK 收集统计数据
 
 ```
 log_format nginx_log_json '{"accessip_list":"$proxy_add_x_forwarded_for","client_ip":"$clientRealIp","http_host":"$host","@timestamp":"$time_iso8601","method":"$request_method","url":"$request_uri","status":"$status","http_referer":"$http_referer","body_bytes_sent":"$body_bytes_sent","request_time":"$request_time","http_user_agent":"$http_user_agent","total_bytes_sent":"$bytes_sent","server_ip":"$server_addr"}';
 access_log /var/log/nginx/test.log nginx_log_json;
```

### 默认变量详解

变量名称 | 说明
---|---
$args | 请求中的参数值
$query_string | 同 $args
$arg_NAME | GET请求中NAME的值
$is_args | 如果请求中有参数，值为"?"，否则为空字符串
$uri | 请求中的当前URI(不带请求参数，参数位于$args)，可以不同于浏览器传递的$request_uri的值，它可以通过内部重定向，或者使用index指令进行修改，$uri不包含主机名，如"/foo/bar.html"。
$document_uri | 同 $uri
$document_root | 当前请求的文档根目录或别名
$host | 优先级：HTTP请求行的主机名>"HOST"请求头字段>符合请求的服务器名.请求中的主机头字段，如果请求中的主机头不可用，则为服务器处理请求的服务器名称
$hostname | 主机名
$https | 如果开启了SSL安全模式，值为"on"，否则为空字符串。
$binary_remote_addr | 客户端地址的二进制形式，固定长度为4个字节
$body_bytes_sent | 传输给客户端的字节数，响应头不计算在内；这个变量和Apache的mod_log_config模块中的"%B"参数保持兼容
$bytes_sent | 传输给客户端的字节数
$connection | TCP连接的序列号
$connection_requests | TCP连接当前的请求数量
$content_length | "Content-Length" 请求头字段
$content_type | "Content-Type" 请求头字段
$cookie_name | cookie名称
$limit_rate | 用于设置响应的速度限制
$msec | 当前的Unix时间戳
$nginx_version | nginx版本
$pid | 工作进程的PID
$pipe | 如果请求来自管道通信，值为"p"，否则为"."
$proxy_protocol_addr | 获取代理访问服务器的客户端地址，如果是直接访问，该值为空字符串
$realpath_root | 当前请求的文档根目录或别名的真实路径，会将所有符号连接转换为真实路径
$remote_addr | 客户端地址
$remote_port | 客户端端口
$remote_user | 用于HTTP基础认证服务的用户名
$request | 代表客户端的请求地址
$request_body | 客户端的请求主体：此变量可在location中使用，将请求主体通过proxy_pass，fastcgi_pass，uwsgi_pass和scgi_pass传递给下一级的代理服务器
$request_body_file | 将客户端请求主体保存在临时文件中。文件处理结束后，此文件需删除。如果需要之一开启此功能，需要设置client_body_in_file_only。如果将次文件传 递给后端的代理服务器，需要禁用request body，即设置proxy_pass_request_body off，fastcgi_pass_request_body off，uwsgi_pass_request_body off，or scgi_pass_request_body off
$request_completion | 如果请求成功，值为"OK"，如果请求未完成或者请求不是一个范围请求的最后一部分，则为空
$request_filename | 当前连接请求的文件路径，由root或alias指令与URI请求生成
$request_length | 请求的长度 (包括请求的地址，http请求头和请求主体)
$request_method | HTTP请求方法，通常为"GET"或"POST"
$request_time | 处理客户端请求使用的时间,单位为秒，精度毫秒； 从读入客户端的第一个字节开始，直到把最后一个字符发送给客户端后进行日志写入为止。
$request_uri | 这个变量等于包含一些客户端请求参数的原始URI，它无法修改，请查看$uri更改或重写URI，不包含主机名，例如："/cnphp/test.php?arg=freemouse"
$scheme | 请求使用的Web协议，"http" 或 "https"
$server_addr | 服务器端地址，需要注意的是：为了避免访问linux系统内核，应将ip地址提前设置在配置文件中
$server_name | 服务器名
$server_port | 服务器端口
$server_protocol | 服务器的HTTP版本，通常为 "HTTP/1.0" 或 "HTTP/1.1"
$status | HTTP响应代码
$time_iso8601 | 服务器时间的ISO 8610格式
$time_local | 服务器时间（LOG Format 格式）
$cookie_NAME | 客户端请求Header头中的cookie变量，前缀"$cookie_"加上cookie名称的变量，该变量的值即为cookie名称的值
$http_NAME | 匹配任意请求头字段；变量名中的后半部分NAME可以替换成任意请求头字段，如在配置文件中需要获取http请求头："Accept-Language"，$http_accept_language即可
$http_cookie | 
$http_host | 请求地址，即浏览器中你输入的地址（IP或域名）
$http_referer | url跳转来源,用来记录从那个页面链接访问过来的
$http_user_agent | 用户终端浏览器等信息
$http_x_forwarded_for | 
$sent_http_NAME | 可以设置任意http响应头字段；变量名中的后半部分NAME可以替换成任意响应头字段，如需要设置响应头Content-length，$sent_http_content_length即可
$sent_http_cache_control | 
$sent_http_connection | 
$sent_http_content_type | 
$sent_http_keep_alive | 
$sent_http_last_modified | 
$sent_http_location | 
$sent_http_transfer_encoding | 
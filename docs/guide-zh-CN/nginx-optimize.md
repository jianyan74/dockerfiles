目录

- worker 进程数
- 单个进程允许的最大连接数
- 开启高效文件传输模式
- 连接超时时间
- 限制上传文件的大小
- FastCGI 相关参数调优
- gzip 压缩
- 配置 expires 缓存期限
- 配置防盗链

> 注意: 配置 `nginx.conf`

### worker 进程数

Nginx 有 master 和 worker 两种进程
- master 进程用于管理 worker 进程
- worker 进程用于 nginx 服务

查看cpu核心数量

```
grep -c processor /proc/cpuinfo
```

优化: 进程数应该设置为服务器 CPU 的核数

> worker_processes最多开启8个，8个以上性能就不会再提升了，而且稳定性会变的更低，因此8个进程够用了

```
worker_processes  2;
```

根据 worker_processes 绑定进程到不同的 CPU 上 

优化: worker_cpu_affinity

```
# 2核CPU的配置
worker_cpu_affinity 01 10;
# 4核CPU的配置
worker_cpu_affinity 0001 0010 0100 1000; 
# 8核CPU的配置  
worker_cpu_affinity 00000001 00000010 00000100 00001000 00010000 00100000 01000000 1000000;
```

### 单个进程允许的最大连接数

控制单个进程允许的最大连接数的参数为 worker_connections ，这个参数要根据服务器性能和内存使用量来调整。  
总并发连接数 = worker_processes * worker_connections 总数保持在 3w 左右即可

```
查看 work 最大打开文件数
ulimit -HSn

// 设置最大打开数量(可选)
ulimit -HSn 65535
```

优化: worker 进程打开的最大文件数

```
worker_rlimit_nofile 65535;
```

优化: 单个进程允许的最大连接数的参数

```
events {
    use epoll;
    worker_connections 15000;
}
```

### 开启高效文件传输模式

- sendfile: 参数用于开启文件的高效传输模式，该参数实际上是激活了 sendfile() 功能
  - sendfile() 是作用于两个文件描述符之间的数据拷贝函数，这个拷贝操作是在内核之中的，被称为 "零拷贝" 。sendfile 比 read 和 write 函数要高效得多，因为 read 和 write 函数要把数据拷贝到应用层再进行操作
- tcp_nopush: 参数用于激活 Linux 上的 TCP_CORK socket 选项，此选项仅仅当开启 sendfile 时才生效，tcp_nopush 参数可以把 http response header 和文件的开始部分放在一个文件里发布，以减少网络报文段的数量

```
http {
    ...
    
    sendfile      on;    # 开启文件的高效传输模式
    tcp_nopush    on;    # 激活 TCP_CORK socket 选择
    tcp_nodelay   on;    # 数据在传输的过程中不进缓存
    
    ...
}
```

### 连接超时时间

连接超时的作用

- 将无用的连接设置为尽快超时，可以保护服务器的系统资源（CPU、内存、磁盘）
- 当连接很多时，及时断掉那些建立好的但又长时间不做事的连接，以减少其占用的服务器资源
- 如果黑客攻击，会不断地和服务器建立连接，因此设置连接超时以防止大量消耗服务器的资源
- 如果用户请求了动态服务，则 Nginx 就会建立连接，请求 FastCGI 服务以及后端 MySQL 服务，设置连接超时，使得在用户容忍的时间内返回数据

连接超时存在的问题

- 服务器建立新连接是要消耗资源的，因此，连接超时时间不宜设置得太短，否则会造成并发很大，导致服务器瞬间无法响应用户的请求。
- 有些 PHP 站点会希望设置成短连接，因为 PHP 程序建立连接消耗的资源和时间相对要少些。
- 有些 Java 站点会希望设置成长连接，因为 Java 程序建立连接消耗的资源和时间要多一些，这是由语言的运行机制决定的。

设置超时时间

- keepalive_timeout: 用于设置客户端连接保持会话的超时时间，超过这个时间服务器会关闭该连接
- client_header_timeout: 用于设置读取客户端请求头数据的超时时间，如果超时客户端还没有发送完整的 header 数据，服务器将返回 "Request time out (408)" 错误
- client_body_timeout: 用于设置读取客户端请求主体数据的超时时间，如果超时客户端还没有发送完整的主体数据，服务器将返回 "Request time out (408)" 错误
- send_timeout: 用于指定响应客户端的超时时间，如果超过这个时间，客户端没有任何活动，Nginx 将会关闭连接
- tcp_nodelay: 默认情况下当数据发送时，内核并不会马上发送，可能会等待更多的字节组成一个数据包，这样可以提高 I/O 性能，但是，在每次只发送很少字节的业务场景中，使用 tcp_nodelay 功能，等待时间会比较长

```
http {
    ...
    
    keepalive_timeout  65;
    client_header_timeout 15;
    client_body_timeout 15;
    send_timeout 25;
    
    ...
}
```

### 限制上传文件的大小

client_max_body_size 用于设置最大的允许客户端请求主体的大小。  
在请求头中有 "Content-Length" ，如果超过了此配置项，客户端会收到 413 错误，即请求的条目过大。

```
http {
    ...
    
    client_max_body_size 8m;    # 设置客户端最大的请求主体大小为 8 M
    
    ...
}
```

### FastCGI 相关参数调优

```
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    fastcgi_connect_timeout  240;    # Nginx服务器和后端FastCGI服务器连接的超时时间
    fastcgi_send_timeout     240;    # Nginx允许FastCGI服务器返回数据的超时时间，即在规定时间内后端服务器必须传完所有的数据，否则Nginx将断开这个连接
    fastcgi_read_timeout     240;    # Nginx从FastCGI服务器读取响应信息的超时时间，表示连接建立成功后，Nginx等待后端服务器的响应时间
    fastcgi_buffer_size      64k;    # Nginx FastCGI 的缓冲区大小，用来读取从FastCGI服务器端收到的第一部分响应信息的缓冲区大小
    fastcgi_buffers        4 64k;    # 设定用来读取从FastCGI服务器端收到的响应信息的缓冲区大小和缓冲区数量
    fastcgi_busy_buffers_size    128k;    # 用于设置系统很忙时可以使用的 proxy_buffers 大小
    fastcgi_temp_file_write_size 128k;    # FastCGI 临时文件的大小
    # fastcti_temp_path            /data/ngx_fcgi_tmp;    # FastCGI 临时文件的存放路径
    fastcgi_cache_path           /data/ngx_fcgi_cache  levels=2:2  keys_zone=ngx_fcgi_cache:512m  inactive=1d  max_size=40g;    # 缓存目录
     
    server {
        listen       80;
        server_name  www.abc.com;
        location / {
            root   html/www;
            index  index.html index.htm;
        }
        location ~ .*\.(php|php5)?$ {
            root            html/www;
            fastcgi_pass    127.0.0.1:9000;
            fastcgi_index   index.php;
            include         fastcgi.conf;
            fastcgi_cache   ngx_fcgi_cache;            # 缓存FastCGI生成的内容，比如PHP生成的动态内容
            fastcgi_cache_valid      200  302  1h;     # 指定http状态码的缓存时间，这里表示将200和302缓存1小时
            fastcgi_cache_valid      301  1d;          # 指定http状态码的缓存时间，这里表示将301缓存1天
            fastcgi_cache_valid      any  1m;          # 指定http状态码的缓存时间，这里表示将其他状态码缓存1分钟
            fastcgi_cache_min_uses   1;                # 设置请求几次之后响应被缓存，1表示一次即被缓存
            fastcgi_cache_use_stale  error  timeout  invalid_header  http_500;    # 定义在哪些情况下使用过期缓存
            fastcgi_cache_key        http://$host$request_uri;                    # 定义 fastcgi_cache 的 key
        }
    }
}
```


### gzip 压缩

gzip 压缩模块提供了压缩文件内容的功能，用户请求的内容在发送到客户端之前，Nginx 服务器会根据一些具体的策略实施压缩，以节约网站出口带宽，同时加快数据传输效率，来提升用户访问体验。  
需要压缩的对象有 html 、js 、css 、xml 、shtml ，图片和视频尽量不要压缩，因为这些文件大多都是已经压缩过的，如果再压缩可能反而变大。  
另外，压缩的对象必须大于 1KB，由于压缩算法的特殊原因，极小的文件压缩后可能反而变大。

```
http {
    gzip  on;                    # 开启压缩功能
    gzip_min_length  1k;         # 允许压缩的对象的最小字节
    gzip_buffers  4 32k;         # 压缩缓冲区大小，表示申请4个单位为32k的内存作为压缩结果的缓存
    gzip_http_version  1.1;      # 压缩版本，用于设置识别HTTP协议版本
    gzip_comp_level  9;          # 压缩级别，1级压缩比最小但处理速度最快，9级压缩比最高但处理速度最慢
    gzip_types  text/plain application/x-javascript text/css application/xml;    # 允许压缩的媒体类型
    gzip_vary  on;               # 该选项可以让前端的缓存服务器缓存经过gzip压缩的页面，例如用代理服务器缓存经过Nginx压缩的数据
}
```

### 配置 expires 缓存期限

expires 的功能就是给用户访问的静态内容设定一个过期时间。  
当用户第一次访问这些内容时，会把这些内容存储在用户浏览器本地，这样用户第二次及以后继续访问该网站时，浏览器会检查加载已经缓存在用户浏览器本地的内容，就不会去服务器下载了，直到缓存的内容过期或被清除。  
不希望被缓存的内容：广告图片、网站流量统计工具、更新很频繁的文件

```
server {
    listen       80;
    server_name  www.abc.com abc.com;
    root    html/www;
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|js|css)$    # 缓存的对象
    {
        expires 3650d;     # 缓存期限为 10 年
    }
}
```

### 配置防盗链

什么是防盗链？  
简单地说，就是其它网站未经许可，通过在其自身网站程序里非法调用其他网站的资源，然后在自己的网站上显示这些调用的资源，使得被盗链的那一端消耗带宽资源 。  
通过 HTTP referer 实现防盗链。

```
#第一种,匹配后缀
location ~ .*\.(gif|jpg|jpeg|png|bm|swf|flv|rar|zip|gz|bz2)$ {    # 指定需要使用防盗链的媒体资源
    access_log  off;                                              # 不记录日志
    expires  15d;                                                 # 设置缓存时间
    valid_referers  none  blocked  *.test.com  *.abc.com;         # 表示仅允许这些域名访问上面的媒体资源
    if ($invalid_referer) {                                       # 如果域名不是上面指定的地址就返回403
        return 403
    }
}
 
#第二种,绑定目录
location /images {  
    root /web/www/img;
    vaild_referers none blocked *.spdir.com *.spdir.top;
    if ($invalid_referer) {
        return 403;
    }
}
```
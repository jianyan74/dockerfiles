目录

- 预先设置
- 负载均衡

### 预先设置

> 在需要使用负载均衡的server中增加 proxy_pass http://bakend/;   
> bakend为你定义的upstream的名字

```
# 配置虚拟机
server {
    # 配置监听端口
    listen 80;
    
    # 配置访问域名
    server_name www.test.com;
    
    location / {
        # 设置被代理服务器的端口或套接字，以及URL
        proxy_pass http://backend;
        
        # 以下三行，目的是将代理服务器收到的用户的信息传到真实服务器上
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

注意：想避免资源浪费的情况可以在负载均衡的情况下分配到本机，由于80端口被占用可用8080端口

### 负载均衡

> upstream目前支持4种方式的分配

1、轮询（默认）

每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。  
weight是权重指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。

```
upstream backend {
    server 127.0.0.1:8080;
    server 192.168.80.121:80 down;
    server 192.168.80.122:80 weight=2;
    server 192.168.80.123:80 backup;
    server 192.168.80.125:80 weight=3 max_fails=1 fail_timeout=2
}
```

- down: 表示单前的server暂时不参与负载
- weight: 默认为1，weight越大，负载的权重就越大
- backup: 其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻
- max_fails: 允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream 模块定义的错误
- fail_timeout: max_fails请求失败后，暂停的时间

2、ip_hash

每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题  
使用 ip_hash 的时候，不能使用 weight 和 backup

```
upstream backend {
    ip_hash;
    server 192.168.0.14:80;
    server 192.168.0.15:80;
}
```

3、fair（第三方）

按后端服务器的响应时间来分配请求，响应时间短的优先分配

```
upstream backend {
    fair;
    server 192.168.0.14:80;
    server 192.168.0.15:80;
}
```

4、url_hash（第三方）

按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。  
例：在upstream中加入hash语句，server语句中不能写入weight等其他的参数，hash_method是使用的hash算法

```
upstream backend {
    server 192.168.0.14:80;
    server 192.168.0.15:80;
    hash $request_uri;
    hash_method crc32;
}
```
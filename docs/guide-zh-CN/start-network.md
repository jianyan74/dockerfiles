## 网络模式

目录

- 默认网络
- host
- none
- container
- bridge
- 创建加入自定义网络

> 开始之前先了解 docker 网络模式的区别是很重要的一部分，关乎到各自的容器是否能连得上，不会一头雾水

### 默认网络

当你安装Docker时，它会自动创建三个网络。你可以使用以下 docker network ls 命令列出这些网络

```
$ docker network ls
NETWORK ID          NAME                DRIVER
7fca4eb8c647        bridge              bridge
9f904ee27bf5        none                null
cf03ee007fb4        host                host
```

### host

与宿主机在同一个网络中，但没有独立IP地址。比如 nginx 安装后容器映射的是 80 端口，那么 `127.0.0.1:80` 就可以直接访问

```
# 查看容器;
$ docker ps  
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
74c911272942        nginx               "nginx -g 'daemon ..."   25 seconds ago      Up 25 seconds   
```

打印看这里的 PORTS 是没有任何映射信息的，就如直接跑在宿主机中一样。但是，容器的其他方面，如文件系统、进程列表等还是和宿主机隔离的

```
$ netstat -nplt | grep nginx
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      27340/nginx: master
```

> 注意：你想以 `127.0.0.1:80` 的方式去访问，其容器本身也需要以 host 的网络模式去运行

### none

该模式将容器放置在它自己的网络栈中，但是并不进行任何配置。实际上，该模式关闭了容器的网络功能，在以下两种情况下是有用的：容器并不需要网络（例如只需要写磁盘卷的批处理任务）。

### containert

在理解了host模式后，这个模式也就好理解了。这个模式指定新创建的容器和已经存在的一个容器共享一个Network Namespace，而不是和宿主机共享。新创建的容器不会创建自己的网卡，配置自己的IP，而是和一个指定的容器共享IP、端口范围等。同样，两个容器除了网络方面，其他的如文件系统、进程列表等还是隔离的。两个容器的进程可以通过lo网卡设备通信。

### bridge

> 这个模式是默认模式，如果你不指定网络模式，自动为 bridge

该模式创建后会动态生成一个 ip 地址，别的容器不能直接通过 `127.0.0.1:端口号` 的方式去访问，可以用 links 模式 直接连接，因为 links 会在对应的容器直接把 ip 映射过去

举个例子：

docker-compose.yml

```
  ...
  
  php-fpm:
    image: php:7.3-fpm
    ports:
      - "9000:9000"
    restart: always
    command: php-fpm

  nginx:
    image: nginx:1.17
    depends_on: # 先启动 php-fpm 镜像
      - php-fpm
    links: # 我是关联
      - php-fpm:php-fpm
  
  ...
```

那么我就可以直接在 nginx 配置里面使用 php-fpm 就可以直接连上该容器，例如：

```
    ...

    # 匹配.php后缀的文件
    location ~ \.php$
    {
        try_files $uri =404;

        # fastcgi_pass  unix:/var/run/php/php7.0-fpm.sock;
        # php-fpm:9000 的 php-fpm 指的是容器名直接链接映射
        fastcgi_pass  php-fpm:9000;

        ...
    }
    
    ...
```

或者可以以分配过去的动态 ip 去访问，注意因为这个 ip 有可能会变动 所以不建议这种方式访问，或者你可以选择分配指定 ip

显示所有容器 ip 地址

```
docker inspect --format='{{.Name}} - {{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -aq)
```

会输出如下类似的，可以看出 php-fpm 地址为 172.21.0.4

```
/services_nginx_1 - 172.21.0.5
/services_php-fpm_1 - 172.21.0.4
/services_mysql-db_1 - 172.21.0.2
/services_redis-db_1 - 172.21.0.3
```

然后修改为配置内的 ip 地址为上述的 ip 

```
...
fastcgi_pass  172.21.0.4:9000;
...
```

### 创建加入自定义网络

1、创建网桥类型的自定义网络

```
$ docker network create --driver bridge my-net
```

2、查看网络

```
$  docker network ls
NETWORK ID          NAME                  DRIVER              SCOPE
8a5f470ffe77        bridge                bridge              local
10470df7f76f        host                  host                local
67b19436a9e3        my-net                bridge              local
3ba807431203        none                  null                local
```

3、查看容器

```
[root@iZuf69nwaq73qeeydl7havZ php]# docker ps
CONTAINER ID        IMAGE                         COMMAND                  CREATED             STATUS              PORTS                                                NAMES
bae3d39ea36a        php_php-fpm                   "docker-php-entrypoi…"   9 minutes ago       Up 3 minutes        0.0.0.0:9000->9000/tcp                               php-fpm
0dd3bb3e0e69        nginx_nginx                                            37 hours ago        Up 37 hours         0.0.0.0:80->80/tcp                                   nginx
824238b39be4        kibana_kibana                 "/usr/local/bin/dumb…"   37 hours ago        Up 37 hours         0.0.0.0:5601->5601/tcp                               kibana_kibana_1
```

4、加入网络

> 最后面的是 CONTAINER ID

```
// 将 php-fpm 加入 my-net 网络
docker network connect my-net bae3d39ea36a

// 将 nginx 加入 my-net 网络
docker network connect my-net 0dd3bb3e0e69
```

5、查看是否加入

输出信息主要看 Containers

```
$  docker network inspect my-net
[
    {
        "Name": "my-net",
        "Id": "07fa53026a89eb49abeef2aa7b330521a1a264f83a39439a92a2f389ecadd15b",
        "Created": "2019-09-23T22:58:02.739183084+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "192.168.32.0/20",
                    "Gateway": "192.168.32.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": true,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "1e3ec49c6661bdbc2c95fa66475a24499b523dd5adbd05ff16e327e3bb38f76c": {
                "Name": "php-fpm",
                "EndpointID": "b2528672d6b90ce701c09aa3cbbc59fc2a26bde961a76833a5ac2d6b76ed7fae",
                "MacAddress": "02:42:c0:a8:20:02",
                "IPv4Address": "192.168.32.2/20",
                "IPv6Address": ""
            },
            "fdf58de260558d339e9f02df640c145811f3d672a2073937f02dff116c9cfb47": {
                "Name": "nginx",
                "EndpointID": "a9d9ab8252718a694481351a6f22748fe8c69e5df6a9c113c7f155326624c084",
                "MacAddress": "02:42:c0:a8:20:03",
                "IPv4Address": "192.168.32.3/20",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {
            "com.docker.compose.network": "default2",
            "com.docker.compose.project": "php",
            "com.docker.compose.version": "1.24.1"
        }
    }
]

```

这样子可以以 `php-fpm:端口号` 来进行访问了,这里的别名为 Containers 里面的 Name

5、其他说明

有些软件创建容器的时候会也会默认创建一些容器，例如 `elasticsearch` 的默认的 `elasticsearch_default`，如果你想和这些容器在同一个网络内请在 `docker-compose.yml` 里面配置

```
network_mode: elasticsearch_default
```



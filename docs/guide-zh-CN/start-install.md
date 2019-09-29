## 程序安装

- 环境要求
- 前言
- 安装

### 环境要求

- docker
- docker-compose

> 注意必看

- [环境搭建](start-environment.md)
- [常用命令](start-command.md)
- [常见问题](start-issue.md)
- [网络模式](start-network.md)

### 前言

> 以下说明和安装都是单个容器进行安装，以 services/php 容器为例，混合容器搭建的可自行看单独的安装说明

执行前如果需要定制化可自行修改 docker-composer.yml 文件，注意默认映射 9000 端口到宿主机上，宿主机想直接访问可用 `127.0.0.1:9000` 的方式访问。

其他容器想访问当前的容器，有以下几种方式:

1. 其他容器以 host [网络模式](start-network.md)启动后直接用 `127.0.0.1:9000` 访问
2. 其他容器加入到当前的网络进行访问 如果没有请去[创建加入自定义网络](start-network.md)
3. 查找当前容器的 [动态IP](start-command.md) 用 `动态IP:9000` 方式去绑定(不建议,因为可能会变)
4. 设置当前的容器 固定IP，然后以 `固定IP:9000` 的方式去访问
5. 放入其他容器的配置以 links 的方式去关联，例如当前容器名称 `php-fpm` ，那边其他容器就可以以 `php-fpm:9000` 的方式去访问，这种情况就是一个配置文件来配置不同的软件，例如一键搭建 [nginx+php+mysql+redis](other-nginx-php-mysql-redis.md) 环境

### 安装

1、进入程序目录

```
cd services/php
```

2、 执行安装/启动

```
docker-compose up
```

3、守护模式启用

```
docker-compose up -d
```

使用 docker-compose 基本上就这么简单，docker 就跑起来了  

4、其他说明

- 有些软件还需要配置的请按照自己的需要看附加的配置说明
- 有些软件默认配置了密码请看[默认密码](start-default-password.md)文档按需使用

5、其他简单命令

这里的 php-fpm 是 docker-compose.yml 里面设置的容器名称

- `docker-compose stop` 关闭
- `docker-compose start` 开启
- `docker-compose restart` 重启
- `docker-compose down` 关闭容器并删除服务
- `docker-compose rm` 删除服务
- `docker-compose logs php-fpm` 查看容器日志
- `docker-compose exec php-fpm bash` 进入容器

更多可以去看 [常用命令](start-command.md)

## nmpr

目录

- 前言
- 安装
- Demo 站点搭建

### 前言

软件：

- nginx
- php
- mysql
- redis

php扩展：

- swoole
- mongo
- amqp
- redis

### 安装

1、进入程序目录

```
cd gather/nmpr
```

2、 执行安装/启动

```
docker-compose up
```

3、守护模式启用

```
docker-compose up -d
```

### Demo 站点搭建

1、进入 app/php 目录并克隆

```
git clone https://git.oschina.net/jianyan94/rageframe2.git
```

2、重新进入 gather/nmpr 目录

```
cd gather/nmpr
```

3、进入容器


```
docker-compose exec php-fpm bash
```

3、进入容器


```
cd /data/www/rageframe2
```

4、 安装依赖

```
composer install
```

5、初始化项目

```
php init // 然后输入0回车,再输入yes回车，注意如果想修改应用入口请先看入口修改文档
```

6、配置数据库信息，默认的数据库密码看 [默认密码](start-default-password.md)

```
找到 common/config/main-local.php 并配置相应的信息
```

7、项目根目录执行安装数据库

```
php ./yii migrate/up
```

8、退出容器

```
exit
```

9、域名解析

找到 `services/nginx/conf.d` 下的 demo.cnf 里修改第三行 server_name

```
server_name [为你自己的域名]; 
```

10、重启一下 nginx 容器
### 目录

- 安装composer时候出现错误
- 启动后出现类似的错误
- 容器内数据库连不上怎么办

### 安装composer时候出现错误

```
curl: (56) SSL read: error:00000000:lib(0):func(0):reason(0), errno 104
```

##### 解决方法

网络请求问题，重新执行一下 `docker-compose up`

### 启动后出现类似的错误

```
Removing intermediate container d91a90bb00b5
 ---> 40e52ab712e3
Successfully built 40e52ab712e3
Successfully tagged services_nginx:latest
Creating services_mysql-db_1 ... error
Creating services_mysql-db_1 ... 
Creating services_redis-db_1 ... error

ERROR: for services_mysql-db_1  Cannot start service mysql-db: driver failed programming external connectivity on endpoint services_mysql-db_1 (2cce096d8d21d8324c101d2bd50f8f90b55f37a253be57a4fbfc9098bcf20d61): Error starting userland proxy: listen tcp 0.0.0.0:3306: bind: address already in use

ERROR: for services_redis-db_1  Cannot start service redis-db: driver failed programming external connectivity on endpoint services_redis-db_1 (617a4e9988ef5c3d0f74bbe4988a916d9f654d3f168bbead9451c3b97842e0bc): Error starting userland proxy: listen tcp 0.0.0.0:6379: bind: address already in use

ERROR: for redis-db  Cannot start service redis-db: driver failed programming external connectivity on endpoint services_redis-db_1 (617a4e9988ef5c3d0f74bbe4988a916d9f654d3f168bbead9451c3b97842e0bc): Error starting userland proxy: listen tcp 0.0.0.0:6379: bind: address already in use

ERROR: for mysql-db  Cannot start service mysql-db: driver failed programming external connectivity on endpoint services_mysql-db_1 (2cce096d8d21d8324c101d2bd50f8f90b55f37a253be57a4fbfc9098bcf20d61): Error starting userland proxy: listen tcp 0.0.0.0:3306: bind: address already in use
ERROR: Encountered errors while bringing up the project.
```

解决方法

由于映射端口和宿主机端口冲突导致的，修改 docker-composer.yml 文件里面对应的容器映射端口，比如 3306:3306 => 3307:3306

### 容器内数据库连不上怎么办

##### 解决方法

显示所有容器IP地址

```
docker inspect --format='{{.Name}} - {{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -aq)
```

会输出如下类似的，可以看出mysql地址为172.21.0.2，redis地址为172.21.0.3

```
/services_nginx_1 - 172.21.0.5
/services_php-fpm_1 - 172.21.0.4
/services_mysql-db_1 - 172.21.0.2
/services_redis-db_1 - 172.21.0.3
```

然后修改为配置内的ip地址为上述的ip

> 注意：另外一种解决方法直接用别名访问 例如 mysql-db
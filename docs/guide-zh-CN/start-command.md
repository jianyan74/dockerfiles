## 常用命令

重新编译安装

```
docker-compose build

// 强制不从缓存读取
docker-compose build --no-cache
```

查看mysql日志

```
docker-compose logs -f mysql
```

进入容器

```
// 例如 docker-compose exec php-fpm bash
docker-compose exec [images] bash 
```

查看已经创建的容器

```
docker ps -a 
```
查看已经启动的容器

```
docker ps -s 
```

启动容器名为con_name的容器

```
docker start con_name 
```

停止容器名为con_name的容器

```
docker stop con_name 
```

删除容器名为con_name的容器

```
docker rm con_name 
```

重命名一个容器 

```
docker rename old_name new_name 
```

将终端附着到正在运行的容器名为con_name的容器的终端上面去，前提是创建该容器时指定了相应的sh

```
docker attach con_name 
```

根据容器ID查看容器报错

```
docker inspect container [container id]
```

杀死所有正在运行的容器

```
docker kill $(docker ps -a -q)
```

删除所有已经停止的容器

```
docker rm $(docker ps -a -q)
```

删除所有未打 dangling 标签的镜像

```
docker rmi $(docker images -q -f dangling=true)
```

删除所有镜像

```
docker rmi $(docker images -q)
```

强制删除镜像名称中包含“doss-api”的镜像

```
docker rmi --force $(docker images | grep doss-api | awk '{print $3}')
```

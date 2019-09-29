## 默认密码

目录

- Mysql
- Redis
- RabbitMQ
- ELK

> 安全考虑请安装后自行修改

### Mysql

账号 | 密码 | 说明
---|---|---
MYSQL_ROOT_PASSWORD | ZPzmnmszd5vO6rd | root密码
MYSQL_DATABASE | rageframe |  数据库名
MYSQL_USER | rageframe | 用户账号
MYSQL_PASSWORD | MofVpkK | 用户密码

如何修改？

编辑 `services/mysql/docker-compose.yml`文件在46行左右修改初始密码;

如果是混合安装的请自行去单独的配置文件修改

### Redis

默认密码: B45Ybmzu

如何修改？

编辑 `services/redis/redis.conf`找到 `requirepass` 并修改


### RabbitMQ

默认账号: YtxiYt  
默认密码: zMHjxcSSD

如何修改？

编辑 `services/redis/docker-compose.yml`找到 `RABBITMQ_DEFAULT_USER` 和 `RABBITMQ_DEFAULT_PASS` 并修改

### ELK

#### 密码初始化

默认配置了必须使用密码且为当节点

查看文件

```
elasticsearch.yml
```

看见开启了密码访问如果不需要请关闭，修改后注意重启

```
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
```

初始化

1、启动 elasticsearch

```
docker-compose exec elasticsearch bash
```

2、进入目录

```
cd /usr/share/elasticsearch/bin
```

3、执行初始化命令

> 注意这里会一起添加 apm_system、kibana、logstash_system、beats_system、remote_monitoring_user、elastic 密码
> 按照步骤往下执行即可

```
/elasticsearch-setup-passwords interactive
```
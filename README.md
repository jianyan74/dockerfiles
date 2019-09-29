## Dockerfiles

搭建基础公用环境包，方便使用，支持业务不断的提升而需要一些软件的支持但由于本身不懂或耗时导致不方便安装升级的一种解决方案

> 目前还没有完整的测试过请不要在生产环境中使用

## 简介
用docker容器服务的方式搭建环境，易于维护、升级。使用前需了解Docker的基本概念，常用基本命令。
可以一条条命令执行docker命令来构建镜像，容器。这里推荐使用docker-compose来管理，执行项目，下面是使用流程。

#### 目录

目录 | 说明
---|---
--- app | 应用目录
--- --- php | php 应用运行目录
--- data | mongo、 mysql 等数据库文件存储
--- --- backups | 数据备份目录
--- docs | 使用文档
--- logs | nginx、 mongo、 mysql、php 等日志
--- sercices | 服务软件配置包
--- --- mongo | ubuntu: 4.2
--- --- mysql | ubuntu: 8.latest
--- --- nginx | ubuntu: 1.17
--- --- redis | ubuntu: 5.latest
--- --- rabbitmq | ubuntu: 3.7-management
--- --- php | contos: 7.3.latest
--- --- elasticsearch | 7.3.2
--- --- logstash | 7.3.2
--- --- kibana | 7.3.2
--- --- kafka(zk) | latest
--- --- zabbix(client) | 4.0.1
--- --- filebeat | 7.3.2
--- --- metricbeat | 7.3.2
--- gather | 常用集合
--- --- zabbix | zabbix server + nginx + mysql + java
--- --- ---  data | zabbix mysql 数据
--- --- elk | elasticsearch + logstash + kibana
--- --- nmpr | nginx + mysql + php + redis
--- demo | Demo 案例
--- --- elasticsearch-cluster | elasticsearch 集群案例
--- --- mysql-proxy | mysql 读写分离案例(待完成)
--- --- nginx-load-leveling | nginx 负载均衡(待完成)

## 文档

[安装软件](docs/guide-zh-CN/start-install.md) · [本地文档](docs/guide-zh-CN/README.md) · [网络模式](docs/guide-zh-CN/start-network.md) · [常见问题](docs/guide-zh-CN/start-issue.md) · [更新历史](docs/guide-zh-CN/start-update-log.md)

## 问题反馈

在使用中有任何问题，欢迎反馈给我，可以用以下联系方式跟我交流

QQ群：[655084090](https://jq.qq.com/?_wv=1027&k=4BeVA2r)

## 学习文档

[Docker 配置详解](https://www.jianshu.com/p/2217cfed29d7)

[Docker 入门教程](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)

[Docker 微服务教程](http://www.ruanyifeng.com/blog/2018/02/docker-wordpress-tutorial.html)


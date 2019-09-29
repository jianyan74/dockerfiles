## zabbix-agent 安装

1、安装

```
// 注意要和zabbix版本一致
rpm -i https://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm
yum -y install zabbix-agent-4.0.1
```

2、编辑

```
vim /etc/zabbix/zabbix_agentd.conf
```

3、修改为

```
Server=172.18.0.4  ## 注意是你的可访问地址
UnsafeUserParameters=1
Include=/etc/zabbix/zabbix_agentd.d/*.conf
```
4、启动
```
systemctl start zabbix-agent.service
```
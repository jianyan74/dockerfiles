目录

- 配置调整

### 配置调整

配置 | 默认 | 说明
---|---|---
bind | 127.0.0.1 | 绑定IP(修改绑定IP可能会存在安全隐患)
port | 6379 | 绑定端口
timeout | 0 | 空闲连接超时时间,0表示不断开
maxclients | 10000 | 最大连接数
databases | 16 | 数据库数量
requirepass |  | redis密码,留空代表没有设置密码
maxmemory | 0 | MB,最大使用内存，0表示不限制

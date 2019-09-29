## Redis 持久化

目录

- AOF 持久化
- RDB 持久化

### AOF 持久化(日志)

```
# 是否开启aof
appendonly yes

# 文件名称
appendfilename "appendonly.aof"

# 同步方式
appendfsync everysec

# aof重写期间是否同步
no-appendfsync-on-rewrite no

# 重写触发配置
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# 加载aof时如果有错如何处理
aof-load-truncated yes

# 文件重写策略
aof-rewrite-incremental-fsync yes
```

重点解释一些关键的配置：

`appendfsync everysec` 它其实有三种模式:

- always：把每个写命令都立即同步到aof，很慢，但是很安全
- everysec：每秒同步一次，是折中方案
- no：redis不处理交给OS来处理，非常快，但是也最不安全

一般情况下都采用 `everysec` 配置，这样可以兼顾速度与安全，最多损失 1s 的数据。

`aof-load-truncated yes` 如果该配置启用，在加载时发现 aof 尾部不正确是，会向客户端写入一个 log ，但是会继续执行，如果设置为 no ，发现错误就会停止，必须修复后才能重新加载。

### RDB 持久化(快照)

任意触发一条生效，若 3 个选项都屏蔽，则 rdb 禁用

```
# 时间策略
save 900 1
save 300 10
save 60 10000

# 文件名称
dbfilename dump.rdb

# 文件保存路径
dir /home/app/redis/data/

# 如果持久化出错，主进程是否停止写入
stop-writes-on-bgsave-error yes

# 是否压缩
rdbcompression yes

# 导入时是否检查
rdbchecksum yes
```

配置其实非常简单，这里说一下持久化的时间策略具体是什么意思。

- save 900 1 表示 900s 内如果有 1 条是写入命令，就触发产生一次快照，可以理解为就进行一次备份
- save 300 10 表示 300s 内有 10 条写入，就产生快照

下面的类似，那么为什么需要配置这么多条规则呢？因为 Redis 每个时段的读写请求肯定不是均衡的，为了平衡性能与数据安全，我们可以自由定制什么情况下触发备份。所以这里就是根据自身 Redis 写入情况来进行合理配置。

`stop-writes-on-bgsave-error yes` 这个配置也是非常重要的一项配置，这是当备份进程出错时，主进程就停止接受新的写入操作，是为了保护持久化的数据一致性问题。如果自己的业务有完善的监控系统，可以禁止此项配置， 否则请开启。

关于压缩的配置 `rdbcompression yes` ，建议没有必要开启，毕竟 Redis 本身就属于CPU密集型服务器，再开启压缩会带来更多的 CPU 消耗，相比硬盘成本，CPU 更值钱。

当然如果你想要禁用RDB配置，也是非常容易的，只需要在save的最后一行写上：save ""
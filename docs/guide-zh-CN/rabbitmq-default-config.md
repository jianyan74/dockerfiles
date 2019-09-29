## RabbitMQ 完整配置

目录

- 配置文件 rabbitmq.conf
- 环境变量文件 rabbitmq-env.conf
- 补充配置文件 advanced.config

rabbitmq.conf和rabbitmq-env.conf的位置

- 在二进制安装中路径是在 ：安装目录下的/etc/rabbitmq/
- rpm 安装： /etc/rabbitmq/

如果rabbitmq.conf和rabbitmq-env.conf 的两个文件不存在，那么我们可以创建该文件，然后我们可以通过环境变量
指定该文件的位置。

补充 ：

- rabbitmqctl rabbitmqctl 是管理虚拟主机和用户权限的工具
- rabbitmq-plugins 是管理插件的工具

### 配置文件 rabbitmq.conf

sysctl 语法：

- 单个信息都在一行里面
- 配置信息以key value 的形式保存。
- ‘#’ 开头表示注释。

属性 | 描述 | 默认值
---|---|---
listeners | 要监听 AMQP 0-9-1 and AMQP 1.0 的端口 | listeners.tcp.default = 5672
num_acceptors.tcp | 接受tcp连接的erlang 进程数 | num_acceptors.tcp = 10
handshake_timeout | AMQP 0-9-1 超时时间，也就是最大的连接时间，单位毫秒 | handshake_timeout = 10000
listeners.ssl | 启用TLS的协议 | 默认值为none
num_acceptors.ssl | 接受基于TLS协议的连接的erlang 进程数 | num_acceptors.ssl = 10
ssl_options | TLS 配置 | ssl_options =none
ssl_handshake_timeout | TLS 连接超时时间 单位为毫秒 | ssl_handshake_timeout = 5000
vm_memory_high_watermark | 触发流量控制的内存阈值，可以为相对值(0.5),或者绝对值 vm_memory_high_watermark.relative = 0.6 ,vm_memory_high_watermark.absolute = 2GB | 默认vm_memory_high_watermark.relative = 0.4
vm_memory_calculation_strategy | 内存使用报告策略，assigned：使用Erlang内存分配器统计信息 rss：使用操作系统RSS内存报告。这使用特定于操作系统的方法，并可能启动短期子进程。legacy：使用遗留内存报告（运行时认为将使用多少内存）。这种策略相当不准确。erlang 与legacy一样 是为了向后兼容 | vm_memory_calculation_strategy = allocated
vm_memory_high_watermark_paging_ratio | 当内存的使用达到了50%后,队列开始将消息分页到磁盘 | vm_memory_high_watermark_paging_ratio = 0.5
total_memory_available_override_value | 该参数用于指定系统的可用内存总量，一般不使用，适用于在容器等一些获取内存实际值不精确的环境 | 默认未设置
disk_free_limit | Rabbitmq存储数据的可用空间限制，当低于该值的时候，将触发流量限制，设置可参考vm_memory_high_watermark参数 | disk_free_limit.absolute = 50MB
log.file.level | 控制记录日志的等级，有info,error,warning,debug | log.file.level = info
channel_max | 最大通道数，但不包含协议中使用的特殊通道号0，设置为0表示无限制，不建议使用该值，容易出现channel泄漏 | channel_max = 2047
channel_operation_timeout | 通道操作超时，单位为毫秒 | channel_operation_timeout = 15000
heartbeat | 表示连接参数协商期间服务器建议的心跳超时的值。如果两端都设置为0，则禁用心跳,不建议禁用 | heartbeat = 60
default_vhost | rabbitmq安装后启动创建的虚拟主机 | default_vhost = /
default_user | 默认创建的用户名 | default_user = guest
default_pass | 默认用户的密码 | default_pass = guest
default_user_tags | 默认用户的标签 | default_user_tags.administrator = true
default_permissions | 在创建默认用户是分配给默认用户的权限 | default_permissions.configure = .* default_permissions.read = .* default_permissions.write = .*
loopback_users | 允许通过回环地址连接到rabbitmq的用户列表,如果要允许guest用户远程连接(不安全)请将该值设置为none,如果要将一个用户设置为仅localhost连接的话，配置loopback_users.username =true(username要替换成用户名) | loopback_users.guest = true(默认为只能本地连接)
cluster_formation.classic_config.nodes | 设置集群节点cluster_formation.classic_config.nodes.1 = rabbit@hostname1
cluster_formation.classic_config.nodes.2 = rabbit@hostname2 | 默认为空，未设置
collect_statistics | 统计收集模式，none 不发出统计信息事件，coarse每个队列连接都发送统计一次,fine每发一条消息的统计数据 | collect_statistics = none
collect_statistics_interval | 统计信息收集间隔，以毫秒为单位 | collect_statistics_interval = 5000
delegate_count | 用于集群内通信的委托进程数。在多核的服务器上我们可以增加此值 | delegate_count = 16
tcp_listen_options | 默认的套接字选项 | tcp_listen_options.backlog = 128 .....
hipe_compile | 设置为true以使用HiPE预编译RabbitMQ的部分，HiPE是Erlang的即时编译器,启用HiPE可以提高吞吐量两位数，但启动时会延迟几分钟。Erlang运行时必须包含HiPE支持。如果不是，启用此选项将不起作用。HiPE在某些平台上根本不可用，尤其是Windows。 | hipe_compile = false
cluster_keepalive_interval | 节点应该多长时间向其他节点发送keepalive消息(以毫秒为单位),keepalive的消息丢失不会被视为关闭 | cluster_keepalive_interval = 10000
queue_index_embed_msgs_below | 消息的字节大小,低于该大小，消息将直接嵌入队列索引中 bytes | queue_index_embed_msgs_below = 4096
mnesia_table_loading_retry_timeout | 等待集群中Mnesia表可用的超时时间，单位毫秒 | mnesia_table_loading_retry_timeout = 30000
mnesia_table_loading_retry_limit | 集群启动时等待Mnesia表的重试次数，不适用于Mnesia升级或节点删除。 | mnesia_table_loading_retry_limit = 10
mirroring_sync_batch_size | 要在队列镜像之间同步的消息的批处理大小 | mirroring_sync_batch_size = 4096
queue_master_locator | 队列主节点的策略，有三大策略 min-masters，client-local，random | queue_master_locator = client-local
proxy_protocol | 如果设置为true ,则连接需要通过反向代理连接，不能直连接 | proxy_protocol = false
management.listener.port | rabbitmq web管理界面使用的端口 | management.listener.port = 15672

### advanced.config

[示例文件](https://github.com/rabbitmq/rabbitmq-server/blob/master/docs/advanced.config.example)
某些配置设置不可用或难以使用sysctl格式进行配置。因此，可以使用Erlang术语格式的其他配置文件advanced.config
它将与rabbitmq.conf 文件中提供的配置合并。

配置属性和描述 [官网链接](http://www.rabbitmq.com/configure.html#config-items)

属性	 | 描述 | 默认值
---|---|---
msg_store_index_module | 设置队列索引使用的模块 | {rabbit，[ {msg_store_index_module，rabbit_msg_store_ets_index} ]}
backing_queue_module | 队列内容的实现模块。 | {rabbit，[ {backing_queue_module，rabbit_variable_queue} ]}
msg_store_file_size_limit | 消息储存的文件大小,现有的节点更改是危险的，可能导致数据丢失 | 默认值16777216
trace_vhosts | 内部的tracer使用，不建议更改 | {rabbit，[ {trace_vhosts，[]} ]}
msg_store_credit_disc_bound | 设置消息储存库给队列进程的积分,默认一个队列进程被赋予4000个消息积分 | {rabbit, [{msg_store_credit_disc_bound, {4000, 800}}]}
queue_index_max_journal_entries | 队列的索引日志超过该阈值将刷新到磁盘 | {rabbit, [{queue_index_max_journal_entries, 32768}]}
lazy_queue_explicit_gc_run_operation_threshold | 在内存压力下为延迟队列设置的值，该值可以触发垃圾回收和减少内存使用，降低该值，会降低性能，提高该值，会导致更高的内存消耗 | {rabbit,[{lazy_queue_explicit_gc_run_operation_threshold, 1000}]}
queue_explicit_gc_run_operation_threshold | 在内存压力下，正常队列设置的值，该值可以触发垃圾回收和减少内存使用，降低该值，会降低性能，提高该值，会导致更高的内存消耗 | {rabbit, [{queue_explicit_gc_run_operation_threshold, 1000}]}

### 环境变量文件 rabbitmq-env.conf

某些配置设置不可用或难以使用sysctl格式进行配置。因此，可以使用Erlang术语格式的其他配置文件advanced.config
它将与rabbitmq.conf 文件中提供的配置合并。

[官网链接](http://www.rabbitmq.com/configure.html#config-items)
    
属性	 | 描述 | 默认值
---|---|---
RABBITMQ_NODE_IP_ADDRESS | 绑定的网络接口 | 默认为空字符串表示绑定本机所有的网络接口
RABBITMQ_NODE_PORT | 端口 | 默认为5672
RABBITMQ_DISTRIBUTION_BUFFER_SIZE | 节点之间通信连接的数据缓冲区大小 | 默认为128000,该值建议不要使用低于64MB
RABBITMQ_IO_THREAD_POOL_SIZE | 运行时用于io的线程数 | 建议不要低于32，linux默认为128 ，windows默认为64
RABBITMQ_NODENAME | rabbitmq节点名称，集群中要注意节点名称唯一 | linux 默认节点名为 rabbit@$hostname
RABBITMQ_CONFIG_FILE | rabbitmq 的配置文件路径，注意不要加文件的后缀(.conf) | 默认 $RABBITMQ_HOME/etc/rabbitmq/rabbitmq(二进制安装) /etc/rabbitmq/rabbitmq(rpm 安装)
RABBITMQ_ADVANCED_CONFIG_FILE | advanced.config文件路径 | 默认 $RABBITMQ_HOME/etc/rabbitmq/advanced(二进制安装) /etc/rabbitmq/advanced(rpm 安装)
RABBITMQ_CONF_ENV_FILE | 环境变量配置文件路径 | 默认 $RABBITMQ_HOME/etc/rabbitmq/rabbitmq-env.conf(二进制安装) /etc/rabbitmq/rabbitmq-env.conf(rpm 安装)
RABBITMQ_SERVER_CODE_PATH | 在使用HiPE 模块时需要使用 | 默认为空
RABBITMQ_LOGS | 指定日志文件位置 | 默认为 $RABBITMQ_HOME/etc/var/log/rabbitmq/

网络设置 http://www.rabbitmq.com/networking.html
RABBITMQ_DISTRIBUTION_BUFFER_SIZE 节点间通信缓冲区大小,默认值 128Mb,节点流量比较多的集群中，可以提升该值，建议该值不要低于64MB。

tcp 缓存区大小
下示例将AMQP 0-9-1连接的TCP缓冲区设置为192 KiB：

tcp_listen_options.backlog = 128
tcp_listen_options.nodelay = true
tcp_listen_options.linger.on = true
tcp_listen_options.linger.timeout = 0
tcp_listen_options.sndbuf = 196608
tcp_listen_options.recbuf = 196608

### 在生产环境中不适用的策略。

#### 1.4.1 vhost

在生产中，如果rabbitmq只为单个系统提供服务的时候，我们使用默认的(/)是可以的。在为多个系统提供的服务时，我们建议使用单独的vhost.

#### 1.4.2 user

对于生产环境，请删除默认用户(guest),默认用户只能从localhost 连接。
我们可以创建指定权限的单独用户为每个应用提供服务。对于开启权限用户来说，我们可以使用证书，和源ip地址过滤，和身份验证。来加强安全性。

#### 1.4.3 最大打开文件限制

在生产环境我们可能需要调整一些系统的默认限制，以便处理大量的并发连接和队列。
需要调整的值有打开的最大文件数。在生产环境为rabbitmq 运行的用户设定为65536，但是对于大多数开发环境来说，4096就已经足够了。

查看默认的打开文件的最大数量。


```
ulimit -n
```


更改方式：

1 临时修改

```
ulimit -n 65536
```

2 永久修改

##### 2.1如果是systemed 来进行管理的话我们可以编辑systemed配置文件来进行控制

```
[service]
    LimitNOFILE=300000
```

2.2 如果不是systemed
来进行管理的话，我们可以更改rabbitmq的启动加载的环境配置文件 rabbitmq-env.conf。在里面开头添加ulimit -S -n 4096，但该值不能超过系统的默认值的最大值。

```
ulimit -S -n 4096
```

2.3 系统级别更改

更改配置文件：/etc/security/limits.conf  
在文件末尾前面加入  
rabbitmq(启动的用户名) - nofile 65536  
如果更改前用户已经登录的话，需要重新登录下才能生效。

#### 1.4.4 内存
    当rabbitmq 检测到它使用的内存超过系统的40%，它将不会接受任何新的消息，这个值是由参数 vm_memory_high_watermark来控制的，默认值是一个安全的值，修改该值需要注意。 rabbitmq 的至少需要128MB,建议vm_memory_high_watermark 值为 0.4~0..66 ，不要使用大于0.7的值。

#### 1.4.5 磁盘
    磁盘默认的储存数据阈值是50MB,当低于该值的时候，将触发流量限制。50MB 只适用于开发环境，生产环境需要调高该值，不然容易由磁盘空间不足导致节点故障，也可能导致数据丢失。

    在生产环境中我们设置的值
        建议的最小值 {disk_free_limit, {mem_relative, 1.0}}
    它是基于mem_relative的值，例如在具有4GB内存的rabbitmq主机上，那么该磁盘的阈值就是4G,如果磁盘可用空间低于4G，所有生产者和消息都将拒绝。在允许恢复发布之前，通常需要消费者将队列消息消费完。
        建议的更安全值 {disk_free_limit, {mem_relative, 1.5}}
    在具有4GB内存的RabbitMQ节点上，如果可用磁盘空间低于6GB，则所有新消息都将被阻止，但是如果我们在停止的时候rabbitmq需要储存4GB的数据到磁盘，再下一次启动的时候，就只有2G空间了。
        建议的最大值 {disk_free_limit, {mem_relative, 2.0}}
        这个是最安全的值，如果你的磁盘有足够多的空间话，建议设置该值。但该值容易触发警告，因为在具有4GB内存的rabbitmq主机上，需要最低空间大于8G,如果你的磁盘空间比较少的话，不建议设置该值。

#### 1.4.6 连接
    少使用短连接，使用连接池或者长连接。
#### 1.4.7 TLS
    建议尽可能使用TLS连接，使用TLS会对传输的数据加密，但是对系统的吞吐量产生很大的影响

#### 1.4.8 更改默认端口
    我们常用的web界面的端口 15672 和AMQP 0-9-1 协议端口 5672 ，建议更改，web界面更改，配置参数 management.listener.port ，AMQP 0-9-1 协议端口配置参数 listeners.tcp.default。

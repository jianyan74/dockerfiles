## PHP 调优

目录

- PHP 配置基本优化
- PHP-fpm 配置优化
- Zend OPcache 设置

## PHP 配置基本优化

```
;设置用于设定单个 PHP 进程可以使用的系统内存最大值
memory_limit = 128M

;最大脚本运行时间 按秒计
max_execution_time = 30;

;最大输入时间 按秒计
max_input_time = 60;

;POST数据最大尺寸
post_max_size = 50M;

;是否允许上传文件
file_uploads = On;

;允许上传文件的最大尺寸
upload_max_filesize = 50M;

;允许同时上传文件的最大数量
max_file_uploads = 20;

;Socket超时时间
default_socket_timeout = 60;

;错误级别
error_reporting = E_ALL & ~E_NOTICE;

;是否输出详细错误信息
display_errors = On;

;是否开启pathinfo
cgi.fix_pathinfo = On;
```

## PHP-fpm 配置优化

#### 简单说明

- pm: 运行模式(dynamic/static/ondemand)
- max_children: 允许创建的最大子进程数
- start_servers: 起始进程数（服务启动后初始进程数量）
- min_spare_servers: 最小空闲进程数（清理空闲进程后的保留数
- max_spare_servers: 最大空闲进程数（当空闲进程达到此值时清理）

#### pm 单独说明

- pm = dynamic：动态模式，子进程的数量是根据以下指令来动态生成的：pm.max_children, pm.start_servers, pm.min_spare_servers, pm.max_spare_servers.
- pm = ondemand：在服务启动的时候根据 pm.start_servers 指令生成进程，而非动态生成。
- pm = static：静态模式，子进程的数量是由 pm.max_children 指令来确定的。

> 安装好，默认是 pm = dynamic，

如果您的服务器内存小，可以默认就好，大内存服务器则可以设置为 pm = static，然后 pm.max_children 数量根据服务器的内存来决定，这样就可以最大化你的服务器负载能力。

####  进程查看方式

```
ps aux|grep php-fpm
```

#### 计算方式

> 注意: 服务器配置需要跟得上且不是单php要考虑其他资源占用情况不然其他都是空谈  
> 可以通过 pmap 指令查看哪些地方占用了内存。尽量不要加载不必要的php扩展模块，可以减少不必要的内存浪费

占用多少内存(具体取决于php扩展模块)

- 运行中：一个php-cgi大概占用20MB
- 未释放：一个php-cgi大概占用20kb内存

#### 参考并发方案

30 并发

```
pm.max_children = 30
pm.start_servers = 5
pm.min_spare_servers = 5
pm.max_spare_servers = 20
```

50 并发

```
pm.max_children = 50
pm.start_servers = 15
pm.min_spare_servers = 15
pm.max_spare_servers = 35
```

100 并发

```
pm.max_children = 100
pm.start_servers = 20
pm.min_spare_servers = 20
pm.max_spare_servers = 70
```

200 并发

```
pm = dynamic
pm.max_children = 200
pm.start_servers = 25
pm.min_spare_servers = 25
pm.max_spare_servers = 150
```

300 并发

```
pm.max_children = 300
pm.start_servers = 30
pm.min_spare_servers = 30
pm.max_spare_servers = 180
```

500 并发

```
pm.max_children = 500
pm.start_servers = 35
pm.min_spare_servers = 35
pm.max_spare_servers = 250
```

## Zend OPcache

```
;开关打开
opcache.enable=1

;开启CLI
opcache.enable_cli=1

;可用内存, 酌情而定, 单位为：Mb
opcache.memory_consumption=528

;Zend Optimizer + 暂存池中字符串的占内存总量.(单位:MB)
opcache.interned_strings_buffer=8

;对多缓存文件限制, 命中率不到 100% 的话, 可以试着提高这个值
opcache.max_accelerated_files=10000

;Opcache 会在一定时间内去检查文件的修改时间, 这里设置检查的时间周期, 默认为 2, 定位为秒
opcache.revalidate_freq=1

;打开快速关闭, 打开这个在PHP Request Shutdown的时候回收内存的速度会提高
opcache.fast_shutdown=1

;检查脚本时间戳是否有更新的周期，以秒为单位。设置为 0 会导致针对每个请求， OPcache 都会检查脚本更新。
opcache.revalidate_freq=0  

;如果启用，那么 OPcache 会每隔 opcache.revalidate_freq 设定的秒数 检查脚本是否更新。
;如果禁用此选项，你必须使用 opcache_reset() 或者 opcache_invalidate() 函数来手动重置 OPcache，也可以 通过重启 Web 服务器来使文件系统更改生效。
opcache.validate_timestamps=0  
```

注意：如果设置opcache的opcache.validate_timestamps的指令设成０，那么zend opcache就察觉不到ＰＨＰ脚本的变化，我们必须手动清空zend opcache缓存的字节码，让他发现php脚本的变动．这个设置适合在生产环境中设置成０，在开发环境下最好还是设置成1．

我们可以这样配置，启用自动重新验证缓存功能：

```
opcache.validate_timestamps=1
opcache.revalidate_freq=0
```


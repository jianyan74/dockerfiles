目录

- 占用计算
- 配置调优

### 占用计算

线程独享计算公式：(sort_buffer_size + join_buffer_size + read_buffer_size + read_rnd_buffer_size + thread_statck) * 最大连接数

假设是一台单独给MySQL 使用的主机，物理内存总大小为8G，MySQL 最大连接数为500，同时还使用
了MyISAM 存储引擎，这时候我们的整体内存该如何分配呢？

内存分配为如下几大部分

- 系统使用，假设预留800M；
- 线程独享，约2GB = 500 * (1MB + 1MB + 1MB + 512KB + 512KB)，组成大概如下:

```
sort_buffer_size：1MB
join_buffer_size：1MB
read_buffer_size：1MB
read_rnd_buffer_size：512KB
thread_statck：512KB
```

- MyISAM Key Cache，假设大概为1.5GB；
- Innodb Buffer Pool 最大可用量：8GB - 800MB - 2GB - 1.5GB = 3.7GB；

### 配置调优

> 以下配置方案提供基础参考，实际需要按照自己情况去调整大小

不同内存的配置方案

配置 | 1-2g | 2-4g | 4-8g | 8-16g | 16-32g | 说明
---|---|---|---|---|---|---
key_buffer_size | 128 | 256 | 384 | 512 | 1024 | MB, 用于索引的缓冲区大小
query_cache_size | 64 | 128 | 192 | 256 | 384 | MB, 查询缓存,不开启请设为0
tmp_table_size | 64 | 384 | 512 | 1024 | 2048 | MB, 临时表缓存大小
innodb_buffer_pool_size | 256 | 384 | 512 | 1024 | 4096 | MB, Innodb缓冲区大小
innodb_log_buffer_size | 8 | 8 | 8 | 8 | 8 | MB, Innodb日志缓冲区大小
sort_buffer_size | 768 | 768 | 1024 | 2048 | 4096 | KB * 连接数, 每个线程排序的缓冲大小
read_buffer_size | 768 | 768 | 1024 | 2048 | 4096 | KB * 连接数, 读入缓冲区大小
read_rnd_buffer_size | 512 | 512 | 768 | 1024 | 2048 | KB * 连接数, 随机读取缓冲区大小
join_buffer_size | 1024 | 2024 | 2048 | 4096 | 8192 | KB * 连接数, 关联表缓存大小
thread_stack | 256 | 256 | 256 | 384 | 512 | KB * 连接数, 每个线程的堆栈大小
binlog_cache_size | 64 | 64 | 128 | 192 | 256 | KB * 连接数, 二进制日志缓存大小(4096的倍数)
thread_cache_size | 64 | 96 | 128 | 192 | 256 | 线程池大小
table_open_cache | 128 | 192 | 384 | 1024 | 2048 | 表缓存(最大不要超过2048)
max_connections | 100 | 200 | 300 | 400 | 500 | 最大连接数
 占用合计 | 851.25 | 2022.50 | 3145.50 | 6649.00 | 16935.00 | MB, 最大使用内存
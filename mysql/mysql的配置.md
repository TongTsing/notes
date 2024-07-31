# mysql的常用配置

## 一、基础配置

```ini
[mysqld]
# GENERAL
datadir = /var/Lib/mysql
socket = /var/lib/mysql/mysql.sock
pid_file = /var/lib/mysql/mysql.pid
user = mysql
port = 3306

# Innodb配置
innodb_buffer_pool_size = <value>
innodb_log_file_size    = <value>
innodb_file_per_table   = 1
innodb_flush_method     = O_DIRECT

# OTHER
tmp_table_size.         = 32M
max_heap_table_size     = 32M
max_connections         = <value>
thread_cache_size       = <value>
table_open_cache        = <value>
open_files_limit        = 65545

# LOGGING
log_error               = /var/lib/mysql/mysql-error.log
log_slow_queries        = /var/lib/mysql/mysql-slow.log

[client]
sokcet                  = /var/lib/mysql/mysql.sock
port                    = 3306
```

InnoDB 是 MySQL 中最常用的存储引擎之一，以下是一些常用的 InnoDB 配置参数的介绍：

1. **innodb_buffer_pool_size**：这是 InnoDB 最重要的配置参数之一，用于设置 InnoDB 缓冲池的大小。缓冲池是 InnoDB 存储引擎用于缓存数据和索引的内存区域。适当设置这个参数可以提高数据库的性能，一般建议将其设置为系统内存的 50% 到 70%。

2. **innodb_log_file_size**：这个参数用于设置 InnoDB 的事务日志文件的大小。事务日志是用于持久化事务的关键组件。设置过小的日志文件大小可能会导致频繁的日志切换，影响性能，一般建议将其设置为 100MB 到 4GB 之间。

3. **innodb_flush_log_at_trx_commit**：这个参数用于控制事务提交时事务日志的刷新策略。0 表示事务提交时不刷新日志，1 表示事务提交时每次都刷新日志，2 表示事务提交时每秒刷新一次日志。通常情况下，建议将其设置为 1，以确保事务的持久性。

4. **innodb_file_per_table**：这个参数用于控制 InnoDB 表空间的管理方式。设置为 ON 时，每个表都会有独立的表空间文件，这样可以更好地管理表的空间使用。建议在创建新的数据库时开启这个选项。

5. **innodb_flush_method**：这个参数用于设置 InnoDB 刷新数据和日志文件到磁盘的方法。可以选择的值包括 fsync、O_DSYNC、O_DIRECT 等。通常情况下，使用默认值即可，但在一些特定的环境下可能需要根据实际情况进行调整。

6. **innodb_thread_concurrency**：这个参数用于设置 InnoDB 的线程并发控制。它控制了 InnoDB 内部的线程并发执行的数量，适当的设置可以提高系统的并发性能。

这些是常见的 InnoDB 配置参数，根据实际情况，你可能还需要根据服务器的硬件配置、应用的负载情况等因素进行调整。
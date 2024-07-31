除了 `SHOW VARIABLES` 和 `SHOW STATUS` 外，MySQL 还提供了其他几个常用的 `SHOW` 命令，用于显示数据库相关的信息。以下是一些常见的 `SHOW` 命令：

1. **SHOW DATABASES**：显示当前 MySQL 服务器上的所有数据库列表。

```sql
SHOW DATABASES;
```

2. **SHOW TABLES**：显示指定数据库中的所有表列表。

```sql
SHOW TABLES FROM dbname;
```

3. **SHOW CREATE TABLE**：显示指定表的创建语句，包括表结构、索引等信息。

```sql
SHOW CREATE TABLE tablename;
```

4. **SHOW GRANTS**：显示用户的权限信息，包括授予给用户的权限和角色。

```sql
SHOW GRANTS FOR user;
```

5. **SHOW PROCESSLIST**：显示当前 MySQL 服务器上的活动线程列表，包括正在执行的查询、连接状态等信息。

```sql
SHOW PROCESSLIST;
```

6. **SHOW ENGINE INNODB STATUS**：显示 InnoDB 存储引擎的状态信息，包括事务信息、锁等待情况等。

```sql
SHOW ENGINE INNODB STATUS;
```

7. **SHOW INDEX**：显示指定表的索引信息。

```sql
SHOW INDEX FROM tablename;
```

8. **SHOW VARIABLES LIKE**：根据指定的模式显示系统变量的值。

```sql
SHOW VARIABLES LIKE 'pattern';
```

这些 `SHOW` 命令可以帮助您查看数据库的结构、状态和性能信息，有助于进行数据库管理、性能优化和故障排除。
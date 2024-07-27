MySQL 中的锁主要分为以下几种类型：

1. 行级锁（Row-level Locks）
2. 表级锁（Table-level Locks）
3. 页面级锁（Page-level Locks）

下面分别介绍每种锁的操作：

### 1. 行级锁（Row-level Locks）：

行级锁是最细粒度的锁，它只锁定表中的某一行数据，允许多个事务同时访问表中不同行的数据，从而提高并发性能。

**操作方式**：
- 在事务中通过 `SELECT ... FOR UPDATE` 或 `SELECT ... LOCK IN SHARE MODE` 查询语句加锁，分别用于悲观锁和乐观锁的实现。
- 通过设置事务隔离级别为 `REPEATABLE READ` 或 `SERIALIZABLE` 来默认使用行级锁，以防止并发问题。

### 2. 表级锁（Table-level Locks）：

表级锁是针对整个表的锁，当一个事务获取表级锁后，其他事务无法对表进行修改操作，只能等待锁释放。

**操作方式**：
- 使用 `LOCK TABLES` 命令对表进行加锁，语法如下：
  ```
  LOCK TABLES table_name [READ | WRITE];
  ```
- 使用 `UNLOCK TABLES` 命令释放表级锁。

### 3. 页面级锁（Page-level Locks）：

页面级锁是 MySQL 存储引擎层面的锁，它锁定存储引擎的数据页面，但并不是所有存储引擎都支持页面级锁，常见的如 InnoDB 支持，MyISAM 不支持。

**操作方式**：
- 在使用支持页面级锁的存储引擎（如 InnoDB）时，行级锁会自动升级为页面级锁。

总体来说，行级锁是最常用和最灵活的一种锁类型，能够最大程度地减少并发访问数据时的冲突，但也会增加系统的开销。在实际应用中，根据业务需求和性能要求选择合适的锁策略非常重要。
# MySQL复制

MySQL提供了多种复制方式来满足不同的需求，包括异步复制、脱机复制、联机复制和半同步复制。下面是这些复制方式的详细介绍及其使用场景：

### 1. 异步复制（Asynchronous Replication）
#### 介绍
在异步复制中，主库（Master）在执行事务提交后，不等待从库（Slave）的确认即认为事务完成并继续执行新的事务。主库将事务日志发送给从库，从库在接收到日志后进行重放。

#### 使用场景
- **高性能需求**：由于主库不需要等待从库的确认，异步复制对主库性能影响最小，适用于高并发写入的场景。
- **宽松的数据一致性要求**：由于存在一定的延迟，数据可能在短时间内不一致，适合数据一致性要求不高的应用，如分析系统。

### 2. 脱机复制（Offline Replication）
#### 介绍
脱机复制是指在复制过程中，不需要持续的网络连接。通常使用物理介质（如硬盘、磁带）将数据从一个服务器传输到另一个服务器。常见于数据的冷备份或灾难恢复。

#### 使用场景
- **灾难恢复**：在发生数据中心故障后，通过脱机复制恢复数据。
- **大规模数据迁移**：在需要迁移大规模数据而网络带宽有限时，使用物理介质进行复制。

### 3. 联机复制（Online Replication）
#### 介绍
联机复制是指在持续的网络连接下进行数据复制。通常指主从复制，其中主库实时将数据变化传递给从库。

#### 使用场景
- **实时备份**：确保从库始终与主库保持一致，用于数据恢复。
- **负载均衡**：通过将读请求分配到从库，减轻主库压力，提高系统整体性能。

### 4. 半同步复制（Semi-Synchronous Replication）
#### 介绍
半同步复制介于异步和同步复制之间。当主库提交事务时，会等待至少一个从库确认收到并写入了二进制日志（binlog）后，才返回确认事务提交。这增加了数据一致性的保障。

#### 使用场景
- **中等数据一致性要求**：适用于对数据一致性有一定要求，但又不希望完全同步复制带来的高延迟的场景。
- **提高数据可靠性**：相比异步复制，半同步复制在数据安全性上更有保障，适用于数据可靠性要求较高的业务系统。

### 小结
- **异步复制**：适用于高性能和低延迟需求，但不适合数据一致性要求高的场景。
- **脱机复制**：适用于灾难恢复和大规模数据迁移，但不适合实时性要求高的应用。
- **联机复制**：适用于实时备份和负载均衡，提供较高的数据一致性和系统性能。
- **半同步复制**：适用于数据一致性和可靠性要求较高的场景，在性能和数据一致性之间取得平衡。

这些复制技术各有优缺点，选择哪种复制方式应根据具体的业务需求、性能要求和数据一致性要求来决定。

## 一、异步复制

```mysql
# 设置server_id
set global server_id=1125;
set global server_id=1126;
set global server_id=1127;

# 主库创建用于复制的用户

create user 'repl'@'%' identified with mysql_native_password by '123456';
grant replication client,replication slave on *.* to 'repl'@'%';

# 在从库创建主库信息

change master to master_host='172.16.1.125',master_port=3306,master_user='repl',master_password='123456',master_log_file='binlog.000012',master_log_pos=195;

# 在从库启动复制
start slave;
show slave status;
select * from mysql.user where user='repl'\G;
```

## 二、脱机复制

```mysql
# 在主库上建立专属的复制用户
create user 'repl'@'%' identified with mysql_native_password by '123456';
grant replication client,replication slave on *.* to 'repl'@'%';

# 停到复制涉及到的实例
mysqladmin -uroot -p123456 shutdown

# 把主库的数据目录整体复制到从库
scp -r /usr/local/mysql/data 172.17.1.126:/usr/local/mysql

# 重启实例（一主两从 都启动）
mysqld_safe --user=mysql &

# 查看主库的二进制日志信息
show master status;

# 根据主库的二进制信息，在从库创建要连接的主库信息
change master to master_host='172.16.1.125',master_port=3306,master_user='repl',master_password='123456',master_log_file='binlog.000002',master_log_pos=155;

# 在从库启动复制，并查看复制信息
start slave;
show slave status;
select * from mysql.user where user='repl'\G;
```

## 三、联机复制

```mysql
# 在主库上建立专属的复制用户
create user 'repl'@'%' identified with mysql_native_password by '123456';
grant replication client,replication slave on *.* to 'repl'@'%';
# 在从库设置主库的信息
change master to master_host='172.16.1.125',master_port=3306,master_user='repl',master_password='123456';

# 在从库用mysqldump建立复制
mysqldump --single-transaction --all-databases --master-data=1 --host=172.16.1.125 --default-character-set=utf8mb4 --user=wxy --password=123456 --apply-slave-statements|mysql -uroot -p123456 -h127

```

## 四、半同步复制

### 1、配置并半同步复制

```mysql
# 查看当前mysql是否支持动态加载，可以通过查看“have_dynamic_loading”变量来查看
 show variables like 'have_dynamic_loading';
 
# 安装设置半同步复制，需要REPLICATION_SLAVE_ADMIN或SUPER权限。MySQL发行版包括主、从端的半同步复制插件文件semisync_master.so和semisync_slave.so，默认位于MySQL安装目录下的lib/plugin目录下，本例中为/usr/local/mysql/lib/plugin。也可以通过设置plugin_dir系统变量的值指定插件目录位置。（“show variables where Variable_name='plugin_dir';”）
#1.查看mysql插件目录位置
show variables where Variable_name='plugin_dir';

# 安装semisync_master.so和semisync_slave.so
-- 在主库 
install plugin rpl_semi_sync_master soname 'semisync_master.so';￼
-- 在每个从库￼
install plugin rpl_semi_sync_slave soname 'semisync_slave.so';


# 启动半同步复制
-- 主库
set global rpl_semi_sync_master_enabled=1;
-- 从库
set global rpl_semi_sync_slave_enabled=1;
-- ps：也可以把上面两个命令写入到配置文件中
# 主库
plugin-load="rpl_semi_sync——master=semisync_master.so"
rpl_semi_sync_master_enabled=1
＃ 从库
plugin-load="rpl_semi_sync——master=semisync_slave.so"
rpl_semi_sync_slave_enabled=1
```

### 2、重启从库的i/o线程

```mysql
stop slave io_thread;
start slave io_thread;
```

### 3、查看半同步是否在运行	

```mysql
  msyql> show status like 'Rpl_semi_sync_master_status';
  msyql> show status like 'Rpl_semi_sync_slave_status';
```

### 4、监控半同步复制

```mysql
mysql> show status like 'rpl_semi_sync%';
```

## 五、GTID复制

#### 5.1、脱机复制

##### 5.1.1、启用gtid，并查看相关信息

需要设置变量：

```ini
gtid_mode = ON
enforce-gtid-consistency = ON
```

验证方法：

```mysql
use test;
create table t1(a int);
create table t2(a int);
insert into t1 values(1),(2);
insert into t2 values(1),(2);
commit;
show master status；
```

##### 5.1.2 配置GTID脱机复制

主、从服务器已经进行了以下配置：
•  在主库上建立复制专属用户。
•  在主、从库上安装XtraBackup。
•  配置主库到从库的SSH免密码连接。
•  停止作为从库的MySQL实例，并清空其数据目录。

##### 5.1.3 检查主库中是否有不支持GTID的操作

```mysql
set global enforce_gtid_consistency=warn;
```

##### 5.1.4 在主库联机设置GTID相关参数

```mysql
set global enforce_gtid_consistency=true;
set global gtid_mode=off_permissive;
set global gtid_mode=on_permissive;
set global gtid_mode=on;
```

##### 5.1.5 使用xtrabackup进行备份并传输

```shell
xtrabackup -uroot --password=123456 --socket=/tmp/mysql.sock --no-lock --backup --compress --stream=xbstream --parallel=4 --target-dir=./backup/ | ssh mysql@172.16.1.126 "xbstream -x -C /usr/local/mysql/data/ --decompress"
```

##### 5.1.6 在从库恢复备份

```mysql
xtrabackup --prepare --target-dir=/usr/local/mysql/data/
```

##### 5.1.7 从库配置文件

```ini
server_id = 1126
read_only = on
gtid_mode = on
enforce-gtid-consistency=true
```

##### 5.1.8 启动从库

```shell
mysqld_safe --defaults-file=etc/my.conf &
```

##### 5.1.9 在从库启动复制

```mysql
change master to master_host='172.16.1.125',master_port=3306,master_user='repl',master_password='123456',master_auto_position=1;

start slave;
show slave status\G;
```

##### 5.1.10 修改主库配置文件

```css
gtid_mode=on
enforece=gtid-consistency=true
```

### 5.2、 联机匿名复制更改为GTID复制模式

*背景：在未启用GITD的情况下配置了主从复制， 可以联机将复制模式修改为GTID以及自动定位，整个过程不需要停止MySQL实力， 因此这种方式适合在生产环境中使用*

```mysql
set global enforce_gtid_consistency=warn;
set global enforce_gtid_consistency=on;
set global gtid_mode=off_permissive;
set global gtid_mode=on_permissive;
show status like 'ongoing_anonynous_transaction_count';
set global gtid_mode=ON;
CHANGE MASTER TO
  MASTER_HOST = '172.16.1.125',
  MASTER_USER = 'repl',
  MASTER_PASSWORD = '123456',
  MASTER_PORT = 3306,
  MASTER_AUTO_POSITION = 1;

```

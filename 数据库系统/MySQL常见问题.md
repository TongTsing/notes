# MySQL常见问题

## 一、查看mysql 默认端口号和修改端口号

**简介：** 1. 登录mysql mysql -u root -p //输入密码 　　 2. 使用命令show global variables like 'port';查看端口号 mysql> show global variables like 'port'; 　　 3. 修改端口，编辑/etc/my.cnf文件，早期版本有可能是my.conf文件名，增加端口参数，并且设定端口，注意该端口未被使用，保存退出。

### 1. 登录mysql

```shell
mysql -u root -p
//输入密码
```

　　

### 2.使用命令show global variables like 'port';查看端口号

```shell
mysql> show global variables like 'port';
```

　　

### 3.修改端口，编辑/etc/my.cnf文件，早期版本有可能是my.conf文件名，增加端口参数，并且设定端口，注意该端口未被使用，保存退出。

```shell
[root@test etc]# vi my.cnf
[mysqld]
port=3506
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

"my.cnf" 11L, 261C written
[root@test etc]#
```

　　

### 4.重启mysql

```shell
[root@test ~]# /etc/init.d/mysqld restart
Stopping mysqld: [ OK ]
Starting mysqld: [ OK ]
```
# MySQL主从问题

MySQL 组件自身的 package.conf.yaml 中声明的名字从 data.mysql

改为 data.mysql.instance ，启动后由 ens_client 自动注册。

data.mysql.instance 这个名字不对外提供服务。

 

data.mysql （实际上是 data.mysql.master）名字改由 mysql_replica 工具来主动注册和注销。

## mysql_replica 用途

用途1：

1. 根据 env.ini 设置 MySQL 的主从，或做主从切换
2. 注册 data.mysql 名字

用途2：

1. 注销 data.mysql 名字

## TODO:

mysql_replica 是否做成程序包，由 easyops start，easyops stop 来调用。

MySQL 主从切换完成之前，如何避免外部在新的从库执行写操作，造成数据不一致。（从库只读）

 
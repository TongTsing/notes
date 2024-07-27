mysql安装：

```shell
# 安装依赖
yum update -y

yum install -y gcc gcc-c++ make cmake openssl-devel zlib-devel  libaio-devel numactl libstdc++.so.6 glibc

# 进入安装目录
cd /usr/local
# 创建mysql用户
useradd mysql

wget -O /usr/local/src/mysql8.0.36.tar.xz https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.36-linux-glibc2.12-x86_64.tar.xz

# 从tar包中提取文件

tar xvf /usr/local/src/mysql8.0.36.tar.xz -C /usr/local/

# 建立软连接
cp -r mysql-8.0.36-linux-glibc2.12-x86_64 /usr/local/mysql

# 进入mysql目录
cd /usr/local/mysql/

# 建立secure_file_priv系统变量指向的目录
mkdir mysql-files

# 修改属主为mysql
chown -R mysql:mysql /usr/local/mysql

# 修改目录权限
chmod 750 mysql-files

# mysql系统初始化
mysql_init_password=$(bin/mysqld --initialize --user=mysql 2>&1|tail -1|awk -F ':' '{print $4}'|cut -c2-)
# 建立SSL/RSA相关文件，如果不启用SSL连接，此步可省略
bin/mysql_ssl_rsa_setup

# 启动mysql服务器
bin/mysqld_safe --user=mysql &

# 连接mysql服务器
bin/mysql -u root -p

# 修改root密码
bin/mysql --connect-expired-password -u root -p${mysql_init_password} -e "ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';" -e "create user 'wxy'@'%' identified with mysql_native_password by '123456';" -e "grant all on *.* to 'wxy'@'%' with grant option;"

# 创建一个新的mysql管理员账户
create user 'wxy'@'%' identified with mysql_native_password by '123456';
grant all on *.* to 'wxy'@'%' with grant option;



#重装命令表
pkill -9 mysql
rm -rf mysql*
rm -rf /etc/my.cnf
rm -rf /etc/my.cnf.d/
rm -rf /var/lib/mysql/
rm -rf /var/log/mariadb/
rm -rf /usr/local/mysql/data
mysql_install.sh 
cd mysql
ll
bin/mysqld --initialize --user=mysql > /tmp/mysql.log
bin/mysql_ssl_rsa_setup
bin/mysqld_safe --user=mysql &
ps -ef|grep mysql
bin/mysql -u root -p
ps -ef|grep mysql
```


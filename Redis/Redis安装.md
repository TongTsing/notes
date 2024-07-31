# CentOS7 linux下yum安装redis以及使用

- [1 前言](https://www.ahwgs.cn/#i)
- [2 安装redis](https://www.ahwgs.cn/#redis)
- [3 使用redis desktop manager远程连接redis](https://www.ahwgs.cn/#redis_desktop_managerredis)
- [4 关于](https://www.ahwgs.cn/#i-2)

### 前言

- 继之前 [window环境下安装Redis及可视化工具Redis Desktop Manager](https://www.ahwgs.cn/redis-for-windows.html) 文章后，这里记录一下Linux系统下的`redis`的使用

### 安装[redis](https://cloud.tencent.com/product/crs?from=10680)

- 检查是否有redis yum 源

```javascript
yum install redis
```

复制

- 下载fedora的epel仓库

```javascript
yum install epel-release
```

复制

- 安装redis[数据库](https://cloud.tencent.com/solution/database?from=10680)

```javascript
yum install redis
```

复制

- 安装完毕后，使用下面的命令启动redis服务

```javascript
# 启动redis
service redis start
# 停止redis
service redis stop
# 查看redis运行状态
service redis status
# 查看redis进程
ps -ef | grep redis
```

复制

- 设置redis为开机自动启动

```javascript
chkconfig redis on
```

复制

- 进入redis服务

~~~javascript
# 进入本机redis
redis-cli
# 列出所有key
keys *
``
- 防火墙开放相应端口
```bash
# 开启6379
/sbin/iptables -I INPUT -p tcp --dport 6379 -j ACCEPT
# 开启6380
/sbin/iptables -I INPUT -p tcp --dport 6380 -j ACCEPT
# 保存
/etc/rc.d/init.d/iptables save
# centos 7下执行
service iptables save
``

### 修改redis默认端口和密码
- 打开配置文件
```bash
vi /etc/redis.conf
~~~

复制

- 修改默认端口，查找 port 6379 修改为相应端口即可

![img](../7000.png)

- 修改默认密码，查找 requirepass foobared 将 foobared 修改为你的密码 

![img](../7000-16795410584311.png)

-  使用配置文件启动 redis 

```javascript
redis-server /etc/redis.conf &
```

复制

- 使用端口登录

```javascript
redis-cli -h 127.0.0.1 -p 6179
```

复制

-  此时再输入命令则会报错 

![img](../7000-16795410584312.png)

-  输入刚才输入的密码 

```javascript
auth 111
```

复制

![img](../7000-16795410584313.png)

- 停止redis

```javascript
redis-cli -h 127.0.0.1 -p 6179
shutdown
```

复制

= 进程号杀掉redis

```javascript
ps -ef | grep redis
kill -9 XXX
```

复制

### 使用redis desktop manager远程连接redis

- 可参考[window环境下安装Redis及可视化工具Redis Desktop Manager](https://www.ahwgs.cn/redis-for-windows.html) 
-  如果长时间连接不上，可能有两种可能性    1、bind了127.0.01：只允许在本机连接redis   2、protected-mode设置了yes（使用redis desktop manager工具需要配置，其余不用）  

**解决办法：**

```javascript
# 打开redis配置文件
vi /etc/redis.conf
# 找到 bind 127.0.0.1 将其注释
# 找到 protected-mode yes 将其改为
protected-mode no
```

复制

- 重启redis

```javascript
service redis stop
service redis start
```

复制

- 再次连接

### 关于

- 参考https://www.cnblogs.com/rslai/p/8249812.html
- 本文首发于[CentOS7 linux下yum安装redis以及使用](https://www.ahwgs.cn/centos7-redis-use-help.html)
# redis主从同步

# 一、现象描述

平台版本：3.build.005，七台集群。 redis：3.0.0 gateway：3.3.0

redis 主从切换后，agent 的状态在【agent 管理】和【主机实例】页面全部异常。

# 二、解决方案

## 2.1 状态判断

我们 agent 的状态上报链路图如下：

![img](https://tapd.easyops.local/download/attachments/24085365/image2019-8-27%2022%3A41%3A8.png?version=1&modificationDate=1566916864000&api=v2)

 

## 2.2 故障定位

### 2.2.1 所有状态异常

1. 由于 cmdb 的界面是通过拉取【agent 管理】来定时更新数据，所以重点关注【agent 管理】页面的agent 状态；详细原理请看 [主机的agent状态显示异常（旧）](https://tapd.easyops.local/pages/viewpage.action?pageId=21758527)
2. 根据数据流所示，**所有**的 agent 状态异常，排查网络原因外，可以断定是链路在某一处断掉；
3. 查看 gateway 日志，会发现 ” [ERROR] READONLY You cannt wirte a read only slave.“ 字样。表示 gateway 与 redis 连接有异常；
4. 查看 redis 日志，发现 redis 最近有过主从切换；
5. 推断原因 gateway 连接了错误的 redis，全部agent 状态异常；
6. 集群环境中，gateway 是通过 sentinel 来连接 redismaster；
7. 检查所有 sentinel 的运行状态，保证 sentinel 与 gateway 是正常连接。并且返回是 redismaster；
8. 本次案例中，重启所有 sentinel 后，gateway 日志不在出现 read only 的字样，表示已连接到正确的 redis master；
9. agent 恢复到正常状态；

### 2.2.2 恢复 redis 主从同步

1. 根据数据流所示，部分agent状态异常排除网络原因外，很有可能是 redis 的主从状态不一致导致的。这个时候我们修复 redis 主从同步就行了；

2. 恢复 redis 主从同步：（

   此方法可以同样适用其他的原因导致的redis不同步

   ）

   1. redis-sentienl 目录内：./bin/redis-cli -a 6edc5a13 -p 26379  （平台密码改造过，现在redis的密码不是6edc5a13，需要手动获取。具体看评论）
      1. sentinel masters/info replication
      2. 使用上面的命令查看sentinel中 master是哪个 ip；
      3. 所有的 redis-sentinel 服务器都要查看
   2. redis目录内：./bin/redis-cli -a 6edc5a13
      1. info replication
      2. 查看本机的 redis role，及对应的 master ip；
      3. 所有的 redis 服务器都要查看
   3. 确保【步骤 a】和【步骤 b】的 ip 是否一样；
   4. 如果不一样：
      1. 停掉所有的sentinel；
      2. 然后在启用一台sentinel，仅保证一台 sentinel 运行；
      3. 重复步骤a-c，直至【步骤C】的返回一样；
      4. 启用其他的sentinel；
   5. 如果此时，agent状态还是异常
      1.  tailf 查看 gateway 日式是否有报错。关键字 redis 或者 read 
      2. 如果有报错，重启所有的gateway即可；
   6. 还有其他问题。联系@张勋
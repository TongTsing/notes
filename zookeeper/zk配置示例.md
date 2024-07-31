以下是一个简单的 ZooKeeper 集群配置文件示例：

```properties
# zoo.cfg

# The number of milliseconds of each tick
tickTime=2000

# The number of ticks that the initial synchronization phase can take
initLimit=10

# The number of ticks that can pass between sending a request and getting an acknowledgment
syncLimit=5

# The directory where the snapshot is stored
dataDir=/var/lib/zookeeper

# The port at which the clients will connect
clientPort=2181

# The maximum number of client connections
maxClientCnxns=60

# Enable/disable the peer-to-peer communication among servers.
peerType=leader

# Server list, the syntax is `<server id>=<hostname>:<port>:<port>`
# For example: server.1=zookeeper1.example.com:2888:3888
# Make sure to replace the placeholders with actual hostnames and ports for each server in your cluster.
server.1=zookeeper1.example.com:2888:3888
server.2=zookeeper2.example.com:2888:3888
server.3=zookeeper3.example.com:2888:3888

# Specify the location of the ZooKeeper's transaction log
dataLogDir=/var/lib/zookeeper/log
```

在这个示例配置中：

- `tickTime` 定义了 ZooKeeper 使用的基本时间单位（以毫秒为单位）。它用于心跳和超时检测等时间相关的操作。
- `initLimit` 定义了初始同步阶段可以花费的时间以允许follower服务器连接到leader服务器。
- `syncLimit` 定义了请求和收到确认之间可以经过的时间的最大数目。
- `dataDir` 是 ZooKeeper 存储数据快照的目录。
- `clientPort` 是客户端连接到 ZooKeeper 服务器的端口。
- `maxClientCnxns` 是每个客户端 IP 地址允许的最大连接数。
- `peerType` 定义了服务器的角色，可以是 leader 或 follower。
- `server.x` 定义了每个 ZooKeeper 服务器的 ID、主机名以及用于对等通信的端口和选举通信的端口。
- `dataLogDir` 定义了 ZooKeeper 事务日志的位置。

在实际使用中，需要根据集群的实际配置和需求进行适当的调整。
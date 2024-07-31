Redis 部署架构主要包括以下几种：

1. **单机模式（Standalone）**
2. **主从复制（Master-Slave Replication）**
3. **哨兵模式（Sentinel）**
4. **集群模式（Cluster）**

### 1. 单机模式（Standalone）

- **描述**：最简单的部署方式，所有数据存储在一个 Redis 实例中。
- **优点**：配置简单，适用于开发和测试环境。
- **缺点**：单点故障，无法扩展到高可用和高并发的场景。

### 2. 主从复制（Master-Slave Replication）

- **描述**：一个主节点（Master）负责读写操作，一个或多个从节点（Slave）复制主节点的数据，仅处理读操作。
- **优点**：读写分离，提高读取性能，增强数据冗余。
- **缺点**：主节点故障时需手动故障转移，存在一定的延迟。

#### 示例配置：

主节点（Master）配置：
```plaintext
port 6379
```

从节点（Slave）配置：
```plaintext
port 6380
slaveof <master-ip> 6379
```

### 3. 哨兵模式（Sentinel）

- **描述**：在主从复制的基础上，引入哨兵节点（Sentinel）监控主节点和从节点的状态，自动进行故障转移。
- **优点**：提供自动故障转移和监控，提升系统的高可用性。
- **缺点**：配置较为复杂，需要额外的哨兵节点资源。

#### 示例配置：

哨兵（Sentinel）配置：
```plaintext
port 26379
sentinel monitor mymaster <master-ip> 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 10000
```

### 4. 集群模式（Cluster）

- **描述**：将数据分片存储在多个节点上，每个节点负责部分数据，实现水平扩展。
- **优点**：支持自动分片和数据分布，提供高可用性和扩展性。
- **缺点**：配置和管理较为复杂，涉及节点间通信和数据迁移。

#### 示例配置：

每个 Redis 实例的配置（如 `redis-node-7000.conf`）：
```plaintext
port 7000
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes-7000.conf
cluster-node-timeout 5000
appendonly yes
dir /var/lib/redis/7000
logfile "/var/log/redis_7000.log"
```

集群创建命令：
```bash
redis-cli --cluster create <ip1>:7000 <ip1>:7001 <ip2>:7000 <ip2>:7001 <ip3>:7000 <ip3>:7001 --cluster-replicas 1
```

### 其他架构：

- **多重 Redis 实例**：在一台服务器上运行多个 Redis 实例，通过不同的端口分开管理，适用于充分利用服务器资源。
- **持久化配置**：通过 RDB 和 AOF 方式实现数据持久化，提升数据安全性。
- **混合部署**：结合哨兵和集群模式，既实现高可用性，又具备水平扩展能力。

### 部署架构的选择

- **单机模式**：适用于开发和测试环境，简单易用。
- **主从复制**：适用于读多写少的场景，提高读取性能和数据冗余。
- **哨兵模式**：适用于需要高可用性的生产环境，提供自动故障转移。
- **集群模式**：适用于需要高可用性和水平扩展的大规模生产环境。

根据应用需求和规模，选择合适的 Redis 部署架构可以有效提升系统性能和可靠性。
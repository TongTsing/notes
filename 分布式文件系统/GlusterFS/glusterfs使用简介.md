GlusterFS 提供了一套强大的命令行工具，用于管理和维护分布式文件系统。以下是一些常用的 Gluster 命令及其功能简介：

### 基本命令

1. **查看版本信息**
   ```bash
   gluster --version
   ```
   显示 GlusterFS 的版本信息。

2. **查看帮助信息**
   ```bash
   gluster help
   ```
   显示 GlusterFS 命令的帮助信息。

### 集群管理命令

1. **添加节点到集群**
   ```bash
   gluster peer probe <hostname>
   ```
   将指定节点添加到 GlusterFS 集群。

2. **查看集群中的节点**
   ```bash
   gluster peer status
   ```
   显示集群中所有节点的状态。

3. **从集群中删除节点**
   ```bash
   gluster peer detach <hostname>
   ```
   将指定节点从 GlusterFS 集群中移除。

### 卷管理命令

1. **创建卷**
   ```bash
   gluster volume create <volume_name> <transport> <node>:<brick_path> [<node>:<brick_path> ...]
   ```
   创建一个新的 GlusterFS 卷。`<transport>` 通常为 `tcp` 或 `rdma`，`<node>` 是节点的主机名，`<brick_path>` 是砖的位置。

   示例（创建一个复制卷）：
   ```bash
   gluster volume create myvolume replica 3 node1:/data/brick1 node2:/data/brick1 node3:/data/brick1
   ```

2. **启动卷**
   ```bash
   gluster volume start <volume_name>
   ```
   启动一个已经创建的卷。

3. **停止卷**
   ```bash
   gluster volume stop <volume_name>
   ```
   停止一个正在运行的卷。

4. **删除卷**
   ```bash
   gluster volume delete <volume_name>
   ```
   删除一个已经停止的卷。

5. **扩展卷**
   ```bash
   gluster volume add-brick <volume_name> <node>:<brick_path> [<node>:<brick_path> ...]
   ```
   向现有卷中添加砖以扩展存储容量。

6. **查看卷信息**
   ```bash
   gluster volume info [<volume_name>]
   ```
   显示所有卷或指定卷的详细信息。

7. **查看卷状态**
   ```bash
   gluster volume status [<volume_name>]
   ```
   显示所有卷或指定卷的状态信息。

### 配置命令

1. **设置卷选项**
   ```bash
   gluster volume set <volume_name> <option> <value>
   ```
   为指定卷设置配置选项。

   示例（设置性能相关选项）：
   ```bash
   gluster volume set myvolume performance.cache-size 256MB
   ```

2. **查看卷选项**
   ```bash
   gluster volume get <volume_name> [<option>]
   ```
   获取指定卷的配置选项。如果未指定 `<option>`，则显示所有选项。

### 监控和诊断命令

1. **检查卷的一致性**
   ```bash
   gluster volume heal <volume_name> info
   ```
   检查并显示卷的一致性信息。

2. **修复卷**
   ```bash
   gluster volume heal <volume_name>
   ```
   修复卷中的不一致数据。

3. **查看日志**
   ```bash
   gluster volume log <volume_name> [rotate]
   ```
   查看指定卷的日志文件，`rotate` 参数用于轮转日志文件。

### 示例操作流程

1. **添加节点到集群**：
   ```bash
   gluster peer probe node2
   gluster peer probe node3
   ```

2. **创建一个复制卷**：
   ```bash
   gluster volume create myvolume replica 3 node1:/data/brick1 node2:/data/brick1 node3:/data/brick1
   ```

3. **启动卷**：
   ```bash
   gluster volume start myvolume
   ```

4. **查看卷信息**：
   ```bash
   gluster volume info myvolume
   ```

5. **扩展卷**：
   ```bash
   gluster volume add-brick myvolume node4:/data/brick1
   ```

6. **设置卷选项**：
   ```bash
   gluster volume set myvolume performance.cache-size 512MB
   ```

通过以上命令，您可以有效地管理和维护 GlusterFS 集群，实现分布式文件存储的高可用性和高性能。
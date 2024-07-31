是的，你可以使用 cgroup 来限制特定 IP 地址和端口的 TCP 连接的网速。你可以使用 `tc` 命令（Traffic Control）来实现这一目标，结合 `iptables` 过滤规则和 `tc` 配置，可以对指定的流量进行限速。

下面是一个简单的步骤：

1. **创建一个 cgroup**：首先，你需要创建一个 cgroup，用于管理你想要限制网速的进程。

2. **标记流量**：使用 `iptables` 来标记通过指定 IP 地址和端口的流量。

3. **使用 `tc` 命令配置带宽限制**：针对标记的流量，使用 `tc` 命令来配置带宽限制。

下面是一个基本的示例：

假设你想要限制 IP 地址为 `192.168.1.100`，端口为 `8080` 的流量的网速。

1. 创建一个 cgroup：
   ```bash
   sudo cgcreate -g net_cls:limited_group
   ```

2. 使用 `iptables` 标记流量：
   ```bash
   sudo iptables -A OUTPUT -t mangle -p tcp -d 192.168.1.100 --dport 8080 -j MARK --set-mark 1
   ```

3. 配置 `tc` 命令限制带宽：
   ```bash
   sudo tc qdisc add dev eth0 root handle 1: htb default 12
   sudo tc class add dev eth0 parent 1: classid 1:1 htb rate 100mbit
   sudo tc class add dev eth0 parent 1:1 classid 1:12 htb rate 100mbit ceil 100mbit
   sudo tc filter add dev eth0 parent 1: protocol ip prio 1 handle 1 fw flowid 1:12
   ```

这将限制 IP 地址为 `192.168.1.100`，端口为 `8080` 的流量带宽为 100mbit。

请注意，这只是一个简单的示例，实际情况可能更复杂。确保你对 `iptables` 和 `tc` 命令的使用有一定的了解，并根据你的具体需求进行配置。
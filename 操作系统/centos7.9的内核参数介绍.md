一、涉及的名词含义详解：

- **IP 转发功能**：IP转发功能指的是操作系统在接收到一个数据包后，决定将该数据包转发到另一个网络接口的能力。

- **反向路径过滤**：反向路径过滤（Reverse Path Filtering，RP Filter）是一种网络安全机制，用于验证数据包的源地址是否合法。它用于防止IP欺骗攻击和IP源地址伪装。

  在正常的网络通信中，数据包的源地址应该来自于与目标地址相对应的网络。例如，如果一个数据包的目标地址是网络A，那么该数据包的源地址应该属于网络A。如果数据包的源地址不属于目标地址对应的网络，那么可能存在网络攻击或错误配置的风险。

  反向路径过滤器通过检查数据包的源地址和源接口的关联性，来验证数据包的合法性。它会在数据包进入系统时，检查数据包的源地址是否经过与目标地址对应的接口传输的路径。如果数据包的源地址与目标地址对应的接口不匹配，那么该数据包可能是非法的，反向路径过滤器会丢弃或拒绝该数据包。

  通过启用反向路径过滤器，可以增强网络的安全性，防止来自外部网络的恶意攻击和欺骗。它可以减少网络攻击的风险，例如IP欺骗、源地址伪装和反射攻击等。

  `net.ipv4.conf.default.rp_filter = 1`可以确保默认情况下所有的网络接口都启用了反向路径过滤器。而使用`net.ipv4.conf.all.rp_filter = 1`则可以确保所有的网络接口，包括默认配置和单独配置的接口，都启用了反向路径过滤器。举个例子

  在Linux系统中，您可以对单个网络接口进行配置覆盖，以更改特定接口的参数设置。这种配置覆盖允许您为某个特定的网络接口设置不同于全局默认设置的参数值。

  例如，假设您的系统有两个网络接口，eth0和eth1。全局默认配置中启用了反向路径过滤器（`net.ipv4.conf.default.rp_filter = 1`和`net.ipv4.conf.all.rp_filter = 1`）。但是，您希望对eth1接口禁用反向路径过滤器。

  为了单独为eth1接口进行配置覆盖，您可以编辑系统的网络配置文件（例如`/etc/sysctl.conf`或`/etc/sysctl.d/`目录中的文件），并添加以下行：

  复制

  ```
  net.ipv4.conf.eth1.rp_filter = 0
  ```

  这将覆盖全局默认配置，并将eth1接口的反向路径过滤器设置为禁用。其他接口（如eth0）仍将使用全局默认配置。

- **TCP SYN Cookies**：`net.ipv4.tcp_syncookies = 1`是一个用于启用TCP SYN Cookies的内核参数。TCP SYN Cookies是一种防范SYN洪水攻击的机制。

  SYN洪水攻击是一种常见的网络攻击方式，攻击者通过发送大量伪造的TCP连接请求（SYN包）来消耗目标服务器的资源，使其无法正常响应合法的连接请求。这种攻击利用了TCP三次握手过程中的漏洞，攻击者发送大量的SYN包，但不完成握手的最后一步，导致服务器资源被占用。

  为了应对这种攻击，TCP SYN Cookies机制被引入。当`net.ipv4.tcp_syncookies`参数被设置为1时，操作系统会启用SYN Cookies机制。它的工作方式是，当服务器收到一个新的SYN连接请求时，如果已经达到了系统定义的最大半开连接数，服务器不会分配资源为该连接保留状态，而是根据目标IP地址、源IP地址、目标端口和源端口等信息生成一个特殊的SYN-ACK响应。该响应中的ACK序列号被加密，作为一个临时的连接标识，然后将该SYN-ACK响应发送回给客户端。

  客户端接收到这个特殊的SYN-ACK响应后，会根据其中的ACK序列号解密出临时连接标识，并将其作为下一步发送回服务器的ACK包的一部分。服务器通过验证这个ACK序列号来确定这个连接是否合法。如果验证通过，服务器就能够建立正常的TCP连接，否则将丢弃该连接请求。

  通过使用TCP SYN Cookies，服务器可以在不保留任何状态的情况下处理大量的连接请求，从而有效地防范SYN洪水攻击，并减少对服务器资源的占用。因此，将`net.ipv4.tcp_syncookies`参数设置为1可以提高服务器的安全性和可靠性

- **TCP时间戳：**`net.ipv4.tcp_timestamps = 1`是一个用于启用TCP时间戳的内核参数。

  TCP时间戳是一种用于在TCP连接中跟踪和测量时间的机制。当`net.ipv4.tcp_timestamps`参数被设置为1时，操作系统会在TCP头部中添加时间戳选项。该选项包含了发送和接收数据包的时间戳信息。

  启用TCP时间戳有以下几个作用：

  1. 改进性能：TCP时间戳可以用于计算往返时间（RTT），通过测量数据包从发送方到接收方再返回发送方所需要的时间。这个信息可以用于TCP拥塞控制算法和流量控制，以提高网络性能和吞吐量。
  2. 时序校验：时间戳可以用于确保数据包按照正确的顺序传递。接收方可以通过比较时间戳来检查数据包是否按照发送方的顺序到达，从而保证数据的可靠传输。
  3. 抵御一些攻击：启用TCP时间戳可以增加一定的安全性，使得一些攻击变得更加困难。例如，对于某些攻击，如TCP序列号预测攻击，攻击者需要知道TCP连接中的时间戳信息，而启用时间戳可以使攻击者更难获取这些信息。

  总的来说，启用`net.ipv4.tcp_timestamps`参数可以提供更好的网络性能和可靠性，并增加一定的安全性。然而，需要注意的是，启用时间戳可能会增加一些额外的开销，因此在某些特定的情况下，可能需要根据实际需求进行配置。

- **TIIME-WAIT 状态的连接重用**: `net.ipv4.tcp_tw_reuse = 1`是一个用于允许TIME-WAIT状态连接重用的内核参数。

  在TCP连接关闭后，通常会进入TIME-WAIT状态，该状态持续一段时间以确保网络中的所有数据包都已经被接收和处理。在此状态下，操作系统将为连接保留一段时间的资源，以便处理可能在网络中滞留的后续数据包。

  启用`net.ipv4.tcp_tw_reuse`参数后，操作系统允许将TIME-WAIT状态的连接重用。这意味着当新的连接请求到达时，如果其与之前的TIME-WAIT状态连接具有相同的源IP地址、目标IP地址、源端口和目标端口，操作系统可以重用之前的连接资源，而无需等待TIME-WAIT状态的连接完全结束。

  启用TIME-WAIT连接重用的主要作用是：

  1. 提高连接处理能力：通过允许重用TIME-WAIT状态的连接，操作系统可以更快地处理新的连接请求，从而提高连接处理能力和并发性能。
  2. 减少资源占用：由于不需要为每个新的连接请求创建新的连接资源，启用TIME-WAIT连接重用可以减少系统资源的占用，如内存和文件描述符等。

  需要注意的是，启用TIME-WAIT连接重用可能会引入一些风险和问题，如可能导致连接混淆、数据干扰等。因此，在配置系统时，需要根据实际需求和网络环境来权衡是否启用TIME-WAIT连接重用

  需要注意的是，新建的TCP连接只会接收到挥手报文段（FIN），而不会接收到已经经过TIME-WAIT状态的连接重用的其他数据包。只有在新建的TCP连接建立后，之前延迟收到的挥手报文段才会被传输到新的TCP连接，并触发相应的连接关闭流程。

  总结而言，如果packA是一个TCP挥手报文段（FIN），在新建的TCP连接建立后到达了相同的目标端口，被动关闭方会接收到这个挥手报文段，并按照TCP协议规定进行相应的连接关闭流程。而对于其他状态的新建TCP连接，挥手报文段将被丢弃。

- **FIN_WAIT_2**: `net.ipv4.tcp_fin_timeout`参数用于设置TCP连接处于FIN-WAIT-2状态的超时时间。FIN-WAIT-2状态是指连接关闭后，一方发送了FIN（结束标志）并等待对方的ACK（确认标志）的状态。

  默认情况下，`net.ipv4.tcp_fin_timeout`参数的值是60秒，意味着当TCP连接处于FIN-WAIT-2状态超过60秒后，操作系统将终止该连接并释放相应的资源。

  通过调整`net.ipv4.tcp_fin_timeout`参数的值，可以更改FIN-WAIT-2状态的超时时间。较短的超时时间可以更快地释放资源，但可能会导致连接的不稳定性和丢失。较长的超时时间可以确保连接能够正常关闭，但可能会占用更多的资源。

  根据具体的应用需求和网络环境，可以根据实际情况来调整`net.ipv4.tcp_fin_timeout`参数的值，以平衡连接稳定性和资源利用率。需要注意的是，在调整参数值之前，最好进行适当的测试和评估，以确定最佳的超时时间

  附tcp握手和挥手的过程对应Linux中的状态：

  1. 第一次握手：客户端向服务器发送SYN（同步）包。
     - Linux状态：SYN_SENT
  2. 第二次握手：服务器接收到SYN包后，向客户端发送SYN-ACK（同步-确认）包。
     - Linux状态：SYN_RECV
  3. 第三次握手：客户端接收到服务器的SYN-ACK包后，向服务器发送ACK（确认）包。
     - Linux状态：ESTABLISHED

  挥手过程：

  1. 第一次挥手：一方发送FIN（结束）包，表示要关闭连接。
     - Linux状态：FIN_WAIT1
  2. 第二次挥手：接收到FIN包的对方发送ACK包进行确认。
     - Linux状态：FIN_WAIT2
  3. 第三次挥手：对方发送FIN包，表示同意关闭连接。
     - Linux状态：TIME_WAIT
  4. 第四次挥手：发送ACK包进行确认。
     - Linux状态：CLOSED

- **半连接队列:**`net.ipv4.tcp_max_syn_backlog`参数用于设置TCP半连接队列（SYN队列）的最大长度。半连接队列是指在TCP三次握手过程中，服务器接收到客户端的SYN包后，但还未完成连接的队列

- **TIME_WAIT:**   `net.ipv4.tcp_max_tw_buckets`参数用于设置TIME-WAIT状态的最大数量。TIME-WAIT状态是指TCP连接关闭后，等待一段时间以确保网络中的所有数据包都被处理完毕的状态。

  默认情况下，`net.ipv4.tcp_max_tw_buckets`参数的值通常为4096。这意味着系统可以同时保持的TIME-WAIT状态的最大数量为4096个。

  通过调整`net.ipv4.tcp_max_tw_buckets`参数的值，可以增加或减少系统允许的TIME-WAIT状态的最大数量。较大的值可以提高系统处理并发连接和连接重用的能力，但也会占用更多的内存资源。较小的值可以减少内存占用，但可能会导致TIME-WAIT状态的连接被丢弃或重用较少。

  **连接重用通常比新建立的连接使用的时间短。连接重用是指在一个TCP连接关闭后，可以将该连接的资源（例如端口、序列号等）重新分配给新的连接，而不必等待一段时间，这样可以减少连接建立的时间开销。**

- **网络设备接收队列的最大长度：**`net.core.netdev_max_backlog`参数用于设置网络设备接收队列的最大长度。该参数定义了网络设备接收队列中可以排队等待处理的数据包的最大数量。

  默认情况下，`net.core.netdev_max_backlog`参数的值通常为1000。这意味着网络设备接收队列中最多可以排队1000个数据包等待处理。

  通过调整`net.core.netdev_max_backlog`参数的值，可以增加或减少网络设备接收队列的最大长度。较大的值可以增加系统处理并发网络流量的能力，但也会占用更多的内存资源。较小的值可以减少内存占用，但可能会导致数据包丢失或被丢弃。

  根据具体的网络负载和系统资源情况，可以根据实际需求来调整`net.core.netdev_max_backlog`参数的值，以平衡网络吞吐量和资源利用率。需要注意的是，在调整参数值之前，最好进行适当的测试和评估，以确定最佳的接收队列长度。

- 内存脏数据上限百分比：`vm.dirty_ratio`参数用于设置内存中脏数据的上限百分比。脏数据是指已被修改但还未写回磁盘的数据。

  默认情况下，`vm.dirty_ratio`参数的值通常为20%，即内存中脏数据的上限为物理内存的20%。

  通过调整`vm.dirty_ratio`参数的值，可以增加或减少内存中脏数据的上限百分比。较大的值可以增加系统缓存脏数据的容量，提高写入磁盘的效率，但也会占用更多的内存。较小的值可以减少内存占用，但可能会导致更频繁的磁盘写入操作。

  根据系统的内存容量和磁盘写入需求，可以根据实际情况来调整`vm.dirty_ratio`参数的值，以平衡缓存容量和资源利用率。需要注意的是，在调整参数值之前，最好了解应用程序的需求和系统的磁盘性能，以确保不会引入不必要的开销或性能下降。

  查看内存脏数据的方法：

  ```shell
  [root@hecs-36968 ~]# grep MemTotal /proc/meminfo
  MemTotal:        1881420 kB
  [root@hecs-36968 ~]# grep Dirty /proc/meminfo
  Dirty:                40 kB
  ```

  ![image-20240305204248830](/Users/tongqing/Library/Application Support/typora-user-images/image-20240305204248830.png)

下面是一个带有详细注释的 CentOS 7.9 内核参数列表，涵盖了网络、文件系统、虚拟内存、CPU、安全性、缓存等方面：

```plaintext
# 网络相关参数
net.ipv4.ip_forward = 0                # 是否开启 IP 转发功能
net.ipv4.conf.default.rp_filter = 1     # 反向路径过滤器，提高网络安全性
net.ipv4.conf.all.rp_filter = 1         # 同上
net.ipv4.tcp_syncookies = 1             # 启用 SYN Cookies，防范 SYN 洪水攻击
net.ipv4.tcp_timestamps = 1            # 启用 TCP 时间戳
net.ipv4.tcp_tw_reuse = 1              # 允许 TIME-WAIT 状态的连接重用
net.ipv4.tcp_tw_recycle = 0            # 禁用 TIME-WAIT 状态的快速回收
net.ipv4.tcp_fin_timeout = 60          # FIN-WAIT-2 状态的超时时间
net.ipv4.tcp_max_syn_backlog = 1024    # 半连接队列的最大长度
net.ipv4.tcp_max_tw_buckets = 4096     # TIME-WAIT 状态的最大数量
net.ipv4.tcp_keepalive_time = 7200     # TCP keep-alive 时间
net.ipv4.tcp_keepalive_probes = 9      # TCP keep-alive 探测次数
net.ipv4.tcp_keepalive_intvl = 75      # TCP keep-alive 探测间隔
net.core.netdev_max_backlog = 1000     # 网络设备接收队列的最大长度
net.core.somaxconn = 128               # 允许等待连接的最大数量
net.ipv4.tcp_synack_retries = 2        # SYN-ACK 确认尝试次数

# 磁盘和文件系统相关参数
vm.dirty_ratio = 20                    # 内存脏数据的上限百分比
vm.dirty_background_ratio = 10         # 后台写回脏数据的百分比，超过阈值则写会
vm.dirty_expire_centisecs = 3000       # 内存中脏数据的超时时间
vm.dirty_writeback_centisecs = 1000    # 后台写回脏数据的间隔时间，

vm.dirty_writeback_centisecs参数用于设置后台写回脏数据的间隔时间。它定义了系统在进行后台写回操作时，两次写回之间的最小时间间隔。

# CPU 相关参数
kernel.sched_migration_cost_ns = 5000000  # 系统中可用最小时间片
kernel.sched_autogroup_enabled = 1        # 启用自动分组调度

# 内存和交换内存相关参数
vm.overcommit_memory = 0                # 内存过量分配策略（0表示使用默认策略）
vm.overcommit_ratio = 50                # 允许系统内存过量分配的百分比
vm.swappiness = 30                      # 系统倾向使用交换空间的程度
vm.vfs_cache_pressure = 100             # 控制目录和inode缓存的倾向
vm.max_map_count = 65530                # 最大映射表项数量

# 安全性相关参数
kernel.exec-shield = 1                  # 启用 Exec Shield，提高系统安全性
kernel.randomize_va_space = 2           # 随机化用户空间地址，提高安全性

# 缓存相关参数
fs.file-max = 65536                     # 系统最大文件句柄数
fs.nr_open = 1048576                    # 文件系统同时打开的最大文件数
fs.inotify.max_user_watches = 8192      # inotify最大用户观察数

# TCP buffer 参数
net.ipv4.tcp_rmem = 4096 87380 16777216 # 接收缓冲区参数
net.ipv4.tcp_wmem = 4096 65536 16777216 # 发送缓冲区参数
net.core.rmem_default = 262144          # 接收缓冲区默认值
net.core.wmem_default = 262144          # 发送缓冲区默认值
net.core.rmem_max = 16777216            # 接收缓冲区最大值
net.core.wmem_max = 16777216            # 发送缓冲区最大值
```

这些注释应该能够帮助您理解每个参数的作用。请注意，根据具体需求，您可能需要进一步调整这些参数。在修改前务必备份配置文件，并进行充分的测试，以确保修改在特定环境中的有效性。
## File system 参数介绍

这是一系列Linux内核参数的设置，涉及文件系统（fs）和与文件系统相关的一些方面。以下是对每个参数的简要介绍和解释：

1. `fs.aio-max-nr = 65536`: 设置系统支持的最大异步I/O操作的数量。异步I/O允许应用程序在等待I/O操作完成的同时执行其他任务。

2. `fs.aio-nr = 2305`: 当前系统中已分配的异步I/O操作的数量。

3. `fs.binfmt_misc.status = enabled`: 启用二进制格式描述的misc模块，允许内核加载支持的可执行文件格式。

4. `fs.dentry-state`: 提供有关目录项（dentry）状态的信息，包括已分配的、未使用的、负载的等。

5. `fs.dir-notify-enable = 1`: 启用目录通知功能，允许进程监视文件系统中目录的变化。

6. `fs.epoll.max_user_watches = 381009`: 设置每个用户可以监视的文件描述符（file descriptor）的最大数量，用于epoll机制。

7. `fs.file-max = 182615`: 系统允许打开的文件描述符的最大数量。

8. `fs.file-nr`: 提供有关打开文件的数量的统计信息。

9. `fs.inode-nr`: 提供有关inode数量的统计信息，inode是用于表示文件和目录的数据结构。

10. `fs.inode-state`: 提供有关inode状态的详细信息，包括已分配的、未使用的等。

11. `fs.inotify.max_queued_events = 16384`: 设置inotify通知队列中最大允许的事件数量。

12. `fs.inotify.max_user_instances = 128`: 设置每个用户最大允许的inotify实例数量。

13. `fs.inotify.max_user_watches = 8192`: 设置每个用户最大允许的inotify监视点数量。

14. `fs.lease-break-time = 45`: 设置文件系统租约（lease）的中断时间。

15. `fs.leases-enable = 1`: 启用文件系统租约机制。

16. `fs.may_detach_mounts = 1`: 允许卸载已分离的挂载点。

17. `fs.mount-max = 100000`: 系统允许创建的最大挂载点数量。

18. `fs.mqueue.*`: 与POSIX消息队列相关的参数设置。

19. `fs.negative-dentry-limit = 0`: 设置负载目录项的最大数量，0表示没有限制。

20. `fs.nr_open = 1048576`: 系统允许打开的文件描述符和inode的最大总数。

21. `fs.overflowgid` 和 `fs.overflowuid`: 在文件系统溢出时，分别指定使用的组ID和用户ID。

22. `fs.pipe-max-size = 1048576`: 设置管道的最大大小。

23. `fs.pipe-user-pages-*`: 管道用户页的软和硬限制。

24. `fs.protected_hardlinks` 和 `fs.protected_symlinks`: 启用保护机制，防止特定目录下的硬链接和符号链接被普通用户创建。

25. `fs.quota.*`: 与磁盘配额相关的参数设置。

26. `fs.suid_dumpable = 0`: 限制核心转储文件对setuid程序的访问，0表示禁用核心转储。

这些参数的设置可以影响文件系统的性能、安全性和行为。根据具体的系统需求，可以调整这些参数以满足特定的要求。

## kernel参数介绍

这些参数是Linux内核中的一些配置参数，它们通过 `/proc/sys/kernel/` 路径下的文件进行设置和读取。这些参数控制了操作系统的各种行为，如核心转储的方式、启动加载程序的类型等。以下是其中一些参数的说明：

- `kernel.acct`: 用于开启和关闭进程记账功能。当该值设置为 0 时，关闭记账功能；设置为 4 时，开启记录任务的执行时间。
- `kernel.acpi_video_flags`: ACPI 视频驱动的标志位。
- `kernel.auto_msgmni`: 自动设置消息队列的最大数量。设置为 0 表示禁用此功能。
- `kernel.bootloader_type`: 引导加载程序的类型。通常情况下，GRUB 的类型是 114。
- `kernel.bootloader_version`: 引导加载程序的版本号。
- `kernel.cad_pid`: Ctrl-Alt-Del 组合键对应的进程 PID。
- `kernel.cap_last_cap`: 内核允许用户空间程序使用的最大能力标志位数。
- `kernel.compat-log`: 用于开启和关闭内核与用户空间兼容性日志记录功能。
- `kernel.core_pattern`: 决定了生成的核心转储文件的文件名模式。
- `kernel.core_pipe_limit`: 决定了能够通过管道传输核心转储的最大字节数。
- `kernel.core_uses_pid`: 决定了生成的核心转储文件是否包含进程 ID

- `kernel.ctrl-alt-del`: 控制是否允许使用 Ctrl-Alt-Del 组合键来重启系统。当值为 0 时表示禁用，为 1 时表示启用。
- `kernel.dmesg_restrict`: 控制是否限制普通用户对 dmesg 命令的访问权限。当值为 0 时表示不限制，为 1 时表示限制。
- `kernel.domainname`: 指定了系统的域名。
- `kernel.ftrace_dump_on_oops`: 决定了在内核发生 oops 错误时是否进行函数追踪的转储。当值为 0 时表示禁用，为 1 时表示启用。
- `kernel.ftrace_enabled`: 控制是否启用 ftrace 功能。当值为 0 时表示禁用，为 1 时表示启用。
- `kernel.hardlockup_all_cpu_backtrace`: 控制是否在所有 CPU 都挂起时生成回溯信息。当值为 0 时表示禁用，为 1 时表示启用。
- `kernel.hardlockup_panic`: 控制是否在检测到硬锁死时触发系统崩溃。当值为 0 时表示禁用，为 1 时表示启用。
- `kernel.hostname`: 指定了系统的主机名。
- `kernel.hotplug`: 控制热插拔事件的处理。

- `kernel.hung_task_warnings`: 决定了系统在检测到任务挂起时发出警告的次数。
- `kernel.io_delay_type`: 控制了 I/O 延迟的类型。
- `kernel.kexec_load_disabled`: 控制是否禁用 kexec 载入功能。
- `kernel.keys.gc_delay`: 指定了密钥垃圾收集器的延迟时间。
- `kernel.keys.maxbytes`、`kernel.keys.maxkeys`: 分别指定了密钥环的最大大小和最大密钥数。
- `kernel.keys.persistent_keyring_expiry`: 指定了持久密钥环的到期时间。
- `kernel.keys.root_maxbytes`、`kernel.keys.root_maxkeys`: 分别指定了根密钥环的最大大小和最大密钥数。
- `kernel.kptr_restrict`: 控制是否限制内核指针的访问权限。当值为 0 时表示不限制，为 1 时表示限制。
- `kernel.max_lock_depth`: 指定了内核中锁的最大深度。
- `kernel.modprobe`: 指定了模块加载器的路径。
- `kernel.modules_disabled`: 控制是否禁用内核模块加载和卸载。

- `kernel.msgmnb`: 指定了消息队列的最大字节数。
- `kernel.msgmni`: 指定了系统中消息队列的最大数量。
- `kernel.ngroups_max`: 指定了系统中用户组的最大数量。
- `kernel.nmi_watchdog`: 控制是否启用 NMI（Non-Maskable Interrupt）监视狗。当值为 1 时表示启用，为 0 时表示禁用。
- `kernel.ns_last_pid`: 指定了上一个命名空间中使用的最后一个进程 ID。
- `kernel.numa_balancing`: 控制是否启用 NUMA（Non-Uniform Memory Access）节点的平衡。
- `kernel.numa_balancing_scan_delay_ms`: 指定了 NUMA 平衡扫描的延迟时间。
- `kernel.numa_balancing_scan_period_max_ms`、`kernel.numa_balancing_scan_period_min_ms`: 分别指定了 NUMA 平衡扫描的最大和最小时间间隔。
- `kernel.numa_balancing_scan_size_mb`: 指定了 NUMA 平衡扫描的大小限制。
- `kernel.numa_balancing_settle_count`: 指定了 NUMA 平衡达到稳定状态所需的扫描次数。
- `kernel.osrelease`: 指定了系统当前正在运行的内核版本。
- `kernel.ostype`: 指定了系统的操作系统类型。
- `kernel.overflowgid`、`kernel.overflowuid`: 分别指定了文件系统超出限制时的默认组 ID 和用户 ID。

- `kernel.panic`: 决定了系统在遇到不可恢复的内核错误时是否自动重启。当值为 0 时表示禁用自动重启，为非 0 值时表示启用自动重启，并设置了系统在遇到不可恢复的内核错误时自动重启的时间间隔。
- `kernel.panic_on_oops`: 控制系统在遇到 oops 错误（非致命的内核错误）时是否自动重启。当值为 1 时表示启用自动重启，为 0 时表示禁用自动重启。
- `kernel.perf_cpu_time_max_percent`: 指定了每个 CPU 上用于性能计数器的最大 CPU 时间百分比。
- `kernel.perf_event_max_sample_rate`: 指定了性能计数器的最大采样率。
- `kernel.perf_event_mlock_kb`: 指定了性能计数器的锁定内存的大小。
- `kernel.perf_event_paranoid`: 控制对性能计数器的访问权限级别。
- `kernel.pid_max`: 指定了系统中进程 ID 的最大值。
- `kernel.poweroff_cmd`: 指定了系统关机时调用的命令。
- `kernel.printk`: 控制内核消息的输出级别。
- `kernel.printk_delay`: 控制内核消息的延迟输出。

- `kernel.printk_ratelimit`: 控制内核消息的速率限制，表示每秒最多允许输出的消息数量。
- `kernel.printk_ratelimit_burst`: 控制内核消息的速率限制突发值，表示在超过速率限制之后允许的突发消息数量。
- `kernel.pty.max`: 指定了系统中伪终端（PTY）的最大数量。
- `kernel.pty.nr`: 指定了当前已分配的伪终端（PTY）数量。
- `kernel.pty.reserve`: 指定了系统保留用于内核使用的伪终端（PTY）的数量。
- `kernel.random.boot_id`: 表示系统启动时生成的唯一引导 ID。
- `kernel.random.entropy_avail`: 表示系统中随机熵的可用性，即系统中可用于生成随机数的熵的数量。
- `kernel.random.poolsize`: 指定了随机数池的大小。
- `kernel.random.read_wakeup_threshold`: 指定了从随机数池中读取随机数据时唤醒的阈值。
- `kernel.random.urandom_min_reseed_secs`: 指定了对 `/dev/urandom` 设备进行重新播种所需的最小时间间隔。
- `kernel.random.uuid`: 表示系统生成的唯一 Universally Unique Identifier (UUID)。
- `kernel.random.write_wakeup_threshold`: 指定了向随机数池中写入随机数据时唤醒的阈值。
- `kernel.randomize_va_space`: 控制了虚拟地址空间的随机化，以提高系统的安全性。
- `kernel.real-root-dev`: 指定了系统的真实根设备。

- `kernel.sched_autogroup_enabled`: 控制是否启用自动任务分组。当值为 1 时表示启用，会根据任务的调度行为自动将任务分组。
- `kernel.sched_cfs_bandwidth_slice_us`: 定义了完全公平调度（CFS）的带宽片段大小，以微秒为单位。这个参数用于控制 CFS 调度器的行为。
- `kernel.sched_child_runs_first`: 控制子进程是否优先运行。当值为 1 时表示子进程优先运行，为 0 时表示不优先。（“优先”指的是下一次运行时间上的先后顺序，而不是后续运行的优先级。具体来说，如果`kernel.sched_child_runs_first`参数设置为1，那么在父进程fork出子进程后，子进程将优先于父进程获得CPU执行时间，并在下一个时间片中先于父进程执行；而如果参数设置为0，则子进程和父进程将共享相同的运行时间片，执行的顺序取决于调度算法的具体实现和进程状态的优先级）
- `kernel.sched_latency_ns`: 定义了任务的最大调度延迟时间，以纳秒为单位。
- `kernel.sched_migration_cost_ns`: 定义了任务迁移的成本，以纳秒为单位。
- `kernel.sched_min_granularity_ns`: 定义了最小的任务调度粒度，以纳秒为单位。
- `kernel.sched_nr_migrate`: 定义了在一个时钟周期内可以迁移的任务数量。
- `kernel.sched_rr_timeslice_ms`: 定义了实时任务的时间片大小，以毫秒为单位。
- `kernel.sched_rt_period_us`: 定义了实时任务的调度周期，以微秒为单位。
- `kernel.sched_rt_runtime_us`: 定义了实时任务的运行时间限制，以微秒为单位。
- `kernel.sched_schedstats`: 控制是否启用调度统计信息。当值为 1 时表示启用，为 0 时表示禁用。
- `kernel.sched_shares_window_ns`: 定义了任务共享的窗口大小，以纳秒为单位。
- `kernel.sched_time_avg_ms`: 定义了任务平均调度时间，以毫秒为单位。
- `kernel.sched_tunable_scaling`: 控制调度器参数是否可以动态调整。当值为 1 时表示启用，为 0 时表示禁用。
- `kernel.sched_wakeup_granularity_ns`: 定义了任务唤醒的粒度，以纳秒为单位

- `kernel.sem_next_id`: 下一个系统信号量标识符的值。
- `kernel.shm_next_id`: 下一个系统共享内存标识符的值。
- `kernel.shm_rmid_forced`: 控制在系统重启时是否强制删除未分离的共享内存段。当值为 1 时表示强制删除，为 0 时表示不强制。
- `kernel.shmall`: 系统中所有共享内存段的总页框数。
- `kernel.shmmax`: 系统中单个共享内存段的最大大小。
- `kernel.shmmni`: 系统中最大共享内存段的数量。
- `kernel.softlockup_all_cpu_backtrace`: 控制是否在所有 CPU 发生软锁死时生成回溯信息。当值为 1 时表示生成，为 0 时表示不生成。
- `kernel.softlockup_panic`: 控制是否在检测到软锁死时触发系统崩溃。当值为 1 时表示触发，为 0 时表示不触发。
- `kernel.stack_tracer_enabled`: 控制是否启用内核堆栈跟踪器。当值为 1 时表示启用，为 0 时表示禁用。
- `kernel.sysctl_writes_strict`: 控制对 `/proc/sys` 写操作的严格性。当值为 1 时表示严格，为 0 时表示不严格。
- `kernel.sysrq`: 控制 SysRq 功能的权限。SysRq 功能允许用户通过按下 Magic SysRq 键和某个字母键来执行一些系统操作，如重新启动、同步磁盘等。这个参数指定了哪些字母键是允许的。
- `kernel.tainted`: 内核标志，用于标识内核的状态。不同的标志位表示不同的状态，详情可以查阅相关文档。
- `kernel.threads-max`: 系统中的最大线程数。
- `kernel.timer_migration`: 控制定时器是否可以在不同 CPU 上迁移。当值为 1 时表示允许迁移，为 0 时表示不允许。
- `kernel.traceoff_on_warning`: 控制在警告消息中是否关闭跟踪。当值为 1 时表示关闭，为 0 时表示不关闭。
- `kernel.unknown_nmi_panic`: 控制是否在检测到未知的 NMI（非屏蔽中断）时触发系统崩溃。当值为 1 时表示触发，为 0 时表示不触发。
- `kernel.usermodehelper.bset`: 控制在用户模式下辅助程序的权限集。
- `kernel.usermodehelper.inheritable`: 控制在用户模式下辅助程序的继承权限。
- `kernel.version`: 内核的版本号和编译日期。
- `kernel.watchdog`: 控制是否启用系统看门狗。当值为 1 时表示启用，为 0 时表示禁用。
- `kernel.watchdog_cpumask`: 定义了哪些 CPU 参与系统看门狗的监视。
- `kernel.watchdog_thresh`: 系统看门狗的阈值，即看门狗超时的时间。
- `kernel.yama.ptrace_scope`: 控制 ptrace 系统调用的权限范围。

## NET参数

以下是您提供的网络相关参数的解释：

- `net.bridge.bridge-nf-call-arptables`: 控制是否在桥接设备上调用 arptables 进行 ARP 报文过滤。
- `net.bridge.bridge-nf-call-ip6tables`: 控制是否在桥接设备上调用 ip6tables 进行 IPv6 报文过滤。
- `net.bridge.bridge-nf-call-iptables`: 控制是否在桥接设备上调用 iptables 进行 IPv4 报文过滤。
- `net.bridge.bridge-nf-filter-pppoe-tagged`: 控制是否在桥接设备上过滤 PPPoE 标签。
- `net.bridge.bridge-nf-filter-vlan-tagged`: 控制是否在桥接设备上过滤 VLAN 标签。
- `net.bridge.bridge-nf-pass-vlan-input-dev`: 控制是否允许 VLAN 输入设备通过桥接设备。
- `net.core.bpf_jit_enable`: 控制是否启用 BPF（Berkeley Packet Filter）即时编译器。
- `net.core.bpf_jit_harden`: 控制是否对 BPF JIT 进行加固。
- `net.core.bpf_jit_kallsyms`: 控制是否允许 BPF JIT 使用内核符号表。
- `net.core.busy_poll`: 控制是否启用忙碌轮询模式。
- `net.core.busy_read`: 控制是否启用忙碌读取模式。
- `net.core.default_qdisc`: 指定了默认的队列调度算法。
- `net.core.dev_weight`: 设置网络设备的权重。
- `net.core.dev_weight_rx_bias`: 设置网络接收权重的偏置。
- `net.core.dev_weight_tx_bias`: 设置网络发送权重的偏置。
- `net.core.message_burst`: 控制网络消息的突发大小。
- `net.core.message_cost`: 控制网络消息的成本。
- `net.core.netdev_budget`: 控制每个处理器核心每个网络设备的处理预算。
- `net.core.netdev_max_backlog`: 控制每个网络设备的最大输入队列长度。
- `net.core.netdev_rss_key`: 设置用于 RSS（Receive Side Scaling）的密钥。RSS 允许将网络流量均匀地分配到多个 CPU 核心上处理。

- `net.core.netdev_tstamp_prequeue`: 控制是否在网络接收数据包进入队列之前进行时间戳记录。
- `net.core.optmem_max`: 指定了套接字选项内存池的最大大小。
- `net.core.rmem_default`: 指定了默认的套接字接收缓冲区大小。
- `net.core.rmem_max`: 指定了最大的套接字接收缓冲区大小。
- `net.core.rps_sock_flow_entries`: 指定了用于网络接收数据包的 RPS（Receive Packet Steering）流表的大小。
- `net.core.somaxconn`: 指定了套接字连接的最大排队数量。
- `net.core.warnings`: 控制是否启用网络核心警告信息。当值为 1 时表示启用，为 0 时表示禁用。
- `net.core.wmem_default`: 指定了默认的套接字发送缓冲区大小。
- `net.core.wmem_max`: 指定了最大的套接字发送缓冲区大小。
- `net.core.xfrm_acq_expires`: 定义了 XFRM 事务的过期时间。
- `net.core.xfrm_aevent_etime`: 定义了 XFRM 事务事件的等待时间。
- `net.core.xfrm_aevent_rseqth`: 定义了 XFRM 事务事件的重复序列阈值。
- `net.core.xfrm_larval_drop`: 控制是否丢弃处于 XFRM 初始状态（Larval）的数据包。
- `net.ipv4.cipso_cache_bucket_size`: 定义了 CIPSO（Common IP Security Option）缓存的桶大小。
- `net.ipv4.cipso_cache_enable`: 控制是否启用 CIPSO 缓存。
- `net.ipv4.cipso_rbm_optfmt`: 定义了 CIPSO RBM（Role-Based Access Control）选项的格式。
- `net.ipv4.cipso_rbm_strictvalid`: 控制是否严格验证 CIPSO RBM 选项的有效性。
- `net.ipv4.conf.all.accept_local`: 控制是否接受发送到本地 IP 地址的数据包。
- `net.ipv4.conf.all.accept_redirects`: 控制是否接受 ICMP 重定向消息。
- `net.ipv4.conf.all.accept_source_route`: 控制是否接受源路由选项的数据包。

- `net.ipv4.conf.all.arp_accept`: 控制是否接受所有 ARP 请求。
- `net.ipv4.conf.all.arp_announce`: 控制发送 ARP 响应时使用的源地址。当值为 0 时表示使用接口地址，为 1 时表示使用全局地址，为 2 时表示使用配置的代理地址。
- `net.ipv4.conf.all.arp_filter`: 控制是否启用 ARP 过滤。启用后，内核会检查发出去的 ARP 请求和接收到的 ARP 响应，只有符合特定条件的才会接受。
- `net.ipv4.conf.all.arp_ignore`: 控制是否忽略所有 ARP 请求。
- `net.ipv4.conf.all.arp_notify`: 控制是否通知 ARP 子系统有新的地址绑定发生了变化。
- `net.ipv4.conf.all.bootp_relay`: 控制是否启用 BOOTP 转发。
- `net.ipv4.conf.all.disable_policy`: 控制是否禁用策略路由功能。
- `net.ipv4.conf.all.disable_xfrm`: 控制是否禁用 XFRM（IPsec）功能。
- `net.ipv4.conf.all.force_igmp_version`: 控制是否强制使用指定的 IGMP 版本。
- `net.ipv4.conf.all.forwarding`: 控制是否启用 IP 转发功能。启用后，Linux 主机将转发接收到的数据包。
- `net.ipv4.conf.all.igmpv2_unsolicited_report_interval`: 指定 IGMPv2 未请求报告的发送间隔，单位为毫秒。
- `net.ipv4.conf.all.igmpv3_unsolicited_report_interval`: 指定 IGMPv3 未请求报告的发送间隔，单位为毫秒。
- `net.ipv4.conf.all.log_martians`: 控制是否记录引起警告的异常数据包（例如，IP 地址和 MAC 地址不匹配）。
- `net.ipv4.conf.all.mc_forwarding`: 控制是否启用多播转发。
- `net.ipv4.conf.all.medium_id`: 设置介质类型标识符。
- `net.ipv4.conf.all.promote_secondaries`: 控制是否优先使用第二 IP 地址。
- `net.ipv4.conf.all.proxy_arp`: 控制是否启用代理 ARP。
- `net.ipv4.conf.all.proxy_arp_pvlan`: 控制是否启用 PVLAN 的代理 ARP。

- `net.ipv4.conf.all.route_localnet`: 控制是否接受路由到本地网络的数据包。当值为 0 时表示不接受，为 1 时表示接受。
- `net.ipv4.conf.all.rp_filter`: 控制反向路径过滤器的设置。反向路径过滤器用于防止 IP 欺骗攻击。
- `net.ipv4.conf.all.secure_redirects`: 控制是否接受来自其他主机的安全重定向。安全重定向是指目标 IP 地址和网关 IP 地址必须在同一子网上。
- `net.ipv4.conf.all.send_redirects`: 控制是否发送 ICMP 重定向消息给其他主机。
- `net.ipv4.conf.all.shared_media`: 控制是否允许多个网络接口共享相同的媒体。
- `net.ipv4.conf.all.src_valid_mark`: 设置源地址验证标记，用于验证发往系统的数据包的源 IP 地址是否有效。
- `net.ipv4.conf.all.tag`: 设置网络接口的标记。
- `net.ipv4.conf.default.accept_local`: 控制是否接受发送到本地 IP 地址的数据包。
- `net.ipv4.conf.default.accept_redirects`: 控制是否接受 ICMP 重定向消息。
- `net.ipv4.conf.default.accept_source_route`: 控制是否接受源路由选项的数据包。
- `net.ipv4.conf.default.arp_accept`: 控制是否接受所有 ARP 请求。
- `net.ipv4.conf.default.arp_announce`: 控制发送 ARP 响应时使用的源地址。
- `net.ipv4.conf.default.arp_filter`: 控制是否启用 ARP 过滤。
- `net.ipv4.conf.default.arp_ignore`: 控制是否忽略所有 ARP 请求。
- `net.ipv4.conf.default.arp_notify`: 控制是否通知 ARP 子系统有新的地址绑定发生了变化。
- `net.ipv4.conf.default.bootp_relay`: 控制是否启用 BOOTP 转发。
- `net.ipv4.conf.default.disable_policy`: 控制是否禁用策略路由功能。
- `net.ipv4.conf.default.disable_xfrm`: 控制是否禁用 XFRM（IPsec）功能。
- `net.ipv4.conf.default.force_igmp_version`: 控制是否强制使用指定的 IGMP 版本。

- `net.ipv4.conf.default.forwarding`: 控制是否启用IP转发功能。启用后，Linux主机将转发接收到的数据包。
- `net.ipv4.conf.default.igmpv2_unsolicited_report_interval`: 指定IGMPv2未请求报告的发送间隔，单位为毫秒。
- `net.ipv4.conf.default.igmpv3_unsolicited_report_interval`: 指定IGMPv3未请求报告的发送间隔，单位为毫秒。
- `net.ipv4.conf.default.log_martians`: 控制是否记录引起警告的异常数据包（例如，IP地址和MAC地址不匹配）。
- `net.ipv4.conf.default.mc_forwarding`: 控制是否启用多播转发。
- `net.ipv4.conf.default.medium_id`: 设置介质类型标识符。
- `net.ipv4.conf.default.promote_secondaries`: 控制是否优先使用第二个IP地址。
- `net.ipv4.conf.default.proxy_arp`: 控制是否启用代理ARP。
- `net.ipv4.conf.default.proxy_arp_pvlan`: 控制是否启用PVLAN的代理ARP。
- `net.ipv4.conf.default.route_localnet`: 控制是否接受路由到本地网络的数据包。
- `net.ipv4.conf.default.rp_filter`: 控制是否启用反向路径过滤器。反向路径过滤器用于防止IP欺骗攻击。
- `net.ipv4.conf.default.secure_redirects`: 控制是否接受来自其他主机的安全重定向。安全重定向是指目标IP地址和网关IP地址必须在同一子网上。
- `net.ipv4.conf.default.send_redirects`: 控制是否发送ICMP重定向消息给其他主机。
- `net.ipv4.conf.default.shared_media`: 控制是否允许多个网络接口共享相同的媒体。
- `net.ipv4.conf.default.src_valid_mark`: 设置源地址验证标记，用于验证发往系统的数据包的源IP地址是否有效。

- `net.ipv4.fib_multipath_hash_policy`: 指定多路径的哈希策略。
- `net.ipv4.fwmark_reflect`: 控制是否将防火墙标记（firewall mark）反射到响应数据包上。
- `net.ipv4.icmp_echo_ignore_all`: 控制是否忽略所有 ICMP 回显请求。
- `net.ipv4.icmp_echo_ignore_broadcasts`: 控制是否忽略广播的 ICMP 回显请求。
- `net.ipv4.icmp_errors_use_inbound_ifaddr`: 控制是否使用入站接口地址来发送 ICMP 错误消息。
- `net.ipv4.icmp_ignore_bogus_error_responses`: 控制是否忽略虚假的 ICMP 错误响应。
- `net.ipv4.icmp_msgs_burst`: 指定允许突发发送 ICMP 消息的数量。
- `net.ipv4.icmp_msgs_per_sec`: 指定每秒发送 ICMP 消息的最大数量。
- `net.ipv4.icmp_ratelimit`: 控制 ICMP 消息的速率限制。
- `net.ipv4.icmp_ratemask`: 指定 ICMP 消息的掩码，用于确定哪些类型的消息受到速率限制。
- `net.ipv4.igmp_max_memberships`: 指定每个系统可以加入的 IGMP 组的最大数量。
- `net.ipv4.igmp_max_msf`: 指定每个 IGMP 主机的 MSF （Maximum State Forwarding）条目的最大数量。
- `net.ipv4.igmp_qrv`: 指定 IGMP Query Response Value （QRV）。
- `net.ipv4.inet_peer_maxttl`: 指定用于 IPv4 互联网对等体的最大 TTL 值。
- `net.ipv4.inet_peer_minttl`: 指定用于 IPv4 互联网对等体的最小 TTL 值。
- `net.ipv4.inet_peer_threshold`: 指定用于 IPv4 互联网对等体的最大阈值。
- `net.ipv4.ip_default_ttl`: 指定传输 IPv4 数据包的默认 TTL（生存时间）。
- `net.ipv4.ip_dynaddr`: 控制是否允许动态分配 IP 地址。
- `net.ipv4.ip_early_demux`: 控制是否启用 IPv4 早期分离。
- `net.ipv4.ip_forward`: 控制是否启用 IP 转发功能。

- `net.ipv4.ip_forward_use_pmtu`: 控制是否使用路径 MTU （PMTU）发现。
- `net.ipv4.ip_local_port_range`: 指定用于本地端口分配的范围。
- `net.ipv4.ip_local_reserved_ports`: 指定保留给特定服务的本地端口。
- `net.ipv4.ip_no_pmtu_disc`: 控制是否禁用路径 MTU 发现。
- `net.ipv4.ip_nonlocal_bind`: 控制是否允许非本地地址绑定到套接字。
- `net.ipv4.ipfrag_high_thresh`: 指定 IP 分片的高阈值。
- `net.ipv4.ipfrag_low_thresh`: 指定 IP 分片的低阈值。
- `net.ipv4.ipfrag_max_dist`: 指定 IP 分片的最大距离。
- `net.ipv4.ipfrag_secret_interval`: 指定 IP 分片的密钥间隔。
- `net.ipv4.ipfrag_time`: 指定 IP 分片的超时时间。
- `net.ipv4.neigh.default.anycast_delay`: 指定 IPv4 邻居的任播延迟。
- `net.ipv4.neigh.default.app_solicit`: 指定 IPv4 邻居应用程序请求的数量。
- `net.ipv4.neigh.default.base_reachable_time_ms`: 指定 IPv4 邻居的基础可达时间。
- `net.ipv4.neigh.default.delay_first_probe_time`: 指定 IPv4 邻居的延迟首次探测时间。
- `net.ipv4.neigh.default.gc_interval`: 指定 IPv4 邻居垃圾收集的间隔。
- `net.ipv4.neigh.default.gc_stale_time`: 指定 IPv4 邻居的过期时间。
- `net.ipv4.neigh.default.gc_thresh1`: 指定 IPv4 邻居垃圾收集的阈值1。
- `net.ipv4.neigh.default.gc_thresh2`: 指定 IPv4 邻居垃圾收集的阈值2。
- `net.ipv4.neigh.default.gc_thresh3`: 指定 IPv4 邻居垃圾收集的阈值3。
- `net.ipv4.neigh.default.locktime`: 指定 IPv4 邻居锁定时间。
- `net.ipv4.neigh.default.mcast_solicit`: 指定 IPv4 邻居组播请求的数量。
- `net.ipv4.neigh.default.proxy_delay`: 指定 IPv4 邻居代理的延迟。
- `net.ipv4.neigh.default.proxy_qlen`: 指定 IPv4 邻居代理的队列长度。
- `net.ipv4.neigh.default.retrans_time_ms`: 指定 IPv4 邻居重传时间。
- `net.ipv4.neigh.default.ucast_solicit`: 指定 IPv4 邻居单播请求的数量。
- `net.ipv4.neigh.default.unres_qlen`: 指定 IPv4 邻居未解析队列的长度。
- `net.ipv4.neigh.default.unres_qlen_bytes`: 指定 IPv4 邻居未解析队列的字节数。

- `net.ipv4.ping_group_range`: 指定用于 ICMP ECHO 请求的组范围。
- `net.ipv4.route.error_burst`: 控制路由错误通知的突发数量。
- `net.ipv4.route.error_cost`: 控制路由错误通知的成本。
- `net.ipv4.route.gc_elasticity`: 控制路由缓存的弹性。
- `net.ipv4.route.gc_interval`: 指定路由缓存垃圾收集的间隔。
- `net.ipv4.route.gc_min_interval`: 指定路由缓存最小垃圾收集间隔。
- `net.ipv4.route.gc_min_interval_ms`: 指定路由缓存最小垃圾收集间隔（以毫秒为单位）。
- `net.ipv4.route.gc_thresh`: 控制路由缓存垃圾收集的阈值。
- `net.ipv4.route.gc_timeout`: 指定路由缓存垃圾收集的超时时间。
- `net.ipv4.route.max_size`: 指定路由缓存的最大大小。
- `net.ipv4.route.min_adv_mss`: 指定路由最小的 MSS（Maximum Segment Size）值。
- `net.ipv4.route.min_pmtu`: 指定路由最小的 PMTU（Path MTU）。
- `net.ipv4.route.mtu_expires`: 指定路由 MTU 的过期时间。
- `net.ipv4.route.redirect_load`: 控制重定向负载的数量。
- `net.ipv4.route.redirect_number`: 指定允许的重定向数量。
- `net.ipv4.route.redirect_silence`: 指定重定向的沉默期。
- `net.ipv4.tcp_abort_on_overflow`: 控制 TCP 在接收队列溢出时是否中止连接。
- `net.ipv4.tcp_adv_win_scale`: 指定 TCP 高级窗口缩放因子。
- `net.ipv4.tcp_allowed_congestion_control`: 指定允许使用的 TCP 拥塞控制算法。
- `net.ipv4.tcp_app_win`: 指定 TCP 应用程序窗口的大小。
- `net.ipv4.tcp_autocorking`: 控制 TCP 自动 Corking 功能。

- `net.ipv4.tcp_available_congestion_control`: 指定可用的 TCP 拥塞控制算法。
- `net.ipv4.tcp_base_mss`: 指定 TCP 通讯的最大分段大小（MSS）。
- `net.ipv4.tcp_challenge_ack_limit`: 控制 TCP 挑战应答机制的限制。
- `net.ipv4.tcp_congestion_control`: 指定当前使用的 TCP 拥塞控制算法。
- `net.ipv4.tcp_dsack`: 控制 TCP 支持的重复选择 ACK（DSACK）。
- `net.ipv4.tcp_early_retrans`: 控制 TCP 提早重传的行为。
- `net.ipv4.tcp_ecn`: 指定 TCP 支持的显式拥塞通知（ECN）。
- `net.ipv4.tcp_fack`: 控制 TCP 是否启用 FACK 拥塞控制。
- `net.ipv4.tcp_fastopen`: 控制 TCP 快速打开的行为。
- `net.ipv4.tcp_fastopen_key`: 指定用于 TCP 快速打开的密钥。
- `net.ipv4.tcp_fin_timeout`: 指定 TCP 连接的 FIN 等待时间。
- `net.ipv4.tcp_frto`: 控制 TCP 支持的 F-RTO 算法。
- `net.ipv4.tcp_invalid_ratelimit`: 控制 TCP 无效报文的限制。
- `net.ipv4.tcp_keepalive_intvl`: 指定 TCP keepalive 消息发送的间隔时间。
- `net.ipv4.tcp_keepalive_probes`: 指定 TCP keepalive 消息发送的尝试次数。
- `net.ipv4.tcp_keepalive_time`: 指定 TCP keepalive 消息的间隔时间。
- `net.ipv4.tcp_limit_output_bytes`: 控制 TCP 输出缓冲区的大小限制。
- `net.ipv4.tcp_low_latency`: 控制 TCP 是否启用低延迟模式。
- `net.ipv4.tcp_max_orphans`: 指定系统允许的最大 TCP 孤立连接数。
- `net.ipv4.tcp_max_ssthresh`: 控制 TCP 慢启动门限的最大值。
- `net.ipv4.tcp_max_syn_backlog`: 指定 TCP SYN 队列的最大长度。
- `net.ipv4.tcp_max_tw_buckets`: 指定系统中允许的最大 TIME-WAIT 套接字数量。
- `net.ipv4.tcp_mem`: 指定 TCP 全局内存的使用限制。
- `net.ipv4.tcp_min_snd_mss`: 指定 TCP 最小的发送 MSS。
- `net.ipv4.tcp_min_tso_segs`: 指定 TCP 最小的 TSO（TCP Segmentation Offload）分段数量。
- `net.ipv4.tcp_moderate_rcvbuf`: 控制 TCP 是否自动调整接收缓冲区。
- `net.ipv4.tcp_mtu_probing`: 控制 TCP 是否启用 MTU 探测功能。
- `net.ipv4.tcp_no_metrics_save`: 控制是否禁用 TCP 保存指标。
- `net.ipv4.tcp_notsent_lowat`: 指定发送队列空闲时 TCP 不发送数据的阈值。
- `net.ipv4.tcp_orphan_retries`: 指定系统重新尝试 TCP 孤立连接的次数。
- `net.ipv4.tcp_reordering`: 控制 TCP 支持的数据包重排序数。
- `net.ipv4.tcp_retrans_collapse`: 控制 TCP 是否启用重传合并功能。

- `net.ipv4.tcp_retries1`: 指定 TCP 第一次重传的次数。
- `net.ipv4.tcp_retries2`: 指定 TCP 第二次重传的次数。
- `net.ipv4.tcp_rfc1337`: 控制是否启用 RFC 1337 保护（防止 SYN 攻击）。
- `net.ipv4.tcp_rmem`: 指定 TCP 接收缓冲区的最小、默认和最大值。
- `net.ipv4.tcp_sack`: 控制 TCP 是否启用选择确认（SACK）。
- `net.ipv4.tcp_slow_start_after_idle`: 控制 TCP 是否在空闲一段时间后重新启动慢启动。
- `net.ipv4.tcp_stdurg`: 控制是否禁用 TCP 标准 URG 数据（紧急指针）处理。
- `net.ipv4.tcp_syn_retries`: 指定 TCP 建立连接时 SYN 报文的重传次数。
- `net.ipv4.tcp_synack_retries`: 指定 TCP 建立连接时 SYN-ACK 报文的重传次数。
- `net.ipv4.tcp_syncookies`: 控制是否启用 TCP SYN cookies。
- `net.ipv4.tcp_thin_dupack`: 控制是否启用 TCP 轻量级的快速重传功能。
- `net.ipv4.tcp_thin_linear_timeouts`: 控制是否启用 TCP 轻量级的线性超时。
- `net.ipv4.tcp_timestamps`: 控制是否启用 TCP 时间戳选项。
- `net.ipv4.tcp_tso_win_divisor`: 指定 TCP TSO（TCP Segmentation Offload）窗口大小的调整因子。
- `net.ipv4.tcp_tw_recycle`: 控制是否启用 TIME-WAIT 快速回收功能。
- `net.ipv4.tcp_tw_reuse`: 控制是否允许 TIME-WAIT 套接字重用。
- `net.ipv4.tcp_window_scaling`: 控制是否启用 TCP 窗口缩放选项。
- `net.ipv4.tcp_wmem`: 指定 TCP 发送缓冲区的最小、默认和最大值。
- `net.ipv4.tcp_workaround_signed_windows`: 控制是否启用 TCP 窗口符号修复功能。
- `net.ipv4.udp_mem`: 指定 UDP 全局内存的使用限制。
- `net.ipv4.udp_rmem_min`: 指定 UDP 接收缓冲区的最小值。
- `net.ipv4.udp_wmem_min`: 指定 UDP 发送缓冲区的最小值。
- `net.ipv4.xfrm4_gc_thresh`: 指定 IPv4 XFRM 缓存项的垃圾回收阈值。

- `net.netfilter.nf_conntrack_acct`: 控制是否启用连接跟踪的流量计费。
- `net.netfilter.nf_conntrack_buckets`: 设置连接跟踪哈希表的桶数。
- `net.netfilter.nf_conntrack_checksum`: 控制是否检查连接跟踪数据包的校验和。
- `net.netfilter.nf_conntrack_count`: 显示当前连接跟踪表中的条目数。
- `net.netfilter.nf_conntrack_dccp_loose`: 控制是否启用松散的 DCCP 连接跟踪模式。
- `net.netfilter.nf_conntrack_dccp_timeout_*`: 设置 DCCP 协议连接的超时时间参数。
- `net.netfilter.nf_conntrack_events`: 控制是否启用连接跟踪事件记录。
- `net.netfilter.nf_conntrack_events_retry_timeout`: 设置连接跟踪事件重试超时时间。
- `net.netfilter.nf_conntrack_expect_max`: 设置连接跟踪期望条目的最大数量。
- `net.netfilter.nf_conntrack_generic_timeout`: 设置通用连接跟踪超时时间。
- `net.netfilter.nf_conntrack_helper`: 控制是否启用连接跟踪助手。
- `net.netfilter.nf_conntrack_icmp_timeout`: 设置 ICMP 协议连接的超时时间。
- `net.netfilter.nf_conntrack_log_invalid`: 控制是否记录连接跟踪中的无效数据包。
- `net.netfilter.nf_conntrack_max`: 设置连接跟踪表的最大条目数。

- `net.netfilter.nf_conntrack_sctp_timeout_closed`: SCTP 协议连接关闭状态的超时时间。
- `net.netfilter.nf_conntrack_sctp_timeout_cookie_echoed`: SCTP 协议接收到 COOKIE_ECHO 包的超时时间。
- `net.netfilter.nf_conntrack_sctp_timeout_cookie_wait`: SCTP 协议等待 COOKIE 包的超时时间。
- `net.netfilter.nf_conntrack_sctp_timeout_established`: SCTP 协议连接已建立状态的超时时间。
- `net.netfilter.nf_conntrack_sctp_timeout_heartbeat_acked`: SCTP 协议心跳被确认的超时时间。
- `net.netfilter.nf_conntrack_sctp_timeout_heartbeat_sent`: SCTP 协议发送心跳的超时时间。
- `net.netfilter.nf_conntrack_sctp_timeout_shutdown_ack_sent`: SCTP 协议发送关闭连接确认包的超时时间。

**对于 TCP 连接：**

- `net.netfilter.nf_conntrack_tcp_be_liberal`: 控制 TCP 连接跟踪是否采用宽松模式。
- `net.netfilter.nf_conntrack_tcp_loose`: 控制是否启用 TCP 连接跟踪的松散模式。
- `net.netfilter.nf_conntrack_tcp_max_retrans`: 设置 TCP 最大重传次数。
- `net.netfilter.nf_conntrack_tcp_timeout_close`: TCP 连接关闭状态的超时时间。
- `net.netfilter.nf_conntrack_tcp_timeout_close_wait`: TCP 连接等待关闭状态的超时时间。
- `net.netfilter.nf_conntrack_tcp_timeout_established`: TCP 连接已建立状态的超时时间。
- `net.netfilter.nf_conntrack_tcp_timeout_fin_wait`: TCP 连接等待关闭确认状态的超时时间。
- `net.netfilter.nf_conntrack_tcp_timeout_last_ack`: TCP 连接最后确认状态的超时时间。
- `net.netfilter.nf_conntrack_tcp_timeout_max_retrans`: TCP 连接最大重传超时时间。

**对于 TCP 连接：**

- `net.netfilter.nf_conntrack_tcp_timeout_syn_recv`: TCP SYN_RECV 状态的连接超时时间。
- `net.netfilter.nf_conntrack_tcp_timeout_syn_sent`: TCP SYN_SENT 状态的连接超时时间。
- `net.netfilter.nf_conntrack_tcp_timeout_time_wait`: TCP TIME_WAIT 状态的连接超时时间。
- `net.netfilter.nf_conntrack_tcp_timeout_unacknowledged`: TCP 未确认连接的超时时间。

**对于 UDP 连接：**

- `net.netfilter.nf_conntrack_udp_timeout`: UDP 连接的超时时间。
- `net.netfilter.nf_conntrack_udp_timeout_stream`: UDP 流连接的超时时间。

- `net.netfilter.nf_log.*`: 控制 netfilter 的日志级别。设置为 `NONE` 表示禁用该日志。
- `net.netfilter.nf_log_all_netns`: 控制是否为每个网络命名空间记录 netfilter 日志。
- `net.nf_conntrack_max`: 指定系统中允许的最大活动连接数。在达到此限制后，新的连接将被拒绝。
- `net.unix.max_dgram_qlen`: Unix 套接字的最大队列长度，用于控制传输的数据包数量。
- `user.max_ipc_namespaces`: 用户能够创建的最大 IPC 命名空间数量。
- `user.max_mnt_namespaces`: 用户能够创建的最大挂载命名空间数量。
- `user.max_net_namespaces`: 用户能够创建的最大网络命名空间数量。
- `user.max_pid_namespaces`: 用户能够创建的最大 PID 命名空间数量。
- `user.max_user_namespaces`: 用户能够创建的最大用户命名空间数量。
- `user.max_uts_namespaces`: 用户能够创建的最大 UTS 命名空间数量。

## 虚拟内存配置

1. `vm.admin_reserve_kbytes`：此参数保留一定数量的内存（以千字节为单位）供特权（管理员）进程使用。
2. `vm.block_dump`：此参数确定是否记录块层调试信息。
3. `vm.dirty_background_bytes` 和 `vm.dirty_background_ratio`：这些参数控制在后台写回开始之前内存中可以存在多少脏数据。一个以字节表示，另一个以总内存的百分比表示。
4. `vm.dirty_bytes` 和 `vm.dirty_ratio`：类似于前一对，这些参数控制在所有进程被强制写出脏数据之前内存中可以存在多少脏数据。
5. `vm.dirty_expire_centisecs` 和 `vm.dirty_writeback_centisecs`：这些参数控制脏数据在内存中可以保留多长时间，然后被视为过期并需要写回磁盘。
6. `vm.drop_caches`：此参数允许你清除内核中的各种缓存。
7. `vm.extfrag_threshold`：此参数设置内核伙伴分配器的碎片化阈值。
8. `vm.hugepages_treat_as_movable` 和 `vm.hugetlb_shm_group`：这些参数与内存管理中的大页设置相关。
9. `vm.laptop_mode`：此参数通常用于通过调整磁盘的电源管理来节省笔记本电脑的电力。
10. `vm.legacy_va_layout`：此参数控制内核是否使用传统的虚拟地址布局。
11. `vm.lowmem_reserve_ratio`：此参数控制为系统保留的低内存比例。它由三个数字表示，分别表示低、高和总内存的比例。
12. `vm.max_map_count`：此参数设置进程可以拥有的最大内存映射区域数。
13. `vm.memory_failure_early_kill`：此参数确定内核如何处理内存故障。

1. `vm.memory_failure_recovery = 1`：内存故障恢复设置为启用（1）。
2. `vm.min_free_kbytes = 45056`：保持的最小空闲内存量为45056 KB。
3. `vm.min_slab_ratio = 5`：最小的Slab内存占比为5%。
4. `vm.min_unmapped_ratio = 1`：最小未映射内存比例为1%。
5. `vm.mmap_min_addr = 4096`：mmap最小地址为4096字节。
6. `vm.mmap_rnd_bits = 28` 和 `vm.mmap_rnd_compat_bits = 8`：mmap随机化位数设置。
7. `vm.nr_hugepages`, `vm.nr_hugepages_mempolicy`, `vm.nr_overcommit_hugepages`：巨页设置相关参数。
8. `vm.nr_pdflush_threads = 0`：pdflush线程数量设置为0。
9. `vm.numa_zonelist_order = default`：NUMA区域列表顺序设置为默认。
10. `vm.oom_dump_tasks = 1` 和 `vm.oom_kill_allocating_task = 0`：OOM（Out Of Memory）事件处理相关参数。
11. `vm.overcommit_kbytes = 0`、`vm.overcommit_memory = 0`、`vm.overcommit_ratio = 50`：内存过commit设置相关参数。
12. `vm.page-cluster = 3`：页聚类设置为3。
13. `vm.panic_on_oom = 0`：内存耗尽时是否触发panic的设置。
14. `vm.percpu_pagelist_fraction = 0`：percpu页列表的分配比例设置。
15. `vm.stat_interval = 1`：内存统计信息输出间隔为1秒。
16. `vm.swappiness = 70`：交换空间使用倾向性为70。
17. `vm.user_reserve_kbytes = 56379`：用户保留内存量为56379 KB。
18. `vm.vfs_cache_pressure = 50`：VFS（Virtual File System）缓存压力设置为50。
19. `vm.zone_reclaim_mode = 0`：区域回收模式设置为0。

## IPV6

net.ipv6.anycast_src_echo_reply = 0
net.ipv6.bindv6only = 0
net.ipv6.conf.all.accept_dad = 0
net.ipv6.conf.all.accept_ra = 1
net.ipv6.conf.all.accept_ra_defrtr = 1
net.ipv6.conf.all.accept_ra_pinfo = 1
net.ipv6.conf.all.accept_ra_rt_info_max_plen = 0
net.ipv6.conf.all.accept_ra_rtr_pref = 1
net.ipv6.conf.all.accept_redirects = 1
net.ipv6.conf.all.accept_source_route = 0
net.ipv6.conf.all.autoconf = 1
net.ipv6.conf.all.dad_transmits = 1
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.all.enhanced_dad = 1
net.ipv6.conf.all.force_mld_version = 0
net.ipv6.conf.all.force_tllao = 0
net.ipv6.conf.all.forwarding = 0
net.ipv6.conf.all.hop_limit = 64
net.ipv6.conf.all.keep_addr_on_down = 0
net.ipv6.conf.all.max_addresses = 16
net.ipv6.conf.all.max_desync_factor = 600
net.ipv6.conf.all.mc_forwarding = 0
net.ipv6.conf.all.mldv1_unsolicited_report_interval = 10000
net.ipv6.conf.all.mldv2_unsolicited_report_interval = 1000
net.ipv6.conf.all.mtu = 1280
net.ipv6.conf.all.ndisc_notify = 0
net.ipv6.conf.all.optimistic_dad = 0
net.ipv6.conf.all.proxy_ndp = 0
net.ipv6.conf.all.regen_max_retry = 3
net.ipv6.conf.all.router_probe_interval = 60
net.ipv6.conf.all.router_solicitation_delay = 1
net.ipv6.conf.all.router_solicitation_interval = 4
net.ipv6.conf.all.router_solicitations = 3
sysctl: reading key "net.ipv6.conf.all.stable_secret"
net.ipv6.conf.all.temp_prefered_lft = 86400
net.ipv6.conf.all.temp_valid_lft = 604800
net.ipv6.conf.all.use_optimistic = 0
net.ipv6.conf.all.use_tempaddr = 0
net.ipv6.conf.default.accept_dad = 1
net.ipv6.conf.default.accept_ra = 1
net.ipv6.conf.default.accept_ra_defrtr = 1
net.ipv6.conf.default.accept_ra_pinfo = 1
net.ipv6.conf.default.accept_ra_rt_info_max_plen = 0
net.ipv6.conf.default.accept_ra_rtr_pref = 1
net.ipv6.conf.default.accept_redirects = 1
net.ipv6.conf.default.accept_source_route = 0
net.ipv6.conf.default.autoconf = 1
net.ipv6.conf.default.dad_transmits = 1
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.default.enhanced_dad = 1
net.ipv6.conf.default.force_mld_version = 0
net.ipv6.conf.default.force_tllao = 0
net.ipv6.conf.default.forwarding = 0
net.ipv6.conf.default.hop_limit = 64
net.ipv6.conf.default.keep_addr_on_down = 0
net.ipv6.conf.default.max_addresses = 16
net.ipv6.conf.default.max_desync_factor = 600
net.ipv6.conf.default.mc_forwarding = 0
net.ipv6.conf.default.mldv1_unsolicited_report_interval = 10000
net.ipv6.conf.default.mldv2_unsolicited_report_interval = 1000
net.ipv6.conf.default.mtu = 1280
net.ipv6.conf.default.ndisc_notify = 0
net.ipv6.conf.default.optimistic_dad = 0
net.ipv6.conf.default.proxy_ndp = 0
net.ipv6.conf.default.regen_max_retry = 3
net.ipv6.conf.default.router_probe_interval = 60
net.ipv6.conf.default.router_solicitation_delay = 1
net.ipv6.conf.default.router_solicitation_interval = 4
net.ipv6.conf.default.router_solicitations = 3
sysctl: reading key "net.ipv6.conf.default.stable_secret"
net.ipv6.conf.default.temp_prefered_lft = 86400
net.ipv6.conf.default.temp_valid_lft = 604800
net.ipv6.conf.default.use_optimistic = 0
net.ipv6.conf.default.use_tempaddr = 0
net.ipv6.conf.docker0.accept_dad = 1
net.ipv6.conf.docker0.accept_ra = 1
net.ipv6.conf.docker0.accept_ra_defrtr = 1
net.ipv6.conf.docker0.accept_ra_pinfo = 1
net.ipv6.conf.docker0.accept_ra_rt_info_max_plen = 0
net.ipv6.conf.docker0.accept_ra_rtr_pref = 1
net.ipv6.conf.docker0.accept_redirects = 1
net.ipv6.conf.docker0.accept_source_route = 0
net.ipv6.conf.docker0.autoconf = 1
net.ipv6.conf.docker0.dad_transmits = 1
net.ipv6.conf.docker0.disable_ipv6 = 0
net.ipv6.conf.docker0.enhanced_dad = 1
net.ipv6.conf.docker0.force_mld_version = 0
net.ipv6.conf.docker0.force_tllao = 0
net.ipv6.conf.docker0.forwarding = 0
net.ipv6.conf.docker0.hop_limit = 64
net.ipv6.conf.docker0.keep_addr_on_down = 0
net.ipv6.conf.docker0.max_addresses = 16
net.ipv6.conf.docker0.max_desync_factor = 600
net.ipv6.conf.docker0.mc_forwarding = 0
net.ipv6.conf.docker0.mldv1_unsolicited_report_interval = 10000
net.ipv6.conf.docker0.mldv2_unsolicited_report_interval = 1000
net.ipv6.conf.docker0.mtu = 1500
net.ipv6.conf.docker0.ndisc_notify = 0
net.ipv6.conf.docker0.optimistic_dad = 0
net.ipv6.conf.docker0.proxy_ndp = 0
net.ipv6.conf.docker0.regen_max_retry = 3
net.ipv6.conf.docker0.router_probe_interval = 60
net.ipv6.conf.docker0.router_solicitation_delay = 1
net.ipv6.conf.docker0.router_solicitation_interval = 4
net.ipv6.conf.docker0.router_solicitations = 3
sysctl: reading key "net.ipv6.conf.docker0.stable_secret"
net.ipv6.conf.docker0.temp_prefered_lft = 86400
net.ipv6.conf.docker0.temp_valid_lft = 604800
net.ipv6.conf.docker0.use_optimistic = 0
net.ipv6.conf.docker0.use_tempaddr = 0
net.ipv6.conf.eth0.accept_dad = 1
net.ipv6.conf.eth0.accept_ra = 1
net.ipv6.conf.eth0.accept_ra_defrtr = 1
net.ipv6.conf.eth0.accept_ra_pinfo = 1
net.ipv6.conf.eth0.accept_ra_rt_info_max_plen = 0
net.ipv6.conf.eth0.accept_ra_rtr_pref = 1
net.ipv6.conf.eth0.accept_redirects = 1
net.ipv6.conf.eth0.accept_source_route = 0
net.ipv6.conf.eth0.autoconf = 1
net.ipv6.conf.eth0.dad_transmits = 1
net.ipv6.conf.eth0.disable_ipv6 = 0
net.ipv6.conf.eth0.enhanced_dad = 1
net.ipv6.conf.eth0.force_mld_version = 0
net.ipv6.conf.eth0.force_tllao = 0
net.ipv6.conf.eth0.forwarding = 0
net.ipv6.conf.eth0.hop_limit = 64
net.ipv6.conf.eth0.keep_addr_on_down = 0
net.ipv6.conf.eth0.max_addresses = 16
net.ipv6.conf.eth0.max_desync_factor = 600
net.ipv6.conf.eth0.mc_forwarding = 0
net.ipv6.conf.eth0.mldv1_unsolicited_report_interval = 10000
net.ipv6.conf.eth0.mldv2_unsolicited_report_interval = 1000
net.ipv6.conf.eth0.mtu = 1500
net.ipv6.conf.eth0.ndisc_notify = 0
net.ipv6.conf.eth0.optimistic_dad = 0
net.ipv6.conf.eth0.proxy_ndp = 0
net.ipv6.conf.eth0.regen_max_retry = 3
net.ipv6.conf.eth0.router_probe_interval = 60
net.ipv6.conf.eth0.router_solicitation_delay = 1
net.ipv6.conf.eth0.router_solicitation_interval = 4
net.ipv6.conf.eth0.router_solicitations = 3
sysctl: reading key "net.ipv6.conf.eth0.stable_secret"
net.ipv6.conf.eth0.temp_prefered_lft = 86400
net.ipv6.conf.eth0.temp_valid_lft = 604800
net.ipv6.conf.eth0.use_optimistic = 0
net.ipv6.conf.eth0.use_tempaddr = 0
net.ipv6.conf.lo.accept_dad = -1
net.ipv6.conf.lo.accept_ra = 1
net.ipv6.conf.lo.accept_ra_defrtr = 1
net.ipv6.conf.lo.accept_ra_pinfo = 1
net.ipv6.conf.lo.accept_ra_rt_info_max_plen = 0
net.ipv6.conf.lo.accept_ra_rtr_pref = 1
net.ipv6.conf.lo.accept_redirects = 1
net.ipv6.conf.lo.accept_source_route = 0
net.ipv6.conf.lo.autoconf = 1
net.ipv6.conf.lo.dad_transmits = 1
net.ipv6.conf.lo.disable_ipv6 = 0
net.ipv6.conf.lo.enhanced_dad = 1
net.ipv6.conf.lo.force_mld_version = 0
net.ipv6.conf.lo.force_tllao = 0
net.ipv6.conf.lo.forwarding = 0
net.ipv6.conf.lo.hop_limit = 64
net.ipv6.conf.lo.keep_addr_on_down = 0
net.ipv6.conf.lo.max_addresses = 16
net.ipv6.conf.lo.max_desync_factor = 600
net.ipv6.conf.lo.mc_forwarding = 0
net.ipv6.conf.lo.mldv1_unsolicited_report_interval = 10000
net.ipv6.conf.lo.mldv2_unsolicited_report_interval = 1000
net.ipv6.conf.lo.mtu = 65536
net.ipv6.conf.lo.ndisc_notify = 0
net.ipv6.conf.lo.optimistic_dad = 0
net.ipv6.conf.lo.proxy_ndp = 0
net.ipv6.conf.lo.regen_max_retry = 3
net.ipv6.conf.lo.router_probe_interval = 60
net.ipv6.conf.lo.router_solicitation_delay = 1
net.ipv6.conf.lo.router_solicitation_interval = 4
net.ipv6.conf.lo.router_solicitations = 3
sysctl: reading key "net.ipv6.conf.lo.stable_secret"
net.ipv6.conf.lo.temp_prefered_lft = 86400
net.ipv6.conf.lo.temp_valid_lft = 604800
net.ipv6.conf.lo.use_optimistic = 0
net.ipv6.conf.lo.use_tempaddr = -1
net.ipv6.conf.tun0.accept_dad = -1
net.ipv6.conf.tun0.accept_ra = 1
net.ipv6.conf.tun0.accept_ra_defrtr = 1
net.ipv6.conf.tun0.accept_ra_pinfo = 1
net.ipv6.conf.tun0.accept_ra_rt_info_max_plen = 0
net.ipv6.conf.tun0.accept_ra_rtr_pref = 1
net.ipv6.conf.tun0.accept_redirects = 1
net.ipv6.conf.tun0.accept_source_route = 0
net.ipv6.conf.tun0.autoconf = 1
net.ipv6.conf.tun0.dad_transmits = 1
net.ipv6.conf.tun0.disable_ipv6 = 0
net.ipv6.conf.tun0.enhanced_dad = 1
net.ipv6.conf.tun0.force_mld_version = 0
net.ipv6.conf.tun0.force_tllao = 0
net.ipv6.conf.tun0.forwarding = 0
net.ipv6.conf.tun0.hop_limit = 64
net.ipv6.conf.tun0.keep_addr_on_down = 0
net.ipv6.conf.tun0.max_addresses = 16
net.ipv6.conf.tun0.max_desync_factor = 600
net.ipv6.conf.tun0.mc_forwarding = 0
net.ipv6.conf.tun0.mldv1_unsolicited_report_interval = 10000
net.ipv6.conf.tun0.mldv2_unsolicited_report_interval = 1000
net.ipv6.conf.tun0.mtu = 1500
net.ipv6.conf.tun0.ndisc_notify = 0
net.ipv6.conf.tun0.optimistic_dad = 0
net.ipv6.conf.tun0.proxy_ndp = 0
net.ipv6.conf.tun0.regen_max_retry = 3
net.ipv6.conf.tun0.router_probe_interval = 60
net.ipv6.conf.tun0.router_solicitation_delay = 1
net.ipv6.conf.tun0.router_solicitation_interval = 4
net.ipv6.conf.tun0.router_solicitations = 3
net.ipv6.conf.tun0.stable_secret = 512f:e222:4299:804e:5351:af65:7394:ce23
net.ipv6.conf.tun0.temp_prefered_lft = 86400
net.ipv6.conf.tun0.temp_valid_lft = 604800
net.ipv6.conf.tun0.use_optimistic = 0
net.ipv6.conf.tun0.use_tempaddr = -1
net.ipv6.conf.vethbd49170.accept_dad = 1
net.ipv6.conf.vethbd49170.accept_ra = 1
net.ipv6.conf.vethbd49170.accept_ra_defrtr = 1
net.ipv6.conf.vethbd49170.accept_ra_pinfo = 1
net.ipv6.conf.vethbd49170.accept_ra_rt_info_max_plen = 0
net.ipv6.conf.vethbd49170.accept_ra_rtr_pref = 1
net.ipv6.conf.vethbd49170.accept_redirects = 1
net.ipv6.conf.vethbd49170.accept_source_route = 0
net.ipv6.conf.vethbd49170.autoconf = 1
net.ipv6.conf.vethbd49170.dad_transmits = 1
net.ipv6.conf.vethbd49170.disable_ipv6 = 0
net.ipv6.conf.vethbd49170.enhanced_dad = 1
net.ipv6.conf.vethbd49170.force_mld_version = 0
net.ipv6.conf.vethbd49170.force_tllao = 0
net.ipv6.conf.vethbd49170.forwarding = 0
net.ipv6.conf.vethbd49170.hop_limit = 64
net.ipv6.conf.vethbd49170.keep_addr_on_down = 0
net.ipv6.conf.vethbd49170.max_addresses = 16
net.ipv6.conf.vethbd49170.max_desync_factor = 600
net.ipv6.conf.vethbd49170.mc_forwarding = 0
net.ipv6.conf.vethbd49170.mldv1_unsolicited_report_interval = 10000
net.ipv6.conf.vethbd49170.mldv2_unsolicited_report_interval = 1000
net.ipv6.conf.vethbd49170.mtu = 1500
net.ipv6.conf.vethbd49170.ndisc_notify = 0
net.ipv6.conf.vethbd49170.optimistic_dad = 0
net.ipv6.conf.vethbd49170.proxy_ndp = 0
net.ipv6.conf.vethbd49170.regen_max_retry = 3
net.ipv6.conf.vethbd49170.router_probe_interval = 60
net.ipv6.conf.vethbd49170.router_solicitation_delay = 1
net.ipv6.conf.vethbd49170.router_solicitation_interval = 4
net.ipv6.conf.vethbd49170.router_solicitations = 3
sysctl: reading key "net.ipv6.conf.vethbd49170.stable_secret"
net.ipv6.conf.vethbd49170.temp_prefered_lft = 86400
net.ipv6.conf.vethbd49170.temp_valid_lft = 604800
net.ipv6.conf.vethbd49170.use_optimistic = 0
net.ipv6.conf.vethbd49170.use_tempaddr = 0
net.ipv6.conf.vethd221ead.accept_dad = 1
net.ipv6.conf.vethd221ead.accept_ra = 1
net.ipv6.conf.vethd221ead.accept_ra_defrtr = 1
net.ipv6.conf.vethd221ead.accept_ra_pinfo = 1
net.ipv6.conf.vethd221ead.accept_ra_rt_info_max_plen = 0
net.ipv6.conf.vethd221ead.accept_ra_rtr_pref = 1
net.ipv6.conf.vethd221ead.accept_redirects = 1
net.ipv6.conf.vethd221ead.accept_source_route = 0
net.ipv6.conf.vethd221ead.autoconf = 1
net.ipv6.conf.vethd221ead.dad_transmits = 1
net.ipv6.conf.vethd221ead.disable_ipv6 = 0
net.ipv6.conf.vethd221ead.enhanced_dad = 1
net.ipv6.conf.vethd221ead.force_mld_version = 0
net.ipv6.conf.vethd221ead.force_tllao = 0
net.ipv6.conf.vethd221ead.forwarding = 0
net.ipv6.conf.vethd221ead.hop_limit = 64
net.ipv6.conf.vethd221ead.keep_addr_on_down = 0
net.ipv6.conf.vethd221ead.max_addresses = 16
net.ipv6.conf.vethd221ead.max_desync_factor = 600
net.ipv6.conf.vethd221ead.mc_forwarding = 0
net.ipv6.conf.vethd221ead.mldv1_unsolicited_report_interval = 10000
net.ipv6.conf.vethd221ead.mldv2_unsolicited_report_interval = 1000
net.ipv6.conf.vethd221ead.mtu = 1500
net.ipv6.conf.vethd221ead.ndisc_notify = 0
net.ipv6.conf.vethd221ead.optimistic_dad = 0
net.ipv6.conf.vethd221ead.proxy_ndp = 0
net.ipv6.conf.vethd221ead.regen_max_retry = 3
net.ipv6.conf.vethd221ead.router_probe_interval = 60
net.ipv6.conf.vethd221ead.router_solicitation_delay = 1
net.ipv6.conf.vethd221ead.router_solicitation_interval = 4
net.ipv6.conf.vethd221ead.router_solicitations = 3
sysctl: reading key "net.ipv6.conf.vethd221ead.stable_secret"
net.ipv6.conf.vethd221ead.temp_prefered_lft = 86400
net.ipv6.conf.vethd221ead.temp_valid_lft = 604800
net.ipv6.conf.vethd221ead.use_optimistic = 0
net.ipv6.conf.vethd221ead.use_tempaddr = 0
net.ipv6.fwmark_reflect = 0
net.ipv6.icmp.ratelimit = 1000
net.ipv6.idgen_delay = 1
net.ipv6.idgen_retries = 3
net.ipv6.ip6frag_high_thresh = 4194304
net.ipv6.ip6frag_low_thresh = 3145728
net.ipv6.ip6frag_secret_interval = 600
net.ipv6.ip6frag_time = 60
net.ipv6.ip_nonlocal_bind = 0
net.ipv6.mld_max_msf = 64
net.ipv6.mld_qrv = 2
net.ipv6.neigh.default.anycast_delay = 100
net.ipv6.neigh.default.app_solicit = 0
net.ipv6.neigh.default.base_reachable_time_ms = 30000
net.ipv6.neigh.default.delay_first_probe_time = 5
net.ipv6.neigh.default.gc_interval = 30
net.ipv6.neigh.default.gc_stale_time = 60
net.ipv6.neigh.default.gc_thresh1 = 128
net.ipv6.neigh.default.gc_thresh2 = 512
net.ipv6.neigh.default.gc_thresh3 = 1024
net.ipv6.neigh.default.locktime = 0
net.ipv6.neigh.default.mcast_solicit = 3
net.ipv6.neigh.default.proxy_delay = 80
net.ipv6.neigh.default.proxy_qlen = 64
net.ipv6.neigh.default.retrans_time_ms = 1000
net.ipv6.neigh.default.ucast_solicit = 3
net.ipv6.neigh.default.unres_qlen = 31
net.ipv6.neigh.default.unres_qlen_bytes = 65536
net.ipv6.neigh.docker0.anycast_delay = 100
net.ipv6.neigh.docker0.app_solicit = 0
net.ipv6.neigh.docker0.base_reachable_time_ms = 30000
net.ipv6.neigh.docker0.delay_first_probe_time = 5
net.ipv6.neigh.docker0.gc_stale_time = 60
net.ipv6.neigh.docker0.locktime = 0
net.ipv6.neigh.docker0.mcast_solicit = 3
net.ipv6.neigh.docker0.proxy_delay = 80
net.ipv6.neigh.docker0.proxy_qlen = 64
net.ipv6.neigh.docker0.retrans_time_ms = 1000
net.ipv6.neigh.docker0.ucast_solicit = 3
net.ipv6.neigh.docker0.unres_qlen = 31
net.ipv6.neigh.docker0.unres_qlen_bytes = 65536
net.ipv6.neigh.eth0.anycast_delay = 100
net.ipv6.neigh.eth0.app_solicit = 0
net.ipv6.neigh.eth0.base_reachable_time_ms = 30000
net.ipv6.neigh.eth0.delay_first_probe_time = 5
net.ipv6.neigh.eth0.gc_stale_time = 60
net.ipv6.neigh.eth0.locktime = 0
net.ipv6.neigh.eth0.mcast_solicit = 3
net.ipv6.neigh.eth0.proxy_delay = 80
net.ipv6.neigh.eth0.proxy_qlen = 64
net.ipv6.neigh.eth0.retrans_time_ms = 1000
net.ipv6.neigh.eth0.ucast_solicit = 3
net.ipv6.neigh.eth0.unres_qlen = 31
net.ipv6.neigh.eth0.unres_qlen_bytes = 65536
net.ipv6.neigh.lo.anycast_delay = 100
net.ipv6.neigh.lo.app_solicit = 0
net.ipv6.neigh.lo.base_reachable_time_ms = 30000
net.ipv6.neigh.lo.delay_first_probe_time = 5
net.ipv6.neigh.lo.gc_stale_time = 60
net.ipv6.neigh.lo.locktime = 0
net.ipv6.neigh.lo.mcast_solicit = 3
net.ipv6.neigh.lo.proxy_delay = 80
net.ipv6.neigh.lo.proxy_qlen = 64
net.ipv6.neigh.lo.retrans_time_ms = 1000
net.ipv6.neigh.lo.ucast_solicit = 3
net.ipv6.neigh.lo.unres_qlen = 31
net.ipv6.neigh.lo.unres_qlen_bytes = 65536
net.ipv6.neigh.tun0.anycast_delay = 100
net.ipv6.neigh.tun0.app_solicit = 0
net.ipv6.neigh.tun0.base_reachable_time_ms = 30000
net.ipv6.neigh.tun0.delay_first_probe_time = 5
net.ipv6.neigh.tun0.gc_stale_time = 60
net.ipv6.neigh.tun0.locktime = 0
net.ipv6.neigh.tun0.mcast_solicit = 3
net.ipv6.neigh.tun0.proxy_delay = 80
net.ipv6.neigh.tun0.proxy_qlen = 64
net.ipv6.neigh.tun0.retrans_time_ms = 1000
net.ipv6.neigh.tun0.ucast_solicit = 3
net.ipv6.neigh.tun0.unres_qlen = 31
net.ipv6.neigh.tun0.unres_qlen_bytes = 65536
net.ipv6.neigh.vethbd49170.anycast_delay = 100
net.ipv6.neigh.vethbd49170.app_solicit = 0
net.ipv6.neigh.vethbd49170.base_reachable_time_ms = 30000
net.ipv6.neigh.vethbd49170.delay_first_probe_time = 5
net.ipv6.neigh.vethbd49170.gc_stale_time = 60
net.ipv6.neigh.vethbd49170.locktime = 0
net.ipv6.neigh.vethbd49170.mcast_solicit = 3
net.ipv6.neigh.vethbd49170.proxy_delay = 80
net.ipv6.neigh.vethbd49170.proxy_qlen = 64
net.ipv6.neigh.vethbd49170.retrans_time_ms = 1000
net.ipv6.neigh.vethbd49170.ucast_solicit = 3
net.ipv6.neigh.vethbd49170.unres_qlen = 31
net.ipv6.neigh.vethbd49170.unres_qlen_bytes = 65536
net.ipv6.neigh.vethd221ead.anycast_delay = 100
net.ipv6.neigh.vethd221ead.app_solicit = 0
net.ipv6.neigh.vethd221ead.base_reachable_time_ms = 30000
net.ipv6.neigh.vethd221ead.delay_first_probe_time = 5
net.ipv6.neigh.vethd221ead.gc_stale_time = 60
net.ipv6.neigh.vethd221ead.locktime = 0
net.ipv6.neigh.vethd221ead.mcast_solicit = 3
net.ipv6.neigh.vethd221ead.proxy_delay = 80
net.ipv6.neigh.vethd221ead.proxy_qlen = 64
net.ipv6.neigh.vethd221ead.retrans_time_ms = 1000
net.ipv6.neigh.vethd221ead.ucast_solicit = 3
net.ipv6.neigh.vethd221ead.unres_qlen = 31
net.ipv6.neigh.vethd221ead.unres_qlen_bytes = 65536
net.ipv6.route.gc_elasticity = 9

net.ipv6.route.gc_interval = 30
net.ipv6.route.gc_min_interval = 0
net.ipv6.route.gc_min_interval_ms = 500
net.ipv6.route.gc_thresh = 1024
net.ipv6.route.gc_timeout = 60
net.ipv6.route.max_size = 16384
net.ipv6.route.min_adv_mss = 1220
net.ipv6.route.mtu_expires = 600
net.ipv6.xfrm6_gc_thresh = 32768
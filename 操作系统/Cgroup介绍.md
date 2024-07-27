cgroup（Control Group）是 Linux 内核中的一项特性，用于限制、管理和监控进程组的资源使用。它提供了一种机制，可以将一组进程视为一个单独的实体，并对其分配的资源进行限制和控制。以下是 cgroup 的详细介绍和逻辑架构：

### 1. cgroup 的组成

cgroup 由以下几个组件组成：

- **Hierarchy（层次结构）**：cgroup 可以组织成多级的层次结构，每一级都包含多个 cgroup，形成一个树状结构。这种层次结构允许管理员根据需要对不同的进程组进行分组和管理。

- **Controller（控制器）**：每个 cgroup 都与一个或多个控制器相关联，控制器负责监控和限制 cgroup 的资源使用。常见的控制器包括 CPU、内存、磁盘IO、网络带宽等。

- **Tasks（任务）**：每个 cgroup 包含一个或多个任务，即进程或线程。这些任务受 cgroup 中定义的资源限制和控制的约束。

### 2. cgroup 的逻辑架构

cgroup 的逻辑架构如下：

- **Hierarchy Root（层次结构根节点）**：cgroup 的层次结构根节点是整个 cgroup 层次结构的起点，通常位于文件系统的某个特定位置（如 /sys/fs/cgroup）。根节点包含了整个层次结构的信息，以及各个 cgroup 和控制器的配置。

- **cgroup 文件系统（cgroupfs）**：cgroup 通过虚拟文件系统（cgroupfs）暴露给用户空间。管理员可以通过在 cgroupfs 中的文件和目录进行配置，以控制 cgroup 的行为。

- **Control Group（cgroup）**：每个 cgroup 表示一个资源控制组，它包含了一组相关的进程或线程，以及与之关联的控制器。cgroup 可以设置资源限制、优先级、监控参数等。

- **Controller Interface（控制器接口）**：每个控制器都提供了一组接口，用于管理 cgroup 中的资源。管理员可以通过控制器接口设置和调整资源限制、监控参数等。

- **Tasks Interface（任务接口）**：cgroup 允许管理员将任务（进程或线程）添加到或移出 cgroup 中。这通过在 cgroup 的 tasks 文件中添加或删除任务的 PID 来实现。

### 3. cgroup 的工作原理

cgroup 的工作原理如下：

- **创建 cgroup**：管理员可以通过 cgroup 文件系统创建新的 cgroup，并为其配置所需的资源限制和控制器。

- **配置控制器**：管理员可以选择性地为 cgroup 关联一个或多个控制器，以控制其资源使用情况。不同的控制器提供不同类型的资源管理功能，如 CPU、内存、磁盘IO、网络带宽等。

- **添加任务**：管理员可以通过向 cgroup 的 tasks 文件中写入任务的 PID，将任务添加到 cgroup 中。添加的任务将受到 cgroup 中定义的资源限制和控制的约束。

- **资源限制和控制**：一旦任务被添加到 cgroup 中，控制器将开始监视和控制该 cgroup 中任务的资源使用情况。根据配置，控制器可以对任务的 CPU 使用率、内存占用、磁盘IO等进行限制和控制。

- **监控和管理**：管理员可以通过 cgroup 文件系统和控制器接口实时监控和管理 cgroup 中任务的资源使用情况。根据需要，管理员可以动态地调整资源限制和控制参数，以优化系统性能和资源利用率。

总的来说，cgroup 提供了一种灵活、可靠的资源管理机制，使得管理员能够对系统中的进程组进行细粒度的资源限制、控制和监控，从而提高系统的稳定性、性能和安全性。

下面是一个 cgroup 中各个资源的限制配置示例：

1. **CPU 使用限制**：
```shell
sudo cgset -r cpu.cfs_quota_us=20000 /mycontainer
```
这将限制容器使用的 CPU 时间为 20 毫秒每秒。

2. **内存使用限制**：
```shell
sudo cgset -r memory.limit_in_bytes=1G /mycontainer
```
这将限制容器最大可用内存为 1GB。

3. **磁盘 IO 读写带宽限制**：
```shell
sudo cgset -r blkio.throttle.read_bps_device="8:0 1048576" /mycontainer
```
这将限制容器对块设备 8:0（通常是 /dev/sda）的读取速度为每秒 1MB。

4. **网络带宽限制**：
```shell
sudo cgset -r net_cls.classid=0x00110011 /mycontainer
sudo tc qdisc add dev eth0 root handle 1: htb default 11
sudo tc class add dev eth0 parent 1: classid 1:11 htb rate 100Mbps ceil 100Mbps
sudo tc filter add dev eth0 parent 1: protocol ip prio 1 handle 0x11 u32 match ip dst 192.168.1.10 flowid 1:11
```
这将限制容器的网络带宽为 100Mbps。

这些示例展示了如何使用 cgset 命令为 cgroup 中的任务设置各种资源限制。这些限制可以根据实际需求进行调整和组合，以满足不同场景下的资源管理需求。
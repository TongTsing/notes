# 浅谈Linux Cgroups机制



## **概述**

- [1. Cgroups简介](https://zhuanlan.zhihu.com/p/81668069/edit#1-cgroup简介)
  - [1.1 功能和定位](https://zhuanlan.zhihu.com/p/81668069/edit#11-功能和定位)
  - [1.2 相关概念介绍](https://zhuanlan.zhihu.com/p/81668069/edit#12-相关概念介绍)
  - [1.3 子系统](https://zhuanlan.zhihu.com/p/81668069/edit#13-子系统)
  - [1.4 cgroups文件系统](https://zhuanlan.zhihu.com/p/81668069/edit#14-cgroups文件系统)
- [2. cgroups子系统](https://zhuanlan.zhihu.com/p/81668069/edit#2-cgroups子系统)
  - [2.1 cpu子系统](https://zhuanlan.zhihu.com/p/81668069/edit#21-cpu子系统)
  - [2.2 cpuacct子系统](https://zhuanlan.zhihu.com/p/81668069/edit#22-cpuacct子系统)
  - [2.3 cpuset子系统](https://zhuanlan.zhihu.com/p/81668069/edit#23-cpuset子系统)
  - [2.4 memory子系统](https://zhuanlan.zhihu.com/p/81668069/edit#24-memory子系统)
  - [2.5 blkio子系统 - block io](https://zhuanlan.zhihu.com/p/81668069/edit#25-blkio子系统-block-io)
- [3. cgroups的安装和使用](https://zhuanlan.zhihu.com/p/81668069/edit#3-cgroups的安装和使用)
  - [3.1 cgroup的安装](https://zhuanlan.zhihu.com/p/81668069/edit#31-cgroup的安装)
  - [3.2 将进程加入到资源限制组](https://zhuanlan.zhihu.com/p/81668069/edit#32-将进程加入到资源限制组)
- [4. 总结](https://zhuanlan.zhihu.com/p/81668069/edit#4-总结)
- [5. 参考](https://zhuanlan.zhihu.com/p/81668069/edit#5-参考)

## 1. Cgroups简介

### 1.1 功能和定位

Cgroups全称Control Groups，是Linux内核提供的物理资源隔离机制，通过这种机制，可以实现对Linux进程或者进程组的资源限制、隔离和统计功能。

比如可以通过cgroup限制特定进程的资源使用，比如使用特定数目的cpu核数和特定大小的内存，如果资源超限的情况下，会被暂停或者杀掉。

Cgroup是于2.6内核由Google公司主导引入的，它是Linux内核实现资源虚拟化的技术基石，LXC(Linux Containers)和docker容器所用到的资源隔离技术，正是Cgroup。

### 1.2 相关概念介绍

- 任务(task): 在cgroup中，任务就是一个进程。
- 控制组(control group): cgroup的资源控制是以控制组的方式实现，控制组指明了资源的配额限制。进程可以加入到某个控制组，也可以迁移到另一个控制组。
- 层级(hierarchy): 控制组有层级关系，类似树的结构，子节点的控制组继承父控制组的属性(资源配额、限制等)。
- 子系统(subsystem): 一个子系统其实就是一种资源的控制器，比如memory子系统可以控制进程内存的使用。子系统需要加入到某个层级，然后该层级的所有控制组，均受到这个子系统的控制。

概念间的关系：

- 子系统可以依附多个层级，当且仅当这些层级没有其他的子系统，比如两个层级同时只有一个cpu子系统，是可以的。
- 一个层级可以附加多个子系统。
- 一个任务可以是多个cgroup的成员，但这些cgroup必须位于不同的层级。
- 子进程自动成为父进程cgroup的成员，可按需求将子进程移到不同的cgroup中。

cgroup关系图如下：

![img](https://pic3.zhimg.com/80/v2-206ab019ab2d93cd771b545914c0e6ea_1440w.webp)

两个任务组成了一个 Task Group，并使用了 CPU 和 Memory 两个子系统的 cgroup，用于控制 CPU 和 MEM 的资源隔离。

### 1.3 子系统

- cpu: 限制进程的 cpu 使用率。
- cpuacct 子系统，可以统计 cgroups 中的进程的 cpu 使用报告。
- cpuset: 为cgroups中的进程分配单独的cpu节点或者内存节点。
- memory: 限制进程的memory使用量。
- blkio: 限制进程的块设备io。
- devices: 控制进程能够访问某些设备。
- net_cls: 标记cgroups中进程的网络数据包，然后可以使用tc模块（traffic control）对数据包进行控制。
- net_prio: 限制进程网络流量的优先级。
- huge_tlb: 限制HugeTLB的使用。
- freezer:挂起或者恢复cgroups中的进程。
- ns: 控制cgroups中的进程使用不同的namespace。

### 1.4 cgroups文件系统

Linux通过文件的方式，将cgroups的功能和配置暴露给用户，这得益于Linux的虚拟文件系统（VFS）。VFS将具体文件系统的细节隐藏起来，给用户态提供一个统一的文件系统API接口，cgroups和VFS之间的链接部分，称之为cgroups文件系统。

比如挂在 cpu、cpuset、memory 三个子系统到 /cgroups/cpu_mem 目录下

```bash
mount -t cgroup -o cpu,cpuset,memory cpu_mem /cgroups/cpu_mem
```

关于虚拟文件系统机制，见[浅谈Linux虚拟文件系统机制](https://zhuanlan.zhihu.com/p/69289429)

## 2. cgroups子系统

这里简单介绍几个常见子系统的概念和用法，包括cpu、cpuacct、cpuset、memory、blkio。

### 2.1 cpu子系统

cpu子系统限制对CPU的访问，每个参数独立存在于cgroups虚拟文件系统的伪文件中，参数解释如下：

- **cpu.shares**: cgroup对时间的分配。比如cgroup A设置的是1，cgroup B设置的是2，那么B中的任务获取cpu的时间，是A中任务的2倍。
- **cpu.cfs_period_us**: 完全公平调度器的调整时间配额的周期。
- **cpu.cfs_quota_us**: 完全公平调度器的周期当中可以占用的时间。
- **cpu.stat** 统计值
  - nr_periods 进入周期的次数
  - nr_throttled 运行时间被调整的次数
  - throttled_time 用于调整的时间

### 2.2 cpuacct子系统

子系统生成cgroup任务所使用的CPU资源报告，不做资源限制功能。

- **cpuacct.usage**: 该cgroup中所有任务总共使用的CPU时间（ns纳秒）
- **cpuacct.stat**: 该cgroup中所有任务总共使用的CPU时间，区分user和system时间。
- **cpuacct.usage_percpu**: 该cgroup中所有任务使用各个CPU核数的时间。

通过cpuacct如何计算CPU利用率呢？可以通过`cpuacct.usage`来计算整体的CPU利用率，计算如下：

```bash
# 1. 获取当前时间（纳秒）
tstart=$(date +%s%N)
# 2. 获取cpuacct.usage
cstart=$(cat /xxx/cpuacct.usage)
# 3. 间隔5s统计一下
sleep 5
# 4. 再次采点
tstop=$(date +%s%N)
cstop=$(cat /xxx/cpuacct.usage)
# 5. 计算利用率
($cstop - $cstart) / ($tstop - $tstart) * 100
```

### 2.3 cpuset子系统

适用于分配独立的CPU节点和Mem节点，比如将进程绑定在指定的CPU或者内存节点上运行，各参数解释如下：

- cpuset.cpus: 可以使用的cpu节点
- cpuset.mems: 可以使用的mem节点
- cpuset.memory_migrate: 内存节点改变是否要迁移？
- cpuset.cpu_exclusive: 此cgroup里的任务是否独享cpu？
- cpuset.mem_exclusive： 此cgroup里的任务是否独享mem节点？
- cpuset.mem_hardwall: 限制内核内存分配的节点（mems是用户态的分配）
- cpuset.memory_pressure: 计算换页的压力。
- cpuset.memory_spread_page: 将page cache分配到各个节点中，而不是当前内存节点。
- cpuset.memory_spread_slab: 将slab对象(inode和dentry)分散到节点中。
- cpuset.sched_load_balance: 打开cpu set中的cpu的负载均衡。
- cpuset.sched_relax_domain_level: the searching range when migrating tasks
- cpuset.memory_pressure_enabled: 是否需要计算 memory_pressure?

### 2.4 memory子系统

memory子系统主要涉及内存一些的限制和操作，主要有以下参数：

- memory.usage_in_bytes # 当前内存中的使用量
- memory.memsw.usage_in_bytes # 当前内存和交换空间中的使用量
- memory.limit_in_bytes # 设置or查看内存使用量
- memory.memsw.limit_in_bytes # 设置or查看 内存加交换空间使用量
- memory.failcnt # 查看内存使用量被限制的次数
- memory.memsw.failcnt # - 查看内存和交换空间使用量被限制的次数
- memory.max_usage_in_bytes # 查看内存最大使用量
- memory.memsw.max_usage_in_bytes # 查看最大内存和交换空间使用量
- memory.soft_limit_in_bytes # 设置or查看内存的soft limit
- memory.stat # 统计信息
- memory.use_hierarchy # 设置or查看层级统计的功能
- memory.force_empty # 触发强制page回收
- memory.pressure_level # 设置内存压力通知
- memory.swappiness # 设置or查看vmscan swappiness 参数
- memory.move_charge_at_immigrate # 设置or查看 controls of moving charges?
- memory.oom_control # 设置or查看内存超限控制信息(OOM killer)
- memory.numa_stat # 每个numa节点的内存使用数量
- memory.kmem.limit_in_bytes # 设置or查看 内核内存限制的硬限
- memory.kmem.usage_in_bytes # 读取当前内核内存的分配
- memory.kmem.failcnt # 读取当前内核内存分配受限的次数
- memory.kmem.max_usage_in_bytes # 读取最大内核内存使用量
- memory.kmem.tcp.limit_in_bytes # 设置tcp 缓存内存的hard limit
- memory.kmem.tcp.usage_in_bytes # 读取tcp 缓存内存的使用量
- memory.kmem.tcp.failcnt # tcp 缓存内存分配的受限次数
- memory.kmem.tcp.max_usage_in_bytes # tcp 缓存内存的最大使用量

### 2.5 blkio子系统 - block io

主要用于控制设备IO的访问。有两种限制方式：权重和上限，权重是给不同的应用一个权重值，按百分比使用IO资源，上限是控制应用读写速率的最大值。

按权重分配IO资源：

- blkio.weight：填写 100-1000 的一个整数值，作为相对权重比率，作为通用的设备分配比。
- blkio.weight_device： 针对特定设备的权重比，写入格式为 `device_types:node_numbers weight`，空格前的参数段指定设备，weight参数与blkio.weight相同并覆盖原有的通用分配比。

按上限限制读写速度：

- blkio.throttle.read_bps_device：按每秒读取块设备的数据量设定上限，格式device_types:node_numbers bytes_per_second。
- blkio.throttle.write_bps_device：按每秒写入块设备的数据量设定上限，格式device_types:node_numbers bytes_per_second。
- blkio.throttle.read_iops_device：按每秒读操作次数设定上限，格式device_types:node_numbers operations_per_second。
- blkio.throttle.write_iops_device：按每秒写操作次数设定上限，格式device_types:node_numbers operations_per_second

针对特定操作 (read, write, sync, 或 async) 设定读写速度上限

- blkio.throttle.io_serviced：针对特定操作按每秒操作次数设定上限，格式device_types:node_numbers operation operations_per_second
- blkio.throttle.io_service_bytes：针对特定操作按每秒数据量设定上限，格式device_types:node_numbers operation bytes_per_second

## 3. cgroups的安装和使用

测试环境为 ubuntu 18.10

### 3.1 cgroups的安装

1. 安装 cgroups

```bash
sudo apt install cgroup-bin
```

安装完成后，系统会出现该目录`/sys/fs/cgroup`。

1. 创建cpu资源控制组，限制cpu使用率最大为50%

```bash
$ cd /sys/fs/cgroup/cpu
$ sudo mkdir test_cpu
$ sudo echo '10000' > test_cpu/cpu.cfs_period_us
$ sudo echo '5000' > test_cpu/cpu.cfs_quota_us
```

1. 创建mem资源控制组，限制内存最大使用为100MB

```text
$ cd /sys/fs/cgroup/memory
$ sudo mkdir test_mem
$ sudo echo '104857600' > test_mem/memory.limit_in_bytes
```

### 3.2 将进程加入到资源限制组

测试代码`test.cc`如下：

```cpp
#include <unistd.h>
#include <stdio.h>
#include <cstring>
#include <thread>

void test_cpu() {
    printf("thread: test_cpu start\n");
    int total = 0;
    while (1) {
        ++total;
    }
}

void test_mem() {
    printf("thread: test_mem start\n");
    int step = 20;
    int size = 10 * 1024 * 1024; // 10Mb
    for (int i = 0; i < step; ++i) {
        char* tmp = new char[size];
        memset(tmp, i, size);
        sleep(1);
    }
    printf("thread: test_mem done\n");
}

int main(int argc, char** argv) {
    std::thread t1(test_cpu);
    std::thread t2(test_mem);
    t1.join();
    t2.join();
    return 0;
}
```

**1. 编译该程序**

```bash
g++ -o test test.cc --std=c++11 -lpthread
```

**2. 观察限制之前的运行状态**

![img](https://pic4.zhimg.com/80/v2-0859e258d128bfa31354c43b47ab137f_1440w.webp)

**3. 测试cpu的限制**

```bash
cgexec -g cpu:test_cpu ./test
```

cpu使用率降低了一半。

![img](https://pic3.zhimg.com/80/v2-ab21e67c10eccb21700a47ebc4ed566e_1440w.webp)

除了使用 cgexec 限制进程外，还可以通过将进程号加入到 cgroup.procs 的方式，来达到限制目的。

## 4. 总结

本文简单介绍了Cgroups的概念和使用，通过Cgroups可以实现资源限制和隔离。在实际的生产环境中，Cgroups技术被大量应用在各种容器技术中，包括docker、rocket等。

这种资源限制和隔离技术的出现，使得模块间相互混部成为可能，大大提高了机器资源利用率，这也是云计算的关键技术之一。

## 5. 参考

- [CGroups 介绍、应用实例及原理描述](https://link.zhihu.com/?target=https%3A//www.ibm.com/developerworks/cn/linux/1506_cgroup/index.h)
- [Linux资源管理之cgroups简介](https://link.zhihu.com/?target=https%3A//tech.meituan.com/2015/03/31/cgroups.html)
- [Linux内核文档](https://link.zhihu.com/?target=https%3A//www.kernel.org/doc/Documentation)
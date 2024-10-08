# linux命令之top

# 1、top命令简介

top命令是动态查看进程变化，监控linux的系统状况；它是常用的性能分析工具，能够实时显示系统资源各个进程占用状况，类是windows的任务管理器。

## 1.1 语法



```ruby
[root@localhost ~]# top -h
  procps-ng version 3.3.10
Usage:
  top -hv | -bcHiOSs -d secs -n max -u|U user -p pid(s) -o field -w [cols]
```

- -h | -v: 显示帮助或者版本信息】
- -c: 命令行列显示程序名以及参数
- -d: 启动时设置刷新时间间隔
- -H： 设置线程模式
- -i: 只显示活跃进程
- -n: 显示指定数量的进程
- -p: 显示指定PID的进程
- -u: 显示指定用户的进程

## 1.2 视图参数含义



```cpp
[root@localhost ~]# top
top - 09:56:25 up 49 days, 15:19,  1 user,  load average: 0.01, 0.05, 0.05
Tasks: 147 total,   1 running, 146 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.8 sy,  0.0 ni, 99.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem : 32781220 total,  9375268 free, 10749464 used, 12656488 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 21365776 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                                                                                      
 7150 root      20   0  157724   2156   1480 R   6.2  0.0   0:00.01 top                                                                                                                                          
    1 root      20   0   43320   3604   2308 S   0.0  0.0   1:33.24 systemd
```

从上面的结果可以看出，top视图分为两部分：操作系统资源概况信息和进程信息。首先是分析资源概况中各个参数含义，再来分析进程信息中各个参数含义。

我们先利用`top`命令进入视图，再`f`键看下全称信息，再`q`退出回到top视图。



```tsx
Fields Management for window 1:Def, whose current sort field is %CPU
   Navigate with Up/Dn, Right selects for move then <Enter> or Left commits,
   'd' or <Space> toggles display, 's' sets sort.  Use 'q' or <Esc> to end!

* PID     = Process Id             TGID    = Thread Group Id     
* USER    = Effective User Name    ENVIRON = Environment vars    
* PR      = Priority               vMj     = Major Faults delta  
* NI      = Nice Value             vMn     = Minor Faults delta  
* VIRT    = Virtual Image (KiB)    USED    = Res+Swap Size (KiB) 
* RES     = Resident Size (KiB)    nsIPC   = IPC namespace Inode 
* SHR     = Shared Memory (KiB)    nsMNT   = MNT namespace Inode 
* S       = Process Status         nsNET   = NET namespace Inode 
* %CPU    = CPU Usage              nsPID   = PID namespace Inode 
* %MEM    = Memory Usage (RES)     nsUSER  = USER namespace Inode
* TIME+   = CPU Time, hundredths   nsUTS   = UTS namespace Inode 
* COMMAND = Command Name/Line   
  PPID    = Parent Process pid  
  UID     = Effective User Id   
  RUID    = Real User Id        
  RUSER   = Real User Name      
  SUID    = Saved User Id       
  SUSER   = Saved User Name     
  GID     = Group Id            
  GROUP   = Group Name          
  PGRP    = Process Group Id    
  TTY     = Controlling Tty     
  TPGID   = Tty Process Grp Id  
  SID     = Session Id          
  nTH     = Number of Threads   
  P       = Last Used Cpu (SMP) 
  TIME    = CPU Time            
  SWAP    = Swapped Size (KiB)  
  CODE    = Code Size (KiB)     
  DATA    = Data+Stack (KiB)    
  nMaj    = Major Page Faults   
  nMin    = Minor Page Faults   
  nDRT    = Dirty Pages Count   
  WCHAN   = Sleeping in Function
  Flags   = Task Flags <sched.h>
  CGROUPS = Control Groups      
  SUPGIDS = Supp Groups IDs     
  SUPGRPS = Supp Groups Names
```

**1、资源概况**

a、操作系统时间、登录用户、负载情况 - top



```css
top - 09:56:25 up 49 days, 15:19,  1 user,  load average: 0.01, 0.05, 0.05
```

- 09:56:25：当前系统时间。
- up 49 days, 15:19：操作系统从开机后运行的时间，运行天时分。
- users：当前系统三个用户登录在线。
- load average：1，5，15min的系统平均负载。

b、运行任务概况 - tasks



```undefined
Tasks: 147 total,   1 running, 146 sleeping,   0 stopped,   0 zombie
```

- total：系统当前运行进程数。
- running：当前运行的进程数。
- sleeping：睡眠中的进程数。

c、CPU概览： %Cpu(s) 表示CPU使用百分比，按照时间占用计算，单位s



```css
%Cpu(s):  0.0 us,  0.8 sy,  0.0 ni, 99.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
```

- us：用户空间占用cpu时间百分比，如果是多核，这个数值表示占用的平均百分比，可以按1进行多核统计和平均统计切换。
- sy：内核空间占用cpu时间百分比，后续同上。
- ni：用户空间改变过优先级的进程占用cpu时间百分比。
- id：空闲时间占用cpu的百分比。
- wa：等待输入输出占用cpu时间百分比。
- hi：硬中断占用cpu时间百分比。
- si：软中断占用cpu时间百分比。

**时间占用百分比=该种类型操作消耗cpu时间/top刷新间隔时间。top每3s刷新一次，用户空间进程在这3秒内试用了cpu时间1.5s，那么us为50%=1.5s/3s。**

d、内存概览（KB）



```cpp
KiB Mem : 32781220 total,  9375268 free, 10749464 used, 12656488 buff/cache
```

- total：内存总量。
- free：内存剩余总量。
- used：内存使用数量。
- buff/cached：用于缓冲的内存数量。

e、缓存区概况（KB）



```cpp
KiB Swap:        0 total,        0 free,        0 used. 21365776 avail Mem
```

- total：交换区总量。
- free：空闲交换区数量。
- used：使用交换区数量。

**2、进程概况**

进程概况的统计是从多个维度进行展示的，其中几个计较重要的参数如下：

- PID：进程ID，唯一标识。
- USER：进程所属用户。
- %CPU：自上一次top刷新该进程的cpu占用时间百分比。
- %MEM：进程消耗内存百分比。
- TIME+：自进程开始以来，消耗CPU时间，单位1/100s。

其它参数可以在上面`f`键视图的全称中了解。

## 1.3 top视图中交互命令

**1、全局**

- Enter/Space：刷新视图。
- h：帮助信息，查询各个交互命令的含义。
- O：是否展示进程区域中的0值，比如%CPU是0的将全部隐藏。
- A：在全屏模式和多窗口选择模式中切换。
- d：设置刷新时间间隔。
- E：切换内存和交换区单位。
- H：开启/关闭线程模式，以线程的方式展示。
- k：杀掉指定进程或线程。
- Z：改变颜色配置。
- q：退出。

**2、概要区域**

- 1：显示CPU平均状态/分开显示各个逻辑CPU状态。
- m：切换显示内存统计的数据。

**3、进程区域**

- x：切换高亮行的排序位置。
- z：切换颜色。
- b：块状标记高亮行。
- c：切换显示命令/程序名和参数。
- f：显示field管理。
- u：按照执行用户显示进程。
- i：显示所有进程或活跃的进程。
- n：设置显示的进程数。

## 1.4 top常用场景



```bash
top -H -p pid # 显示某个进程所有活跃的线程消耗情况。
```



作者：道无虚
链接：https://www.jianshu.com/p/8a6754f919c5
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
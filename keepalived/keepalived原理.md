# Keepalive详解

------

## 工作原理

Keepalived本质就是为ipvs服务的，它也不需要共享存储。IPVS其实就是一些规则，Keepalived主要的任务就是去调用ipvsadm命令，来生成规则，并自动实现将用户需要访问的地址转移到可用LVS节点实现。所以keepalive的高可用是属于具有很强针对性的高可用，它和corosync这种通用性HA方案不同。

Keepalived的主要目的就是它自身启动为一个服务，它工作在多个LVS主机节点上，当前活动的节点叫做Master备用节点叫做Backup，*Master会不停的向Backup节点通告自己的心跳，这种通告是基于VRRP协议的。Backup节点一旦接收不到Master的通告信息，它就会把LVS的VIP拿过来，并且把ipvs的规则也拿过来，在自己身上生效，从而替代Master节点*。

Keepalived除了可以监控和转移LVS资源之外，它还可以直接配置LVS而不需要直接使用ipvsadm命令，因为它可以调用，也就是说在LVS+KEEPALIVED模型中，你所有的工作在Keepalived中配置就可以了，而且它还有对后端应用服务器健康检查的功能。

直接一句话Keepalived就是VRRP协议的实现，该协议是虚拟冗余路由协议。

### VRRP工作原理简述

那么这个VRRP协议是干嘛用呢？传统上来说我们通过一个路由器上网，如果故障那就不能用了，如果使用2个路由器，有一个故障你就需要手动的设置客户端切换到另外的路由器上，或者使用ARP客户端也可以实现，但总之部署比较麻烦不利于管理，就像下图：

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140322369-437523071.png)

有没有一种办法可以自动转移而省去手动配置呢？我们就可以通过VRRP协议来实现路由器的故障转移。如下图：

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140331315-1105050518.png)

这里有个问题，VRRP提供一个VIP，它可以来设定那个路由器是活动节点，然后出现故障进行切换，VIP也随之对应到新的路由器上，但是内网是用过MAC地址来寻址的，虽然VIP对应到了新的路由器上，可是MAC变了，客户端的ARP表也没有更新，所以还是用不了，为了解决这个问题VRRP不但提供VIP还提供VMAC地址，这个VMAC地址是VRRP单独申请的，大家都可以正常使用。

故障切换的时候虽然改变了后端路由器，但是由于客户端使用的是VIP和VMAC地址，这样就不会有任何影响了。

所以Keepalived就是在Linux系统上提供了VRRP功能，当然还提供了服务监控功能，比如监控后端服务器的健康检查、LVS服务可用性检查。

VRRP的工作过程是这样的：

1. 虚拟路由器中的路由器根据优先级选举出Master，Master路由器通过发送免费ARP报文，将自己的虚拟MAC地址通告给与它连接的设备。
2. Master路由器周期性发送VRRP报文，以公布自己的配置信息（优先级等）和工作状态
3. 如果Master故障，虚拟路由器中的Backup路由器将根据优先级重新选举新的Master
4. 虚拟路由器状态切换时，Master路由器由一台设备切换会另外一台设备，新的Master路由器只是简单的发送一个携带虚拟MAC地址和虚拟IP的免费ARP报文，这样就可以更新其他设备中缓存的ARP信息
5. Backup路由器的优先级高于Master时，由Backup的工作方式（抢占式或者非抢占式）决定是否重新选举Master。

VRRP还支持认证，就是为了防止随意一个VRRP设备加入到当前的虚拟路由组离来，它提供无认证、简单8位字符串认证和MD5认证（该认证方式Keepalive不支持）。

### Keepalive软件结构

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140344609-1485693863.png)

Keepalived启动后以后会有一个主进程Master，它会生成还有2个子进程，一个是VRRP Stack负责VRRP（也就是VRRP协议的实现）、一个是Checkers负责IPVS的后端的应用服务器的健康检查，当检测失败就会调用IPVS规则删除后端服务器的IP地址，检测成功了再加回来。当检测后端有失败的情况可以使用SMTP通知管理员。另外VRRP如果检测到另外一个Keepalive失败也可以通过SMTP通知管理员。

Control Plane：这个就是主进程，主进程的功能是分析配置文件，读取、配置和生效配置文件，指挥那2个子进程工作。

WatchDog：看门狗，这个是Linux系统内核的一个模块，它的作用是帮助主进程盯着那2个子进程，因为主进程并不负责具体工作，具体工作都是子进程完成的。如果子进程挂了，那Keepalived就不完整了，所以那2个子进程会定期的向主进程打开的一个内部Unix Socket文件写心跳信息。如果有某个子进程不写信息了，它就会重启子进程，主进程就是让WatchDog来监控子进程的。下面我们就使用Keepalive来做LVS的高可用讲解。关于后端服务器上的设置我这里就不说了请看另外一篇博文。

## Keepalive安装和配置

| 服务器 | IP地址                           | 角色          |
| ------ | -------------------------------- | ------------- |
| Srv01  | 172.16.42.100 VIP: 172.16.42.111 | LVS+Keepalive |
| Srv02  | 172.16.42.101 VIP: 192.168.100.1 | LVS+Keepalive |
| Srv03  | 172.16.42.102 VIP: 172.16.42.111 | Nginx         |
| Srv04  | 172.16.42.103 VIP: 172.16.42.111 | Nginx         |

### 先决条件

1. 禁用SElinux、清除iptables规则、关闭防火墙。就算因某种原因不能清除iptables规则，那么你需要增加一条规则放行多播
2. 各个节点时间同步，启用时间同步服务`systemctl start chronyd`
3. 确保Keepalive使用的网卡开启了多播，如下图：

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140651567-247655842.png)

如果没有开启，可以使用该命令打开`ip link set multicast on dev ens33`，ens33是网卡名称。

### 安装keepalive

之间通过yum安装即可`yum install -y keepalived`。我这里使用的是阿里云的源，它默认就在里面，如下图：

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140659396-1389198011.png)

在2个节点都安装。

| 文件                                       | 说明       |
| ------------------------------------------ | ---------- |
| /usr/sbin/keepalived                       | 二进制程序 |
| /etc/keepalived/keepalived.conf            | 配置文件   |
| /usr/lib/systemd/system/keepalived.service | 服务文件   |

### Keepalive配置文件说明

```bash
# 全局配置
global_defs {
   # 邮件通知信息
   notification_email {
     # 定义收件人
     acassen@firewall.loc
   }
   # 定义发件人
   notification_email_from Alexandre.Cassen@firewall.loc
   # SMTP服务器地址
   smtp_server 192.168.200.1
   smtp_connect_timeout 30
   # 路由器标识，一般不用改，也可以写成每个主机自己的主机名
   router_id LVS_DEVEL
   # VRRP的ipv4和ipv6的广播地址，配置了VIP的网卡向这个地址广播来宣告自己的配置信息，下面是默认值
   vrrp_mcast_group4 224.0.0.18
   vrrp_mcast_group6 ff02::12
}

# 定义用于实例执行的脚本内容，比如可以在线降低优先级，用于强制切换
vrrp_script SCRIPT_NAME {

}

# 一个vrrp_instance就是定义一个虚拟路由器的，实例名称
vrrp_instance VI_1 {
    # 定义初始状态，可以是MASTER或者BACKUP
    state MASTER
    # 工作接口，通告选举使用哪个接口进行
    interface ens33
    # 虚拟路由ID，如果是一组虚拟路由就定义一个ID，如果是多组就要定义多个，而且这个虚拟
    # ID还是虚拟MAC最后一段地址的信息，取值范围0-255
    virtual_router_id 51
    # 使用哪个虚拟MAC地址
    use_vmac XX:XX:XX:XX:XX
    # 监控本机上的哪个网卡，网卡一旦故障则需要把VIP转移出去
    track_interface {
        eth0
        ens33
    }
    # 如果你上面定义了MASTER,这里的优先级就需要定义的比其他的高
    priority 100
    # 通告频率，单位为秒
    advert_int 1
    # 通信认证机制，这里是明文认证还有一种是加密认证
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    # 设置虚拟VIP地址，一般就设置一个，在LVS中这个就是为LVS主机设置VIP的，这样你就不用自己手动设置了
    virtual_ipaddress {
        # IP/掩码 dev 配置在哪个网卡
        192.168.200.16/24 dev eth1
        # IP/掩码 dev 配置在哪个网卡的哪个别名上
        192.168.200.17/24 dev label eth1:1
    }
    # 虚拟路由，在需要的情况下可以设置lvs主机 数据包在哪个网卡进来从哪个网卡出去
    virtual_routes {
        192.168.110.0/24 dev eth2
    }
    # 工作模式，nopreempt表示工作在非抢占模式，默认是抢占模式 preempt
    nopreempt|preempt
    # 如果是抢占默认则可以设置等多久再抢占，默认5分钟
    preempt delay 300
    # 追踪脚本，通常用于去执行上面的vrrp_script定义的脚本内容
    track_script {

    }
    # 三个指令，如果主机状态变成Master|Backup|Fault之后会去执行的通知脚本，脚本要自己写
    notify_master ""
    notify_backup ""
    notify_fault ""
}

# 定义LVS集群服务，可以是IP+PORT；也可以是fwmark 数字，也就是防火墙规则
# 所以通过这里就可以看出来keepalive天生就是为ipvs而设计的
virtual_server 10.10.10.2 1358 {
    delay_loop 6
    # 算法
    lb_algo rr|wrr|lc|wlc|lblc|sh|dh 
    # LVS的模式
    lb_kind NAT|DR|TUN
    # 子网掩码，这个掩码是VIP的掩码
    nat_mask 255.255.255.0
    # 持久连接超时时间
    persistence_timeout 50
    # 定义协议
    protocol TCP
    # 如果后端应用服务器都不可用，就会定向到那个服务器上
    sorry_server 192.168.200.200 1358

    # 后端应用服务器 IP PORT
    real_server 192.168.200.2 1358 {
        # 权重
        weight 1
        # MSIC_CHECK|SMTP_CHEKC|TCP_CHECK|SSL_GET|HTTP_GET这些都是
        # 针对应用服务器做健康检查的方法
        MISC_CHECK {}
        # 用于检查SMTP服务器的
        SMTP_CHEKC {}

        # 如果应用服务器不是WEB服务器，就用TCP_CHECK检查
        TCP_CHECK {
          # 向哪一个端口检查，如果不指定默认使用上面定义的端口
          connect_port <PORT>
          # 向哪一个IP检测，如果不指定默认使用上面定义的IP地址
          bindto <IP>
          # 连接超时时间
          connect_timeout 3
        }

        # 如果对方是HTTPS服务器就用SSL_GET方法去检查，里面配置的内容和HTTP_GET一样
        SSL_GET {}

        # 应用服务器UP或者DOWN，就执行那个脚本
        notify_up "这里写的是路径，如果脚本后有参数，整体路径+参数引起来"
        notify_down "/PATH/SCRIPTS.sh 参数"

        # 使用HTTP_GET方法去检查
        HTTP_GET {
            # 检测URL
            url { 
              # 具体检测哪一个URL
              path /testurl/test.jsp
              # 检测内容的哈希值
              digest 640205b7b0fc66c1ea91c463fac6334d
              # 除了检测哈希值还可以检测状态码，比如HTTP的200 表示正常，两种方法二选一即可
              status_code 200
            }
            url { 
              path /testurl2/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            url { 
              path /testurl3/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            # 向哪一个端口检查，如果不指定默认使用上面定义的端口
            connect_port <PORT>
            # 向哪一个IP检测，如果不指定默认使用上面定义的IP地址
            bindto <IP>
            # 连接超时时间
            connect_timeout 3
            # 尝试次数
            nb_get_retry 3
            # 每次尝试之间间隔几秒
            delay_before_retry 3
        }
    }

    real_server 192.168.200.3 1358 {
        weight 1
        HTTP_GET {
            url { 
              path /testurl/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334c
            }
            url { 
              path /testurl2/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334c
            }
            connect_timeout 3
            nb_get_retry 3
            delay_before_retry 3
        }
    }
}
```

### 配置Srv01和Srv02

#### 配置VRRP部分

Srv01上的keepalived.conf

```bash
global_defs {
   notification_email {
     acassen@firewall.loc
   }
   notification_email_from Alexandre.Cassen@firewall.loc
   smtp_server 127.0.0.1
   smtp_connect_timeout 30
   router_id srv01
}


vrrp_instance VI_1 {
    state MASTER
    interface ens33
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }

    virtual_ipaddress {
        172.16.42.111/24 brd 172.16.42.111 dev ens33 label ens33:0
    }
    preempt delay 60
}
```

Srv02上的keepalived.conf，唯一不同的就是state、priority以及router_id。

```bash
global_defs {
   notification_email {
     acassen@firewall.loc
   }
   notification_email_from Alexandre.Cassen@firewall.loc
   smtp_server 127.0.0.1
   smtp_connect_timeout 30
   router_id srv02
}


vrrp_instance VI_1 {
    state BACKUP
    interface ens33
    virtual_router_id 51
    priority 90
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }

    virtual_ipaddress {
        172.16.42.111/24 brd 172.16.42.111 dev ens33 label ens33:0
    }
    preempt delay 60
}
```

启动2个节点，启动后会自动配置ens33:0这个子接口的虚拟IP

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140413416-399572956.png)

在主节点上你通过`systemctl status keepalived`看不到它到底是什么角色，不过在BACKUP节点上你可以看到，但是你在主节点日志中`cat /var/log/message`里可以看到Srv01进入到MASTER状态，如下图：

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140423298-1476811506.png)

查看Srv02的状态

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140431862-1131478201.png)

那么你通过停止Srv01上的keepalived服务就看到MASTER会被转移到Srv02上。

使用该命令查看VRRP通告`tcpdum -i ens33 -nn host 224.0.0.18`，你在2台主机都会看到相同的信息。

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140438713-488130917.png)

Srv01使用真实物理IP对该地址进行发送通告，那么Srv02也会收到，如果Srv01宕机，那么Srv02就会使用自己的物理IP向该地址发送通告，由于Srv01已经宕机那么此时Srv02的优先级就是最高的，所以Srv02就变成了MASTER。

#### 配置LVS部分

这里只是用了LVS来说明如何配置Keepalived，如果要看完整内容请移步[使用Keepalived构建LVS高可用集群](https://www.cnblogs.com/rexcheny/p/10778636.html)

在keepalived.conf文件中增加下面的内容，2台服务器增加的内容一致，所以这里就写一份。

```bash
virtual_server 172.16.42.111 80 {
    delay_loop 6
    lb_algo rr
    lb_kind DR
    nat_mask 255.255.255.0
    persistence_timeout 0
    protocol TCP

    sorry_server 192.168.200.200 1358

    # 后端应用服务器 IP PORT
    real_server 172.16.42.102 80 {
        weight 1
        # 应用服务器UP或者DOWN，就执行那个脚本
        notify_up "/usr/local/notify.sh up"
        notify_down "/usr/local/notify.sh down"
        HTTP_GET {
            # 检测URL
            url { 
              path /index.html
              # 除了检测哈希值还可以检测状态码，比如HTTP的200 表示正常，两种方法二选一即可
              status_code 200
            }
            connect_timeout 3
            nb_get_retry 3
            delay_before_retry 3
        }
    }

    real_server 172.16.42.103 80 {
        weight 1
        # 应用服务器UP或者DOWN，就执行那个脚本
        notify_up "/usr/local/notify.sh up"
        notify_down "/usr/local/notify.sh down"
        HTTP_GET {
            # 检测URL
            url { 
              path /index.html
              # 除了检测哈希值还可以检测状态码，比如HTTP的200 表示正常，两种方法二选一即可
              status_code 200
            }
            connect_timeout 3
            nb_get_retry 3
            delay_before_retry 3
        }
    }
}
```

这里的notify_up|down脚本我写的很简单就是为了使用一下这个功能，内容如下：

```bash
#!/bin/bash

# 不同的是2个主机上的echo部分不一样，因为主机名不同。
if [ $1 == "up" ]; then
  echo "Srv02 is UP" > /tmp/notify.txt
elif [ $1 == "down" ]; then
  echo "Srv02 is DOWN" > /tmp/notify.txt
fi
```

重启Keepalived服务之后你就可以通过`ipvsadm -Ln`查看ipvs规则了，这些规则在2台服务器上都会有，如下图：

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140450911-1523773191.png)

测试访问

使用下面的命令快速访问`for i in {1..20}; do curl http://172.16.42.111/ | grep "Srv0" --color ; done`

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140459240-1648543658.png)

可以看到2台服务器交替，因为我们使用的rr调度算法。

### 故障转移测试

连续访问VIP，然后停止Srv01上面的keepalived服务，这就意味着Srv01也就是失去了VIP，然后观察请求情况以及是否触发之前设定的脚本。

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140509022-1113468240.png)

在Srv01上查看脚本执行情况

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140517193-1817722247.png)

查看Srv02上面的日志

![img](https://img2018.cnblogs.com/blog/1448094/201904/1448094-20190427140528410-207065449.png)
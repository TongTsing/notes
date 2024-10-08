# iptables教程

### 0. Iptables介绍

iptables防火墙可以用于创建过滤(filter)与NAT规则。所有Linux发行版都能使用iptables，因此理解如何配置iptables将会帮助你更有效地管理Linux防火墙。如果你是第一次接触iptables，你会觉得它很复杂，但是一旦你理解iptables的工作原理，你会发现其实它很简单。

首先介绍iptables的结构：iptables -> Tables -> Chains -> Rules. 简单地讲，tables由chains组成，而chains又由rules组成。如下图所示。

![img](https:////upload-images.jianshu.io/upload_images/13838098-a1a23a58ea0dbfeb.png?imageMogr2/auto-orient/strip|imageView2/2/w/540/format/webp)

iptables-table-chain-rule-structure.png

### 1.  iptables的表与链

iptables具有四种内建表：Filter表、NAT表、Mangle表和Raw表

#### 1.1 Filter表

Filter表示iptables的默认表，因此如果你没有自定义表，那么就默认使用filter表，它具有以下三种内建链：

- INPUT链 – 处理来自外部的数据。
- OUTPUT链 – 处理向外发送的数据。
- FORWARD链 – 将数据转发到本机的其他网卡设备上。

#### 1.2 NAT表

NAT表有三种内建链：

- PREROUTING链 – 处理刚到达本机并在路由转发前的数据包。它会转换数据包中的目标IP地址（destination ip address），通常用于DNAT(destination NAT)。
- POSTROUTING链 – 处理即将离开本机的数据包。它会转换数据包中的源IP地址（source ip address），通常用于SNAT（source NAT）。
- OUTPUT链 – 处理本机产生的数据包。

#### 3. Mangle表

Mangle表用于指定如何处理数据包。它能改变TCP头中的QoS位。Mangle表具有5个内建链(解释见上)：

- PREROUTING
- OUTPUT
- FORWARD
- INPUT
- POSTROUTING

### 4. Raw表

Raw表用于处理异常，它具有2个内建链：

- PREROUTING
- OUTPUT

以下是Iptables中的三个重要表格

![img](https:////upload-images.jianshu.io/upload_images/13838098-b9b4675931350c4a.png?imageMogr2/auto-orient/strip|imageView2/2/w/540/format/webp)

iptables-filter-nat-mangle-tables.png

### 2.IPTABLES 规则(Rules)

牢记以下三点式理解iptables规则的关键：

- Rules包括一个条件和一个**目标值**(target)
- 如果满足条件，就执行目标(target)中的规则或者特定值。
- 如果不满足条件，就判断下一条Rules。

**目标值（Target Values）**

下面是你可以在target里指定的特殊值：

- ACCEPT – 允许防火墙接收数据包
- DROP – 防火墙丢弃包
- QUEUE – 防火墙将数据包移交到用户空间
- RETURN – 防火墙停止执行当前链中的后续Rules，并返回到调用链(the calling chain)中。

如果你执行`iptables --list`你将看到防火墙上的可用规则。下例说明当前系统没有定义防火墙，你可以看到，它显示了默认的filter表，以及表内默认的input链, forward链, output链。



```shell
# iptables -t filter --list
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

**参数说明：**

- target：目标值
- prot： 协议
- opt：选项
- source：数据包源地址
- destination：数据包目标地址

![img](https:////upload-images.jianshu.io/upload_images/13838098-97d2c29e31e90ddf.png?imageMogr2/auto-orient/strip|imageView2/2/w/479/format/webp)

filter表内容.png

bw_INPUT、bw_OUTPUT和bw_FORWARD chain用于带宽(Bandwidth)控制；

fw_INPUT、fw_OUTPUT和fw_FORWARD chain用于防火墙(Firewall)控制；

natctrl_FORWARD用于网络地址转换(NAT)控制；

oem_fwd、oem_out用于OEM厂商自定义的控制。



```shell
# 执行以下指令查看mangle表
iptables -t mangle --list

# 执行以下指令查看nat表
iptables -t nat --list

# 执行以下指令查看raw表
iptables -t raw --list

# 由于默认表是filter表，不加-t选项默认输出filter表
iptables -t filter --list
(or)
iptables --list
```

### 3.清空所有iptables规则

在配置iptables之前，你通常需要用`iptables --list`命令或者`iptables-save`命令查看有无现存规则，因为有时需要删除现有的iptables规则：



```shell
iptables --flush
或者
iptables -F
```

这两条命令是等效的。但是并非执行后就万事大吉了。你仍然需要检查规则是不是真的清空了，因为有的linux发行版上这个命令不会清除NAT表中的规则，此时只能手动清除：



```shell
iptables -t NAT -F
```

### 4.永久生效

当你删除、添加规则后，这些更改并不能永久生效，这些规则很有可能在系统重启后恢复原样。为了让配置永久生效，根据平台的不同，具体操作也不同。下面进行简单介绍：

#### 4.1 Ubuntu

首先，保存现有的规则：



```jsx
iptables-save > /etc/iptables.rules
```

然后新建一个bash脚本，并保存到/etc/network/if-pre-up.d/目录下：



```bash
#!/bin/bash
iptables-restore < /etc/iptables.rules
```

这样，每次系统重启后iptables规则都会被自动加载。
 /!\注意：不要尝试在.bashrc或者.profile中执行以上命令，因为用户通常不是root，而且这只能在登录时加载iptables规则。

#### 4.2 CentOS, RedHat



```bash
# 保存iptables规则
service iptables save

# 重启iptables服务
service iptables stop
service iptables start
```

查看当前规则：



```undefined
cat  /etc/sysconfig/iptables
```

### 5. 追加iptables规则

可以使用`iptables -A`命令追加新规则，其中-A表示Append。因此，新的规则将追加到链尾。
 一般而言，最后一条规则用于丢弃(DROP)所有数据包。如果你已经有这样的规则了，并且使用-A参数添加新规则，那么就是无用功。

#### 5.1语法



```shell
iptables -A chain firewall-rule

#-A chain – 指定要追加规则的链
#firewall-rule – 具体的规则参数
```

#### 5.2 描述规则的基本参数

以下这些规则参数用于描述数据包的协议、源地址、目的地址、允许经过的网络接口，以及如何处理这些数据包。这些描述是对规则的基本描述。

##### -p 协议（protocol）

- 指定规则的协议，如tcp, udp, icmp等，可以使用all来指定所有协议。
- 如果不指定-p参数，则默认是all值。这并不明智，请总是明确指定协议名称。
- 可以使用协议名(如tcp)，或者是协议值（比如6代表tcp）来指定协议。映射关系请查看/etc/protocols
- 还可以使用–protocol参数代替-p参数

##### -s 源地址（source）

- 指定数据包的源地址
- 参数可以使IP地址、网络地址、主机名
- 例如：-s 192.168.1.101指定IP地址
- 例如：-s 192.168.1.10/24指定网络地址
- 如果不指定-s参数，就代表所有地址
- 还可以使用–src或者–source

##### -d 目的地址（destination）

- 指定目的地址
- 参数和-s相同
- 还可以使用–dst或者–destination

##### -j 执行目标（jump to target）

- -j代表”jump to target”
- -j指定了当与规则(Rule)匹配时如何处理数据包
- 可能的值是ACCEPT, DROP, QUEUE, RETURN
- 还可以指定其他链（Chain）作为目标

##### -i 输入接口（input interface）

- -i代表输入接口(input interface)
- -i指定了要处理来自哪个接口的数据包
- 这些数据包即将进入INPUT, FORWARD, PREROUTE链
- 例如：-i eth0指定了要处理经由eth0进入的数据包
- 如果不指定-i参数，那么将处理进入所有接口的数据包
- 如果出现! -i eth0，那么将处理所有经由eth0以外的接口进入的数据包
- 如果出现-i eth+，那么将处理所有经由eth开头的接口进入的数据包
- 还可以使用–in-interface参数

##### -o 输出（out interface）

- -o代表”output interface”
- -o指定了数据包由哪个接口输出
- 这些数据包即将进入FORWARD, OUTPUT, POSTROUTING链
- 如果不指定-o选项，那么系统上的所有接口都可以作为输出接口
- 如果出现! -o eth0，那么将从eth0以外的接口输出
- 如果出现-i eth+，那么将仅从eth开头的接口输出
- 还可以使用–out-interface参数

#### 5.3 描述规则的扩展参数

对规则有了一个基本描述之后，有时候我们还希望指定端口、TCP标志、ICMP类型等内容。

##### –sport 源端口（source port）针对 -p tcp 或者 -p udp

- 缺省情况下，将匹配所有端口
- 可以指定端口号或者端口名称，例如”–sport 22″与”–sport ssh”。
- /etc/services文件描述了上述映射关系。
- 从性能上讲，使用端口号更好
- 使用冒号可以匹配端口范围，如”–sport 22:100″
- 还可以使用”–source-port”

##### –-dport 目的端口（destination port）针对-p tcp 或者 -p udp

- 参数和–sport类似
- 还可以使用”–destination-port”

##### -–tcp-flags TCP标志 针对-p tcp

- 可以指定由逗号分隔的多个参数
- 有效值可以是：SYN, ACK, FIN, RST, URG, PSH
- 可以使用ALL或者NONE

##### -–icmp-type ICMP类型 针对-p icmp

- –icmp-type 0 表示Echo Reply
- –icmp-type 8 表示Echo

#### 5.4 追加规则的完整实例：仅允许SSH服务

本例实现的规则将仅允许SSH数据包通过本地计算机，其他一切连接（包括ping）都将被拒绝。



```shell
# 1.清空所有iptables规则
iptables -F

# 2.接收目标端口为22的数据包
iptables -A INPUT -i eth0 -p tcp --dport 22 -j ACCEPT

# 3.拒绝所有其他数据包
iptables -A INPUT -j DROP
```

### 6. 更改默认策略

上例的例子仅对接收的数据包过滤，而对于要发送出去的数据包却没有任何限制。本节主要介绍如何更改链策略，以改变链的行为。

#### 6.1 默认链策略

/!\警告：请勿在远程连接的服务器、虚拟机上测试！
 当我们使用-L选项验证当前规则是发现，所有的链旁边都有policy ACCEPT标注，这表明当前链的默认策略为ACCEPT：



```shell
# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     tcp  --  anywhere             anywhere            tcp dpt:ssh
DROP       all  --  anywhere             anywhere            

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

这种情况下，如果没有明确添加DROP规则，那么默认情况下将采用ACCEPT策略进行过滤。除非：
 a)为以上三个链单独添加DROP规则：



```shell
iptables -A INPUT -j DROP
iptables -A OUTPUT -j DROP
iptables -A FORWARD -j DROP
```

b)更改默认策略：



```shell
iptables -P INPUT DROP
iptables -P OUTPUT DROP
iptables -P FORWARD DROP
```

### 7.配置应用程序规则

尽管5.4节已经介绍了如何初步限制除SSH以外的其他连接，但是那是在链默认策略为ACCEPT的情况下实现的，并且没有对输出数据包进行限制。本节在上一节基础上，以SSH和HTTP所使用的端口为例，教大家如何在默认链策略为DROP的情况下，进行防火墙设置。在这里，我们将引进一种新的参数-m state，并检查数据包的状态字段。

#### 7.1 SSH



```shell
# 1.允许接收远程主机的SSH请求
iptables -A INPUT -i eth0 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT

# 2.允许发送本地主机的SSH响应
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT

-m state: 启用状态匹配模块（state matching module）
–-state: 状态匹配模块的参数。当SSH客户端第一个数据包到达服务器时，状态字段为NEW；建立连接后数据包的状态字段都是ESTABLISHED
–sport 22: sshd监听22端口，同时也通过该端口和客户端建立连接、传送数据。因此对于SSH服务器而言，源端口就是22
–dport 22: ssh客户端程序可以从本机的随机端口与SSH服务器的22端口建立连接。因此对于SSH客户端而言，目的端口就是22
```

#### 7.2 HTTP



```shell
# 1.允许接收远程主机的HTTP请求
iptables -A INPUT -i eth0 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT

# 1.允许发送本地主机的HTTP响应
iptables -A OUTPUT -o eth0 -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT
```

#### 7.3 完整的配置



```shell
# 1.删除现有规则
iptables -F

# 2.配置默认链策略
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT DROP

# 3.允许远程主机进行SSH连接
iptables -A INPUT -i eth0 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT

# 4.允许本地主机进行SSH连接
iptables -A OUTPUT -o eth0 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT

# 5.允许HTTP请求
iptables -A INPUT -i eth0 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT
```



作者：Jack_Ou
链接：https://www.jianshu.com/p/ca2aa2bdaf51
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
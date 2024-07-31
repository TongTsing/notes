# TCP 连接过程及状态变化

## 一 . 前言

Time Wait 是 TCP 连接中一个概念 , 在日常的使用中 , 这个概念不明显. 但是在深入问题的时候 , 就能发现里面能看出很多东西 , 了解 Time wait 之前先要了解 TCP 整个连接的过程 .

这一篇先就 TCP 的连接过程来简单了解一下其中的各个状态

## 二. TCP 连接过程

### 2.1 基础概念

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/09ba3664a6584dffaea330c95c6e4596~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp)

**下面来看一下以上参数的作用 :**

- **Source Port** 源端口 (16位)

- Destination Port

   

  目标端口 (16 位)

  - 这2个值与 IP 头部中的源和目的 IP 地址一起 , 唯一的标识了一个连接

- **Windows Size** 窗口大小 (16位) : 用于 TCP 的流量控制 , 限制大小为 65535 字节

- **TCP 校验和:** 强制直段 , 由发送方进行计算和保持 , 由接收方验证

> **Seq** : Sequence Number 序列号 (32 位)

标识了 TCP 发送端到 TCP 接收端的数据流的一个字节 , 该字节代表着包含该序列好的报文段的数据中的第一个字节

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4402807dce754f1a87810b08777febba~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp)

> **ACK :** 响应包 , 标识服务端接收到消息, 并且发送响应信息

TCP 中的通信是通过发送分组包的方式实现的, 在发送分组包的过程中 , 会有几个问题需要解决 :

- 接收方是否已收到分组
- 接收方收到的分组是否和发送方之前发送的一致

为了保障这种场景 , 接收方会给发送方一个 ACK (acknowledgment) 信号 , 以反馈自己已经接收到一个分组 ,而当发送方接收到 ACK 后 ,才会发送下一个分组

- **ACK 号字段被用于指明在接收方已经顺序收到的最大字节+1**
- **注意区分ACK 和 ack , ACK 通常指 ACK 标志位 , ack 一般指确认值 ,为 seq + 1**

> **TCP 标志位** :

TCP 标志是出现在 TCP 报头中的各种类型的标志位。它们每一个都有自己的意义。它们启动连接、传输数据和关闭连接。常用的 TCP 标志是 SYN、 ACK、 RST、 FIN、 URG、 PSH

- SYN : 用于启动连接的数据包
- ACK : 用于确认数据包已被接收的数据包，也用于确认发起请求和终止请求
- RST : 表示连接已关闭，或者可能服务不接受请求
- FIN : 指示连接正在被关闭。发送方和接收方都发送 FIN 数据包以优雅地终止连接
- PSH : 指示传入的数据应该直接传递到应用程序，而不是获得缓冲
- URG : 指示数据包所携带的数据应该被 TCP 堆栈立即处理

如果用 wireshark 抓包 , 就可以看到如下的连接信息 :

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7e72f77b0fcf4ddfaaa4d52c02cb0675~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp)

> TCP 连接中的信息

### 2.2 TCP 握手流程图

> 建立连接三次握手

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d9e63c99c8a84765860992bb94d13281~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp)

**用数据表示 :**

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b2eb8a9c6ccf451da5d4c129176d4470~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp)

```java
java

复制代码// 重点解析 : 
- 连接开启者发送一个 SYN 报文段 , 指明自己想要的连接的端口号和客户端初始序列号 
- 
```

> 数据传输

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1642feb1d91f4fdfb106bc8ea28d1b65~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp)

> 断开连接四次握手

TIME-WAIT 主要发生在 TCP 断开时发生 , 与此相同的还有 CLOSE-WAIT 环节 , 先来看一下四次握手的几个过程 :

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a8eede7d30a143a681c0aaea8ec78534~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp)

这里可以总结出几个细节 : 

1. TIME_WAIT 发生在 Client 端 , 当 Client 发起 Close 后 , 会进入 TIME_WAIT
2. TIME_WAIT 之后不再存在流程 , 所以 TIME_WAIT 是有 Client 在一定时间后主动关闭

### 2.3 状态变化

看 <**TCP/IP详解:卷3**> 时发现了一张很不错的图 , 这里贴了过来 , 感兴趣的可以找一下原书看看 :

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f3bc4c9536d349069a2d9a13589bb92c~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp)

> 各状态含义 :

- **LISTEN** ： 表示服务器端的某个SOCKET处于监听状态，可接收连接
- **SYN_RCVD** : **Server** 已接收到 SYN 报文 , 是 TCP 连接建立连接的一个中间状态 , 该状态很短暂 , 接收到 ACK 后进入 ESTABLISHED
- **SYN_SENT** : **Client** 已发送报文 , 并等待服务端的发送三次握手中的第2个报文
- **ESTABLISHED** : 连接建立完成

------

- **FIN_WAIT_1** : 表示等待对方的FIN报文 , Client 端想主动关闭连接 , 发送 FIN 报文进入该状态
- **CLOSE_WAIT** : 等待关闭 , 此状态下会完成未完成的发送
- **FIN_WAIT_2** : 表示等待对方的FIN报文 , Client 端接收到Server 返回的 ACK 报文后到该状态
- **LAST_ACK** : 被动关闭一方在发送FIN报文后，最后等待对方的ACK报文
- **TIME_WAIT** : Client 接收到最后的关闭报文 , 发送了 ACK 报文 , 等待 2MSL 后关闭
- **CLOSING** : 发送FIN报文后，并没有收到对方的ACK报文，反而却也收到了对方的FIN报文 (双方同时 ClOSE)

### 2.4 数据交互环节

> 端口的状态变化

先来了解一下端口涉及到的基础概念 :

```java
java

复制代码// 逻辑端口是什么 : 
逻辑端口是指逻辑意义上用于区分服务的端口 , 逻辑端口号从 0 到 65535 (80/22/8080 均为逻辑端口的一员)

// 逻辑端口的作用 :
一台服务器中存在多个应用 , Client 端通过端口号来和应用进行通信 . 
所以 ,通常一个端口号后面就会对应一个进程 ,当前TCP连接的所有数据包都可以通过该端口号和该进程进行交互.

// 随机端口号
大部分 TCP 客户程序都是使用临时端口, 让 TCP 模块选择一个当前未使用的端口 , 
```

## 三 .功能详解

### 3.1 TIME-WAIT 详情

首先发出 FIN 命令的一方 , 在通信双方完全关闭连接后 , 仍然要保持 TIME-WAIT状态直至两倍报文段的最大生存时间 (MSL). MSL 的建议值在120秒 . 处在该状态时 ,同一连接不能重复打开.

### 3.2 TIME-WAIT和CLOSE-WAIT 关联

- 主动关闭连接的一方，调用close()；**协议层发送FIN包**
- **被动关闭的一方收到FIN包后，协议层回复ACK**；然后**被动关闭的一方，进入CLOSE_WAIT状态，**主动关闭的一方等待对方关闭，则进入FIN_WAIT_2状态；此时，主动关闭的一方 等待 被动关闭一方的应用程序，调用close操作
- 被动关闭的一方在完成所有数据发送后，调用close()操作；此时，**协议层发送FIN包给主动关闭的一方，等待对方的ACK，被动关闭的一方进入LAST_ACK状态**
- **主动关闭的一方收到FIN包，协议层回复ACK**；此时，**主动关闭连接的一方，进入TIME_WAIT状态；而被动关闭的一方，进入CLOSED状态**

## 四. 总结

TCP 中的知识点非常的多 , 作为一名后端开发 , 了解的实在有限 .

**对整个 TCP 连接的流程进行了解后 , 才好深入 Time-Wait 的相关概念 , 从而解决业务问题.**

相对而言 , 抓包其实还很生疏 , 下一篇来学习一下如何进行高效率的抓包
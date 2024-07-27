[**LVS**（Linux Virtual Server）是一个虚拟的服务器集群系统，由**章文嵩博士**发起的自由软件项目。它在**1998年**成立，是中国国内最早出现的自由软件项目之一](https://zhuanlan.zhihu.com/p/425662554)[1](https://zhuanlan.zhihu.com/p/425662554)[2](https://zhuanlan.zhihu.com/p/623510203). 让我为您详细介绍一下：

1. **LVS简介**：
   - **LVS**是**Linux Virtual Server**的缩写，也就是**Linux虚拟服务器**。
   - 在Linux 2.4内核之前，使用LVS时必须重新编译内核以支持LVS功能模块。但是从Linux 2.4内核开始，已经完全内置了LVS的各个功能模块，无需给内核打任何补丁，可以直接使用LVS提供的各种功能。
   - 使用LVS技术的目标是通过负载均衡技术和Linux操作系统实现高性能、高可用的服务器群集，具有良好的可靠性、可扩展性和可操作性。
2. **LVS体系架构**：
   - **Load Balancer层**：位于整个集群系统的最前端，由一台或多台负载调度器（Director Server）组成。Director Server上安装了LVS模块，其主要作用类似于一个路由器，将用户请求分发给Server Array层的应用服务器（Real Server）。同时，Director Server还监控Real Server服务的健康状况。
   - **Server Array层**：由一组实际运行应用服务的机器组成，可以是WEB服务器、MAIL服务器、FTP服务器、DNS服务器等。在实际应用中，Director Server也可以兼任Real Server的角色。
   - **Shared Storage层**：为所有Real Server提供共享存储空间和内容一致性的存储区域。通常使用磁盘阵列设备或集群文件系统。
3. **LVS基本工作原理**：
   - 用户向负载均衡调度器（Director Server）发起请求。
   - 请求经过PREROUTING链，IPVS比对请求的服务是否为集群服务。
   - 如果是，IPVS修改数据包的目标IP地址为后端服务器IP，然后将数据包发往POSTROUTING链。
   - POSTROUTING链将数据包发送给Real Server。
4. **LVS相关术语**：
   - **DS**（Director Server）：前端负载均衡器节点。
   - **RS**（Real Server）：后端真实的工作服务器。
   - **VIP**：向外部直接面向用户请求的IP地址。
   - **DIP**：Director Server IP，用于和内部主机通讯的IP地址。
   - **RIP**：Real Server IP，后端服务器的IP地址。
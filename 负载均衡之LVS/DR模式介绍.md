## LVS-DR 模式

如果真的能够由真实服务器直接响应客户端，而不经过负载均衡器。那么这个由负载均衡器直接响应回客户端的数据包需要满足什么条件，才能被客户端正常接收？

真实服务器发出的数据包，在客户端接收到的时候，一定要匹配得上从客户端发出的数据包。如果不匹配的话，客户端收到响应数据包后会直接将数据包丢弃。

> 客户端发出的请求数据包：CIP ➡️ VIP，则收到的响应数据包一定是 VIP ➡️ CIP。

做个小思考，为什么没有带上 MAC 地址？后文有解释～

但是 VIP 已经绑定在负载均衡器上，真实服务器也有多个，在连通的网络里，如何能让多个真实服务器和负载均衡器的 VIP 地址相同？并且真实服务器的 VIP 不能被其他设备访问的到。也就是说在每台真实服务器上的 VIP 地址，只能它们自己知道“我悄悄藏着 VIP”，别的设备压根不知道。

这里不得不提另一个非常神奇的 IP 地址 127.0.0.1/0.0.0.0。仔细想一下，127.0.0.1 不就是每台设备上都相同，“悄悄藏着”的 IP 地址吗，除了自己的其他设备都没办法访问。这个神奇的 IP 地址，叫做 Loopback 接口。它是一种纯软件性质的虚拟接口，当有请求数据包送达到 lo 接口，那么 lo 接口会将这个数据包直接 “掉头”，返回给这个设备本身，这就是“环回”接口的由来。所以，如果将 VIP 绑定在 lo 接口上，是不是就可以完成“只有自己知道这个 VIP，别的设备不知道”呢？

将 VIP 绑定在 lo 接口上（在真实服务器上配置），其实只完成了一半，只是做到了在多台真实服务器上绑定相同的 VIP 地址。还记得上篇文章中所讲的 ARP 协议吗，ARP 协议会采集在当前局域网中，除了自己之外的其他主机的 MAC 地址和 IP 地址的映射关系。而 VIP 是一个不能被别的设备采集到的地址，所以我们要对真实服务器的 ARP 协议做一些修改，让 VIP 不会被其他设备采集到。很显然，这不但要修改设备收到 ARP 请求之后的响应（arp_ignore 参数)，也要修改设备向外通告 ARP 的请求 （arp_announce 参数）。

> arp_ignore：定义接收 ARP 请求时的响应级别 0：响应任意网卡上接收到的对本机 IP 地址的 ARP 请求（包括环回网卡），不论目的 IP 地址是否在接收网卡上 1：只响应目的 IP 地址为接收网卡地址的 ARP 请求 2：只响应目的 IP 地址为接收网卡地址的 ARP 请求，且 ARP 请求的源 IP 地址必须和接收网卡的地址在同网段
> arp_announce：定义将自己地址向外通告时的通告级别 0：允许任意网卡上的任意地址向外通告 1：尽量仅向目标网络通告与其网络匹配的地址 2：仅向与本地接口上地址匹配的网络进行通告

可以看到，arp_ignore 为 1 并且 arp_announce 为 1 时，lo 接口上绑定的 VIP 可以被藏在本机上，只有自己知道。

![img](https://pic4.zhimg.com/80/v2-8884b9b0527af5fc6700610e9fb8e9d7_1440w.webp)

> 红色表示发出的数据包 
> 绿色表示返回的数据包
> 黄色表示负载均衡器修改的内容 
> 虚线表示经过 N 个下一跳，即可以不在同一局域网内 
> 实线表示只能 “跳跃一次”，即必须在同一局域网内

1. 当计算机发出一个请求的数据包到达负载均衡器后，负载均衡器修改请求数据包的 { 目标MAC 地址 } 为 真实服务器的 MAC 地址，其余信息不变。**负载均衡器和真实服务器必须在同一局域网内，否则负载均衡器无法替换请求数据包的 { 目标MAC 地址 } 为 真实服务器的 MAC 地址**
2. 真实服务器收到请求的数据包，发现自己有一个 “隐藏“ 的 VIP，于是接收这个数据包，并交由对应的上层应用处理
3. 处理完成后，将响应数据包直接返回给客户端。此时在真实服务器上查看 TCP 连接为：VIP ➡️ CIP

- **总结一下 DR 模式的特点：**

1. **仅修改请求数据包的「目标 MAC 地址」，作用在数据链路层。因此负载均衡器必须和真实服务器在同一局域网内，且不能对端口进行转发**
2. **真实服务器中的 VIP，只能被自己 “看见”，其他设备不可知。因此 VIP 必须绑定在真实服务器的 lo 网卡上，并且不允许将此网卡信息经过 ARP 协议对外通告**
3. **请求的数据包经过负载均衡器后，直接由真实服务器返回给客户端，响应数据包不需要再经过负载均衡器**
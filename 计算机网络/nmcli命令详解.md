# nmcli命令详解

[![Golang发烧友](https://pic1.zhimg.com/v2-bf3151c39948a5b42ebf4854abe71c89_l.jpg?source=172ae18b)](https://www.zhihu.com/people/golangzai-fa-guang)

[Golang发烧友](https://www.zhihu.com/people/golangzai-fa-guang)

关注

## **以下是nmcli命令的一些常用选项和用法：** 

```text
connection show    -- 显示所有网络连接的详细信息。
```

### **1）修改网卡名称**

```text
nmcli c modify uuid f136e0e3-5faf-4d2f-8c5f-4ce976585b30 con-name ens33
```



![img](https://pic3.zhimg.com/80/v2-b7644dcb3f562bb35c7a36fb6e146f52_720w.webp)





![img](https://pic1.zhimg.com/80/v2-fe11abd4160ffdfbe72e910ce2612d08_720w.webp)



### **2）添加网卡**

```text
nmcli connection add type ethernet con-name ens37 ifname ens37
```



![img](https://pic4.zhimg.com/80/v2-770a3fc880a71f810a6d89839447669b_720w.webp)



### **3）启动或停止网卡**

```text
nmcli connection reload ——重载网卡
nmcli connection up ens33 ——激活网卡ens33
nmcli connection down ens33 ——停用网卡ens33
nmcli connection down ens33 && nmcli connection up ens33 ——重启网卡ens33
```



![img](https://pic1.zhimg.com/80/v2-10ee79ea6e4a311a81abbb2aff7f49ac_720w.webp)



### **4）设置网卡获取IP模式**

> 使用 nmcli 来设置网卡为静态IP ，可以使用以下命令：

```text
nmcli connection modify <网卡名> ipv4.method manual ipv4.addresses <静态IP地址>/<子网掩码> ipv4.gateway <网关IP地址> ipv4.dns <DNS服务器IP地址>

nmcli connection modify ens37 ipv4.method manual ipv4.addresses 192.168.70.133/24 ipv4.gateway 192.168.70.2 ipv4.dns 114.114.114.114
#配置完静态IP后需要重启网卡使配置生效
nmcli c down ens37 && nmcli c up ens37
#查看网卡的详细信息
nmcli connection show ens37
```



![img](https://pic4.zhimg.com/80/v2-9d442deb77ce252dc9bda36ba562003b_720w.webp)





![img](https://pic2.zhimg.com/80/v2-d883228208be8e3b54527886ac3c8f65_720w.webp)





![img](https://pic1.zhimg.com/80/v2-65451dd6e6a0d7ac520363c4d0719f44_720w.webp)



> 使用nmcli命令将网卡eth0模式设置为dhcp

```text
#删除配置的静态IP
nmcli connection modify ens37 ipv4.addresses "" ipv4.gateway "" ipv4.dns ""
#修改网卡为dhcp模式
nmcli connection modify eth0 ipv4.method auto
#重启网卡
nmcli c down ens37 && nmcli c up ens37
```









在 CentOS 7 中，您可以使用 `nmcli` 命令（NetworkManager 命令行工具）来创建一个新的网络适配器。以下是一个简单的步骤：

1. **列出可用的网络连接**：

   ```
   bashCopy code
   nmcli connection show
   ```

   这会列出当前系统上的网络连接。

2. **创建新的网络连接**：

   ```
   bashCopy code
   sudo nmcli connection add type ethernet ifname <INTERFACE_NAME> con-name <CONNECTION_NAME>
   ```

   将 `<INTERFACE_NAME>` 替换为您要创建的新网络适配器的名称，例如 `eth1`。将 `<CONNECTION_NAME>` 替换为新连接的名称，例如 `MyConnection`。

   例如：

   ```
   bashCopy code
   sudo nmcli connection add type ethernet ifname eth1 con-name MyConnection
   ```

3. **配置新的网络连接**：

   ```
   bashCopy code
   sudo nmcli connection modify <CONNECTION_NAME> ipv4.addresses "<IP_ADDRESS>/<SUBNET>" ipv4.gateway "<GATEWAY>"
   ```

   将 `<CONNECTION_NAME>` 替换为您在上一步中创建的连接的名称，`<IP_ADDRESS>/<SUBNET>` 替换为您的 IP 地址和子网掩码，`<GATEWAY>` 替换为您的网关地址。

   例如：

   ```
   bashCopy code
   sudo nmcli connection modify MyConnection ipv4.addresses "192.168.1.2/24" ipv4.gateway "192.168.1.1"
   ```

4. **激活新的网络连接**：

   ```
   bashCopy code
   sudo nmcli connection up <CONNECTION_NAME>
   ```

   例如：

   ```
   bashCopy code
   sudo nmcli connection up MyConnection
   ```

这些命令将帮助您在 CentOS 7 中创建一个新的网络适配器，并配置它的 IP 地址和网关。请根据您的实际需求和网络配置进行相应的替换。
永久修改网卡IP

```visual basic
vi /etc/sysconfig/network-scripts/ifcfg-ens33
TYPE=Ethernet			#设置网卡类型，“Ethernet”表示以太网
DEVICE=ens33			#设置网卡的名称
ONBOOT=yes				#设置网卡是否在 Linux 操作系统启动时激活
BOOTPROTO=static		#设置网卡的配置方式，“static”表示使用静态IP地址，“dhcp”时表示动态获取地址
IPADDR=192.168.80.3		#设置网卡的 IP 地址
NETMASK=255.255.255.0	#设置网卡的子网掩码
GATEWAY=192.168.80.2	#设置网卡的默认网关地址
DNS1=192.168.80.2		#设置DNS服务器的 IP 地址
```



重启网卡： 

```shell
systemctl restart network
```


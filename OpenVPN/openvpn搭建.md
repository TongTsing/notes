# Ubuntu 搭建OpenVPN服务器



## 一、介绍

VPN直译译就是虚拟专用通道，是提供给企业之间或者个人与公司之间安全传输的隧道，OpenVPN无疑是Linux下开源VPN的先锋，提供了良好的性能和友好的用户GUI。它大量使用了OpenSSL加密库中的SSLv3/TLSv1协议函数库。

OpenVPN通过使用公开密钥（非对称密钥，加密解密使用不同的Key，一个称为Publice Key，另外一个是Private Key）对数据进行加密的。这种方式称为TLS加密

OpenVPN使用TLS加密的工作过程是，首先VPN Sevrver端和VPN Client端要有相同的CA证书，双方通过交换证书验证双方的合法性，用于决定是否建立VPN连接。

然后使用对方的CA证书，把自己目前使用的数据加密方法加密后发送给对方，由于使用的是对方CA证书加密，所以只有对方CA证书对应的Private Key才能解密该数据，这样就保证了此密钥的安全性，并且此密钥是定期改变的，对于窃听者来说，可能还没有破解出此密钥，VPN通信双方可能就已经更换密钥了。

## 二、条件

1.一台Ubuntu服务器用来搭建OpenVPN服务

2.一台本地计算机用来安装OpenVPN客户端

3.确保两台计算机能够互相ping通

## 三、搭建过程

### 1.安装 OpenVPN 和 Easy-RSA

第一步是安装 OpenVPN 和 Easy-RSA。 Easy-RSA 是一种公钥基础设施 (PKI) 管理工具，用于在OpenVPN 服务器上生成证书请求。然后在 CA 服务器上验证和签名。在这里我们的 OpenVPN 服务器和CA服务器为同一个 Ubuntu 服务器

首先，更新 OpenVPN 服务器的包索引并安装 OpenVPN 和 Easy-RSA。 这两个包都在 Ubuntu 的默认存储库中可用，可以使用 apt 进行安装：

```shell
sudo apt update
sudo apt install openvpn easy-rsa
```


![img](https://img-blog.csdnimg.cn/f3645bbcc7cf44d48d231ae5f4e6d14b.png)openvpn安装完毕后，我们来查看openvpn的版本：

openvpn --version

![img](https://img-blog.csdnimg.cn/9869c96ab5954436aa92525d7e57e31f.png)安装完 easy-rsa 之后我们就可以开始创建 OpenVPN 服务所需要的秘钥了。

### 2.制作相关证书

OpenVPN 的证书分为三部分：CA证书、Server端证书、Client端证书。下面我们通过easy-rsa分别对其进行制作。

#### 2.1制作CA证书

进入 /usr/share/easy-rsa 目录

首先将 vars.example 拷贝一份出来

![img](https://img-blog.csdnimg.cn/0dd444ea7c15437ea39fbdd831ecfc4e.png)

然后编辑 vars 文件

修改下面部分的内容

![img](https://img-blog.csdnimg.cn/cab09e4abeeb4bdfa1cb0e23ae51b693.png)

为

![img](https://img-blog.csdnimg.cn/2ed86835a1d845f0b97da62bf794f5f9.png)

在最后一行添加 KEY_NAME，可以是任何你喜欢的名字，但是请记住，后面制作服务端证书时会用到

![img](https://img-blog.csdnimg.cn/a677e9bb95c242fdbc98fc9ef9cccad7.png)

对于不同版本的easy-rsa，使用方法可能不一样，具体可以参考安装文档的描述

```shell
cat /usr/share/doc/easy-rsa/README.Debian
```


![img](https://img-blog.csdnimg.cn/4ba23851d43f4a42a9075097aa3555c3.png)然后使用以下命令：

```shell
sudo ./easyrsa init-pki
```


![img](https://img-blog.csdnimg.cn/9d45e42f0efc4443a54d2c957856b988.png)会发现多了个pki目录出来

然后再执行第二条命令

![img](https://img-blog.csdnimg.cn/613d4a0051004a7fb2ff5df4b5ec9b91.png)

查看pki目录会发现有一个 ca.crt，这就是CA端证书，ca.crt 包含 CA 的公钥，用于验证服务器和客户端证书的有效性。客户端会使用此证书来验证服务器的身份。

![img](https://img-blog.csdnimg.cn/0cf7f6be7a904a0bafd673f953d6abfe.png)

至此CA端证书制作好了

#### 2.2制作服务端证书

然后用以下命令制作Server端证书（注意改命令中的server要换成前面vars文件中设置的KEY_NAME）：

```shell
sudo ./easyrsa build-server-full server nopass
```


![img](https://img-blog.csdnimg.cn/8d5939bafa8641c29bbe2072872c95c9.png)修改pki目录权限并进入

![img](https://img-blog.csdnimg.cn/78dab5916ece40c79afc104ebee3aae4.png)

查看服务器的证书和私钥：

```shell
sudo ls issued 
sudo ls private
```


至此服务端的证书制作完毕

#### 2.3制作客户端证书

Server端证书制作完成后，开始制作Client端证书，步骤和制作服务端证书类似，注意命令中的client，是客户端的名称，可以换成任意你喜欢的，不要和服务端证书一样就行

```shell
sudo ./easyrsa build-client-full client nopass
```

先制作一个用于给我windows电脑用的

![img](https://img-blog.csdnimg.cn/ded77bd570df4dc389d67ef8a286915d.png)

再次查看 pki/issued/ 和 pki/private/ ,会发现多了windows11的证书和私钥出来

![img](https://img-blog.csdnimg.cn/d3814f6fc201468c8b6e4497f0e6321a.png)

至此客户端的证书制作完毕

2.4创建迪菲·赫尔曼密钥
使用以下命令创建，会生成dh.pem文件

```shell
sudo ./easyrsa gen-dh
```

![img](https://img-blog.csdnimg.cn/625e01a8fc824b5ab8df7039b9926f4d.png)

### ![img](https://img-blog.csdnimg.cn/b53a36ef9f16435282a69573b01e5a54.png)3.配置服务器端配置文件

#### 3.1拷贝相关文件

首先复制一份服务器端配置文件模板 server.conf 到 /etc/openvpn/

```shell
sudo cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz /etc/openvpn/
cd /etc/openvpn/
sudo gzip -d server.conf.gz
```


![img](https://img-blog.csdnimg.cn/b8943f5e0be94e67a9e4c268942536dc.png)然后把刚刚创建好的CA证书、dh.pem、服务端证书/私钥、客户端证书/私钥复制过来，CA证书和dh.pem放在/etc/openvpn目录下，服务端证书/私钥放在server目录下，客户端证书/私钥放在client目录下。

![img](https://img-blog.csdnimg.cn/624d56eb3c4b45b2b3cbf4751d7945f9.png)

#### 3.2编辑server.conf

```shell
sudo vim server.conf
```

配置文件详细说明可以参考这篇：

OpenVPN server端配置文件详细说明-腾讯云开发者社区-腾讯云
https://cloud.tencent.com/developer/article/2348106

我主要做了如下修改：

端口号依然使用1194，传输协议用tcp，注释掉原本的udp

![img](https://img-blog.csdnimg.cn/d59eb485e1d84c7987a04965045ff1ab.png)

修改证书和dh密钥路径

![img](https://img-blog.csdnimg.cn/37d01d83c30c4abbbadc5587d1cb3b9d.png)

修改VPN的IP地址段，也可以不修改，我没做修改

![img](https://img-blog.csdnimg.cn/cf1ca13228084b6ca6fdaf0e60c115da.png)

打开下面选项，让VPN 重定向客户端所有流量

![img](https://img-blog.csdnimg.cn/ae0eb6a105ac4c5cb300016f9885891f.png)

修改下面选项可以使客户端在连接OpenVPN时DNS服务器按照以下设置

![img](https://img-blog.csdnimg.cn/49da874e2932429e976c28fdfd9fe55e.png)

开启后多个客户端可以同时使用一个证书和私钥

![img](https://img-blog.csdnimg.cn/05c66d109e924b13b88afd58fc6f15be.png)

关闭TLS认证

![img](https://img-blog.csdnimg.cn/4f5f017359ff4dcab7cf4b628eed3a80.png)

启用 LZO 压缩算法，以减小通过OpenVPN通道传输的数据量，从而提高性能

![img](https://img-blog.csdnimg.cn/87093881f46644aea9258ca75c483e10.png)

在之前的日志内容后进行追加

![img](https://img-blog.csdnimg.cn/545bceed71dd49cdbfb6a120ee10eaac.png)

关闭“服务器重启，客户端自动重新连接”

![img](https://img-blog.csdnimg.cn/fd2defa4c9f04dc5acb78297b0bb13dc.png)

至此服务端配置文件修改完成。

### 4.运行OpenVPN Server

使用以下命令运行并查看OpenVPN Server

```shell
sudo systemctl start openvpn@server
sudo systemctl status openvpn@server
```


![img](https://img-blog.csdnimg.cn/f9bf5418429947c4a39c270a5059e4b5.png)使用以下命令查看日志

```
sudo tail -f /var/log/openvpn/openvpn.log
```

### ![img](https://img-blog.csdnimg.cn/b60d6d8d1e2142aeb56cdc13ab33de73.png)5.设置防火墙

开启1194和SSH端口并重启ufw防火墙

```shel
sudo ufw allow 1194
sudo ufw allow OpenSSH
sudo ufw disable
sudo ufw enable
```

### 6.Windows客户端配置

在openvpn官网下载客户端：OpenVPN Connect - Client Software For Windows | OpenVPN
https://openvpn.net/client/client-connect-vpn-for-windows/

下载好以后点击运行，然后在右下角状态栏里找到OpenVPN的图标，右键点击，选中选项，在高级里面修改配置文件文件夹路径为 C:\Program Files\OpenVPN\config

然后先在Ubuntu服务器上用chown命令将ca.crt, windows11.crt, windows11.key 的所有者权限改成自己

```shell
sudo chown wt ca.crt client/windows11{.crt,.key}
```

由于我在本地电脑上没有安装ssh服务端，所以无法直接在Ubuntu服务器上使用scp将这三个文件传输到本地电脑，所以在本地电脑执行以下命令将这三个文件远程传输到C:\Program Files\OpenVPN\config，该目录为openvpn客户端的配置文件所在目录

```shell
scp -r wt@114.212.122.48:/etc/openvpn/{ca.crt,client/windows11{.crt,.key}} ./
```


![img](https://img-blog.csdnimg.cn/22bac16d550c4b1aa74950deb4e301bd.png)然后在客户端机器上打开C:\Program Files\OpenVPN\config文件夹，新建一个windows11.ovpn文件，在里面添加如下内容

```ini
client

dev tun

proto tcp

remote 114.212.122.48 1194

resolv-retry infinite

nobind

persist-key

persist-tun

ca ca.crt

cert windows11.crt

key windows11.key

comp-lzo

verb 3
```

保存后关闭

然后连接VPN，显示成功

![img](https://img-blog.csdnimg.cn/b9cf3259288c45b596503825faf826ed.png)

ping OpenVPN服务器的IP，可以ping通

![img](https://img-blog.csdnimg.cn/6677185df6394e97a80343f997b39ba2.png)

### 7.调整 OpenVPN 服务器网络配置

此时已经能够使用OpenVPN的基本功能，但是由于之前我们设置过让VPN重定向客户端的所有流量，导致无法正常访问外部网络

![img](https://img-blog.csdnimg.cn/6769671b4f694450bbe35748a40bee53.png)

此时有几种解决办法

#### 7.1.修改服务端的配置

关掉server.conf 里VPN重定向客户端全部流量的配置

![img](https://img-blog.csdnimg.cn/67397a5774cb4fe7ad46bb429e6fd184.png)

#### 7.2.修改客户端配置

在客户端windows11.ovpn配置文件中，增加下面的内容，第一行表示取消从VPN服务器拉取路由，第二行表示手动指定访问10.8.0.*这个网段的IP才会走VPN

```
route-nopull #don't pull routes from the server
route 10.8.0.0 255.255.255.0 #direct all 192.168.0.* subnet traffic through the VPN
```



#### 7.3配置路由转发和防火墙

##### 7.3.1配置路由转发

要调整 OpenVPN 服务器的默认 转发设置，直接在终端输入命令或者修改/etc/sysctl.conf 文件

方式一：

```shell
sudo sysctl -w net.ipv4.ip_forward=1
```


![img](https://img-blog.csdnimg.cn/aa8540cbc41b4b758a0bcce1e090c578.png)or

方式二：

sudo vim /etc/sysctl.conf，然后在末尾添加以下行

```shell
net.ipv4.ip_forward = 1
```

修改完后保存并关闭文件，然后输入以下命令为当前会话加载新值

```shell
sudo sysctl -p
```

##### ![img](https://img-blog.csdnimg.cn/b5a1a45969fd43e5bbf75373c69c5768.png)7.3.2修改防火墙配置

先输入以下命令查看服务器的公网接口

```shell
ip route list default
```

公网接口是输出中dev后面的字符串，在我的机器里为 eno1![img](https://img-blog.csdnimg.cn/7b8d794e2a834bb4a06b8573ee082ae2.png)

有了默认路由关联的接口后，我们打开 /etc/ufw/before.rules 文件添加相关配置：

```shell
 sudo vim /etc/ufw/before.rules
```

```shell
#
# rules.before
#
# Rules that should be run before the ufw command line added rules. Custom
# rules should be added to one of these chains:
#   ufw-before-input
#   ufw-before-output
#   ufw-before-forward
#
 
# START OPENVPN RULES
# NAT table rules
*nat
:POSTROUTING ACCEPT [0:0]
# Allow traffic from OpenVPN client to eno1 (change to the interface you discovered!)
-A POSTROUTING -s 10.8.0.0/24 -o eno1 -j MASQUERADE
COMMIT
# END OPENVPN RULES
 
# Don't delete these required lines, otherwise there will be errors
*filter
. . .
```

然后需要告诉 UFW 默认情况下也允许转发数据包， 打开并修改 /etc/default/ufw 文件：

```shell
sudo vim /etc/default/ufw
```

找到 DEFAULT_FORWARD_POLICY 指令并将值从 DROP 更改为 ACCEPT：

![img](https://img-blog.csdnimg.cn/bde3c0fa68844da38f16fbb3bb55c48f.png)

重启防火墙

然后重启服务器上的OpenVPN服务

```shell
sudo ufw disable
sudo ufw enable

sudo systemctl restart openvpn@server
```

此时客户端重新连接VPN，会发现可以正常上网了 

![img](https://img-blog.csdnimg.cn/d9e6140d9afd43cfb4b4ef4158b816d0.png)

跳转DNS leak test测试，发现IP已经变成OpenVPN服务器的IP

![img](https://img-blog.csdnimg.cn/d71a8db49d6b42f992223cd7f1e7cd0b.png)

### 8.手机端配置

由于之前开启了“一份证书可以多个客户端使用”，所以手机端上可以也用刚刚制作好的windows11证书和密钥，或者重新制作一份，制作过程和之前的一样，然后使用scp先传输到本地电脑进行编辑

![img](https://img-blog.csdnimg.cn/4adac83cba374d9793f6c042869c03b0.png)

在本地电脑新建一个iphone.ovpn文件，内容和之前的windows11.ovpn大体一致，但是由于手机端只能上传.ovpn格式的文件，需要将ca.crt、iphone.crt、iphone.key这几个文件打包进iphone.ovpn里

```shell
 
client
 
dev tun
 
proto tcp
 
remote 114.212.122.48 1194
 
resolv-retry infinite
 
nobind
 
persist-key
 
persist-tun
 
## 删除下面注释掉的内容
# ca ca.crt

# cert windows11.crt
 
# key windows11.key
 
comp-lzo
 
verb 3
 
## 增加下面的内容
<ca>
...  # 把ca.crt里的内容复制过来
</ca>
 -----BEGIN CERTIFICATE-----
MIIDVDCCAjygAwIBAgIQbHREwWb6dq/ilmxefBUcATANBgkqhkiG9w0BAQsFADAW
MRQwEgYDVQQDDAtFYXN5LVJTQSBDQTAeFw0yMzEyMTUwMTI2MTRaFw0yNjAzMTkw
MTI2MTRaMBExDzANBgNVBAMMBmNsaWVudDCCASIwDQYJKoZIhvcNAQEBBQADggEP
ADCCAQoCggEBAM9BuF/UKoAscM6FFCYTx4hHhknf2577gQndOcfGVLzT2YxE8+mp
+XVJ4akHbtN4DX17Q4nds155CtlZ3h9XS1UF4hW8QxFQWzVKXi+KbUqgsXSPC/Qk
jazKOHbYAz00qFrVJruxgIa4ct4zg9pBNxSQiRqGjFE/ii6Dwj/gOw+ieb/rUB2I
A4+rCmK/FL8S/0ZUdtZ7XMcoXDvBUoq9Wz4cMAE+bG2RP2MwgVq2gxPY9DaxS86l
5HpBFSQ+dNalHTRYmHA38IaNQOd2RsPXnTglFiFFE5Tyar9QWAYlPPEk0kcwkr/q
Jf2RLLsCPdTb08bBlPqM4CQkar1ox3P4ms0CAwEAAaOBojCBnzAJBgNVHRMEAjAA
MB0GA1UdDgQWBBQrvdDoOoevJnCkSvqALd+uaYpdFDBRBgNVHSMESjBIgBQvsnK/
p4PCX00qK7MJeLnZOPoHN6EapBgwFjEUMBIGA1UEAwwLRWFzeS1SU0EgQ0GCFGDX
/ay4w2MmuuF/5qGusQC8ZDbIMBMGA1UdJQQMMAoGCCsGAQUFBwMCMAsGA1UdDwQE
AwIHgDANBgkqhkiG9w0BAQsFAAOCAQEAIWmtQ8BUVyI4vifj4Z62E2rMQrJAl0Tt
wf8Z2gJwEWCgE4lh8u5MHA7ZCkD9zVr74OXgklSi8oxKmkJ8mE8M6fVoc6ekcygd
AU43wJUqjMicaDqn4HslIKnzq+zFVlQ8o1zqgNU/Wwphb4waLoSVFQioeJXEVNZV
3LVGX83JVDwd2GELrOV9/zdI1vZlyn4PPUTGmUHwZwYYtLfslRomTWfvbEXQcdZK
yLt6fhhOpF7IWJFsA9hhCl2Va/Z0/N6C5YFdtV9OAT+pqjJUVFOnTQreL/u4nbF+
b7S3AsSWkEnN06e5jZQPr43DpoJRttWM1+bHCZjwKULWeCjYQEFvZg==
-----END CERTIFICATE-----
<cert>
...  # 把iphone.crt里的内容复制过来
</cert>
 
<key>
...  # 把iphone.key里的内容复制过来
</key>
```





然后将修改好的iphone.ovpn文件用邮箱发送到手机上，然后选择其他应用 → OpenVPN 打开（苹果的OpenVPN需要在美区苹果商店下载，国区没有，安卓的请同学们自行百度）

![img](https://img-blog.csdnimg.cn/12ec07e1dd1b4d68a64069d5e7594d72.png)

然后跳转到OpenVPN APP，点击Add→Connect，显示以下界面即为连接成功

![img](https://img-blog.csdnimg.cn/dff775a7868b4b618f002f5ebad64fb1.png)

搜索what is my ip检查，发现IP也变成了服务器IP，且可以正常上网

![img](https://img-blog.csdnimg.cn/424237b434da400c9dca61e763b51346.png)

### 9.卸载OpenVPN

（以下内容来自ChatGPT）

在Ubuntu上卸载OpenVPN服务器可以通过以下步骤进行。请注意，这将删除OpenVPN服务器及其相关组件，确保您不再需要它们。

#### 9.1.停止OpenVPN服务：

打开终端，然后停止OpenVPN服务。可以使用以下命令：

```
sudo systemctl stop openvpn@<server-config-file>
<server-config-file> 是您实际使用的OpenVPN服务器配置文件的名称。
```



#### 9.2.卸载OpenVPN软件包：

使用以下命令卸载OpenVPN软件包：

```
sudo apt-get remove openvpn
```

如果您还安装了easy-rsa（用于生成证书和密钥的工具），也可以卸载它：

```
sudo apt-get remove easy-rsa
```



#### 9.3.删除配置文件和相关文件：

删除OpenVPN服务器的配置文件和其他相关文件。这包括您自定义的配置文件和证书文件。请小心，确保您不再需要这些文件。

#### 9.4.清理配置目录：

如果您使用的是默认的OpenVPN配置目录，可以执行以下命令来清理相关文件：

```
sudo rm -r /etc/openvpn
```

9.5.清理Easy-RSA目录（如果安装了）：
如果您安装了easy-rsa，清理相关的目录：

```
sudo rm -r /usr/share/easy-rsa/
```



#### 9.6.重新加载系统服务：

在进行更改后，重新加载系统服务：

```
sudo systemctl daemon-reload
```



#### 9.7.清理残留的依赖项：

为了确保删除了所有与OpenVPN相关的依赖项，您可以运行：

```
sudo apt-get autoremove
```

这将自动删除不再需要的依赖项。

请注意，在执行这些步骤之前，请确保您不再需要OpenVPN服务器，并且您备份了任何重要数据或配置文件。

10.参考
————————————————
版权声明：本文为CSDN博主「苹果挨炮」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/m0_56015193/article/details/134590283
原因定位过程：
1、一开始无论如何up都无法将网卡up起来，后来查看netwrking服务发现报错RTNETLINK answers: File exists。

2、对于后者，一开始没找到Ubuntu的原因和解决办法，但是看到了linux的，可以看看这个链接。

链接里主要有两种情况，其中第二种情况，Ubuntu没这个文件，先不考虑，先看第一个，提到和 NetworkManager 服务有冲突，这个确实是有的，确实需要关闭：
service NetworkManager stop

3、然后这个时候去看networking或者重启该服务后查看，会有报错，提示没有/etc/resolv.conf文件，手动创建一个，然后再重启服务，networking服务就正常了。

4、服务正常后，使用ip a查看对应网卡是否进入了DOWN的状态，如果是的话，那就好办了，直接ifconfig eth0 up，其中eth0为网卡名。

注意：如果/etc/network/interfaces没配置IP，可能也会导致网卡还是UNKNOWN的状态。

解决步骤：
由于我的机器是BMC，每个节点都有这样的问题，以下是我进每个节点的操作步骤：

```shell
原因定位过程：
1、一开始无论如何up都无法将网卡up起来，后来查看netwrking服务发现报错RTNETLINK answers: File exists。

2、对于后者，一开始没找到Ubuntu的原因和解决办法，但是看到了linux的，可以看看这个链接。

链接里主要有两种情况，其中第二种情况，Ubuntu没这个文件，先不考虑，先看第一个，提到和 NetworkManager 服务有冲突，这个确实是有的，确实需要关闭：
service NetworkManager stop

3、然后这个时候去看networking或者重启该服务后查看，会有报错，提示没有/etc/resolv.conf文件，手动创建一个，然后再重启服务，networking服务就正常了。

4、服务正常后，使用ip a查看对应网卡是否进入了DOWN的状态，如果是的话，那就好办了，直接ifconfig eth0 up，其中eth0为网卡名。

注意：如果/etc/network/interfaces没配置IP，可能也会导致网卡还是UNKNOWN的状态。

解决步骤：
由于我的机器是BMC，每个节点都有这样的问题，以下是我进每个节点的操作步骤：

1、进入`/etc/network/interfaces`配置IP（配了的可忽略该步）。

2、删除`/etc/resolv.conf`文件。
> rm -rf /etc/resolv.conf

3、停止NetworkManger服务
> service NetworkManager stop

4、重新创建resolv.conf文件(也可以放到第5步之后，然后再多重启一次服务)
> touch  /etc/resolv.conf

5、重启networking服务
> systemctl restart networking/service networking restart

6、`ip a`查看网卡是否已经不是UNKNOWN的状态，而是DOWN的状态，如果是，则up起来
> ifconfig eth0 up

另外，如果这个IP在另一个节点上用过，注释后最好重启节点；还有当前节点，如果网卡和IP关系反复修改，也建议重启，否则可能不生效。

```



# entos7中修改inttab文件不起效的问题

![img](https://csdnimg.cn/release/blogv2/dist/pc/img/reprint.png)

[weixin_33845881](https://blog.csdn.net/weixin_33845881)![img](https://csdnimg.cn/release/blogv2/dist/pc/img/newCurrentTime2.png)于 2017-04-25 12:55:47 发布![img](https://csdnimg.cn/release/blogv2/dist/pc/img/articleReadEyes2.png)368![img](https://csdnimg.cn/release/blogv2/dist/pc/img/tobarCollect2.png) 收藏

文章标签： [人工智能](https://so.csdn.net/so/search/s.do?q=人工智能&t=blog&o=vip&s=&l=&f=&viparticle=)

版权

关于centos7中修改inittab文件来改变默认运行级别无效的问题

首先要说说在centos6之前控制进程文件是/etc/inittab文件,这是因为之前的系统采用init进程（sys v init），根据/etc/rc.*d里面的内容（运行级别）来启动和控制服务，它是基于运行级别的进程；之后又诞生了upstart init，它又比sysvinit要高级一点，它是基于时间驱动的，也就是有事件，才会去打开相应的服务，这样就比直接全部打开所有服务要好的多了，加快了系统启动时间！它兼容sysv init。接下来centos7中采用了最新的系统管理软件systemd，它和init不一样，这样就导致我们修改inittab文件的时候没有奏效！

 

接着说说systemd中的“runlevel”与之前的运行级别的差异

运行级别（runlevel）是一个旧的概念。现在使用systemd引入了一个和运行级别相似的但不同的概念—目标（target）。它不像数字表示的运行级别，每个目标都有自己的名字和独特的功能，并且能够同时启用多个。一些目标会继承其他目标的服务，并开启新的服务。

Systemd用比sysvinit的运行级更为自由的target概念代替

第3运行级用multi-user.target代替

第5运行级用graphical.target代替

详细比较见下表

Systemd目标含义

| Sysv运行级别 | Systemd目标                                         | 解释                                    |
| ------------ | --------------------------------------------------- | --------------------------------------- |
| 0            | runlevel0.target，poweroff.target                   | 中断系统（halt）                        |
| 1            | runlevel1.target，rescue.target                     | 单用户模式                              |
| 2,4          | runlevel2.target,runlevel4.target,multi-user.target | 用户自定义运行级别，通常识别为运行级别3 |
| 3            | runlevel3，multi-user.target                        | 多用户，字符界面                        |
| 5            | runlevel5，graphical.target                         | 多用户，图形界面                        |
| 6            | runlevel6，reboot.target                            | 重启                                    |
| emergency    | emergency.target                                    | 救援模式                                |

 

runlevel3.target和runlevel5.target分别指向multi-user.target和graphical.target这样做的目的就是为了让以前的用户能很好的接受现在的改变，同时，现在的改变赋予了语义，根据名字就可以看出该级别下面能做什么和不能做什么了。



看看runlevel*都是软连接

```
[root@localhost ~]# ll/lib/systemd/system/runlevel?.target
lrwxrwxrwx. 1 root root 15 Mar  9 04:43 /lib/systemd/system/runlevel0.target ->poweroff.target
lrwxrwxrwx. 1 root root 13 Mar  9 04:43 /lib/systemd/system/runlevel1.target-> rescue.target
lrwxrwxrwx. 1 root root 17 Mar  9 04:43 /lib/systemd/system/runlevel2.target-> multi-user.target
lrwxrwxrwx. 1 root root 17 Mar  9 04:43 /lib/systemd/system/runlevel3.target-> multi-user.target
lrwxrwxrwx. 1 root root 17 Mar  9 04:43 /lib/systemd/system/runlevel4.target-> multi-user.target
lrwxrwxrwx. 1 root root 16 Mar  9 04:43 /lib/systemd/system/runlevel5.target-> graphical.target
lrwxrwxrwx. 1 root root 13 Mar  9 04:43 /lib/systemd/system/runlevel6.target-> reboot.target
```

接着试试使用命令切换运行级别（目标）

```
[root@localhost ~]#systemctl isolatemulti-user.target 切换到运行级别3
[root@localhost ~]#systemctl isolaterunlevel3.target 切换到运行级别3
[root@localhost ~]#systemctl isolategraphical.target 切换到运行级别5
[root@localhost ~]#systemctl isolaterunlevel5.target 切换到运行级别5
```

注意的是：runlevel还是可以使用，但是systemd不使用/etc/inittab文件，修改/etc/inittab文件不会更改默认的运行级别。所以严格的说不再有运行级别了。所谓默认的运行级别，指的就是/etc/systemd/system/default.target文件，查看该文件发现它是一个软连接

```
[root@localhost ~]# ll/etc/systemd/system/default.target
lrwxrwxrwx. 1 root root 36 Mar  9 05:14 /etc/systemd/system/default.target-> /lib/systemd/system/graphical.target
##发现它是一个连接到/lib/systemd/system目录下graphical.target的软连接，
##所以默认启动就是级别5图形界面。所以要修改默认的运行级别时，
##就不能再使用修改/etc/inittab的方法了，而是建立软连接的方法。
```

 

到重点了，修改默认的运行级别

方法一：设置默认启动为字符界面，创建一个3级别的目标，连接到默认启动的配置文件中去

```
#ln –sf /lib/systemd/system/multi-user.target   /etc/systemd/system/default.target
```

设置默认启动为图形界面，创建一个5级别的目标，连接到默认启动的配置文件中去

```
#ln –sf /lib/systemd/system/graphical.target   /etc/systemd/system/default.target
```

开机时，系统使用的配置文件是/etc/systemd/system/default.target，该文件默认是连接到

graphical.target文件（大致相当于原来的运行级别5）。

 

方法二：也可以执行systemctl命令，设置启动时默认进入文件模式或图像模式

```
[root@localhost ~]#systemctl enable multi-user.target         字符界面
[root@localhost ~]#systemctl enablegraphical.target  图形界面
```

该命令执行情况由systemctl显示：该方法当且仅当目标配置文件中有如下内容时有效：

[Install]

Alias=default.target

目前/lib/systemd/system/multi-user.target、/lib/systemd/system/graphcal.target文件中都有。

 

转载于:https://blog.51cto.com/11939788/1919175
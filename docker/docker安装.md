# CENTOS安装docker教程

原文地址：https://blog.csdn.net/qq_43721032/article/details/119134118

## 教程内容

CentOS 7.9下安装Docker及常用镜像
本文档为在Centos 7.9下安装Docker及常用镜像的指导文件。

一、安装Docker
1、环境准备
操作系统版本为centos 7.9，内核版本需要在3.10以上，需要保障能够连通互联网，为了避免安装过程中出现网络异常建议关闭linux的防火墙（生产环境下不要关闭防火墙，可根据实际情况设置防火墙出入站规则）。

```shell
#查看内核版本
sudo uname -r
#查看系统版本
sudo cat /etc/redhat-release
#关闭防火墙
sudo systemctl stop firewalld
#禁用防火墙开机自启
sudo systemctl disable firewalld
```

2、卸载依赖（若未安装过Docker可跳过此步骤）

```shell
#卸载Docker相关依赖
sudo yum remove docker  
                  docker-client 
                  docker-client-latest 
                  docker-common 
                  docker-latest 
                  docker-latest-logrotate 
                  docker-logrotate 
                  docker-engine
```

3、安装工具包并设置仓库。

```shell
#安装工具包
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
#设置yum仓库
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

4、安装Docker

```shell
#通过yum安装Docker
sudo yum -y install docker-ce docker-ce-cli containerd.io
```

```
#启动Docker
sudo systemctl start docker
#设置Docker开机自启
sudo systemctl enable docker
#查看Docker版本
sudo docker version
```

5、配置镜像加速
Docker 从 Docker Hub 拉取镜像，因为是从国外获取，所以速度较慢，有时会出现无法拉取镜像的情况，可以通过配置国内镜像源的方式，从国内获取镜像，提高拉取速度。这里介绍中国科学技术大学（LUG@USTC）的开源镜像：https://docker.mirrors.ustc.edu.cn和网易的开源镜像：http://hub-mirror.c.163.com。

USTC 是老牌的 Linux 镜像服务提供者了，USTC 的 Docker 镜像加速服务速度很快。USTC 和网易的优势之一就是不需要注册，属于真正的公共服务。（也可以使用阿里等其他服务商的镜像加速服务）

~~~shell
#编辑文件
sudo vi  /etc/docker/daemon.json
```

#在文件中输入以下内容并保存
{
"registry-mirrors": ["http://hub-mirror.c.163.com", "https://docker.mirrors.ustc.edu.cn"]
}

# 重新加载某个服务的配置文件

sudo systemctl daemon-reload

# 重新启动 Docker

sudo systemctl restart docker
~~~

6、开启远程访问

```shell

编辑Docker服务器上对应的配置文件
vi /usr/lib/systemd/system/docker.service

找到以ExecStart开头的行，在该行的末尾添加内容 -H tcp://0.0.0.0:2375 添加完成后保存文件。
重启Docker

sudo systemctl daemon-reload
sudo service docker restart

```

重启完成后可通过浏览器访问http://Docker主机IP:2375/version将输出Docker版本信息，若无法访问请检查防火墙设置。
————————————————
版权声明：本文为CSDN博主「lailai　」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_43721032/article/details/119134118


#  CentOS 7.9 安装如何 k8s

#### 🐤安装步骤

- [🐶安装前准备事项](https://blog.csdn.net/qq_39017153/article/details/134807469#_1)
- [🐕安装docker](https://blog.csdn.net/qq_39017153/article/details/134807469#docker_8)
- - [🐕‍🦺删除docker](https://blog.csdn.net/qq_39017153/article/details/134807469#docker_10)
  - [🐕‍🦺安装yum工具](https://blog.csdn.net/qq_39017153/article/details/134807469#yum_15)
  - [🐕‍🦺设置docker镜像源](https://blog.csdn.net/qq_39017153/article/details/134807469#docker_21)
  - [🐕‍🦺安装指定版本docker](https://blog.csdn.net/qq_39017153/article/details/134807469#docker_29)
  - [🐕‍🦺设置开启自启](https://blog.csdn.net/qq_39017153/article/details/134807469#_34)
  - [🐕‍🦺阿里云镜像加速](https://blog.csdn.net/qq_39017153/article/details/134807469#_40)
- [🐕准备环境](https://blog.csdn.net/qq_39017153/article/details/134807469#_53)
- [🐕安装kubelet、kubeadm、kubectl](https://blog.csdn.net/qq_39017153/article/details/134807469#kubeletkubeadmkubectl_97)
- - [🐕‍🦺初始化master节点](https://blog.csdn.net/qq_39017153/article/details/134807469#master_132)
  - [🐕‍🦺安装网络插件calico](https://blog.csdn.net/qq_39017153/article/details/134807469#calico_164)
  - [🐕‍🦺work 加入集群](https://blog.csdn.net/qq_39017153/article/details/134807469#work__185)
- [🐕k8s集群测试](https://blog.csdn.net/qq_39017153/article/details/134807469#k8s_200)



## 🐶安装前准备事项

------

> - 一台或多台机器，操作系统 CentOS7.x-86_x64
> - 硬件配置：2GB或更多RAM，2个CPU或更多CPU，硬盘30GB或更多
> - 可以访问外网，需要拉取镜像，如果服务器不能上网，需要提前下载镜像并导入节点
> - 最好保证服务都是同一网络环境下（我自己这边使用的云服务的网络安全组）
> - 没有标注为需要那台机器执行为`全部机器执行`或者`任意机器执行`

## 🐕安装docker

------

### 🐕‍🦺删除docker

------

```sh
yum remove docker
sudo yum install -y yum-utils
sudo yum-config-manager \
--add-repo \
http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
sudo yum install -y docker-ce-20.10.7 docker-ce-cli-20.10.7 containerd.io-1.4.6
systemctl enable docker --now
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<EOF
{
  "registry-mirrors": ["https://soxxp5r5.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
# 将 SELinux 设置为 permissive 模式（相当于将其禁用）
sudo setenforce 0
# 永久禁用
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

# 关闭swap  临时
swapoff -a
# 永久
sed -ri 's/.*swap.*/#&/' /etc/fstab  

# 将桥接的IPv4流量传递到iptables的链 这个是k8s官网的步骤
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

# 关闭防火墙
systemctl stop firewalld
systemctl disable firewalld

sysctl --system  # 生效
#配置k8s的yum源地址
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
   http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

#安装 kubelet，kubeadm，kubectl
sudo yum install -y kubelet-1.20.9 kubeadm-1.20.9 kubectl-1.20.9

#启动kubelet
sudo systemctl enable --now kubelet

#所有机器配置master域名 ,修改hosts
echo "192.168.10.111  k8s-master" >> /etc/hosts
cat >> /etc/hosts << EOF
192.168.10.112   k8s-node2
192.168.10.113   k8s-node3
EOF
```

### 🐕‍🦺安装yum工具

------

```sh
sudo yum install -y yum-utils
```

### 🐕‍🦺设置docker镜像源

------

```bash
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

### 🐕‍🦺安装指定版本docker

------

```bash
sudo yum install -y docker-ce-20.10.7 docker-ce-cli-20.10.7 containerd.io-1.4.6
```

### 🐕‍🦺设置开启自启

------

```sh
systemctl enable docker --now
```

### 🐕‍🦺阿里云镜像加速

------

```sh
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<EOF
{
  "registry-mirrors": ["https://soxxp5r5.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 🐕准备环境

------

> 每个机器ip互通
> 每台机器有自己的hostname 不能使用losthost

| hostName   | IP            |
| ---------- | ------------- |
| k8s-master | 192.168.0.71  |
| k8s-node1  | 192.168.0.138 |
| k8s-node2  | 192.168.0.154 |

```sh
# 根据规划设置主机名
hostnamectl set-hostname <hostname>
```

> 其他机器跟着一样设置即可
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/2afe7ac0f67b44018bab0dfe1664c3d0.png)

```sh
# 将 SELinux 设置为 permissive 模式（相当于将其禁用）
sudo setenforce 0
# 永久禁用
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

# 关闭swap  临时
swapoff -a
# 永久
sed -ri 's/.*swap.*/#&/' /etc/fstab  

# 将桥接的IPv4流量传递到iptables的链 这个是k8s官网的步骤
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

# 关闭防火墙
systemctl stop firewalld
systemctl disable firewalld

sysctl --system  # 生效
```

## 🐕安装kubelet、kubeadm、kubectl

------

```sh
#配置k8s的yum源地址
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
   http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

#安装 kubelet，kubeadm，kubectl
sudo yum install -y kubelet-1.20.9 kubeadm-1.20.9 kubectl-1.20.9

#启动kubelet
sudo systemctl enable --now kubelet

#所有机器配置master域名 ,修改hosts
echo "172.16.1.11  k8s-master" >> /etc/hosts
cat >> /etc/hosts << EOF
172.16.1.11   k8s-master
172.16.1.12   k8s-node2
172.16.1.13   k8s-node3
EOF

```

### 🐕‍🦺初始化master节点

------

> 以下步骤在master执行即可 `一定要注意网段不能重复`
> 我这边服务网段就是`192.168.0.0/16`,所以`-pod-network-cidr=172.31.0.0/16`使用这个网段,后面用网络插件是[calico](https://so.csdn.net/so/search?q=calico&spm=1001.2101.3001.7020),默认网段是`192.168.0.0/16`,需修改其对应[配置文件](https://so.csdn.net/so/search?q=配置文件&spm=1001.2101.3001.7020)

```sh
kubeadm init \
--apiserver-advertise-address=172.16.1.11 \
--control-plane-endpoint=k8s-master \
--image-repository registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images \
--kubernetes-version v1.20.9 \
--service-cidr=173.20.0.0/16 \
--pod-network-cidr=173.21.0.0/16

```

> ```
> 注:这里只是告诉你有这个命令,不是让你执行.` 清除 kubeadm init `kubeadm reset -f
> ```

> - 出现`Your Kubernetes control-plane has initialized successfully!`即为成功,但是需要记录master执行完成后的日志
>   ![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/74affeef28164f53bf3ca8968730fe52.png)

```sh
#配置 kubectl
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
#提前保存令牌
kubeadm join k8s-master:6443 --token afb6st.b7jz45ze7zpg65ii \
    --discovery-token-ca-cert-hash sha256:e5e5854508dafd04f0e9cf1f502b5165e25ff3017afd23cade0fe6acb5bc14ab
    
12345678
```

- 使用命令查看主节点`kubectl get nodes`
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/0e64177c6d094aa5a682788d5ee482b8.png)

### 🐕‍🦺安装网络插件calico

------

```sh
# 下载配置文件
curl https://docs.projectcalico.org/v3.20/manifests/calico.yaml -O
```

> - 修改配置文件,我们前面不是说了calico的默认网段是`192.168.0.0/16`么,所以我们需要修改它
> - 使用命令`cat calico.yaml |gerp 192.168`查看 这里需要把我们这里的网段修改为我们刚才设置
>   ![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/1a773458d6ad4d599dd214a887ee5326.png)
>   ![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/e3be16292fc74951bbdd910ee7523fb7.png)

```sh
# 修改完成后我们开始启动
kubectl apply -f calico.yaml
#查看状态，等待就绪
watch kubectl get pod -n kube-system -o wide

```

> 耐心等待所有的完成即可
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/e091472647e04dd798594fb891bb1e59.png)
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/a80f78ecf85d42fb87f16739b4e24e11.png)

### 🐕‍🦺work 加入集群

------

```powershell
#使用刚才master打印的令牌命令加入
kubeadm join k8s-master:6443 --token afb6st.b7jz45ze7zpg65ii \
    --discovery-token-ca-cert-hash sha256:e5e5854508dafd04f0e9cf1f502b5165e25ff3017afd23cade0fe6acb5bc14ab
#如果超过2小时忘记了令牌，可以这样做
kubeadm token create --print-join-command #打印新令牌
kubeadm token create --ttl 0 --print-join-command #创建个永不过期的令牌
1234567
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/65c8c17f8d5d4632b8a51345d33eb723.png)

> 这样就是已经安装完成了

## 🐕k8s集群测试

------

```sh
#创建一个nginx镜像
kubectl create deployment mynginx --image=nginx
```

> 查看[nginx](https://so.csdn.net/so/search?q=nginx&spm=1001.2101.3001.7020)运行在哪个节点`kubectl get pods,svc -0 wide`

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/257ead7fa7434609a149704412b477fe.png)

> 任意节点访问`curl http://172.31.169.129`
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ada9f1bc4d6f472daaf833c8ea9a5ed9.png)
> 这里可以看到可以访问成功

# Kubernetes dashboardv2.7.0安装指南：从零开始搭建可视化界面

[![栈江湖](https://pica.zhimg.com/v2-f88115f7df1b9e05749ffb11e7b9a43c_l.jpg?source=172ae18b)](https://www.zhihu.com/people/hankzhou007)



## 一、K8S管理控制台

Kubernetes Web UI（或Kubernetes Dashboard）是用于管理和监视Kubernetes集群的不同工具和用户界面。以下是一些常见的Kubernetes Web UI工具和用户界面：

1. Kubernetes Dashboard: Kubernetes官方提供的Web用户界面，用于管理和监视Kubernetes集群中的各种资源。它是最常见和广泛使用的Kubernetes Web UI。
2. KubeSphere: KubeSphere是一个开源的容器化应用管理平台，提供了一个Web UI，用于创建、部署和管理容器化应用程序，以及监视和调优Kubernetes集群。
3. Rancher: Rancher是一个用于管理和操作Kubernetes、Docker和其他容器编排引擎的平台。它提供了一个直观的Web界面，支持多个Kubernetes集群的管理。
4. Octant: Octant是一个开源的Kubernetes Web UI工具，它提供了直观的集群资源查看和交互式探索功能，可以帮助开发人员更容易地理解和调试他们的应用程序。
5. Lens: Lens是一个强大的开源Kubernetes IDE，提供了一个跨平台的桌面应用程序，用于管理和监视Kubernetes集群。它支持多个集群、多个命名空间和内置的CLI终端。
6. Kubernetes Web View: Kubernetes Web View是一个轻量级的开源Web UI，用于查看和导航Kubernetes集群中的资源。它的设计简单，适用于快速查看集群状态。
7. K9s: 虽然不是传统的Web UI，但K9s是一个基于终端的TUI（文本用户界面）工具，用于管理和监视Kubernetes集群。它提供了强大的命令行交互性能。
8. Supergiant: Supergiant是一个用于部署、管理和监视Kubernetes集群的平台。它提供了一个Web界面，用于自动化Kubernetes基础设施。

## 二、Kubernetes-Dashboard v2.7.0 (我的需要装v2.3.1)

Kubernetes Dashboard 是 Kubernetes 的官方 Web UI。它提供了集群的详细信息和管理功能。以下是安装和使用 Kubernetes Dashboard 的步骤：

安装前需要先选择与你安装的Kubernetes对应版本的Dashboard，不然会出现各种问题。每个releases都会有一张对应表，如下图：

![img](https://pic2.zhimg.com/80/v2-ca03fa8f3efde0d4ccff7828d88c9839_1440w.jpg)



[https://github.com/kubernetes/dashboard/releases](https://link.zhihu.com/?target=https%3A//github.com/kubernetes/dashboard/releases)

### 步骤 1：安装Dashboard

首先，您需要安装 Kubernetes Dashboard。执行以下命令：

```text
wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

为了可以在集群外面访问，我们把recommended.yaml里访问方式调整为nodeport



![img](https://pic2.zhimg.com/80/v2-21386ebdad0215e1dd964509aad28d4d_1440w.jpg)



找到这一段，大约在30行左右，特点是：



![img](https://pic2.zhimg.com/80/v2-3f5c9ae33e66aaf3d2d27a941ef1952d_1440w.webp)



kind: Service

k8s-app: kubernetes-dashboard

增加一行，type=NodePort



![img](https://pic1.zhimg.com/80/v2-6d7dc04ea743c510bf184b5b496a8b08_1440w.webp)



再执行apply部署 Kubernetes-Dashboard v2.7.0。

```text
kubectl apply -f recommended.yaml
```

执行后会卡很长时间，主要是在下载docker镜像，从配置文件可以看到是以下两个镜像，如果发现最后下载出问题也可以单独的docker pull下面两镜像。

kubernetesui/dashboard:v2.7.0

kubernetesui/metrics-scraper:v1.0.8



![img](https://pic3.zhimg.com/80/v2-2e4ac424463edb59cb2263a4a2427a4e_1440w.webp)



### 步骤 2：创建 Dashboard 用户

Dashboard 默认启用了令牌认证，因此您需要创建一个用户帐户来登录。首先，创建一个 YAML 文件（例如 dashboard-adminuser.yaml）：

```text
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: admin-user
    namespace: kubernetes-dashboard
```

然后，通过以下命令创建用户：

```text
kubectl apply -f dashboard-adminuser.yaml
```

### 步骤 3：获取令牌

要获取登录到 Dashboard 所需的令牌，请运行以下命令：

```text
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
```

这将显示一个长令牌字符串，将其复制以备用。

### 步骤 4：查看端口

```text
kubectl get pod,svc -n kubernetes-dashboard
```



![img](https://pic3.zhimg.com/80/v2-83a6b94aa12777a6f6e3101945d24aea_1440w.webp)



这样我们通过主机的ip+30081就可以访问dashboard了。下面用的ip是主机的ip，并不是上面出现的cluster-ip，cluster-ip是集群内部访问的ip。



![img](https://pic2.zhimg.com/80/v2-85376b4830cc918d00ba57d4554d6675_1440w.webp)





![img](https://pic1.zhimg.com/80/v2-56accdfc352b5c605b7cbbd5ff537594_1440w.webp)





总结：总体来说dashboard安装还是比较简单，但如果你安装的是新版本，感觉还是会出现不少问题，还是得把版本控制好。
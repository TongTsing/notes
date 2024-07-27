以下是 Kubernetes 各个配置文件的详细注释示例，帮助新手更好地理解每个配置项的作用和用法。

### 1. kube-apiserver 配置文件/etc/kubernetes/` 目录下，通常命名为 `apiserver.conf` 或者 `kube-apiserver.conf
kube-apiserver 是 Kubernetes 集群的 API 服务器，负责处理 API 请求。常见的配置参数包括认证、授权、etcd 连接等。

```bash
kube-apiserver \
  --advertise-address=<Master Node IP> \ # 用于在集群内通告的地址，即其他组件访问 API Server 的地址
  --etcd-servers=http://<etcd-endpoint>:2379 \ # etcd 集群的地址，用于存储集群状态数据
  --authorization-mode=Node,RBAC \ # 授权模式，Node 和 RBAC 两种模式
  --client-ca-file=/etc/kubernetes/pki/ca.crt \ # 客户端 CA 证书文件，用于验证客户端证书
  --tls-cert-file=/etc/kubernetes/pki/apiserver.crt \ # API Server 的 TLS 证书文件
  --tls-private-key-file=/etc/kubernetes/pki/apiserver.key \ # API Server 的 TLS 私钥文件
  --service-cluster-ip-range=10.96.0.0/12 \ # 集群内部服务的 IP 范围
  --runtime-config=api/all=true # 启用所有 API 版本
```

### 2. kube-scheduler 配置文件：/etc/kubernetes/` 目录下，通常命名为 `scheduler.conf` 或者 `kube-scheduler.conf
kube-scheduler 负责为未绑定的 Pod 选择合适的节点。可以通过配置文件或命令行参数指定调度策略。

```yaml
apiVersion: kubescheduler.config.k8s.io/v1beta1
kind: KubeSchedulerConfiguration
clientConnection:
  kubeconfig: "/etc/kubernetes/scheduler.conf" # kube-scheduler 连接 API Server 的 kubeconfig 文件
leaderElection:
  leaderElect: true # 启用 leader 选举机制，确保在高可用环境中只有一个 scheduler 实例在运行
```

### 3. kube-controller-manager 配置文件
kube-controller-manager 运行控制器进程，负责维护集群的状态（如副本数量、终止状态等）。

```yaml
apiVersion: kubecontrollermanager.config.k8s.io/v1alpha1
kind: KubeControllerManagerConfiguration
clientConnection:
  kubeconfig: "/etc/kubernetes/controller-manager.conf" # kube-controller-manager 连接 API Server 的 kubeconfig 文件
leaderElection:
  leaderElect: true # 启用 leader 选举机制，确保在高可用环境中只有一个 controller-manager 实例在运行
```

### 4. kubelet 配置文件 /etc/kubernetes/kubelet.conf` 或者 `/etc/default/kubelet
kubelet 运行在每个节点上，负责管理节点上的 Pod 和容器。

```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
authentication:
  anonymous:
    enabled: false # 禁用匿名访问
  webhook:
    enabled: true # 启用 webhook 认证
  x509:
    clientCAFile: "/etc/kubernetes/pki/ca.crt" # 用于验证客户端证书的 CA 证书文件
authorization:
  mode: Webhook # 使用 webhook 进行授权
clusterDomain: "cluster.local" # 集群内部的 DNS 域名
clusterDNS:
  - "10.96.0.10" # 集群 DNS 服务的 IP 地址
```

### 5. kube-proxy 配置文件：/etc/kubernetes/kube-proxy.conf
kube-proxy 负责在每个节点上维护网络规则，提供集群内部的服务发现和负载均衡。

```yaml
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
clientConnection:
  kubeconfig: "/var/lib/kube-proxy/kubeconfig.conf" # kube-proxy 连接 API Server 的 kubeconfig 文件
mode: "iptables" # 使用 iptables 模式来处理服务的网络规则
clusterCIDR: "10.244.0.0/16" # 集群的 Pod 网络地址范围
```

### 6. etcd 配置文件
etcd 是 Kubernetes 的分布式键值存储，存储集群的所有数据。

位于/etc/etcd/etcd.conf

```yaml
name: etcd-node1 # etcd 节点的名称
data-dir: /var/lib/etcd # 存储 etcd 数据的目录
initial-advertise-peer-urls: http://<NODE1_IP>:2380 # 向其他 etcd 节点通告的地址
listen-peer-urls: http://<NODE1_IP>:2380 # 监听来自其他 etcd 节点的连接
listen-client-urls: http://<NODE1_IP>:2379,http://127.0.0.1:2379 # 监听客户端连接的地址
advertise-client-urls: http://<NODE1_IP>:2379 # 向客户端通告的地址
initial-cluster-token: etcd-cluster-1 # 初始化集群的唯一标识符
initial-cluster: etcd-node1=http://<NODE1_IP>:2380,etcd-node2=http://<NODE2_IP>:2380,etcd-node3=http://<NODE3_IP>:2380 # 初始化集群时的节点列表
initial-cluster-state: new # 指定集群是新的还是现有的
```

### 7. kubeadm 配置文件
kubeadm 是一个用于引导 Kubernetes 集群的工具，简化了集群的初始化和配置。常用的配置文件位于 `/etc/kubernetes/kubeadm-config.yaml`。

```yaml
apiVersion: kubeadm.k8s.io/v1beta2
kind: ClusterConfiguration
kubernetesVersion: v1.18.0 # Kubernetes 版本
controlPlaneEndpoint: "LOAD_BALANCER_DNS:LOAD_BALANCER_PORT" # 控制平面（API 服务器）的端点地址
networking:
  podSubnet: "192.168.0.0/16" # Pod 网络地址范围
etcd:
  external:
    endpoints:
      - https://etcd1:2379 # 外部 etcd 节点的地址
      - https://etcd2:2379
      - https://etcd3:2379
    caFile: /etcd/kubernetes/pki/etcd/ca.crt # etcd CA 证书文件
    certFile: /etcd/kubernetes/pki/etcd/etcd-client.crt # etcd 客户端证书文件
    keyFile: /etcd/kubernetes/pki/etcd/etcd-client.key # etcd 客户端密钥文件
```

### 8. kubeconfig 文件
kubeconfig 文件用于配置访问 Kubernetes 集群的参数，通常位于 `~/.kube/config`。

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /path/to/ca.crt # CA 证书文件路径，用于验证 API Server 的证书
    server: https://<kubernetes-apiserver-endpoint> # API Server 的地址
  name: kubernetes
contexts:
- context:
    cluster: kubernetes # 使用的集群名称
    user: admin # 使用的用户名称
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes # 当前上下文
kind: Config
users:
- name: admin
  user:
    client-certificate: /path/to/admin.crt # 客户端证书文件路径
    client-key: /path/to/admin.key # 客户端密钥文件路径
```

通过这些详细的注释，你可以更好地理解 Kubernetes 各个配置文件的作用和用法。如果你使用 `kubeadm` 初始化集群，这些配置文件会自动生成和配置。
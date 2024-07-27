# 解决Kubernetes node NotReady故障案例

2022年12月16日

Contents [[hide](http://www.knockatdatabase.com/2022/12/16/how-to-solve-kubernetes-node-notready/#)]

- [1 一背景说明](http://www.knockatdatabase.com/2022/12/16/how-to-solve-kubernetes-node-notready/#i)
- [2 二 故障原因](http://www.knockatdatabase.com/2022/12/16/how-to-solve-kubernetes-node-notready/#i-2)
- 3 三 故障现象分析和解决步骤
  - [3.1 1 worker node为NotReady的分析和解决](http://www.knockatdatabase.com/2022/12/16/how-to-solve-kubernetes-node-notready/#1_worker_nodeNotReady)
  - [3.2 2 master node为NotReady](http://www.knockatdatabase.com/2022/12/16/how-to-solve-kubernetes-node-notready/#2_master_nodeNotReady)
- [4 四 参考](http://www.knockatdatabase.com/2022/12/16/how-to-solve-kubernetes-node-notready/#i-4)

### 一背景说明

在Kubernetes cluster中，如果node status为NotReady状态，则通常意味着节点状态异常。如果是worker node出现NotReady的话，那么意味着，master上的scheduler控制器，不会向该worker节点上调度任务，即该节点在Kubernetes cluster中成了摆设。反之，如果是master node状态为NotReady，则意味着master节点上不会调度新的任务运行。

### 二 故障原因

造成node status为NotReady的原因，有很多，常见的有kubelet进程出现故障，导致kubelet无法向master节点发送当前节点状态，那么由于master无法通过目标节点上的kubelet进程获取node状态，就任务node节点为NotReady。

也有可能是docker的container Runtime interface异常，导致container运行异常，也会导致node节点的NotReady。

### 三 故障现象分析和解决步骤

#### 1 worker node为NotReady的分析和解决

##### 1.1 故障现象

```
[root@master121 ~]# kubectl get nodes -owide         
NAME             STATUS     ROLES                  AGE    VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION                CONTAINER-RUNTIME
master121        Ready      control-plane,master   134d   v1.23.1   172.16.19.121   <none>        CentOS Linux 7 (Core)   3.10.0-1160.66.1.el7.x86_64   docker://1.13.1
test-worker12    Ready      <none>                 116d   v1.23.1   172.16.19.12    <none>        CentOS Linux 7 (Core)   3.10.0-1160.71.1.el7.x86_64   docker://1.13.1
test-worker14    Ready      <none>                 115d   v1.23.1   172.16.19.14    <none>        CentOS Linux 7 (Core)   3.10.0-957.el7.x86_64         docker://1.13.1
test-worker16    Ready      <none>                 115d   v1.23.1   172.16.19.16    <none>        CentOS Linux 7 (Core)   3.10.0-957.el7.x86_64         docker://1.13.1
test-worker234   Ready      <none>                 116d   v1.23.1   172.16.19.234   <none>        CentOS Linux 7 (Core)   3.10.0-514.el7.x86_64         docker://1.13.1
worker122        NotReady   <none>                 18m    v1.23.1   172.16.19.122   <none>        CentOS Linux 7 (Core)   3.10.0-1160.76.1.el7.x86_64   docker://1.13.1
worker133        Ready      <none>                 118d   v1.23.1   172.16.19.133   <none>        CentOS Linux 7 (Core)   3.10.0-693.el7.x86_64         docker://1.13.1
worker134        Ready      <none>                 133d   v1.23.1   172.16.19.134   <none>        CentOS Linux 7 (Core)   3.10.0-957.el7.x86_64         docker://1.13.1
worker135        Ready      <none>                 132d   v1.23.1   172.16.19.135   <none>        CentOS Linux 7 (Core)   3.10.0-957.el7.x86_64         docker://1.13.1
[root@master121 ~]# 
```

从上，我们看到worker122这个node的状态为notReady，导致该节点上不能运行任何的Kubernetes应用。

##### 1.2 故障分析

```
[root@master121 ~]# kubectl describe nodes worker122 
Name:               worker122
Roles:              <none>
Labels:             appGroup=ident
                    beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    env=uat
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=worker122
                    kubernetes.io/os=linux
Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"a2:c8:a7:d4:8e:25"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 172.16.19.122
                    kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Wed, 07 Dec 2022 23:25:13 +0800
Taints:             node.kubernetes.io/not-ready:NoExecute
                    node.kubernetes.io/not-ready:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  worker122
  AcquireTime:     <unset>
  RenewTime:       Wed, 07 Dec 2022 23:41:25 +0800
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Wed, 07 Dec 2022 23:41:11 +0800   Wed, 07 Dec 2022 23:41:11 +0800   FlannelIsUp                  Flannel is running on this node
  MemoryPressure       False   Wed, 07 Dec 2022 23:41:05 +0800   Wed, 07 Dec 2022 23:35:35 +0800   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Wed, 07 Dec 2022 23:41:05 +0800   Wed, 07 Dec 2022 23:35:35 +0800   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Wed, 07 Dec 2022 23:41:05 +0800   Wed, 07 Dec 2022 23:35:35 +0800   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                False   Wed, 07 Dec 2022 23:41:05 +0800   Wed, 07 Dec 2022 23:35:35 +0800   KubeletNotReady              container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized
Addresses:
  InternalIP:  172.16.19.122
  Hostname:    worker122
Capacity:
  cpu:                8
  ephemeral-storage:  61410Mi
  hugepages-2Mi:      0
  memory:             65807824Ki
  pods:               110
Allocatable:
  cpu:                8
  ephemeral-storage:  57953746849
  hugepages-2Mi:      0
  memory:             65705424Ki
  pods:               110
System Info:
  Machine ID:                 512e9e75d0f74bf0bc63a611c087ea4e
  System UUID:                564D0356-3D2D-95A1-4AEE-4CA24997C48B
  Boot ID:                    d87bdb0a-d2bc-42f7-945f-7a099da7ee26
  Kernel Version:             3.10.0-1160.76.1.el7.x86_64
  OS Image:                   CentOS Linux 7 (Core)
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://1.13.1
  Kubelet Version:            v1.23.1
  Kube-Proxy Version:         v1.23.1
PodCIDR:                      10.244.1.0/24
PodCIDRs:                     10.244.1.0/24
Non-terminated Pods:          (2 in total)
  Namespace                   Name                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                     ------------  ----------  ---------------  -------------  ---
  kube-system                 kube-flannel-ds-mj5jr    100m (1%)     100m (1%)   50Mi (0%)        50Mi (0%)      16m
  kube-system                 kube-proxy-bvxss         0 (0%)        0 (0%)      0 (0%)           0 (0%)         16m
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests   Limits
  --------           --------   ------
  cpu                100m (1%)  100m (1%)
  memory             50Mi (0%)  50Mi (0%)
  ephemeral-storage  0 (0%)     0 (0%)
  hugepages-2Mi      0 (0%)     0 (0%)
Events:
  Type     Reason                   Age                    From        Message
  ----     ------                   ----                   ----        -------
  Normal   Starting                 16m                    kube-proxy  
  Normal   Starting                 5m46s                  kube-proxy  
  Normal   Starting                 20s                    kube-proxy  
  Normal   Starting                 16m                    kubelet     Starting kubelet.
  Normal   NodeHasSufficientMemory  16m (x2 over 16m)      kubelet     Node worker122 status is now: NodeHasSufficientMemory
  Normal   NodeHasSufficientPID     16m (x2 over 16m)      kubelet     Node worker122 status is now: NodeHasSufficientPID
  Normal   NodeHasNoDiskPressure    16m (x2 over 16m)      kubelet     Node worker122 status is now: NodeHasNoDiskPressure
  Normal   NodeAllocatableEnforced  16m                    kubelet     Updated Node Allocatable limit across pods
  Normal   NodeHasSufficientMemory  5m53s (x2 over 5m53s)  kubelet     Node worker122 status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure    5m53s (x2 over 5m53s)  kubelet     Node worker122 status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID     5m53s (x2 over 5m53s)  kubelet     Node worker122 status is now: NodeHasSufficientPID
  Normal   NodeNotReady             5m53s                  kubelet     Node worker122 status is now: NodeNotReady
  Normal   Starting                 5m53s                  kubelet     Starting kubelet.
  Warning  Rebooted                 5m53s                  kubelet     Node worker122 has been rebooted, boot id: d87bdb0a-d2bc-42f7-945f-7a099da7ee26
  Normal   NodeAllocatableEnforced  5m52s                  kubelet     Updated Node Allocatable limit across pods
  Warning  ImageGCFailed            53s                    kubelet     failed to get imageFs info: non-existent label "docker-images"
  Normal   Starting                 24s                    kubelet     Starting kubelet.
  Normal   NodeHasSufficientMemory  24s                    kubelet     Node worker122 status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure    24s                    kubelet     Node worker122 status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID     24s                    kubelet     Node worker122 status is now: NodeHasSufficientPID
  Normal   NodeAllocatableEnforced  23s                    kubelet     Updated Node Allocatable limit across pods
[root@master121 ~]# 
```

从上，看到

> Ready False Wed, 07 Dec 2022 23:41:05 +0800 Wed, 07 Dec 2022 23:35:35 +0800 KubeletNotReady container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized

发现kuberlet异常，原因是docker cni异常导致的。

##### 1.3 故障原因

worker122机器上的/var/lib/kubelet/kubeadm-flags.env文件中的，配置信息导致：

```
[root@worker122 ~]# cat /var/lib/kubelet/kubeadm-flags.env
KUBELET_KUBEADM_ARGS="--network-plugin=cni --pod-infra-container-image=registry.aliyuncs.com/google_containers/pause:3.6"
[root@worker122 ~]# 
```

##### 1.4 故障解决

修改该配置文件，去掉其中的–network-plugin=cni选项，然后执行：

```
systemctl daemon-reload
systemctl restart docker
systemctl restart kubelet
```

然后，看到worker node状态正常：

```
[root@master121 ~]# kubectl get nodes -owide
NAME             STATUS   ROLES                  AGE    VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION                CONTAINER-RUNTIME
master121        Ready    control-plane,master   134d   v1.23.1   172.16.19.121   <none>        CentOS Linux 7 (Core)   3.10.0-1160.66.1.el7.x86_64   docker://1.13.1
test-worker12    Ready    <none>                 116d   v1.23.1   172.16.19.12    <none>        CentOS Linux 7 (Core)   3.10.0-1160.71.1.el7.x86_64   docker://1.13.1
test-worker14    Ready    <none>                 115d   v1.23.1   172.16.19.14    <none>        CentOS Linux 7 (Core)   3.10.0-957.el7.x86_64         docker://1.13.1
test-worker16    Ready    <none>                 115d   v1.23.1   172.16.19.16    <none>        CentOS Linux 7 (Core)   3.10.0-957.el7.x86_64         docker://1.13.1
test-worker234   Ready    <none>                 116d   v1.23.1   172.16.19.234   <none>        CentOS Linux 7 (Core)   3.10.0-514.el7.x86_64         docker://1.13.1
worker122        Ready    <none>                 29m    v1.23.1   172.16.19.122   <none>        CentOS Linux 7 (Core)   3.10.0-1160.76.1.el7.x86_64   docker://1.13.1
worker133        Ready    <none>                 118d   v1.23.1   172.16.19.133   <none>        CentOS Linux 7 (Core)   3.10.0-693.el7.x86_64         docker://1.13.1
worker134        Ready    <none>                 133d   v1.23.1   172.16.19.134   <none>        CentOS Linux 7 (Core)   3.10.0-957.el7.x86_64         docker://1.13.1
worker135        Ready    <none>                 132d   v1.23.1   172.16.19.135   <none>        CentOS Linux 7 (Core)   3.10.0-957.el7.x86_64         docker://1.13.1
[root@master121 ~]# kubectl describe nodes worker122 
Name:               worker122
Roles:              <none>
Labels:             appGroup=ident
                    beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    env=uat
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=worker122
                    kubernetes.io/os=linux
Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"a2:c8:a7:d4:8e:25"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 172.16.19.122
                    kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Wed, 07 Dec 2022 23:25:13 +0800
Taints:             <none>
Unschedulable:      false
Lease:
  HolderIdentity:  worker122
  AcquireTime:     <unset>
  RenewTime:       Wed, 07 Dec 2022 23:56:31 +0800
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Wed, 07 Dec 2022 23:54:09 +0800   Wed, 07 Dec 2022 23:54:09 +0800   FlannelIsUp                  Flannel is running on this node
  MemoryPressure       False   Wed, 07 Dec 2022 23:54:11 +0800   Wed, 07 Dec 2022 23:35:35 +0800   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Wed, 07 Dec 2022 23:54:11 +0800   Wed, 07 Dec 2022 23:35:35 +0800   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Wed, 07 Dec 2022 23:54:11 +0800   Wed, 07 Dec 2022 23:35:35 +0800   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                True    Wed, 07 Dec 2022 23:54:11 +0800   Wed, 07 Dec 2022 23:54:11 +0800   KubeletReady                 kubelet is posting ready status
Addresses:
  InternalIP:  172.16.19.122
  Hostname:    worker122
Capacity:
  cpu:                8
  ephemeral-storage:  61410Mi
  hugepages-2Mi:      0
  memory:             65807824Ki
  pods:               110
Allocatable:
  cpu:                8
  ephemeral-storage:  57953746849
  hugepages-2Mi:      0
  memory:             65705424Ki
  pods:               110
System Info:
  Machine ID:                 512e9e75d0f74bf0bc63a611c087ea4e
  System UUID:                564D0356-3D2D-95A1-4AEE-4CA24997C48B
  Boot ID:                    d87bdb0a-d2bc-42f7-945f-7a099da7ee26
  Kernel Version:             3.10.0-1160.76.1.el7.x86_64
  OS Image:                   CentOS Linux 7 (Core)
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://1.13.1
  Kubelet Version:            v1.23.1
  Kube-Proxy Version:         v1.23.1
PodCIDR:                      10.244.1.0/24
PodCIDRs:                     10.244.1.0/24
Non-terminated Pods:          (36 in total)
  Namespace                   Name                                                          CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                                          ------------  ----------  ---------------  -------------  ---
  kube-system                 kube-flannel-ds-mj5jr                                         100m (1%)     100m (1%)   50Mi (0%)        50Mi (0%)      31m
  kube-system                 kube-proxy-bvxss                                              0 (0%)        0 (0%)      0 (0%)           0 (0%)         31m
  monitoring                  node-exporter-q7nh6                                           102m (1%)     250m (3%)   180Mi (0%)       180Mi (0%)     2m29s
  monitoring                  prometheus-deployment-868bccb8b8-n54kj                        200m (2%)     500m (6%)   512M (0%)        1Gi (1%)       7h
  monitoring                  promtail-deployment-mr6tr                                     500m (6%)     500m (6%)   500Mi (0%)       500Mi (0%)     2m29s
  uat                         ai-authority-service-deployment-674d9ccb86-wxt97              0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ai-front-end-deployment-6bd557f99-9hhhd                       0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ai-gateway-deployment-8db8cdf5-78jpn                          0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ai-inference-service-deployment-7f8fd4dfd5-mxxpd              0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ai-nginx-deployment-77dbbf7c5b-nl8n7                          0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ai-oauth-server-deployment-6c65c7d64-csfsl                    0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-admin-web-deployment-774bd9bfd9-w5s6w                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-auto-service-deployment-5964858c4-xhnhz                 0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-base-service-deployment-9f4c5b6f8-x7pgk                 0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-batch-service-deployment-8456d744bf-qnrgm               0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-bill-acceptor-deployment-664b985cb8-wx9dq               0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-bill-classifier-service-deployment-7bd9b6d756-9cnr8     0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-bill-data-backer-service-deployment-5db5cfdf7c-dtfj8    0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-bill-data-checker-deployment-bd9949956-tjjx2            0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-classify-service-deployment-7bc78b9ccb-cb79w            0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-cutter-service-deployment-648b76558f-bmlj6              0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-distribute-service-deployment-55b8957474-579k7          0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-due-collector-handler-deployment-699fcf4c87-5w5pc       0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-due-collector-service-deployment-56f6d6c654-7s25x       0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-ei-service-deployment-5c98ddd58d-2dbr8                  0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-flow-log-service-deployment-6f548786fd-rbfp2            0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-input-service-deployment-d5cf5c9b5-p8xh8                0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-open-service-deployment-779894b984-2jbnr                0 (0%)        0 (0%)      0 (0%)           0 (0%)         43m
  uat                         ident-open-tax-service-deployment-6875fc69b-h2cxd             0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-paved-cutter-deployment-778b49bbdc-vkjb6                0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-pdf-acceptor-deployment-78b7d97856-q5zr6                0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-pdf-service-deployment-64869fc767-pdmht                 0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-stat-service-deployment-584d4fc97d-br8ql                0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-task-dispatcher-deployment-7948dc87d-6z7lx              0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-task-generator-deployment-7684988bdd-qdph8              0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
  uat                         ident-task-processor-deployment-648ffd7b8d-2t8wt              0 (0%)        0 (0%)      0 (0%)           0 (0%)         7h
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests        Limits
  --------           --------        ------
  cpu                902m (11%)      1350m (16%)
  memory             1247520Ki (1%)  1754Mi (2%)
  ephemeral-storage  0 (0%)          0 (0%)
  hugepages-2Mi      0 (0%)          0 (0%)
Events:
  Type     Reason                   Age                From        Message
  ----     ------                   ----               ----        -------
  Normal   Starting                 31m                kube-proxy  
  Normal   Starting                 2m58s              kube-proxy  
  Normal   Starting                 15m                kube-proxy  
  Normal   Starting                 2m26s              kube-proxy  
  Normal   Starting                 20m                kube-proxy  
  Normal   Starting                 31m                kubelet     Starting kubelet.
  Normal   NodeHasSufficientPID     31m (x2 over 31m)  kubelet     Node worker122 status is now: NodeHasSufficientPID
  Normal   NodeHasSufficientMemory  31m (x2 over 31m)  kubelet     Node worker122 status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure    31m (x2 over 31m)  kubelet     Node worker122 status is now: NodeHasNoDiskPressure
  Normal   NodeAllocatableEnforced  31m                kubelet     Updated Node Allocatable limit across pods
  Normal   Starting                 21m                kubelet     Starting kubelet.
  Normal   NodeNotReady             21m                kubelet     Node worker122 status is now: NodeNotReady
  Normal   NodeHasSufficientMemory  21m (x2 over 21m)  kubelet     Node worker122 status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure    21m (x2 over 21m)  kubelet     Node worker122 status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID     21m (x2 over 21m)  kubelet     Node worker122 status is now: NodeHasSufficientPID
  Warning  Rebooted                 21m                kubelet     Node worker122 has been rebooted, boot id: d87bdb0a-d2bc-42f7-945f-7a099da7ee26
  Normal   NodeAllocatableEnforced  21m                kubelet     Updated Node Allocatable limit across pods
  Warning  ImageGCFailed            16m                kubelet     failed to get imageFs info: non-existent label "docker-images"
  Normal   NodeHasSufficientPID     15m                kubelet     Node worker122 status is now: NodeHasSufficientPID
  Normal   NodeHasSufficientMemory  15m                kubelet     Node worker122 status is now: NodeHasSufficientMemory
  Normal   Starting                 15m                kubelet     Starting kubelet.
  Normal   NodeHasNoDiskPressure    15m                kubelet     Node worker122 status is now: NodeHasNoDiskPressure
  Normal   NodeAllocatableEnforced  15m                kubelet     Updated Node Allocatable limit across pods
  Normal   NodeHasSufficientMemory  2m30s              kubelet     Node worker122 status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure    2m30s              kubelet     Node worker122 status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID     2m30s              kubelet     Node worker122 status is now: NodeHasSufficientPID
  Normal   Starting                 2m30s              kubelet     Starting kubelet.
  Normal   NodeAllocatableEnforced  2m29s              kubelet     Updated Node Allocatable limit across pods
  Normal   NodeReady                2m29s              kubelet     Node worker122 status is now: NodeReady
[root@master121 ~]# 
```

#### 2 master node为NotReady

##### 2.1 故障现象

```
[root@master80 ~]# kubectl get nodes -owide
NAME       STATUS     ROLES                  AGE     VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION                CONTAINER-RUNTIME
guoxin8    Ready      <none>                 4m20s   v1.23.1   172.16.17.8    <none>        CentOS Linux 7 (Core)   3.10.0-1160.76.1.el7.x86_64   docker://1.13.1
master80   NotReady   control-plane,master   4m47s   v1.23.1   172.16.11.80   <none>        CentOS Linux 7 (Core)   3.10.0-862.el7.x86_64         docker://20.10.16
[root@master80 ~]# kubectl describe nodes master80 
Name:               master80
Roles:              control-plane,master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=master80
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node-role.kubernetes.io/master=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"5e:17:ff:89:2f:90"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 172.16.11.80
                    kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Fri, 16 Dec 2022 15:35:27 +0800
Taints:             node.kubernetes.io/not-ready:NoExecute
                    node-role.kubernetes.io/master:NoSchedule
                    node.kubernetes.io/not-ready:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  master80
  AcquireTime:     <unset>
  RenewTime:       Fri, 16 Dec 2022 15:40:17 +0800
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Fri, 16 Dec 2022 15:37:48 +0800   Fri, 16 Dec 2022 15:37:48 +0800   FlannelIsUp                  Flannel is running on this node
  MemoryPressure       False   Fri, 16 Dec 2022 15:35:41 +0800   Fri, 16 Dec 2022 15:35:24 +0800   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Fri, 16 Dec 2022 15:35:41 +0800   Fri, 16 Dec 2022 15:35:24 +0800   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Fri, 16 Dec 2022 15:35:41 +0800   Fri, 16 Dec 2022 15:35:24 +0800   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                False   Fri, 16 Dec 2022 15:35:41 +0800   Fri, 16 Dec 2022 15:35:24 +0800   KubeletNotReady              container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized
Addresses:
  InternalIP:  172.16.11.80
  Hostname:    master80
Capacity:
  cpu:                4
  ephemeral-storage:  514722244Ki
  hugepages-2Mi:      0
  memory:             8009828Ki
  pods:               110
Allocatable:
  cpu:                4
  ephemeral-storage:  474368019285
  hugepages-2Mi:      0
  memory:             7907428Ki
  pods:               110
System Info:
  Machine ID:                 f8687dc427ed4baa8957cf6cca37dd7d
  System UUID:                42250521-3AF0-9954-BA20-2282B0ED64AB
  Boot ID:                    3efc1fe3-05cf-4922-9d16-eefa33cb8248
  Kernel Version:             3.10.0-862.el7.x86_64
  OS Image:                   CentOS Linux 7 (Core)
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://20.10.16
  Kubelet Version:            v1.23.1
  Kube-Proxy Version:         v1.23.1
PodCIDR:                      10.244.0.0/24
PodCIDRs:                     10.244.0.0/24
Non-terminated Pods:          (6 in total)
  Namespace                   Name                                CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                ------------  ----------  ---------------  -------------  ---
  kube-system                 etcd-master80                       100m (2%)     0 (0%)      100Mi (1%)       0 (0%)         4m49s
  kube-system                 kube-apiserver-master80             250m (6%)     0 (0%)      0 (0%)           0 (0%)         4m49s
  kube-system                 kube-controller-manager-master80    200m (5%)     0 (0%)      0 (0%)           0 (0%)         4m49s
  kube-system                 kube-flannel-ds-tnwls               100m (2%)     100m (2%)   50Mi (0%)        50Mi (0%)      2m36s
  kube-system                 kube-proxy-jzmzd                    0 (0%)        0 (0%)      0 (0%)           0 (0%)         4m36s
  kube-system                 kube-scheduler-master80             100m (2%)     0 (0%)      0 (0%)           0 (0%)         4m49s
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests    Limits
  --------           --------    ------
  cpu                750m (18%)  100m (2%)
  memory             150Mi (1%)  50Mi (0%)
  ephemeral-storage  0 (0%)      0 (0%)
  hugepages-2Mi      0 (0%)      0 (0%)
Events:
  Type    Reason                   Age                    From        Message
  ----    ------                   ----                   ----        -------
  Normal  Starting                 4m33s                  kube-proxy  
  Normal  NodeHasSufficientMemory  4m58s (x5 over 4m58s)  kubelet     Node master80 status is now: NodeHasSufficientMemory
  Normal  NodeHasNoDiskPressure    4m58s (x5 over 4m58s)  kubelet     Node master80 status is now: NodeHasNoDiskPressure
  Normal  NodeHasSufficientPID     4m58s (x4 over 4m58s)  kubelet     Node master80 status is now: NodeHasSufficientPID
  Normal  Starting                 4m50s                  kubelet     Starting kubelet.
  Normal  NodeHasSufficientMemory  4m50s                  kubelet     Node master80 status is now: NodeHasSufficientMemory
  Normal  NodeHasNoDiskPressure    4m50s                  kubelet     Node master80 status is now: NodeHasNoDiskPressure
  Normal  NodeHasSufficientPID     4m50s                  kubelet     Node master80 status is now: NodeHasSufficientPID
  Normal  NodeAllocatableEnforced  4m50s                  kubelet     Updated Node Allocatable limit across pods
[root@master80 ~]#
```

##### 2.2 故障影响

当前故障情形下，master node为notReady状态，倒不影响master节点上的scheduler把Kubernetes的应用分发调度到cluster中的其它worker节点上去。但是，看着notReady也不太习惯。

##### 2.3 故障解决

跟前面的方法一样，先修改配置文件，然后重启docker和kubelet服务：

```
[root@master80 ~]# vi /var/lib/kubelet/kubeadm-flags.env 
KUBELET_KUBEADM_ARGS="--pod-infra-container-image=registry.aliyuncs.com/google_containers/pause:3.6"
~
...
[root@master80 ~]# kubectl get nodes -owide
NAME       STATUS     ROLES                  AGE     VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION                CONTAINER-RUNTIME
guoxin8    Ready      <none>                 5m18s   v1.23.1   172.16.17.8    <none>        CentOS Linux 7 (Core)   3.10.0-1160.76.1.el7.x86_64   docker://1.13.1
master80   NotReady   control-plane,master   5m45s   v1.23.1   172.16.11.80   <none>        CentOS Linux 7 (Core)   3.10.0-862.el7.x86_64         docker://20.10.16
[root@master80 ~]# systemctl daemon-reload 
[root@master80 ~]# systemctl restart docker
[root@master80 ~]# kubectl get nodes -owide
NAME       STATUS     ROLES                  AGE     VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION                CONTAINER-RUNTIME
guoxin8    Ready      <none>                 5m46s   v1.23.1   172.16.17.8    <none>        CentOS Linux 7 (Core)   3.10.0-1160.76.1.el7.x86_64   docker://1.13.1
master80   NotReady   control-plane,master   6m13s   v1.23.1   172.16.11.80   <none>        CentOS Linux 7 (Core)   3.10.0-862.el7.x86_64         docker://20.10.16
[root@master80 ~]# systemctl restart kubelet.service
[root@master80 ~]# kubectl get nodes -owide
NAME       STATUS   ROLES                  AGE     VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION                CONTAINER-RUNTIME
guoxin8    Ready    <none>                 5m58s   v1.23.1   172.16.17.8    <none>        CentOS Linux 7 (Core)   3.10.0-1160.76.1.el7.x86_64   docker://1.13.1
master80   Ready    control-plane,master   6m25s   v1.23.1   172.16.11.80   <none>        CentOS Linux 7 (Core)   3.10.0-862.el7.x86_64         docker://20.10.16
[root@master80 ~]# 
```

查看节点状态变为ready：

```
[root@master80 ~]# kubectl describe nodes master80 
Name:               master80
Roles:              control-plane,master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=master80
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node-role.kubernetes.io/master=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"5e:17:ff:89:2f:90"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 172.16.11.80
                    kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Fri, 16 Dec 2022 15:35:27 +0800
Taints:             node-role.kubernetes.io/master:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  master80
  AcquireTime:     <unset>
  RenewTime:       Fri, 16 Dec 2022 16:16:32 +0800
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Fri, 16 Dec 2022 15:41:40 +0800   Fri, 16 Dec 2022 15:41:40 +0800   FlannelIsUp                  Flannel is running on this node
  MemoryPressure       False   Fri, 16 Dec 2022 16:12:26 +0800   Fri, 16 Dec 2022 15:35:24 +0800   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Fri, 16 Dec 2022 16:12:26 +0800   Fri, 16 Dec 2022 15:35:24 +0800   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Fri, 16 Dec 2022 16:12:26 +0800   Fri, 16 Dec 2022 15:35:24 +0800   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                True    Fri, 16 Dec 2022 16:12:26 +0800   Fri, 16 Dec 2022 15:41:51 +0800   KubeletReady                 kubelet is posting ready status
Addresses:
  InternalIP:  172.16.11.80
  Hostname:    master80
Capacity:
  cpu:                4
  ephemeral-storage:  514722244Ki
  hugepages-2Mi:      0
  memory:             8009828Ki
  pods:               110
Allocatable:
  cpu:                4
  ephemeral-storage:  474368019285
  hugepages-2Mi:      0
  memory:             7907428Ki
  pods:               110
System Info:
  Machine ID:                 f8687dc427ed4baa8957cf6cca37dd7d
  System UUID:                42250521-3AF0-9954-BA20-2282B0ED64AB
  Boot ID:                    3efc1fe3-05cf-4922-9d16-eefa33cb8248
  Kernel Version:             3.10.0-862.el7.x86_64
  OS Image:                   CentOS Linux 7 (Core)
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://20.10.16
  Kubelet Version:            v1.23.1
  Kube-Proxy Version:         v1.23.1
PodCIDR:                      10.244.0.0/24
PodCIDRs:                     10.244.0.0/24
Non-terminated Pods:          (6 in total)
  Namespace                   Name                                CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                ------------  ----------  ---------------  -------------  ---
  kube-system                 etcd-master80                       100m (2%)     0 (0%)      100Mi (1%)       0 (0%)         41m
  kube-system                 kube-apiserver-master80             250m (6%)     0 (0%)      0 (0%)           0 (0%)         41m
  kube-system                 kube-controller-manager-master80    200m (5%)     0 (0%)      0 (0%)           0 (0%)         41m
  kube-system                 kube-flannel-ds-tnwls               100m (2%)     100m (2%)   50Mi (0%)        50Mi (0%)      38m
  kube-system                 kube-proxy-jzmzd                    0 (0%)        0 (0%)      0 (0%)           0 (0%)         40m
  kube-system                 kube-scheduler-master80             100m (2%)     0 (0%)      0 (0%)           0 (0%)         41m
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests    Limits
  --------           --------    ------
  cpu                750m (18%)  100m (2%)
  memory             150Mi (1%)  50Mi (0%)
  ephemeral-storage  0 (0%)      0 (0%)
  hugepages-2Mi      0 (0%)      0 (0%)
Events:
  Type    Reason                   Age                From        Message
  ----    ------                   ----               ----        -------
  Normal  Starting                 40m                kube-proxy  
  Normal  Starting                 34m                kube-proxy  
  Normal  NodeHasNoDiskPressure    41m (x5 over 41m)  kubelet     Node master80 status is now: NodeHasNoDiskPressure
  Normal  NodeHasSufficientPID     41m (x4 over 41m)  kubelet     Node master80 status is now: NodeHasSufficientPID
  Normal  NodeHasSufficientMemory  41m (x5 over 41m)  kubelet     Node master80 status is now: NodeHasSufficientMemory
  Normal  NodeHasNoDiskPressure    41m                kubelet     Node master80 status is now: NodeHasNoDiskPressure
  Normal  NodeHasSufficientPID     41m                kubelet     Node master80 status is now: NodeHasSufficientPID
  Normal  NodeAllocatableEnforced  41m                kubelet     Updated Node Allocatable limit across pods
  Normal  NodeHasSufficientMemory  41m                kubelet     Node master80 status is now: NodeHasSufficientMemory
  Normal  Starting                 41m                kubelet     Starting kubelet.
  Normal  Starting                 34m                kubelet     Starting kubelet.
  Normal  NodeHasSufficientMemory  34m                kubelet     Node master80 status is now: NodeHasSufficientMemory
  Normal  NodeHasNoDiskPressure    34m                kubelet     Node master80 status is now: NodeHasNoDiskPressure
  Normal  NodeHasSufficientPID     34m                kubelet     Node master80 status is now: NodeHasSufficientPID
  Normal  NodeAllocatableEnforced  34m                kubelet     Updated Node Allocatable limit across pods
  Normal  NodeReady                34m                kubelet     Node master80 status is now: NodeReady
[root@master80 ~]# 
```
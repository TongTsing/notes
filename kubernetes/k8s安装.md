# CentOS 搭建 K8S 环境教程，一次性成功，收藏了！

发布于 2020-10-09 09:39:18

8.7K0

举报

文章被收录于专栏：[芋道源码1024](https://cloud.tencent.com/developer/column/2064)

**一. 为什么是k8s v1.16.0？**

最新版的v1.16.2试过了，一直无法安装完成，安装到kubeadm init那一步执行后，报了很多错，如：node xxx not found等。centos7都重装了几次，还是无法解决。用了一天都没安装完，差点放弃。后来在网上搜到的安装教程基本都是v1.16.0的，我不太相信是v1.16.2的坑所以先前没打算降级到v1.16.0。没办法了就试着安装v1.16.0版本，竟然成功了。记录在此，避免后来者踩坑。

本篇文章，安装大步骤如下：

- 安装docker-ce 18.09.9（所有机器）
- 设置k8s环境前置条件（所有机器）
- 安装k8s v1.16.0 master管理节点
- 安装k8s v1.16.0 node工作节点
- 安装flannel（master）

这里有重要的一步，请记住自己master和node之间通信的ip，如我的master的ip为192.168.99.104，node的ip为：192.168.99.105. 请确保使用这两个ip在master和node上能互相ping通，这个master的ip 192.168.99.104接下来配置k8s的时候需要用到。

我的环境：

- 操作系统：win10
- 虚拟机：virtual box
- linux发行版：CentOS7
- linux内核(使用uname -r查看)：3.10.0-957.el7.x86_64
- master和node节点通信的ip(master)：192.168.99.104

## **二. 安装docker-ce 18.09.9（所有机器）**

所有安装k8s的机器都需要安装[docker](https://cloud.tencent.com/product/tke?from_column=20065&from=20065)，命令如下：

```javascript
# 安装docker所需的工具
yum install -y yum-utils device-mapper-persistent-data lvm2
# 配置阿里云的docker源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# 指定安装这个版本的docker-ce
yum install -y docker-ce-18.09.9-3.el7
# 启动docker
systemctl enable docker && systemctl start docker
```

复制

## **三. 设置k8s环境准备条件（所有机器）**

安装k8s的机器需要2个CPU和2g内存以上，这个简单，在虚拟机里面配置一下就可以了。然后执行以下脚本做一些准备操作。所有安装k8s的机器都需要这一步操作。

```javascript
# 关闭防火墙
systemctl disable firewalld
systemctl stop firewalld

# 关闭selinux
# 临时禁用selinux
setenforce 0
# 永久关闭 修改/etc/sysconfig/selinux文件设置
sed -i 's/SELINUX=permissive/SELINUX=disabled/' /etc/sysconfig/selinux
sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config

# 禁用交换分区
swapoff -a
# 永久禁用，打开/etc/fstab注释掉swap那一行。
sed -i 's/.*swap.*/#&/' /etc/fstab

# 修改内核参数
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```

复制

## **四. 安装k8s v1.16.0 master管理节点**

如果还没安装docker，请参照本文步骤二`安装docker-ce 18.09.9（所有机器）`安装。 如果没设置k8s环境准备条件，请参照本文步骤三`设置k8s环境准备条件（所有机器）`执行。 以上两个步骤检查完毕之后，继续以下步骤。

### **1. 安装kubeadm、kubelet、kubectl**

由于官方k8s源在google，国内无法访问，这里使用阿里云yum源

```javascript
# 执行配置k8s阿里云源
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF



# 安装kubeadm、kubectl、kubelet
yum install -y kubectl-1.16.0-0 kubeadm-1.16.0-0 kubelet-1.16.0-0

# 启动kubelet服务
systemctl enable kubelet && systemctl start kubelet

wget https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64/Packages/cee73f8035d734e86f722f77f1bf4e7d643e78d36646fd000148deb8af98b61c-kubeadm-1.28.2-0.x86_64.rpm
wget https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64/Packages/e1cae938e231bffa3618f5934a096bd85372ee9b1293081f5682a22fe873add8-kubelet-1.28.2-0.x86_64.rpm
wget https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64/Packages/a24e42254b5a14b67b58c4633d29c27370c28ed6796a80c455a65acc813ff374-kubectl-1.28.2-0.x86_64.rpm
wget https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64/Packages/0f2a2afd740d476ad77c508847bad1f559afc2425816c1f2ce4432a62dfe0b9d-kubernetes-cni-1.2.0-0.x86_64.rpm
```

复制

### **2. 初始化k8s**

以下这个命令开始安装k8s需要用到的docker镜像，因为无法访问到国外网站，所以这条命令使用的是国内的阿里云的源(registry.aliyuncs.com/google_containers)。**另一个非常重要的是：这里的--apiserver-advertise-address使用的是master和node间能互相ping通的ip，我这里是10.8.110.249，刚开始在这里被坑了一个晚上，你请自己修改下ip执行。** 这条命令执行时会卡在`[preflight] You can also perform this action in beforehand using ''kubeadm config images pull`，大概需要2分钟，请耐心等待。

```javascript
# 下载管理节点中用到的6个docker镜像，你可以使用docker images查看到
# 这里需要大概两分钟等待，会卡在[preflight] You can also perform this action in beforehand using ''kubeadm config images pull
--kubernetes-version v1.28.2
kubeadm init --image-repository registry.aliyuncs.com/google_containers --apiserver-advertise-address 172.16.1.120 --v=6

kubeadm init --apiserver-advertise-address=172.16.1.120 --image-repository registry.aliyuncs.com/google_containers --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --v=6 --cri-socket unix:///var/run/cri-dockerd.sock （--cri-socket在k8s1.24+需要配置）

kubeadm init \
--apiserver-advertise-address=1.92.68.135 \
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version=v1.17.4 \
--pod-network-cidr=10.244.0.0/16 \
--service-cidr=10.96.0.0/12 \
--v=6
```



复制

上面安装完后，会提示你输入如下命令，复制粘贴过来，执行即可。

```javascript
# 上面安装完成后，k8s会提示你输入如下命令，执行
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

复制

### **3. 记住node加入集群的命令**

上面kubeadm init执行成功后会返回给你node节点加入集群的命令，等会要在node节点上执行，需要保存下来，如果忘记了，可以使用如下命令获取。

```javascript
kubeadm token create --print-join-command
```

复制

以上，安装master节点完毕。可以使用`kubectl get nodes`查看一下，此时master处于NotReady状态，暂时不用管。

![img](https://ask.qcloudimg.com/http-save/yehe-1305760/ew0sjap468.png)

图片

## **五. 安装k8s v1.16.0 node工作节点**

如果还没安装docker，请参照本文步骤二`安装docker-ce 18.09.9（所有机器）`安装。 如果没设置k8s环境准备条件，请参照本文步骤三`设置k8s环境准备条件（所有机器）`执行。 以上两个步骤检查完毕之后，继续以下步骤。

### **1. 安装kubeadm、kubelet**

```javascript
# 执行配置k8s阿里云源
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

# 安装kubeadm、kubectl、kubelet
yum install -y  kubeadm-1.16.0-0 kubelet-1.16.0-0

# 启动kubelet服务
systemctl enable kubelet && systemctl start kubelet
```

复制

### **2. 加入集群**

这里加入集群的命令每个人都不一样，可以登录master节点，使用`kubeadm token create --print-join-command` 来获取。获取后执行如下。



```javascript
# 加入集群，如果这里不知道加入集群的命令，可以登录master节点，使用kubeadm token create --print-join-command 来获取
kubeadm join 172.16.1.125:6443 --token bqxwmd.y9cv5u9ku6v7jele     --discovery-token-ca-cert-hash sha256:79384110cf3c14d2f30d0acbbe7c8e442f9052ab6c8dd4ef36619f0d355d4675
```

复制

加入成功后，可以在master节点上使用`kubectl get nodes`命令查看到加入的节点。

## **六. 安装flannel（master机器）**

以上步骤安装完后，机器搭建起来了，但状态还是NotReady状态，如下图，master机器需要安装flanneld。

![img](https://ask.qcloudimg.com/http-save/yehe-1305760/h8a616jo6t.png)

20191101095214.png

### **1. 下载官方fannel配置文件**

使用wget命令，地址为：(https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml)，这个地址国内访问不了，所以我把内容复制下来，为了避免前面文章过长，我把它粘贴到文章末尾第八个步骤`附录`了。这个yml配置文件中配置了一个国内无法访问的地址(quay.io)，我已经将其改为国内可以访问的地址(quay-mirror.qiniu.com)。我们新建一个kube-flannel.yml文件，复制粘贴该内容即可。

### **2. 安装fannel**

```javascript
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

复制

## **七. 大功告成**

至此，k8s集群搭建完成，如下图节点已为Ready状态，大功告成，完结撒花。

![img](https://ask.qcloudimg.com/http-save/yehe-1305760/89qbe3495k.png)

20191101101725.png

## **八. 附录**

这是kube-flannel.yml文件的内容，已经将无法访问的地址(quay.io)全部改为国内可以访问的地址(quay-mirror.qiniu.com)。我们新建一个kube-flannel.yml文件，复制粘贴该内容即可。

```javascript
---
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp.flannel.unprivileged
  annotations:
    seccomp.security.alpha.kubernetes.io/allowedProfileNames: docker/default
    seccomp.security.alpha.kubernetes.io/defaultProfileName: docker/default
    apparmor.security.beta.kubernetes.io/allowedProfileNames: runtime/default
    apparmor.security.beta.kubernetes.io/defaultProfileName: runtime/default
spec:
  privileged: false
  volumes:
    - configMap
    - secret
    - emptyDir
    - hostPath
  allowedHostPaths:
    - pathPrefix: "/etc/cni/net.d"
    - pathPrefix: "/etc/kube-flannel"
    - pathPrefix: "/run/flannel"
  readOnlyRootFilesystem: false
  # Users and groups
  runAsUser:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  # Privilege Escalation
  allowPrivilegeEscalation: false
  defaultAllowPrivilegeEscalation: false
  # Capabilities
  allowedCapabilities: ['NET_ADMIN']
  defaultAddCapabilities: []
  requiredDropCapabilities: []
  # Host namespaces
  hostPID: false
  hostIPC: false
  hostNetwork: true
  hostPorts:
  - min: 0
    max: 65535
  # SELinux
  seLinux:
    # SELinux is unused in CaaSP
    rule: 'RunAsAny'
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: flannel
rules:
  - apiGroups: ['extensions']
    resources: ['podsecuritypolicies']
    verbs: ['use']
    resourceNames: ['psp.flannel.unprivileged']
  - apiGroups:
      - ""
    resources:
      - pods
    verbs:
      - get
  - apiGroups:
      - ""
    resources:
      - nodes
    verbs:
      - list
      - watch
  - apiGroups:
      - ""
    resources:
      - nodes/status
    verbs:
      - patch
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: flannel
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: flannel
subjects:
- kind: ServiceAccount
  name: flannel
  namespace: kube-system
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: flannel
  namespace: kube-system
---
kind: ConfigMap
apiVersion: v1
metadata:
  name: kube-flannel-cfg
  namespace: kube-system
  labels:
    tier: node
    app: flannel
data:
  cni-conf.json: |
    {
      "name": "cbr0",
      "cniVersion": "0.3.1",
      "plugins": [
        {
          "type": "flannel",
          "delegate": {
            "hairpinMode": true,
            "isDefaultGateway": true
          }
        },
        {
          "type": "portmap",
          "capabilities": {
            "portMappings": true
          }
        }
      ]
    }
  net-conf.json: |
    {
      "Network": "10.244.0.0/16",
      "Backend": {
        "Type": "vxlan"
      }
    }
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: kube-flannel-ds-amd64
  namespace: kube-system
  labels:
    tier: node
    app: flannel
spec:
  selector:
    matchLabels:
      app: flannel
  template:
    metadata:
      labels:
        tier: node
        app: flannel
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: beta.kubernetes.io/os
                    operator: In
                    values:
                      - linux
                  - key: beta.kubernetes.io/arch
                    operator: In
                    values:
                      - amd64
      hostNetwork: true
      tolerations:
      - operator: Exists
        effect: NoSchedule
      serviceAccountName: flannel
      initContainers:
      - name: install-cni
        image: quay-mirror.qiniu.com/coreos/flannel:v0.11.0-amd64
        command:
        - cp
        args:
        - -f
        - /etc/kube-flannel/cni-conf.json
        - /etc/cni/net.d/10-flannel.conflist
        volumeMounts:
        - name: cni
          mountPath: /etc/cni/net.d
        - name: flannel-cfg
          mountPath: /etc/kube-flannel/
      containers:
      - name: kube-flannel
        image: quay-mirror.qiniu.com/coreos/flannel:v0.11.0-amd64
        command:
        - /opt/bin/flanneld
        args:
        - --ip-masq
        - --kube-subnet-mgr
        resources:
          requests:
            cpu: "100m"
            memory: "50Mi"
          limits:
            cpu: "100m"
            memory: "50Mi"
        securityContext:
          privileged: false
          capabilities:
            add: ["NET_ADMIN"]
        env:
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        volumeMounts:
        - name: run
          mountPath: /run/flannel
        - name: flannel-cfg
          mountPath: /etc/kube-flannel/
      volumes:
        - name: run
          hostPath:
            path: /run/flannel
        - name: cni
          hostPath:
            path: /etc/cni/net.d
        - name: flannel-cfg
          configMap:
            name: kube-flannel-cfg
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: kube-flannel-ds-arm64
  namespace: kube-system
  labels:
    tier: node
    app: flannel
spec:
  selector:
    matchLabels:
      app: flannel
  template:
    metadata:
      labels:
        tier: node
        app: flannel
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: beta.kubernetes.io/os
                    operator: In
                    values:
                      - linux
                  - key: beta.kubernetes.io/arch
                    operator: In
                    values:
                      - arm64
      hostNetwork: true
      tolerations:
      - operator: Exists
        effect: NoSchedule
      serviceAccountName: flannel
      initContainers:
      - name: install-cni
        image: quay-mirror.qiniu.com/coreos/flannel:v0.11.0-arm64
        command:
        - cp
        args:
        - -f
        - /etc/kube-flannel/cni-conf.json
        - /etc/cni/net.d/10-flannel.conflist
        volumeMounts:
        - name: cni
          mountPath: /etc/cni/net.d
        - name: flannel-cfg
          mountPath: /etc/kube-flannel/
      containers:
      - name: kube-flannel
        image: quay-mirror.qiniu.com/coreos/flannel:v0.11.0-arm64
        command:
        - /opt/bin/flanneld
        args:
        - --ip-masq
        - --kube-subnet-mgr
        resources:
          requests:
            cpu: "100m"
            memory: "50Mi"
          limits:
            cpu: "100m"
            memory: "50Mi"
        securityContext:
          privileged: false
          capabilities:
             add: ["NET_ADMIN"]
        env:
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        volumeMounts:
        - name: run
          mountPath: /run/flannel
        - name: flannel-cfg
          mountPath: /etc/kube-flannel/
      volumes:
        - name: run
          hostPath:
            path: /run/flannel
        - name: cni
          hostPath:
            path: /etc/cni/net.d
        - name: flannel-cfg
          configMap:
            name: kube-flannel-cfg
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: kube-flannel-ds-arm
  namespace: kube-system
  labels:
    tier: node
    app: flannel
spec:
  selector:
    matchLabels:
      app: flannel
  template:
    metadata:
      labels:
        tier: node
        app: flannel
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: beta.kubernetes.io/os
                    operator: In
                    values:
                      - linux
                  - key: beta.kubernetes.io/arch
                    operator: In
                    values:
                      - arm
      hostNetwork: true
      tolerations:
      - operator: Exists
        effect: NoSchedule
      serviceAccountName: flannel
      initContainers:
      - name: install-cni
        image: quay-mirror.qiniu.com/coreos/flannel:v0.11.0-arm
        command:
        - cp
        args:
        - -f
        - /etc/kube-flannel/cni-conf.json
        - /etc/cni/net.d/10-flannel.conflist
        volumeMounts:
        - name: cni
          mountPath: /etc/cni/net.d
        - name: flannel-cfg
          mountPath: /etc/kube-flannel/
      containers:
      - name: kube-flannel
        image: quay-mirror.qiniu.com/coreos/flannel:v0.11.0-arm
        command:
        - /opt/bin/flanneld
        args:
        - --ip-masq
        - --kube-subnet-mgr
        resources:
          requests:
            cpu: "100m"
            memory: "50Mi"
          limits:
            cpu: "100m"
            memory: "50Mi"
        securityContext:
          privileged: false
          capabilities:
             add: ["NET_ADMIN"]
        env:
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        volumeMounts:
        - name: run
          mountPath: /run/flannel
        - name: flannel-cfg
          mountPath: /etc/kube-flannel/
      volumes:
        - name: run
          hostPath:
            path: /run/flannel
        - name: cni
          hostPath:
            path: /etc/cni/net.d
        - name: flannel-cfg
          configMap:
            name: kube-flannel-cfg
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: kube-flannel-ds-ppc64le
  namespace: kube-system
  labels:
    tier: node
    app: flannel
spec:
  selector:
    matchLabels:
      app: flannel
  template:
    metadata:
      labels:
        tier: node
        app: flannel
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: beta.kubernetes.io/os
                    operator: In
                    values:
                      - linux
                  - key: beta.kubernetes.io/arch
                    operator: In
                    values:
                      - ppc64le
      hostNetwork: true
      tolerations:
      - operator: Exists
        effect: NoSchedule
      serviceAccountName: flannel
      initContainers:
      - name: install-cni
        image: quay-mirror.qiniu.com/coreos/flannel:v0.11.0-ppc64le
        command:
        - cp
        args:
        - -f
        - /etc/kube-flannel/cni-conf.json
        - /etc/cni/net.d/10-flannel.conflist
        volumeMounts:
        - name: cni
          mountPath: /etc/cni/net.d
        - name: flannel-cfg
          mountPath: /etc/kube-flannel/
      containers:
      - name: kube-flannel
        image: quay-mirror.qiniu.com/coreos/flannel:v0.11.0-ppc64le
        command:
        - /opt/bin/flanneld
        args:
        - --ip-masq
        - --kube-subnet-mgr
        resources:
          requests:
            cpu: "100m"
            memory: "50Mi"
          limits:
            cpu: "100m"
            memory: "50Mi"
        securityContext:
          privileged: false
          capabilities:
             add: ["NET_ADMIN"]
        env:
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        volumeMounts:
        - name: run
          mountPath: /run/flannel
        - name: flannel-cfg
          mountPath: /etc/kube-flannel/
      volumes:
        - name: run
          hostPath:
            path: /run/flannel
        - name: cni
          hostPath:
            path: /etc/cni/net.d
        - name: flannel-cfg
          configMap:
            name: kube-flannel-cfg
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: kube-flannel-ds-s390x
  namespace: kube-system
  labels:
    tier: node
    app: flannel
spec:
  selector:
    matchLabels:
      app: flannel
  template:
    metadata:
      labels:
        tier: node
        app: flannel
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: beta.kubernetes.io/os
                    operator: In
                    values:
                      - linux
                  - key: beta.kubernetes.io/arch
                    operator: In
                    values:
                      - s390x
      hostNetwork: true
      tolerations:
      - operator: Exists
        effect: NoSchedule
      serviceAccountName: flannel
      initContainers:
      - name: install-cni
        image: quay-mirror.qiniu.com/coreos/flannel:v0.11.0-s390x
        command:
        - cp
        args:
        - -f
        - /etc/kube-flannel/cni-conf.json
        - /etc/cni/net.d/10-flannel.conflist
        volumeMounts:
        - name: cni
          mountPath: /etc/cni/net.d
        - name: flannel-cfg
          mountPath: /etc/kube-flannel/
      containers:
      - name: kube-flannel
        image: quay-mirror.qiniu.com/coreos/flannel:v0.11.0-s390x
        command:
        - /opt/bin/flanneld
        args:
        - --ip-masq
        - --kube-subnet-mgr
        resources:
          requests:
            cpu: "100m"
            memory: "50Mi"
          limits:
            cpu: "100m"
            memory: "50Mi"
        securityContext:
          privileged: false
          capabilities:
             add: ["NET_ADMIN"]
        env:
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        volumeMounts:
        - name: run
          mountPath: /run/flannel
        - name: flannel-cfg
          mountPath: /etc/kube-flannel/
      volumes:
        - name: run
          hostPath:
            path: /run/flannel
        - name: cni
          hostPath:
            path: /etc/cni/net.d
        - name: flannel-cfg
          configMap:
            name: kube-flannel-cfg
```

复制

本文参与 [腾讯云自媒体分享计划](https://cloud.tencent.com/developer/support-plan)，分享自微信公众号。

原始发表：2020-09-27，如有侵权请联系 [cloudcommunity@tencent.com](mailto:cloudcommunity@tencent.com) 删除


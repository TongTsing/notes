```




yum install -y kubectl kubeadm kubelet


yum install -y kubectl-1.29.2 kubeadm-1.29.2 kubelet-1.29.2
```

```
kubeadm init \
--apiserver-advertise-address=172.16.1.128 \
--control-plane-endpoint=k8s-master \
--image-repository registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images \
--kubernetes-version v1.20.9 \
--service-cidr=10.96.0.0/16 \
--pod-network-cidr=172.31.0.0/16



$ kubeadm init  --image-repository=registry.aliyuncs.com/google_containers --cri-socket=unix:///var/run/cri-dockerd.sock --apiserver-advertise-address=172.16.1.120 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --v=6 --kubernetes-version=v1.29.2


kubeadm init \
  --kubernetes-version 1.18.8 \
  --apiserver-advertise-address=0.0.0.0 \
  --service-cidr=10.96.0.0/16 \
  --pod-network-cidr=10.245.0.0/16 \
  --image-repository registry.aliyuncs.com/google_containers 
```


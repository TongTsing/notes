介绍一下pvc和pv的使用

## 一、介绍一下pv和pvc的使用

### 1. Pv:示例

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mongodb-pv
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  - ReadOnlyMany
  persistentVolumeReclaimPolicy: Retain
	hostPath:
	  /data/k8s_volumes/mongodb

```

**访问模式：**

**RWO——ReadWriteOnce——仅允许单个节点挂载读写。**
**ROX——ReadOnlyMany——允许多个节点挂载只读。**
**RWX——ReadWriteMany——允许多个节点挂载读写这个卷**

### 2. PVC示例：

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongodb-pvc
spec:
  resources:
    requests:
      storage: 1Gi
  accessModes:
  - ReadWriteOnce
  storageClassName: ""
```

### 3. 在Pod中使用pvc的示例

```yaml
apiVersion: 1
kind: Pod
metadata:
  name: mongodb
spec:
  containers:
  - image: mongo
    name: mongodb
    volumeMounts:
    - name: mongodb-data
      mountPath: /data/db
    ports:
    - containerPort: 27017
      protocol: TCP
  volumes:
  - name: mongodb-data
    persistentVolumeClaim:
      claimName: mongodb-pvc

```

## 二、PV和PVC的联系

在Kubernetes中，PersistentVolumeClaim (PVC) 和 PersistentVolume (PV) 之间存在关联。PVC 用于请求存储资源，而 PV 提供实际的存储。当删除 PVC 时，对应的 PV 的状态和行为取决于其 `reclaimPolicy`（回收策略）。以下是不同 `reclaimPolicy` 下的行为：

1. **Retain（保留）**：
   - 当 PVC 被删除时，PV 仍然存在并保持其绑定关系，但是 PV 进入 `Released` 状态，表示该 PV 之前绑定的 PVC 已经被删除。此时，PV 上的数据仍然保留，但需要手动处理才能重新绑定到新的 PVC。管理员可以手动清理 PV，并准备好重新绑定到新的 PVC。

   - 这个策略下，手动删除PV的唯一途径是删除pv然后重新创建
   
     ```shell
     kubectl delete pv {pv-name}
     kubectl apply/create -f pv.yaml
     ```
   
     
   
2. **Recycle（回收）**（注意：这个策略在 Kubernetes 1.11 版本后已被弃用）：
   
   - 当 PVC 被删除时，PV 上的数据会被简单的清理（通过基本的`rm -rf /thevolume/*`命令）。然后，PV 会被重新标记为 `Available`，以便再次绑定到新的 PVC。
   
3. **Delete（删除）**：
   - 当 PVC 被删除时，PV 和其背后的存储资产（例如 AWS EBS、GCE PD、Azure Disk 等）也会被删除。这意味着 PV 和数据都将被永久删除，不可恢复。

### 具体示例

假设你的 PV 配置如下：

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /mnt/data
```

在这个示例中，`persistentVolumeReclaimPolicy` 设置为 `Retain`。当 PVC 被删除后：

- PV 仍然存在，且数据保留。
- PV 状态变为 `Released`。

如果你的 PV 配置为 `Delete`：

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  hostPath:
    path: /mnt/data
```

当 PVC 被删除后：

- PV 和其上的数据都会被删除，PV 不再存在。

总结一下，当 PVC 被删除后，PV 的具体行为取决于其 `reclaimPolicy`。根据你的存储需求和数据持久性要求，选择合适的 `reclaimPolicy` 非常重要。

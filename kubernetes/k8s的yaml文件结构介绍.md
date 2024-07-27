```yaml
kind: Deployment     # 资源类型，用于定义一个 Deployment 对象
metadata:
  name: nginx-deployment  # Deployment 的名称，用于唯一标识这个 Deployment 对象
  labels:                 # Deployment 的标签，用于对 Deployment 对象进行分类和组织
    app: nginx            # 定义了属于这个 Deployment 的标签，这个标签不直接影响 Pod 的选择
spec:                     # spec 在 Kubernetes 中通常表示 "specification"，即规范或规格。它通常用于定义资源对象的规格或规范，包括资源的配置、行为和期望状态等
  replicas: 3             # 副本数量，定义了要创建的 Pod 实例的数量
  selector:
    matchLabels:          # Pod 选择器，用于选择由 Deployment 管理的 Pod
      app: nginx          # 指定了要选择的 Pod 必须具有的标签
  template:               # Pod 模板，用于定义由 Deployment 创建的 Pod 的规范
    metadata:
      labels:
        app: nginx        # Pod 的标签，这些标签将会应用到由 Deployment 创建的每个 Pod 上
    spec:
      containers:         # 容器定义，用于定义要在 Pod 中运行的容器
      - name: nginx       # 容器的名称
        image: nginx:latest  # 容器所使用的镜像
        ports:            # 容器端口配置
        - containerPort: 80  # 容器监听的端口
      volumeMounts:
```

```yaml
apiVersion: v1
kind: ConfigMap                   # 资源类型为 ConfigMap
metadata:
  name: mysql-config              # ConfigMap 的名称
data:
  my.cnf: |                       # 指定了配置文件的内容，作用域为 ConfigMap
    [mysqld]
    server-id=1
    log-bin=mysql-bin
    binlog_format=row
    socket=/var/run/mysqld/mysqld.sock
    datadir=/var/lib/mysql

---
apiVersion: v1
kind: Secret                      # 资源类型为 Secret
metadata:
  name: mysql-secret              # Secret 的名称
type: Opaque                      # Secret 类型为 Opaque，即不透明类型
data:
  mysql-root-password: 123456     # 编码后的 MySQL root 密码
  mysql-user: root                # 编码后的 MySQL 用户名
  mysql-password: 123456          # 编码后的 MySQL 密码

---
apiVersion: v1
kind: PersistentVolume          # 资源类型为 PersistentVolume
metadata:
  name: mysql-pv                # PersistentVolume 的名称
spec:
  capacity:
    storage: 1Gi                # 容量为 1Gi
  accessModes:
    - ReadWriteOnce             # 访问模式为 ReadWriteOnce
  hostPath:
    path: /data/mysql           # 持久卷的主机路径为 /data/mysql

---
apiVersion: v1
kind: PersistentVolumeClaim     # 资源类型为 PersistentVolumeClaim
metadata:
  name: mysql-pvc               # PersistentVolumeClaim 的名称
spec:
  accessModes:
    - ReadWriteOnce             # 访问模式为 ReadWriteOnce
  resources:
    requests:
      storage: 1Gi              # 请求的存储容量为 1Gi

---
apiVersion: apps/v1
kind: Deployment                 # 资源类型为 Deployment
metadata:
  name: mysql-deployment         # Deployment 的名称
spec:
  replicas: 1                    # 副本数量为 1
  selector:
    matchLabels:
      app: mysql                 # 选择器用于选择标签为 app=mysql 的 Pod
  template:
    metadata:
      labels:
        app: mysql               # Pod 的标签为 app=mysql
    spec:
      containers:
      - name: mysql              # 容器名称为 mysql
        image: mysql:latest      # 容器使用的镜像为 mysql:latest
        env:                     # 定义容器环境变量
        - name: MYSQL_ROOT_PASSWORD      # MySQL root 用户密码
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: mysql-root-password
        - name: MYSQL_USER               # MySQL 用户名
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: mysql-user
        - name: MYSQL_PASSWORD           # MySQL 密码
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: mysql-password
        ports:
        - containerPort: 3306           # 容器监听的端口为 3306，相当于docke run -p 3306:3306
        volumeMounts:
        - name: mysql-config-volume      # 挂载 ConfigMap 到容器的 /etc/mysql/my.cnf
          mountPath: /etc/mysql/my.cnf
          subPath: my.cnf
        - name: mysql-data               # 挂载 PersistentVolumeClaim 到容器的 /var/lib/mysql
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-config-volume       # 定义 ConfigMap 卷
        configMap:
          name: mysql-config            # 引用的 ConfigMap 的名称为 mysql-config
      - name: mysql-data                # 定义数据卷
        persistentVolumeClaim:
          claimName: mysql-pvc          # 引用的 PersistentVolumeClaim 的名称为 mysql-pvc

```

```
tickTime=2000
acknowledgment
syncLimit=5
clientPort=2181
maxClientCnxns=60
peerType=leader
server.1=zookeeper1.example.com:2888:3888
server.2=zookeeper2.example.com:2888:3888
server.3=zookeeper3.example.com:2888:3888
dataLogDir=/var/lib/zookeeper/log
```


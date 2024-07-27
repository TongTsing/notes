```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: zookeeper
spec:
  serviceName: "zookeeper"
  replicas: 3
  selector:
    matchLabels:
      app: zookeeper
  template:
    metadata:
      labels:
        app: zookeeper
    spec:
      containers:
      - name: zookeeper
        image: <your-zookeeper-image>
        ports:
        - containerPort: 2181
        - containerPort: 2888
        - containerPort: 3888
        volumeMounts:
        - name: config
          mountPath: /conf
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
  volumeClaimTemplates:
  - metadata:
      name: config
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi

```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: zookeeper
spec:
  clusterIP: None
  selector:
    app: zookeeper
  ports:
  - name: client
    port: 2181
    targetPort: 2181
  - name: followers
    port: 2888
    targetPort: 2888
  - name: election
    port: 3888
    targetPort: 3888
```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: zookeeper-config
data:
  zoo.cfg: |
    tickTime=2000
    initLimit=10
    syncLimit=5
    dataDir=/data
    clientPort=2181
    server.1=zookeeper-0.zookeeper-headless:2888:3888
    server.2=zookeeper-1.zookeeper-headless:2888:3888
    server.3=zookeeper-2.zookeeper-headless:2888:3888
    autopurge.snapRetainCount=3
    autopurge.purgeInterval=1

---

apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: zookeeper
spec:
  serviceName: "zookeeper-headless"
  replicas: 3
  selector:
    matchLabels:
      app: zookeeper
  template:
    metadata:
      labels:
        app: zookeeper
    spec:
      containers:
      - name: zookeeper
        image: zookeeper:3.6.3
        ports:
        - containerPort: 2181
          name: client
        - containerPort: 2888
          name: followers
        - containerPort: 3888
          name: election
        volumeMounts:
        - name: data
          mountPath: /data
        - name: config
          mountPath: /conf
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: zookeeper-data
      - name: config
        configMap:
          name: zookeeper-config
          defaultMode: 0755

---

apiVersion: v1
kind: Service
metadata:
  name: zookeeper
spec:
  clusterIP: None
  selector:
    app: zookeeper
  ports:
  - name: client
    port: 2181
    targetPort: 2181
  - name: followers
    port: 2888
    targetPort: 2888
  - name: election
    port: 3888
    targetPort: 3888

---

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: zookeeper-data
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi

```


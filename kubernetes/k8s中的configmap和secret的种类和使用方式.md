**注意： Configmap和secret都是一种卷，可以直接挂载在到容器目录中**

**secret到创建方式和Configmap的创建方式类似，这里只介绍configmap的使用方式**

## 一、Configmap的创建

ConfigMap在Kubernetes中可以包含多种形式的配置信息，包括键值对、文件和JSON数据。下面是每种形式的具体配置和使用方法：

1. **键值对（Key-Value Pairs）**：
   这是最简单和常见的形式，其中每个键值对表示一个配置项。
   
   配置示例：
   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: my-configmap
   data:
     key1: value1
     key2: value2
   ```

   使用方法：
   你可以通过环境变量或者卷挂载的方式将ConfigMap中的键值对注入到Pod中，让应用程序能够访问这些配置信息。

2. **文件（Files）**：
   除了键值对之外，ConfigMap还可以包含文件。每个文件的内容可以是文本、JSON、YAML等格式。
   
   配置示例：
   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: my-configmap
   data:
     file1.txt: |
       This is the content of file1.txt.
     file2.json: |
       {
         "key": "value"
       }
   ```

   使用方法：
   你可以通过卷挂载的方式将ConfigMap中的文件挂载到Pod中的指定路径，应用程序可以读取这些文件来获取配置信息。

3. **JSON数据**：
   ConfigMap中的数据也可以是JSON格式的，这种形式特别适用于复杂的配置信息。
   
   配置示例：
   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: my-configmap
   data:
     config.json: |
       {
         "key1": "value1",
         "key2": "value2"
       }
   ```

   使用方法：
   你可以通过环境变量或者卷挂载的方式将JSON数据注入到Pod中，应用程序可以解析JSON数据来获取配置信息。

无论是哪种形式，你都可以通过Kubernetes的API或者命令行工具（如kubectl）来创建、管理和使用ConfigMap，以便让你的应用程序更灵活地处理配置信息。

## 二、configmap的使用

configmap的使用方式有三种：

1. 使用configmap的特定键注入环境变量
2. 一次性传入configmap中的所有条目作为环境变量
3. 用configmap的条目作为命令行参数

### 2.1 使用configmap的特定键注入环境变量

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mongodb
spec:
  containers:
    - name: mongodb
      image: mongo:latest
      ports:
        - containerPort: 27017
      env:
      # 这里每次valueFrom从configmap中获取一个键的值
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            configMapKeyRef:
              name: mongodb-config
              key: MONGO_INITDB_ROOT_USERNAME
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            configMapKeyRef:
              name: mongodb-config
              key: MONGO_INITDB_ROOT_PASSWORD
      volumeMounts:
        - name: mongo-persistent-storage
          mountPath: /data/db
  volumes:
    - name: mongo-persistent-storage
      persistentVolumeClaim:
        claimName: mongodb-pvc
```

### 2.2 一次性传入configmap中的所有条目作为环境变量

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mongodb
spec:
  containers:
    - name: mongodb
      image: mongo:latest
      ports:
        - containerPort: 27017
      # 这里使用envFrom获取configmap中的全部键值对作为环境变量
      envFrom:
      - prefix: MONGO
        configMapRef:
          name: mongodb-config
      volumeMounts:
        - name: mongo-persistent-storage
          mountPath: /data/db
  volumes:
    - name: mongo-persistent-storage
      persistentVolumeClaim:
        claimName: mongodb-pvc
```

### 2.3 用configmap的条目作为命令行参数

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mongodb
spec:
  containers:
    - name: mongodb
      image: mongo:latest
      ports:
        - containerPort: 27017
      env:
      # 这里每次valueFrom从configmap中获取一个键的值
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            configMapKeyRef:
              name: mongodb-config
              key: MONGO_INITDB_ROOT_USERNAME
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            configMapKeyRef:
              name: mongodb-config
              key: MONGO_INITDB_ROOT_PASSWORD
       # 使用 MONGO_INITDB_ROOT_USERNAME, 举个例子
     	args: ["$(MONGO_INITDB_ROOT_USERNAME)"]
      volumeMounts:
        - name: mongo-persistent-storage
          mountPath: /data/db
  volumes:
    - name: mongo-persistent-storage
      persistentVolumeClaim:
        claimName: mongodb-pvc
```


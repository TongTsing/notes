# k8s配置介绍

## 一、apiVersion

## 二、kind

## 三、metadata字段

Kubernetes中的Pods元数据（metadata）字段定义了有关Pod的描述性信息。Pods的metadata字段可以设置以下元素：

1. `name`：Pod的名称，必须是唯一的。
2. `namespace`：Pod所属的命名空间（Namespace），用于在Kubernetes集群中对Pod进行逻辑隔离和管理。
3. `labels`：一组键值对，用于对Pod进行标记和分类。标签可以用于选择Pod进行操作，例如通过标签选择器（label selector）选择一组Pod进行扩展、暴露服务等。
4. `annotations`：一组键值对，用于为Pod提供附加的元数据信息。注解通常用于存储与Pod相关的非标准或辅助信息，例如构建信息、监控指标等。
5. `ownerReferences`：指定与Pod相关的所有者引用，用于建立Pod与其他资源（例如ReplicaSet、Deployment等）之间的所有权关系。
6. `generateName`：用于自动生成Pod的名称的前缀。
7. `finalizers`：指定在删除Pod之前要执行的终结操作。
8. `clusterName`：指定Pod所属的集群名称。

## 四、spec字段

以下是一些常见的Deployment的`spec`字段及其介绍：

1. `replicas`：指定要创建的Pod副本数。

2. `selector`：指定用于选择要管理的Pod副本集的标签选择器。

3. `template`：指定创建Pod的模板。

   - `metadata`：Pod模板的元数据信息，包括标签等。

   - `spec`：Pod模板的规格，包括容器定义、卷挂载等。

4. `strategy`：指定滚动升级的策略。

   - `type`：指定滚动升级的类型，可以是"Recreate"或"RollingUpdate"。

   - `rollingUpdate`：如果选择了"RollingUpdate"类型的滚动升级，可以指定滚动升级的具体配置。

5. `minReadySeconds`：指定在滚动升级期间，每个新Pod变为Ready的最小秒数。

6. `progressDeadlineSeconds`：指定滚动升级的进度截止时间。

7. `revisionHistoryLimit`：指定保存的滚动升级的历史修订版本数。

8. `paused`：指定是否暂停Deployment的滚动升级。

9. `rollbackTo`：指定要回滚到的特定修订版本。

10. `strategy.rollingUpdate.maxUnavailable`：指定滚动升级期间允许的不可用Pod的最大数量。

11. `strategy.rollingUpdate.maxSurge`：指定滚动升级期间允许的额外的Pod副本的最大数量。

12. `template.spec.containers`：指定在Pod中运行的容器的定义。

    - `name`：容器的名称。

    - `image`：容器的镜像。

    - `ports`：容器暴露的端口。

    - `env`：容器的环境变量。

13. `template.spec.volumes`：指定要挂载到Pod中的卷。

这些字段只是Deployment的`spec`中的一部分。根据您的需求，还可以使用其他字段和选项。建议您参考Kubernetes官方文档以获取更详细的信息和指导。

以下是一个Deployment的`spec`字段的示例：

```yaml
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx:latest
          ports:
            - containerPort: 80
```

在上述示例中，`spec`字段指定了创建3个Pod副本、选择具有`app: my-app`标签的Pod副本集，并使用nginx容器映像运行Pod的模板。

请注意，这只是一个Deployment的`spec`字段的基本示例。根据需求，可以添加更多的配置选项。建议您参考Kubernetes官方文档以获取更详细的信息和指导。
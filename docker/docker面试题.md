# Docker 面试题 100 道

[![架构师专栏](https://picx.zhimg.com/v2-5810376cc738c9b353e8552116167c99_l.jpg?source=172ae18b)](https://www.zhihu.com/people/souyunku)

[架构师专栏](https://www.zhihu.com/people/souyunku)

[](https://www.zhihu.com/question/48510028)![img](https://picx.zhimg.com/v2-4812630bc27d642f7cafcd6cdeca3d7a.jpg?source=88ceefae)

郑州架构科技有限公司 架构师

关注他

10 人赞同了该文章



涵盖了Docker的基础知识、常用命令、网络和存储管理、Docker Compose、Docker Swarm等方面。

### **下面是一些样例面试题及其答案：**

### **基础概念**

1. **什么是Docker？**
2. 答：Docker是一个开源的容器化平台，它允许开发者将应用及其依赖打包到一个轻量级、可移植的容器中，从而在任何Docker运行的环境中实现一致的运行。
3. **Docker容器和虚拟机的区别是什么？**
4. 答：Docker容器在操作系统级别进行虚拟化，共享宿主机的内核，而虚拟机在硬件级别进行虚拟化，拥有独立的内核。容器通常更轻量级、启动更快，资源占用更少。
5. **什么是Docker镜像？**
6. 答：Docker镜像是一个轻量级、只读的模板，用于创建Docker容器。它包含运行容器所需的代码、库、环境变量和配置文件。
7. **如何创建Docker容器？**
8. 答：可以使用docker run命令来从镜像创建容器。例如，docker run -d -p 80:80 nginx会基于nginx镜像启动一个新的容器，并将容器的80端口映射到宿主机的80端口。
9. **Docker Hub是什么？**
10. 答：Docker Hub是一个公共的容器镜像仓库，可以用来存放、分享和管理Docker镜像。用户可以从Docker Hub下载公共镜像或上传自己的私有镜像。

### **常用命令**

1. **如何查看当前运行的Docker容器？**
2. 答：使用docker ps命令可以查看当前运行的容器。加上-a参数可以看到所有容器，包括未运行的。
3. **如何停止和启动Docker容器？**
4. 答：使用docker stop <容器ID或名称>可以停止容器。使用docker start <容器ID或名称>可以启动容器。
5. **如何进入正在运行的Docker容器？**
6. 答：可以使用docker exec -it <容器ID或名称> /bin/bash命令进入容器。这里-it表示交互式终端。
7. **如何删除Docker镜像和容器？**
8. 答：使用docker rmi <镜像ID>删除镜像，使用docker rm <容器ID>删除容器。如果容器正在运行，首先需要停止容器。
9. **如何查看Docker容器的日志？**
10. 答：使用docker logs <容器ID或名称>可以查看容器的日志输出。

### **网络管理**

1. **Docker的默认网络模式有哪些？**
2. 答：Docker的默认网络模式包括bridge、none、host和container。每种模式提供不同级别的网络隔离和互连。
3. **如何创建Docker网络？**
4. 答：使用docker network create命令可以创建Docker网络。例如，docker network create --driver bridge my_bridge_network创建了一个bridge类型的网络。
5. **Docker容器间通信是如何工作的？**
6. 答：容器可以通过Docker网络进行通信。在同一网络中的容器可以使用容器名称互相解析，实现容器间通信。

### **存储管理**

1. **什么是Docker卷（Volume）？**
2. 答：Docker卷是一种持久化存储数据的机制。它独立于容器的生命周期存在，可以用来存储容器的数据。
3. **如何创建和使用Docker卷？**
4. 答：可以使用docker volume create命令创建卷。使用卷的一个常见场景是在docker run命令中通过-v选项将卷挂载到容器内部。

### **Docker基础与命令**

1. **描述Dockerfile和其用途。**
2. 答：Dockerfile是一个文本文件，包含了构建Docker镜像所需的一系列指令和命令。
3. **Docker镜像和容器之间有什么区别？**
4. 答：Docker镜像是静态的定义，类似于类；而容器是镜像的实例，类似于对象。
5. **怎样使用Dockerfile创建镜像？**
6. 答：使用docker build命令，如docker build -t myimage:latest .。
7. **如何列出本地的Docker镜像？**
8. 答：使用docker images命令。
9. **怎样检查Docker容器的资源使用情况？**
10. 答：使用docker stats命令。

### **Docker网络**

1. **Docker中如何实现容器间的网络隔离？**
2. 答：通过创建不同的Docker网络并将容器连接到这些网络，可以实现网络隔离。
3. **解释Docker的网络模式中的bridge和host。**
4. 答：bridge是默认的网络模式，为容器提供了一个私有的内部网络。host模式使容器共享宿主机的网络栈。
5. **Docker的--link选项是用来做什么的？**
6. 答：--link用于链接两个容器，使它们可以相互通信（已被弃用，推荐使用自定义网络）。

### **Docker存储**

1. **解释Docker的持久化存储。**
2. 答：Docker通过卷（Volumes）和绑定挂载（Bind Mounts）实现数据持久化。
3. **什么是Docker卷的主要用途？**
4. 答：用于数据持久化和数据共享。
5. **Docker卷和绑定挂载有什么区别？**
6. 答：卷由Docker管理，存储在Docker主机的指定区域；绑定挂载可以存储在主机系统的任意位置。

### **Docker Compose**

1. **什么是Docker Compose？**
2. 答：Docker Compose是一个工具，用于定义和运行多容器Docker应用程序。
3. **如何启动使用Docker Compose定义的服务？**
4. 答：使用docker-compose up命令。
5. **在Docker Compose文件中，links参数的作用是什么？**
6. 答：用于定义容器间的依赖和通信规则（在Docker网络普及后，这个选项的重要性下降）。
7. **怎样在Docker Compose中设置环境变量？**
8. 答：可以在docker-compose.yml文件中使用environment键设置环境变量。

### **Docker Swarm**

1. **Docker Swarm是什么？**
2. 答：Docker Swarm是Docker的原生集群管理工具，用于在多个主机上部署和管理Docker容器。
3. **如何创建一个Docker Swarm集群？**
4. 答：使用docker swarm init在主节点上初始化集群。
5. **Docker Swarm中的服务(Service)和任务(Task)是什么？**
6. 答：服务定义了应用的状态，例如运行的副本数。任务是服务的一个实例，通常是一个容器。
7. **如何扩展Docker Swarm中的服务？**
8. 答：使用docker service scale命令来增减服务的副本数量。
9. **Docker Swarm和Kubernetes的主要区别是什么？**
10. 答：Kubernetes提供了更多的功能和灵活性，适用于更复杂的应用场景。Docker Swarm更简单，易于设置和管理。

### **安全和维护**

1. **如何保证Docker容器的安全性？**
2. 答：保持Docker和宿主机系统的更新，使用非root用户运行容器，限制容器的资源使用，使用Docker安全扫描等。
3. **怎样更新Docker容器？**
4. 答：通常，需要停止旧容器，更新镜像，然后使用新镜像启动新容器。
5. **Docker中的健康检查是如何工作的？**
6. 答：可以在Dockerfile中定义HEALTHCHECK指令或在docker-compose.yml中定义healthcheck来检查容器的健康状态。
7. **Docker中的资源限制是如何实现的？**
8. 答：使用docker run命令的--memory（内存）、--cpus（CPU）等选项来限制资源。
9. **如何清理未使用的Docker资源？**
10. 答：使用docker system prune命令可以清理悬挂的镜像、容器、网络和构建缓存。

### **高级话题**

1. **什么是Docker的多阶段构建？**
2. 答：多阶段构建是一种Dockerfile优化技术，允许在单个Dockerfile中使用多个构建阶段，减少最终镜像的大小。
3. **解释Docker的存储驱动。**
4. 答：存储驱动负责管理容器的文件系统，Docker支持多种存储驱动，如overlay2、aufs、btrfs等。
5. **Docker中的COPY和ADD命令有什么区别？**
6. 答：COPY仅用于复制本地文件，而ADD还可以解压缩文件和从URL下载文件。
7. **Docker是如何实现容器隔离的？**
8. 答：Docker使用Linux的命名空间和控制组（cgroups）来实现容器的隔离。
9. **解释Docker容器的重启策略。**
10. 答：Docker容器的重启策略决定了在退出时容器是否和如何重启。常用策略包括no、always、on-failure和unless-stopped。

这只是Docker面试问题的一部分，还有很多其他高级话题和细节问题。

掌握这些基础和进阶的问题，对于准备Docker相关的面试将大有帮助。祝你面试成功！
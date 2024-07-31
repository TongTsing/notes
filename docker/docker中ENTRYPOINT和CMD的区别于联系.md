![image-20240601105117580](/Users/tongqing/Library/Application Support/typora-user-images/image-20240601105117580.png)

在 Dockerfile 中，ENTRYPOINT 和 CMD 都是用于定义容器启动时要执行的命令，但它们之间有一些重要的区别。

1. **CMD**：
   - CMD 定义了容器启动时默认要执行的命令。如果 Dockerfile 中有多个 CMD 指令，只有最后一个会生效。
   - CMD 可以被 docker run 命令的参数覆盖。如果你在运行容器时指定了要执行的命令，它会替换掉 CMD 中定义的默认命令。
   - 如果 Dockerfile 中没有指定 CMD，那么 Docker 会使用基础镜像中的默认 CMD（如果有的话）。

2. **ENTRYPOINT**：
   - ENTRYPOINT 定义了容器启动时要执行的可执行文件或脚本。
   - ENTRYPOINT 的参数可以被 docker run 命令的参数覆盖，但它不会被 CMD 覆盖。换句话说，CMD 会作为 ENTRYPOINT 的参数。
   - 如果在 Dockerfile 中同时使用了 ENTRYPOINT 和 CMD，CMD 的内容会作为 ENTRYPOINT 的参数。

联系：
- 你可以使用 ENTRYPOINT 来指定容器的主要可执行文件或脚本，而使用 CMD 来提供默认的参数。这样做使得容器更加灵活，用户可以通过指定不同的参数来修改容器的行为。
- 如果你的 Dockerfile 中只有一个命令需要执行，那么可以使用 CMD。而如果你想要提供一个通用的容器，可以使用 ENTRYPOINT 来定义容器的主要执行程序，然后使用 CMD 来提供默认参数。
- 当你需要在容器启动时执行一些初始化操作时，可以将这些操作放在 ENTRYPOINT 中。而如果你希望用户能够自定义容器的行为，可以将一些常用的参数放在 CMD 中供用户覆盖。

综上所述，ENTRYPOINT 和 CMD 是定义容器启动时要执行的命令的两种方式，它们可以结合使用以提供更灵活的容器配置。
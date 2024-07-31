在 CentOS 中，Systemd 是主要的服务管理工具，用于启动、停止和管理系统服务。Service 文件是 Systemd 的配置文件，用于定义服务的行为和属性。以下是 CentOS 中 service 文件的基本配置：

1. **[Unit] 部分**：
   - `Description`: 描述服务的简短信息。
   - `After`: 指定在哪些其他服务之后启动该服务。
   - `Requires`/`Wants`: 指定服务所依赖的其他服务，`Requires` 表示依赖关系是必需的，而 `Wants` 表示依赖关系是可选的。

2. **[Service] 部分**：
   - `Type`: 指定服务的类型，如 simple、forking、oneshot 等。
   - `ExecStart`: 指定启动服务时执行的命令或脚本。
   - `ExecStop`: 指定停止服务时执行的命令或脚本。
   - `Restart`: 指定服务在意外终止后的重启策略，如 always、on-failure 等。
   - `User`/`Group`: 指定服务运行的用户和用户组。
   - `WorkingDirectory`: 指定服务的工作目录。

3. **[Install] 部分**：
   - `WantedBy`/`RequiredBy`: 指定哪些目标（targets）或服务依赖于该服务。

一个简单的示例 service 文件可能如下所示：

```plaintext
[Unit]
Description=My Service
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/my_service
Restart=always
User=myuser
Group=mygroup

[Install]
WantedBy=multi-user.target
```

在这个示例中，我们定义了一个名为 "My Service" 的服务，它依赖于网络服务，在系统启动后会被启动。该服务使用 `myuser` 用户和 `mygroup` 用户组来运行，它的启动命令是 `/usr/bin/my_service`，如果服务意外终止，它会被自动重启。最后，它被安装到 `multi-user.target` 目标中，意味着它会在多用户模式下启动。
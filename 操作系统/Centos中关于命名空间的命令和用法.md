Linux 命名空间是 Linux 内核提供的一种机制，用于隔离系统资源。它允许创建具有独立视图的虚拟化环境，使得每个命名空间中的进程可以看到自己的一套资源，而不受其他命名空间中进程的影响。这种隔离性使得命名空间成为容器技术的核心基础之一。以下是 Linux 中常见的几种命名空间：

1. **PID 命名空间**：进程命名空间隔离了进程 ID（PID）的编号空间。在不同的 PID 命名空间中，相同的 PID 可以被分配给不同的进程，从而实现进程的隔离。

2. **Network 命名空间**：网络命名空间隔离了网络设备、IP 地址、路由表、防火墙规则等网络资源。不同的网络命名空间中的进程可以拥有独立的网络栈和网络配置。

3. **Mount 命名空间**：挂载命名空间隔离了文件系统挂载点。在不同的挂载命名空间中，可以拥有独立的文件系统挂载结构，使得不同命名空间中的进程可以看到不同的文件系统视图。

4. **IPC 命名空间**：IPC 命名空间隔离了进程间通信资源，比如消息队列、信号量和共享内存。不同 IPC 命名空间中的进程无法直接通信。

5. **UTS 命名空间**：UTS 命名空间隔离了主机名和域名。每个 UTS 命名空间中的进程可以拥有独立的主机名和域名。

6. **User 命名空间**：用户命名空间隔离了用户和用户组 ID。它允许非特权用户在一个命名空间中拥有特权权限，而在另一个命名空间中则只拥有普通用户权限，从而增强了安全性。

通过命名空间，Linux 提供了一种轻量级的虚拟化技术，使得应用程序和服务可以在隔离的环境中运行，提高了系统资源的利用率和安全性。

### 1. **lsns** :

`lsns` 命令用于列出系统中当前存在的命名空间。它提供了一个简单的方法来查看系统中各种类型的命名空间及其相关信息。`lsns` 命令的基本语法是：

```shell
lsns [options] [type]
```

其中，`type` 参数用于指定要列出的命名空间类型，可选的类型包括：

- `ipc`：IPC 命名空间
- `mnt`：挂载命名空间
- `net`：网络命名空间
- `pid`：PID 命名空间
- `user`：用户命名空间
- `uts`：UTS 命名空间

默认情况下，如果不指定类型，则 `lsns` 命令将列出所有类型的命名空间。

lsns 命令有几个选项（Option）可以用来过滤输出或显示更详细的信息：

- `-j, --json`：以 JSON 格式输出结果。
- `-n, --noheadings`：不显示标题行。
- `-o, --output`：自定义输出格式。
- `-t, --type`：根据命名空间类型进行过滤，比如 "pid"、"net"、"ipc"、"mnt"、"uts"、"cgroup"。
- `-u, --list[=xxx]`：仅显示给定类型的命名空间。可以是 "ipc"、"mnt"、"net"、"pid"、"user"、"uts" 或 "cgroup"。
- `-p, --path`：显示命名空间的路径。

这些选项可以帮助你根据需要调整输出的内容和格式。

### 2. **`nsenter`**：

`nsenter` 命令允许用户进入到另一个进程的命名空间中。这对于在一个命名空间中执行命令或者查看另一个命名空间的状态非常有用。`nsenter` 命令的基本语法是：

```bash
nsenter [options] [ --target PID ] [ --mount[=FILE] ] [ --uts[=FILE] ] [ --ipc[=FILE] ] [ --net[=FILE] ] [ --pid[=FILE] ] [ --user[=FILE] ]
```

`nsenter` 是一个 Linux 命令，允许用户进入命名空间并执行命令。以下是 `nsenter` 命令的全部选项（Option）及其注释：

```
Usage: nsenter [options] [<program> [<argument>...]]
  -t, --target <pid>          Target process to get namespaces from
  -m, --mount                 Enter the mount namespace
  -u, --uts                   Enter the UTS namespace (hostname etc)
  -i, --ipc                   Enter the IPC namespace
  -n, --net                   Enter the network namespace
  -p, --pid                   Enter the pid namespace
  -C, --cgroup <path>         Enter the cgroup namespace
  -S, --setuid <uid>          Set the uid in the new namespace
  -G, --setgid <gid>          Set the gid in the new namespace
  -r, --root[=<dir>]          Set the root directory
  -w, --wd[=<dir>]            Set the working directory
  -F, --no-fork               Do not fork before exec'ing <program>
  -Z, --follow-context        Set the SELinux security context
  -h, --help                  Display this help
  -V, --version               Display version
```

选项解释：
- `-t, --target <pid>`：指定要获取命名空间的目标进程。
- `-m, --mount`：进入挂载命名空间。
- `-u, --uts`：进入 UTS 命名空间（主机名等）。
- `-i, --ipc`：进入 IPC 命名空间。
- `-n, --net`：进入网络命名空间。
- `-p, --pid`：进入 PID 命名空间。
- `-C, --cgroup <path>`：进入 cgroup 命名空间。
- `-S, --setuid <uid>`：在新命名空间中设置 UID。
- `-G, --setgid <gid>`：在新命名空间中设置 GID。
- `-r, --root[=<dir>]`：设置根目录。
- `-w, --wd[=<dir>]`：设置工作目录。
- `-F, --no-fork`：在执行 `<program>` 前不 fork。
- `-Z, --follow-context`：设置 SELinux 安全上下文。
- `-h, --help`：显示帮助信息。
- `-V, --version`：显示版本信息。


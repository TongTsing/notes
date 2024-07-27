要将进程限制到指定的 CPU 上，可以使用 Linux 中的 `taskset` 命令。`taskset` 命令用于设置或检查进程的 CPU 亲和性（Affinity）。

以下是 `taskset` 命令的基本用法：

```bash
taskset -c <cpu_list> <command>
```

其中：

- `-c <cpu_list>`：指定要将进程绑定到的 CPU 列表。`<cpu_list>` 是一个以逗号分隔的 CPU 编号列表，例如 `0,1,2` 表示绑定到 CPU0、CPU1 和 CPU2 上。
- `<command>`：要执行的命令和参数。

例如，如果要将进程绑定到 CPU0 上，可以使用以下命令：

```bash
taskset -c 0 command_to_execute
```

这样就可以将 `command_to_execute` 进程限制在 CPU0 上运行。

另外，如果要将已经在运行的进程绑定到指定的 CPU 上，可以使用 `taskset -p <pid>` 命令，例如：

```bash
taskset -cp 0 <pid>
```

这样就可以将 PID 为 `<pid>` 的进程绑定到 CPU0 上运行。

请注意，`taskset` 命令需要以 root 或具有适当权限的用户身份运行。

关键字：进程绑定cpu
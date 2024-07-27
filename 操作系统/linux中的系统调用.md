理解了，以下是 Linux 系统中所有的系统调用列表（截至到 Linux 内核版本 5.15）：

好的，下面是对每个系统调用的简要介绍以及它们的参数：

1. **`accept()`**：
   - 功能：接受一个新的连接请求，并创建一个新的套接字用于通信。
   - 参数：`int sockfd, struct sockaddr *addr, socklen_t *addrlen`.
     - `sockfd`: 监听套接字描述符。
     - `addr`: 指向一个 `struct sockaddr` 结构的指针，用于存储连接方的地址信息。
     - `addrlen`: 指向一个 `socklen_t` 类型的整数，用于存储 `addr` 结构的大小。

2. **`accept4()`**：
   - 功能：`accept()` 的变体，允许设置附加的标志。
   - 参数：`int sockfd, struct sockaddr *addr, socklen_t *addrlen, int flags`.
     - `sockfd`, `addr`, `addrlen` 参数同 `accept()`。
     - `flags`: 附加的标志，用于控制接受操作的行为。

3. **`access()`**：
   - 功能：检查文件的访问权限。
   - 参数：`const char *pathname, int mode`.
     - `pathname`: 文件路径名。
     - `mode`: 访问模式，如 `R_OK`（检查读权限）、`W_OK`（检查写权限）、`X_OK`（检查执行权限）等。

4. **`acct()`**：
   - 功能：启用或禁用进程账户。
   - 参数：`const char *filepath`.
     - `filepath`: 账户文件路径。

5. **`add_key()`**：
   - 功能：添加一个密钥到内核密钥环。
   - 参数：`const char *type, const char *description, const void *payload, size_t plen, key_serial_t keyring`.
     - `type`: 密钥类型。
     - `description`: 密钥描述。
     - `payload`: 密钥数据。
     - `plen`: 密钥数据长度。
     - `keyring`: 密钥环标识。

6. **`adjtimex()`**：
   - 功能：调整内核中的时钟频率。
   - 参数：`struct timex *buf`.
     - `buf`: 指向 `struct timex` 结构的指针，用于传递时间调整参数和获取调整结果。

7. **`arch_prctl()`**：
   - 功能：设置或获取进程的体系结构相关的控制参数。
   - 参数：`int code, unsigned long addr`.
     - `code`: 控制码，用于指定要执行的操作。
     - `addr`: 操作相关的地址或参数。

8. **`bind()`**：

   - 功能：将套接字与地址绑定。

   - 参数：

     ```
     int sockfd, const struct sockaddr *addr, socklen_t addrlen
     ```

     .

     - `sockfd`: 套接字描述符。
     - `addr`: 指向要绑定的地址的 `struct sockaddr` 结构的指针。
     - `addrlen`: 地址结构的大小。

9. **`bpf()`**：

   - 功能：执行 Berkeley Packet Filter (BPF) 操作。

   - 参数：

     ```
     int cmd, union bpf_attr *attr, unsigned int size
     ```

     .

     - `cmd`: BPF 命令。
     - `attr`: 指向 `bpf_attr` 结构的指针，用于传递操作参数。
     - `size`: `attr` 结构的大小。

10. **`brk()`**：

    - 功能：改变程序的数据段的结束地址。
    - 参数：`void *end_data_segment`.

11. **`capget()`**：

    - 功能：获取进程的能力。
    - 参数：`cap_user_header_t header, cap_user_data_t data`.

12. **`capset()`**：

    - 功能：设置进程的能力。
    - 参数：`cap_user_header_t header, const cap_user_data_t data`.

13. **`chdir()`**：

    - 功能：改变当前工作目录。
    - 参数：`const char *path`.

14. **`chmod()`**：

    - 功能：改变文件的权限。
    - 参数：`const char *pathname, mode_t mode`.

15. **`chown()`**：

    - 功能：改变文件的所有者和所属组。
    - 参数：`const char *pathname, uid_t owner, gid_t group`.

16. **`chroot()`**：

    - 功能：改变根目录。
    - 参数：`const char *path`.

17. **`clock_adjtime()`**：

    - 功能：调整时钟的偏移量。
    - 参数：`clockid_t clk_id, struct timex *buf`.

18. **`clock_getres()`**：

    - 功能：获取时钟的精度。
    - 参数：`clockid_t clk_id, struct timespec *res`.

19. **`clock_gettime()`**：

    - 功能：获取指定时钟的时间。
    - 参数：`clockid_t clk_id, struct timespec *tp`.

20. **`clock_nanosleep()`**：

    - 功能：以纳秒为单位进行睡眠。
    - 参数：`clockid_t clock_id, int flags, const struct timespec *request, struct timespec *remain`.

21. **`clock_settime()`**：

    - 功能：设置指定时钟的时间。
    - 参数：`clockid_t clk_id, const struct timespec *tp`.

22. **`clone()`**：

    - 功能：创建一个新进程。
    - 参数：`int (*fn)(void *), void *child_stack, int flags, void *arg`.

23. **`close()`**：

    - 功能：关闭文件描述符。
    - 参数：`int fd`.

24. **`connect()`**：

    - 功能：建立与指定套接字地址的连接。
    - 参数：`int sockfd, const struct sockaddr *addr, socklen_t addrlen`.

25. **`copy_file_range()`**：

    - 功能：在两个文件描述符之间复制数据。
    - 参数：`int fd_in, loff_t *off_in, int fd_out, loff_t *off_out, size_t len, unsigned int flags`.

26. **`creat()`**：

    - 功能：创建或打开一个文件。
    - 参数：`const char *pathname, mode_t mode`.

27. **`delete_module()`**：

    - 功能：卸载一个内核模块。
    - 参数：`const char *name_user`.

28. **`dup()`**：

    - 功能：复制文件描述符。
    - 参数：`int oldfd`.

29. **`dup2()`**：

    - 功能：将一个文件描述符复制到另一个文件描述符。
    - 参数：`int oldfd, int newfd`.

30. **`dup3()`**：

    - 功能：将一个文件描述符复制到另一个文件描述符，并允许设置标志。
    - 参数：`int oldfd, int newfd, int flags`.

31. **`epoll_create()`**：

    - 功能：创建一个 epoll 实例。
    - 参数：`int size`.

32. **`epoll_create1()`**：

    - 功能：创建一个 epoll 实例，并允许设置标志。
    - 参数：`int flags`.

33. **`epoll_ctl()`**：

    - 功能：控制 epoll 实例上的事件。
    - 参数：`int epfd, int op, int fd, struct epoll_event *event`.

34. **`epoll_ctl_old()`**：

    - 功能：旧版本的 epoll_ctl()，不推荐使用。
    - 参数：`int epfd, int op, int fd, struct epoll_event *event`.

35. **`epoll_pwait()`**：

    - 功能：等待 epoll 事件的发生。
    - 参数：`int epfd, struct epoll_event *events, int maxevents, int timeout, const sigset_t *sigmask`.

36. **`epoll_wait()`**：

    - 功能：等待 epoll 事件的发生。
    - 参数：`int epfd, struct epoll_event *events, int maxevents, int timeout`.

37. **`epoll_wait_old()`**：

    - 功能：旧版本的 epoll_wait()，不推荐使用。
    - 参数：`int epfd, struct epoll_event *events, int maxevents, int timeout`.

38. **`eventfd()`**：

    - 功能：创建一个用于事件通知的文件描述符。
    - 参数：`unsigned int initval, int flags`.

39. **`eventfd2()`**：

    - 功能：创建一个用于事件通知的文件描述符，并允许设置标志。
    - 参数：`unsigned int initval, int flags`.

40. **`execve()`**：

    - 功能：在当前进程中执行一个新的程序。
    - 参数：`const char *filename, char *const argv[], char *const envp[]`.

41. **`exit()`**：

    - 功能：终止当前进程。
    - 参数：`int status`.

42. **`exit_group()`**：

    - 功能：终止当前进程组中的所有线程。
    - 参数：`int status`.

43. **`faccessat()`**：

    - 功能：检查文件的访问权限。

    - 参数：

      ```
      int dirfd, const char *pathname, int mode
      ```

      .

      - `dirfd`: 目录文件描述符。
      - `pathname`: 文件路径名。
      - `mode`: 访问模式，如 `R_OK`（检查读权限）、`W_OK`（检查写权限）、`X_OK`（检查执行权限）等。

44. **`fadvise64()`**：

    - 功能：给文件做出预测性的读取和写入建议。
    - 参数：`int fd, off64_t offset, off64_t len, int advice`.

45. **`fallocate()`**：

    - 功能：为文件分配磁盘空间。
    - 参数：`int fd, int mode, off_t offset, off_t len`.

46. **`fanotify_init()`**：

    - 功能：初始化 fanotify 监视器。
    - 参数：`unsigned int flags, unsigned int event_f_flags`.

47. **`fanotify_mark()`**：

    - 功能：在 fanotify 监视器上标记文件或目录。
    - 参数：`int fanotify_fd, unsigned int flags, uint64_t mask, int fd, const char *pathname`.

48. **`fchdir()`**：

    - 功能：改变当前工作目录。
    - 参数：`int fd`.

49. **`fchmod()`**：

    - 功能：改变文件的权限。
    - 参数：`int fd, mode_t mode`.

50. **`fchmodat()`**：

    - 功能：改变文件的权限。
    - 参数：`int dirfd, const char *pathname, mode_t mode`.

51. **`fchown()`**：

    - 功能：改变文件的所有者和所属组。
    - 参数：`int fd, uid_t owner, gid_t group`.

52. **`fchownat()`**：

    - 功能：改变文件的所有者和所属组。
    - 参数：`int dirfd, const char *pathname, uid_t owner, gid_t group, int flags`.

53. **`fcntl()`**：

    - 功能：对已打开的文件描述符进行控制。
    - 参数：`int fd, int cmd, ...`.

54. **`fdatasync()`**：

    - 功能：同步文件数据到磁盘，但不同步文件的属性信息。
    - 参数：`int fd`.

55. **`fgetxattr()`**：

    - 功能：获取文件的扩展属性。
    - 参数：`int fd, const char *name, void *value, size_t size`.

56. **`finit_module()`**：

    - 功能：初始化一个内核模块。
    - 参数：`int fd, const char *uargs, int flags`.

57. **`flistxattr()`**：

    - 功能：列出文件的扩展属性。
    - 参数：`int fd, char *list, size_t size`.

58. **`flock()`**：

    - 功能：对文件进行加锁。
    - 参数：`int fd, int operation`.

59. **`fremovexattr()`**：

    - 功能：移除文件的扩展属性。
    - 参数：`int fd, const char *name`.

60. **`fsconfig()`**：

    - 功能：对文件系统进行配置。
    - 参数：`int cmd, unsigned int fs_fd, unsigned int flags, const char *data`.

61. **`fsetxattr()`**：

    - 功能：设置文件的扩展属性。
    - 参数：`int fd, const char *name, const void *value, size_t size, int flags`.

62. **`fstat()`**：

    - 功能：获取文件的状态信息。
    - 参数：`int fd, struct stat *statbuf`.

63. **`fstat64()`**：

    - 功能：获取文件的状态信息。
    - 参数：`int fd, struct stat64 *statbuf`.

64. **`fstatat64()`**：

    - 功能：获取文件的状态信息。
    - 参数：`int dirfd, const char *pathname, struct stat64 *statbuf, int flags`.

65. **`fstatfs()`**：

    - 功能：获取文件系统的状态信息。
    - 参数：`int fd, struct statfs *buf`.

66. **`fstatfs64()`**：

    - 功能：获取文件系统的状态信息。
    - 参数：`int fd, struct statfs64 *buf`.

67. **`fsync()`**：

    - 功能：同步文件数据和属性到磁盘。
    - 参数：`int fd`.

68. **`ftruncate()`**：

    - 功能：将文件截断到指定的长度。
    - 参数：`int fd, off_t length`.

69. **`ftruncate64()`**：

    - 功能：将文件截断到指定的长度。
    - 参数：`int fd, off64_t length`.

70. **`futex()`**：

    - 功能：提供一个用户态可扩展的锁。
    - 参数：`int *uaddr, int futex_op, int val, const struct timespec *timeout, int *uaddr2, int val3`.

71. **`futimesat()`**：

    - 功能：改变文件的访问和修改时间。
    - 参数：`int dirfd, const char *pathname, const struct timeval times[2]`

72. **`get_kernel_syms()`**：

    - 功能：获取内核符号表。
    - 参数：`struct kernel_sym *table`.

73. **`get_mempolicy()`**：

    - 功能：获取内存分配策略。
    - 参数：`int *mode, unsigned long *nodemask, unsigned long maxnode, unsigned long addr, unsigned long flags`.

74. **`get_robust_list()`**：

    - 功能：获取 robust mutexes 列表。
    - 参数：`int pid, struct robust_list_head **head_ptr, size_t *len_ptr`.

75. **`get_thread_area()`**：

    - 功能：获取 TLS（Thread-Local Storage）信息。
    - 参数：`struct user_desc *u_info`.

76. **`getcpu()`**：

    - 功能：获取当前 CPU 编号和 NUMA 节点编号。
    - 参数：`unsigned *cpu, unsigned *node, struct getcpu_cache *unused`.

77. **`getcwd()`**：

    - 功能：获取当前工作目录。
    - 参数：`char *buf, size_t size`.

78. **`getdents()`**：

    - 功能：从目录文件描述符中读取目录条目。
    - 参数：`unsigned int fd, struct linux_dirent *dirent, unsigned int count`.

79. **`getdents64()`**：

    - 功能：从目录文件描述符中读取目录条目。
    - 参数：`unsigned int fd, struct linux_dirent64 *dirent, unsigned int count`.

80. **`getegid()`**：

    - 功能：获取有效组 ID。
    - 参数：无。

81. **`geteuid()`**：

    - 功能：获取有效用户 ID。
    - 参数：无。

82. **`getgid()`**：

    - 功能：获取组 ID。
    - 参数：无。

83. **`getgroups()`**：

    - 功能：获取用户所属的附加组列表。
    - 参数：`int size, gid_t list[]`.

84. **`getitimer()`**：

    - 功能：获取定时器的当前值。
    - 参数：`int which, struct itimerval *curr_value`.

85. **`getpeername()`**：

    - 功能：获取连接的对端地址。
    - 参数：`int sockfd, struct sockaddr *addr, socklen_t *addrlen`.

86. **`getpgid()`**：

    - 功能：获取进程组 ID。
    - 参数：`pid_t pid`.

87. **`getpgrp()`**：

    - 功能：获取进程组 ID。
    - 参数：无。

88. **`getpid()`**：

    - 功能：获取进程 ID。
    - 参数：无。

89. **`getppid()`**：

    - 功能：获取父进程 ID。
    - 参数：无。

90. **`getpriority()`**：

    - 功能：获取进程或进程组的优先级。
    - 参数：`int which, int who`.

91. **`getrandom()`**：

    - 功能：获取随机数。
    - 参数：`void *buf, size_t buflen, unsigned int flags`.

92. **`getresgid()`**：

    - 功能：获取实际、有效和保存的组 ID。
    - 参数：`gid_t *rgid, gid_t *egid, gid_t *sgid`.

93. **`getresuid()`**：

    - 功能：获取实际、有效和保存的用户 ID。
    - 参数：`uid_t *ruid, uid_t *euid, uid_t *suid`.

94. **`getrlimit()`**：

    - 功能：获取进程资源限制。
    - 参数：`int resource, struct rlimit *rlim`.

95. **`getrusage()`**：

    - 功能：获取进程或子进程的资源使用情况。
    - 参数：`int who, struct rusage *usage`.

96. **`getsid()`**：

    - 功能：获取会话 ID。
    - 参数：`pid_t pid`.

97. **`getsockname()`**：

    - 功能：获取套接字的本地地址。
    - 参数：`int sockfd, struct sockaddr *addr, socklen_t *addrlen`.

98. **`getsockopt()`**：

    - 功能：获取套接字选项。
    - 参数：`int sockfd, int level, int optname, void *optval, socklen_t *optlen`.

99. **`gettid()`**：

    - 功能：获取线程 ID。
    - 参数：无。

100. **`gettimeofday()`**：

     - 功能：获取当前时间。
     - 参数：`struct timeval *tv, struct timezone *tz`.

101. **`getuid()`**：

     - 功能：获取用户 ID。
     - 参数：无。

102. **`getxattr()`**：

     - 功能：获取文件的扩展属性。
     - 参数：`const char *path, const char *name, void *value, size_t size`.

103. **`init_module()`**：

     - 功能：初始化一个内核模块。
     - 参数：`void *umod, unsigned long len, const char *uargs`.

104. **`inotify_add_watch()`**：

     - 功能：向 inotify 实例添加一个监视项。
     - 参数：`int fd, const char *pathname, uint32_t mask`.

105. **`inotify_init()`**：

     - 功能：初始化一个 inotify 实例。
     - 参数：无。

106. **`inotify_init1()`**：

     - 功能：初始化一个 inotify 实例，并允许设置标志。
     - 参数：`int flags`.

107. **`inotify_rm_watch()`**：

     - 功能：从 inotify 实例中移除一个监视项。
     - 参数：`int fd, int wd`.

108. **`io_cancel()`**：

     - 功能：取消异步 I/O 操作。
     - 参数：`aio_context_t ctx_id, struct iocb *iocb, struct io_event *result`.

109. **`io_destroy()`**：

     - 功能：销毁异步 I/O 上下文。
     - 参数：`aio_context_t ctx`.

110. **`io_getevents()`**：

     - 功能：等待异步 I/O 事件并获取它们的结果。
     - 参数：`aio_context_t ctx_id, long min_nr, long nr, struct io_event *events, struct timespec *timeout`.

111. **`io_pgetevents()`**：

     - 功能：等待异步 I/O 事件并获取它们的结果。
     - 参数：`aio_context_t ctx_id, long min_nr, long nr, struct io_event *events, const struct timespec *timeout, const struct __kernel_timespec *sigmask`.

112. **`io_setup()`**：

     - 功能：初始化一个异步 I/O 上下文。
     - 参数：`unsigned nr_events, aio_context_t *ctxp`.

113. **`io_submit()`**：

     - 功能：提交异步 I/O 操作。
     - 参数：`aio_context_t ctx_id, long nr, struct iocb **iocbpp`.

114. **`ioctl()`**：

     - 功能：设备 I/O 控制操作。
     - 参数：`int fd, unsigned long request, ...`.

115. **`ioperm()`**：

     - 功能：设置 I/O 端口权限。
     - 参数：`unsigned long from, unsigned long num, int on`.

116. **`iopl()`**：

     - 功能：提升当前进程的 I/O 权限级别。
     - 参数：`unsigned int level`.

117. **`ioprio_get()`**：

     - 功能：获取进程或进程组的 I/O 优先级。
     - 参数：`int which, int who`.

118. **`ioprio_set()`**：

     - 功能：设置进程或进程组的 I/O 优先级。
     - 参数：`int which, int who, int ioprio`.

119. **`kcmp()`**：

     - 功能：比较两个进程的内存状态。
     - 参数：`pid_t pid1, pid_t pid2, int type, unsigned long idx1, unsigned long idx2`.

120. **`kexec_load()`**：

     - 功能：加载一个新的内核并执行。
     - 参数：`unsigned long entry, unsigned long nr_segments, struct kexec_segment *segments, unsigned long flags`.

121. **`keyctl()`**：

     - 功能：对密钥管理进行操作。
     - 参数：`int cmd, ...`.

122. **`kill()`**：

     - 功能：向进程发送信号。
     - 参数：`pid_t pid, int sig`.

123. **`lchown()`**：

     - 功能：改变文件的所有者和所属组（不解析符号链接）。
     - 参数：`const char *pathname, uid_t owner, gid_t group`.

124. **`lgetxattr()`**：

     - 功能：获取符号链接的扩展属性。
     - 参数：`const char *path, const char *name, void *value, size_t size`.

125. **`link()`**：

     - 功能：创建一个硬链接。
     - 参数：`const char *oldpath, const char *newpath`.

126. **`linkat()`**：

     - 功能：创建一个硬链接。
     - 参数：`int olddirfd, const char *oldpath, int newdirfd, const char *newpath, int flags`.

127. **`listen()`**：

     - 功能：将套接字设置为监听模式。
     - 参数：`int sockfd, int backlog`.

128. **`listxattr()`**：

     - 功能：列出文件的扩展属性。
     - 参数：`const char *path, char *list, size_t size`.

129. **`llistxattr()`**：

     - 功能：列出符号链接的扩展属性。
     - 参数：`const char *path, char *list, size_t size`.

130. **`lookup_dcookie()`**：

     - 功能：根据 cookie 值获取目录项路径。
     - 参数：`u64 cookie, char *buf, size_t len`.

131. **`lremovexattr()`**：

     - 功能：移除符号链接的扩展属性。
     - 参数：`const char *path, const char *name`.

132. **`lseek()`**：

     - 功能：改变文件读写位置。
     - 参数：`int fd, off_t offset, int whence`.

133. **`lsetxattr()`**：

     - 功能：设置符号链接的扩展属性。
     - 参数：`const char *path, const char *name, const void *value, size_t size, int flags`.

134. **`madvise()`**：

     - 功能：提供对内存映射区域的建议。
     - 参数：`void *addr, size_t length, int advice`.

135. **`mbind()`**：

     - 功能：绑定内存页到节点和/或内存区域。
     - 参数：`const void *addr, unsigned long len, int mode, const unsigned long *nmask, unsigned long maxnode, unsigned flags`.

136. **`membarrier()`**：

     - 功能：控制内存屏障的行为。
     - 参数：`int cmd, int flags`.

137. **`memfd_create()`**：

     - 功能：创建一个匿名文件描述符。
     - 参数：`const char *name, unsigned int flags`.

138. **`migrate_pages()`**：

     - 功能：迁移进程的内存页到其他节点。
     - 参数：`pid_t pid, unsigned long maxnode, const unsigned long *old_nodes, const unsigned long *new_nodes`.

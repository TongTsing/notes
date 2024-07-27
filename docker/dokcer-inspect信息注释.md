```yaml
- Id: "67fc52c62641cd5f72c1c9802f186a0e8fdca7df3ad21e0e7595e14b81589997"  # 容器ID
  Created: "2024-03-14T02:01:56.900364803Z"  # 创建时间
  Path: "docker-entrypoint.sh"  # 容器启动路径
  Args:
    - "mysqld"  # 启动参数
  State:  # 容器状态
    Status: "running"  # 运行状态
    Running: true  # 是否正在运行
    Paused: false  # 是否已暂停
    Restarting: false  # 是否正在重启
    OOMKilled: false  # 是否因内存耗尽而被杀死
    Dead: false  # 是否已停止
    Pid: 85481  # 容器的进程ID
    ExitCode: 0  # 退出码
    Error: ""  # 错误信息
    StartedAt: "2024-03-14T02:01:58.557643091Z"  # 启动时间
    FinishedAt: "0001-01-01T00:00:00Z"  # 完成时间
  Image: "sha256:019814493c7ab16a057af0399b1288a1208b75ba852b915541840095c0fedfd0"  # 镜像ID
  ResolvConfPath: "/var/lib/docker/containers/67fc52c62641cd5f72c1c9802f186a0e8fdca7df3ad21e0e7595e14b81589997/resolv.conf"  # 解析配置文件路径
  HostnamePath: "/var/lib/docker/containers/67fc52c62641cd5f72c1c9802f186a0e8fdca7df3ad21e0e7595e14b81589997/hostname"  # 主机名路径
  HostsPath: "/var/lib/docker/containers/67fc52c62641cd5f72c1c9802f186a0e8fdca7df3ad21e0e7595e14b81589997/hosts"  # Hosts文件路径
  LogPath: "/var/lib/docker/containers/67fc52c62641cd5f72c1c9802f186a0e8fdca7df3ad21e0e7595e14b81589997/67fc52c62641cd5f72c1c9802f186a0e8fdca7df3ad21e0e7595e14b81589997-json.log"  # 日志路径
  Name: "/mysql_container"  # 容器名称
  RestartCount: 0  # 重启次数
  Driver: "overlay2"  # 驱动类型
  Platform: "linux"  # 平台
  MountLabel: ""  # 挂载标签
  ProcessLabel: ""  # 进程标签
  AppArmorProfile: ""  # AppArmor配置文件
  ExecIDs: 
    - "624f70aecb7b469b760271e3d78c4a55273d07e1b791f25d6e5aea0f2402bafa"  # 执行ID列表
  HostConfig:  # 主机配置
    Binds: null  # 绑定的卷
    ContainerIDFile: ""  # 容器ID文件
    LogConfig:  # 日志配置
      Type: "json-file"  # 日志类型
      Config: {}  # 日志配置
    NetworkMode: "host"  # 网络模式
    PortBindings:  # 端口映射
      "3306/tcp": 
        - HostIp: ""  # 主机IP
          HostPort: "3306"  # 主机端口
    RestartPolicy:  # 重启策略
      Name: "no"  # 策略名称
      MaximumRetryCount: 0  # 最大重试次数
    AutoRemove: false  # 是否自动删除
    VolumeDriver: ""  # 卷驱动
    VolumesFrom: null  # 卷来源
    ConsoleSize: [21, 87]  # 控制台大小
    CapAdd: null  # 添加的能力
    CapDrop: null  # 移除的能力
    CgroupnsMode: "host"  # Cgroupns模式
    Dns: []  # DNS服务器
    DnsOptions: []  # DNS选项
    DnsSearch: []  # DNS搜索域名
    ExtraHosts: null  # 额外主机名
    GroupAdd: null  # 添加到的组
    IpcMode: "private"  # IPC模式
    Cgroup: ""  # Cgroup配置
    OomScoreAdj: 0  # OOM分数调整
    PidMode: ""  # PID模式
    Privileged: false  # 是否具有特权
    PublishAllPorts: false  # 是否发布所有端口
    ReadonlyRootfs: false  # 是否只读根文件系统
    SecurityOpt: null  # 安全选项
   
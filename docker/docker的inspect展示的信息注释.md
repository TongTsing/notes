```json
[
    {
        "Id": "588b0932546f1eaff10f3537ce2b2b922606194fb948c738604da0b873ebd738",
        "Created": "2024-02-20T07:35:15.775670332Z", // 镜像创建时间
        "Path": "/bin/bash", // 容器运行的默认进程
        "Args": [], // 运行进程的参数
        "State": {
            // 容器的运行状态信息
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 70749, // 容器的进程 ID
            "ExitCode": 0, // 容器的退出码
            "Error": "",
            "StartedAt": "2024-02-20T07:35:16.397582709Z", // 容器启动时间
            "FinishedAt": "0001-01-01T00:00:00Z" // 容器停止时间
        },
        "Image": "sha256:6d12f4c1edae5897dd39a1be293a3d025b9d24fcc93490c3591a1e35cfbbbeca", // 使用的镜像的哈希值
        "ResolvConfPath": "/var/lib/docker/containers/588b0932546f1eaff10f3537ce2b2b922606194fb948c738604da0b873ebd738/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/588b0932546f1eaff10f3537ce2b2b922606194fb948c738604da0b873ebd738/hostname",
        "HostsPath": "/var/lib/docker/containers/588b0932546f1eaff10f3537ce2b2b922606194fb948c738604da0b873ebd738/hosts",
        "LogPath": "/var/lib/docker/containers/588b0932546f1eaff10f3537ce2b2b922606194fb948c738604da0b873ebd738/588b0932546f1eaff10f3537ce2b2b922606194fb948c738604da0b873ebd738-json.log",
        "Name": "/centos", // 容器的名称
        "RestartCount": 0, // 重启次数
        "Driver": "overlay2", // 存储驱动
        "Platform": "linux", // 平台信息
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": [
            "df4987b8226aa0db2b52db35703135765dd4df5aa298a8732bd4a81b0986c99c"
        ],
        "HostConfig": {
            // 容器的配置信息
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "bridge", // 容器使用的网络模式
            // 其他配置项...
        },
        "GraphDriver": {
            // 存储驱动的详细信息
            "Data": {
                // 存储层的路径信息
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            // 容器的配置信息
            "Hostname": "588b0932546f",
            "Domainname": "",
            "User": "",
            // 其他配置项...
        },
        "NetworkSettings": {
            // 容器的网络设置
            "Bridge": "",
            // 其他网络信息...
        }
    }
]

```


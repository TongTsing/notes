```bash
[base]
name=CentOS-$releasever - Base - mirrors.aliyun.com
#failovermethod=priority
    baseurl=https://mirrors.aliyun.com/centos-stream/$stream/BaseOS/$basearch/os/
            http://mirrors.aliyuncs.com/centos-stream/$stream/BaseOS/$basearch/os/
            http://mirrors.cloud.aliyuncs.com/centos-stream/$stream/BaseOS/$basearch/os/
    gpgcheck=1
    gpgkey=https://mirrors.aliyun.com/centos-stream/RPM-GPG-KEY-CentOS-Official
     
    #additional packages that may be useful
    #[extras]
    #name=CentOS-$releasever - Extras - mirrors.aliyun.com
    #failovermethod=priority
    #baseurl=https://mirrors.aliyun.com/centos-stream/$stream/extras/$basearch/os/
    #        http://mirrors.aliyuncs.com/centos-stream/$stream/extras/$basearch/os/
    #        http://mirrors.cloud.aliyuncs.com/centos-stream/$stream/extras/$basearch/os/
    #gpgcheck=1
    #gpgkey=https://mirrors.aliyun.com/centos-stream/RPM-GPG-KEY-CentOS-Official
     
    #additional packages that extend functionality of existing packages
    [centosplus]
    name=CentOS-$releasever - Plus - mirrors.aliyun.com
    #failovermethod=priority
    baseurl=https://mirrors.aliyun.com/centos-stream/$stream/centosplus/$basearch/os/
            http://mirrors.aliyuncs.com/centos-stream/$stream/centosplus/$basearch/os/
            http://mirrors.cloud.aliyuncs.com/centos-stream/$stream/centosplus/$basearch/os/
    gpgcheck=1
    enabled=0
    gpgkey=https://mirrors.aliyun.com/centos-stream/RPM-GPG-KEY-CentOS-Official
     
    [PowerTools]
    name=CentOS-$releasever - PowerTools - mirrors.aliyun.com
    #failovermethod=priority
    baseurl=https://mirrors.aliyun.com/centos-stream/$stream/PowerTools/$basearch/os/
            http://mirrors.aliyuncs.com/centos-stream/$stream/PowerTools/$basearch/os/
            http://mirrors.cloud.aliyuncs.com/centos-stream/$stream/PowerTools/$basearch/os/
    gpgcheck=1
    enabled=0
    gpgkey=https://mirrors.aliyun.com/centos-stream/RPM-GPG-KEY-CentOS-Official
     
     
    [AppStream]
    name=CentOS-$releasever - AppStream - mirrors.aliyun.com
    #failovermethod=priority
    baseurl=https://mirrors.aliyun.com/centos-stream/$stream/AppStream/$basearch/os/
            http://mirrors.aliyuncs.com/centos-stream/$stream/AppStream/$basearch/os/
            http://mirrors.cloud.aliyuncs.com/centos-stream/$stream/AppStream/$basearch/os/
    gpgcheck=1
    gpgkey=https://mirrors.aliyun.com/centos-stream/RPM-GPG-KEY-CentOS-Official

```


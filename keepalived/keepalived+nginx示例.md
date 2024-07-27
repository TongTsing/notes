

Web1:configure

~~~text
```plaintext
# 定义全局配置项
global_defs {
   # 邮件通知收件人列表
   notification_email {
     acassen@firewall.loc
     failover@firewall.loc
     sysadmin@firewall.loc
   }
   # 发件人邮件地址
   notification_email_from Alexandre.Cassen@firewall.loc
   # SMTP 服务器地址
   smtp_server 192.168.200.1
   # SMTP 连接超时时间
   smtp_connect_timeout 30
   # 路由器 ID
   router_id LVS_DEVEL
   # 是否跳过检查通告地址
   vrrp_skip_check_adv_addr
   # Gratuitous ARP 通告间隔时间
   vrrp_garp_interval 0
   vrrp_gna_interval 0
}

# 定义 VRRP 脚本
vrrp_script nginx_check {
  # 检查 Nginx 进程是否在运行的脚本路径
  script "/tools/nginx_check.sh"
  # 脚本执行的时间间隔
  interval 1
}

# 定义 VRRP 实例
vrrp_instance VI_1 {
  # 当前实例的状态为 MASTER
  state MASTER
  # 监视 VRRP 包的网络接口
  interface ens33
  # 虚拟路由器的 ID
  virtual_router_id 52
  # 实例的优先级
  priority 100
  # VRRP 通告的间隔时间
  advert_int 1
  # 认证类型和密码（用于keepalived集群的通信）
  authentication {
    auth_type PASS
    auth_pass test
  }
  # 虚拟 IP 地址(用于外网访问)
  virtual_ipaddress {
    172.16.1.10
  }
  # 跟踪的脚本，用于检测服务的可用性
  track_script {
    nginx_check
  }
  # 主服务器通知脚本
  notify_master /tools/master.sh
  # 备用服务器通知脚本
  notify_backup /tools/backup.sh
  # 故障通知脚本
  notify_fault /tools/fault.sh
  # 停止通知脚本
  notify_stop /tools/stop.sh
}
``` 

上述注释中，已经对每个配置项的作用进行了解释说明，并在注释中指出了应配置的具体值。您只需要根据实际情况填写相应的值即可。
~~~

Web2:configure

```
global_defs {
   notification_email {
     acassen@firewall.loc
     failover@firewall.loc
     sysadmin@firewall.loc
   }
   notification_email_from Alexandre.Cassen@firewall.loc
   smtp_server 192.168.200.1
   smtp_connect_timeout 30
   router_id LVS_DEVEL
   vrrp_skip_check_adv_addr
   vrrp_garp_interval 0
   vrrp_gna_interval 0
}
vrrp_script nginx_check {
  script "/tools/nginx_check.sh"
  interval 1
}
vrrp_instance VI_1 {
  state BACKUP
  interface ens33
  virtual_router_id 52
  priority 99
  advert_int 1
  authentication {
    auth_type PASS
    auth_pass test
  }
  virtual_ipaddress {
    172.16.1.12
  }
  track_script {
    nginx_check
  }
  notify_master /tools/master.sh
  notify_backup /tools/backup.sh
  notify_fault /tools/fault.sh
  notify_stop /tools/stop.sh
}
```


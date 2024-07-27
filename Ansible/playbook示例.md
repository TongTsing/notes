```yaml
---
# 这是一个简单的Ansible Playbook示例

# 定义Playbook的名称
- name: My First Playbook

  # 指定目标主机，替换 your_target_hosts 为你的目标主机组
  hosts: your_target_hosts

  # 如果需要在远程主机上以超级用户权限执行任务，添加此行
  become: yes  

  # 定义任务列表
  tasks:
    # 任务1：确保Nginx已安装
    - name: Ensure Nginx is installed
      yum:
        name: nginx
        state: present

    # 任务2：确保Nginx服务正在运行
    - name: Ensure Nginx service is running
      service:
        name: nginx
        state: started

    # 任务3：复制本地nginx.conf到远程主机，并在需要时重新启动Nginx
    - name: Copy custom Nginx configuration
      copy:
        src: /path/to/local/nginx.conf
        dest: /etc/nginx/nginx.conf
      notify:
        - Restart Nginx

  # 定义处理程序列表
  handlers:
    # 处理程序1：重新启动Nginx
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted

```


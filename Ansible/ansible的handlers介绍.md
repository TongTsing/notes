在 Ansible Playbook 中，`handlers` 是一种特殊的任务，用于在其他任务中通知它们运行。通常，`handlers` 用于在配置更改后重新启动服务、重新加载配置等操作。以下是一个简单的 Ansible Playbook 示例，演示了如何使用 `handlers`：

```yaml
---
- name: Configure and Restart Web Server
  hosts: web_servers
  become: true

  tasks:
    - name: Copy Nginx Configuration File
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: Restart Nginx

    - name: Ensure Nginx Service is Started
      service:
        name: nginx
        state: started
        enabled: yes

  handlers:
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted
```

在这个示例中：

1. `template` 模块用于将本地的 `nginx.conf.j2` 模板文件复制到远程主机的 `/etc/nginx/nginx.conf`。
2. 如果 `template` 任务执行了变更（即配置文件被更新），则会触发 `handlers` 中的 `Restart Nginx` 处理程序。
3. `service` 模块在主任务中确保 Nginx 服务已启动，而 `handlers` 中的 `Restart Nginx` 处理程序会在需要时重新启动 Nginx。

这是一个简单的例子，说明了如何使用 `handlers` 来处理服务的重启。实际上，`handlers` 可以执行各种操作，具体取决于你的需求。
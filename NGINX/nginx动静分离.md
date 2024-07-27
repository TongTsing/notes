Nginx 的动静分离配置是一种常见的服务器配置模式，它将动态内容（如 PHP 脚本、Java Servlet 或 Ruby on Rails 应用程序）与静态内容（如 HTML 文件、CSS、JavaScript、图像文件等）分开处理，以提高网站的性能和可伸缩性。以下是一个简单的动静分离配置示例：

假设您的网站根目录下有一个名为 `public` 的目录，其中包含了静态文件，而动态请求将被转发到后端服务器，比如 Node.js、Django 或者其他应用程序服务器。

```nginx
server {
    listen 80;
    server_name your_domain.com;

    root /var/www/your_domain.com/public;
    index index.html;

    # 处理静态文件请求
    location / {
        try_files $uri $uri/ =404;
    }

    # 处理动态请求转发
    location /api {
        proxy_pass http://backend_server;  # 后端服务器地址
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    # 可选：处理其他动态请求转发
    # location /another_api {
    #     proxy_pass http://another_backend_server;
    #     proxy_set_header Host $host;
    #     proxy_set_header X-Real-IP $remote_addr;
    #     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    # }

    # 可选：处理其他动态请求，比如 FastCGI
    # location ~ \.php$ {
    #     fastcgi_pass unix:/run/php/php7.4-fpm.sock;
    #     fastcgi_index index.php;
    #     fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    #     include fastcgi_params;
    # }

    # 可选：处理其他动态请求，比如 uWSGI
    # location / {
    #     include uwsgi_params;
    #     uwsgi_pass unix:/run/uwsgi.sock;
    # }
}
```

在上述配置中：

- `listen 80;` 表示监听80端口的 HTTP 请求。
- `server_name your_domain.com;` 将请求与此服务器块关联，替换为您的域名。
- `root /var/www/your_domain.com/public;` 设置静态文件的根目录。
- `location /` 匹配根路径下的请求，并尝试在 `public` 目录中查找对应的文件。
- `location /api` 匹配以 `/api` 开头的请求，并将这些请求转发到后端服务器，可以根据实际需求配置多个类似的 `location` 块。
- 可选地，您可以添加其他动态请求的处理方式，比如处理 PHP、uWSGI 等。

请根据您的实际需求和后端服务器配置来修改和扩展此示例。
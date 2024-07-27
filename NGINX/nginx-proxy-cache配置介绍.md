以下是一个简单的 Nginx 缓存配置示例，其中使用了 `proxy_cache_path` 和 `proxy_cache` 指令：

```nginx
http {
    # 定义共享内存区域用于缓存
    proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m max_size=1g inactive=60m;

    server {
        listen 80;
        server_name example.com;

        location / {
            # 启用缓存
            proxy_cache my_cache;

            # 设置缓存键
            proxy_cache_key "$scheme$request_method$host$request_uri";

            # 指定缓存有效期
            proxy_cache_valid 200 302 10m;
            proxy_cache_valid 404 1m;

            # 设置缓存使用的共享内存区域
            proxy_cache_lock on;
            proxy_cache_lock_timeout 5s;

            # 设置缓存文件的路径
            proxy_cache_use_stale error timeout updating http_500 http_502 http_503 http_504;

            # 设置缓存存储位置
            proxy_cache_bypass $http_cache_control;
            add_header X-Cached $upstream_cache_status;

            # 代理到后端服务器
            proxy_pass http://backend_server;
        }
    }
}
```

在这个示例中：

- 使用 `proxy_cache_path` 指令定义了一个名为 `my_cache` 的共享内存区域，用于缓存键和相关元数据，并设置了缓存的路径、深度、大小和其他参数。
- 在 `location /` 块中，通过 `proxy_cache` 开启了缓存，并使用了 `proxy_cache_key` 指定了缓存键的生成规则。
- 使用 `proxy_cache_valid` 指令设置了不同响应状态码的缓存有效期。
- 使用了 `proxy_cache_lock` 和 `proxy_cache_lock_timeout` 指令来处理并发请求中的缓存锁定。
- 使用了 `proxy_cache_use_stale` 指令来设置在后端服务器不可用时使用过期缓存的策略。
- 使用了 `proxy_cache_bypass` 和 `add_header` 指令来设置缓存绕过规则和添加缓存状态头。

这个配置示例提供了一个基本的 Nginx 缓存配置框架，可以根据实际需求进行调整和扩展。
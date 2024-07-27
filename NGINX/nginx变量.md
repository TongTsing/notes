正向代理的变量：

1. **$proxy_protocol**: 代理协议（http、https等）。
2. **$proxy_host**: 代理服务器的地址。
3. **$proxy_port**: 代理服务器的端口。
4. **$proxy_add_x_forwarded_for**: 在转发请求时，将客户端的IP地址添加到X-Forwarded-For头部中。
5. **$proxy_internal_body_length**: 代理传输的请求体的长度。
6. **$proxy_internal_body_bytes_sent**: 代理传输的请求体的字节数。
7. **$proxy_upstream_name**: 当前使用的上游服务器名称。

反向代理变量

1. **$proxy_protocol**: 代理协议（http、https等）。
2. **$proxy_host**: 反向代理服务器的地址。
3. **$proxy_port**: 反向代理服务器的端口。
4. **$proxy_add_x_forwarded_for**: 在转发请求时，将客户端的IP地址添加到X-Forwarded-For头部中。
5. **$proxy_internal_body_length**: 反向代理传输的请求体的长度。
6. **$proxy_internal_body_bytes_sent**: 反向代理传输的请求体的字节数。
7. **$proxy_upstream_name**: 当前使用的上游服务器名称。

所有变量：

以下是 Nginx 1.18.0 版本的内置变量列表：

1. `$arg_PARAMETER`: 请求中的参数值。
2. `$args`: 请求中的参数。
3. `$binary_remote_addr`: 客户端的 IP 地址（二进制形式）。
4. `$bytes_sent`: 发送给客户端的字节数。
5. `$connection`: 保持连接状态的连接数。
6. `$connection_requests`: 当前连接上的请求数。
7. `$content_length`: 请求头中的 Content-Length 字段。
8. `$cookie_COOKIE`: 请求中的 cookie 值。
9. `$document_root`: 当前请求的文档根目录。
10. `$document_uri`: 不带查询参数的当前请求 URI。
11. `$host`: 请求中的主机头字段。
12. `$hostname`: 主机名。
13. `$http_HEADER`: 请求头中的指定头部字段。
14. `$is_args`: 查询参数的存在标志。
15. `$limit_rate`: 连接限速。
16. `$msec`: 请求的时间戳，精确到毫秒。
17. `$nginx_version`: Nginx 版本号。
18. `$pid`: 当前 Nginx 进程的 PID。
19. `$query_string`: 查询字符串。
20. `$realpath_root`: 当前请求的文档根目录的实际路径。
21. `$remote_addr`: 客户端的 IP 地址。
22. `$remote_port`: 客户端的端口号。
23. `$remote_user`: 认证用户名。
24. `$request`: 完整的 HTTP 请求行。
25. `$request_body`: 请求体的内容。
26. `$request_completion`: 请求处理状态。
27. `$request_filename`: 请求文件的路径。
28. `$request_id`: 请求的唯一标识符。
29. `$request_length`: 请求的字节数。
30. `$request_method`: 请求方法（GET、POST 等）。
31. `$request_time`: 请求处理时间，单位为秒，精确到毫秒。
32. `$request_uri`: 完整的请求 URI，包括查询参数。
33. `$scheme`: 请求使用的协议（http 或 https）。
34. `$secure_link`: 安全链接状态。
35. `$sent_http_HEADER`: 发送给客户端的响应头部字段。
36. `$server_addr`: Nginx 服务器的 IP 地址。
37. `$server_name`: 当前服务器的名称。
38. `$server_port`: 服务器监听的端口号。
39. `$server_protocol`: 使用的协议版本。
40. `$ssl_cipher`: SSL/TLS 连接使用的加密算法。
41. `$ssl_client_cert`: 客户端 SSL 证书。
42. `$ssl_client_serial`: 客户端 SSL 证书序列号。
43. `$ssl_client_s_dn`: 客户端 SSL 证书主题字段。
44. `$ssl_client_i_dn`: 客户端 SSL 证书颁发者字段。
45. `$ssl_protocol`: 使用的 SSL/TLS 协议版本。
46. `$status`: 响应的 HTTP 状态码。
47. `$time_iso8601`: 以 ISO 8601 格式表示的当前时间。
48. `$time_local`: 以 Common Log Format 格式表示的当前时间。

这些是 Nginx 1.18.0 版本中的内置变量，可用于配置文件中，以实现各种灵活的功能需求。
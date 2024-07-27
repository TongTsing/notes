### 一、**http_core_module**: 这是 Nginx 的核心模块，提供了 HTTP 服务器的基本功能，包括配置全局的 HTTP 设置、定义 HTTP 服务器块等。

- 1. `http_core_module` 是 Nginx 的核心模块之一，提供了 HTTP 服务器的基本功能和配置选项。以下是该模块的一些常用指令及其功能：

     1. `listen`: 定义服务器监听的端口和地址。
        - 示例：`listen 80;` 或 `listen 443 ssl;`
  
     2. `server_name`: 定义服务器的域名或 IP 地址。
        - 示例：`server_name example.com;`
  
     3. `root`: 指定网站根目录。
        - 示例：`root /var/www/html;`
  
     4. `index`: 定义默认首页文件。
        - 示例：`index index.html index.htm;`
  
     5. `location`: 定义请求的匹配位置，并配置相关的处理逻辑。
        - 示例：`location / { ... }`
  
     6. `try_files`: 定义尝试查找文件的顺序。
        - 示例：`try_files $uri $uri/ /index.php?$query_string;`
  
     7. `rewrite`: 定义 URL 重写规则。
        - 示例：`rewrite ^/old-url$ /new-url permanent;`
  
     8. `error_page`: 配置错误页面的处理方式。
        - 示例：`error_page 404 /404.html;`
  
     9. `access_log`: 配置访问日志文件路径和格式。
        - 示例：`access_log /var/log/nginx/access.log main;`
  
     10. `error_log`: 配置错误日志文件路径和级别。
         - 示例：`error_log /var/log/nginx/error.log error;`
  
     11. `client_max_body_size`: 定义客户端请求体的最大尺寸。
         - 示例：`client_max_body_size 10m;`
  
     12. `keepalive_timeout`: 定义客户端与服务器之间的持久连接超时时间。
         - 示例：`keepalive_timeout 65;`
  
     13. `gzip`: 启用或配置 Gzip 压缩。
         - 示例：`gzip on;` 或 `gzip_types text/plain text/css application/json;`
  
     14. `include`: 导入其他配置文件。
         - 示例：`include /etc/nginx/conf.d/*.conf;`
  
     这些指令是配置 Nginx HTTP 服务器的基础，可以根据需要进行组合和调整，以实现不同的网站和应用的需求。

### 二、**http_ssl_module**: 提供了 HTTPS 支持，允许在 Nginx 中启用 SSL/TLS 加密功能。

- `http_ssl_module` 是 Nginx 的一个模块，用于提供 HTTPS 支持。它允许 Nginx 作为 HTTPS 服务器来处理加密的 HTTP 请求。以下是该模块常用的指令及其功能：

  1. `ssl_certificate`: 指定服务器证书文件的路径。
     - 示例：`ssl_certificate /etc/nginx/ssl/server.crt;`
  
  2. `ssl_certificate_key`: 指定服务器证书的私钥文件路径。
     - 示例：`ssl_certificate_key /etc/nginx/ssl/server.key;`
  
  3. `ssl_protocols`: 指定支持的 SSL/TLS 协议版本。
     - 示例：`ssl_protocols TLSv1.2 TLSv1.3;`
  
  4. `ssl_prefer_server_ciphers`: 指定是否优先使用服务器端的加密算法。
     - 示例：`ssl_prefer_server_ciphers on;`
  
  5. `ssl_ciphers`: 指定 SSL 加密算法。
     - 示例：`ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";`
  
  6. `ssl_session_cache`: 配置 SSL 会话缓存。
     - 示例：`ssl_session_cache shared:SSL:10m;`
  
  7. `ssl_session_timeout`: 指定 SSL 会话超时时间。
     - 示例：`ssl_session_timeout 10m;`
  
  8. `ssl_stapling`: 启用 OCSP Stapling 功能。
     - 示例：`ssl_stapling on;`
  
  9. `ssl_stapling_verify`: 配置 OCSP Stapling 证书验证方式。
     - 示例：`ssl_stapling_verify on;`
  
  这些指令用于配置 Nginx 作为 HTTPS 服务器时的 SSL/TLS 参数，确保通信的安全性和可靠性。可以根据实际需求进行配置和调整。

### 三、**http_proxy_module**: 提供了代理服务器的功能，允许 Nginx 作为反向代理服务器或正向代理服务器。

- 指令示例：

  以下是 Nginx 1.18.0 版本中 `http_proxy_module` 模块的指令介绍以及它们的指令格式：

  1. `proxy_bind`: 指定用于发出连接的本地地址。
     - 格式：`proxy_bind address [transparent] | off;`
  2. `proxy_buffering`: 设置是否启用代理缓冲。
     - 格式：`proxy_buffering on | off;`
  3. `proxy_buffer_size`: 设置代理缓冲区大小。
     - 格式：`proxy_buffer_size size;`
  4. `proxy_buffers`: 设置代理缓冲区数量和大小。
     - 格式：`proxy_buffers number size;`
  5. `proxy_busy_buffers_size`: 设置用于忙碌代理缓冲区的大小。
     - 格式：`proxy_busy_buffers_size size;`
  6. `proxy_cache`: 设置是否启用代理缓存以及相关参数。
     - 格式：`proxy_cache zone | off;`
  7. `proxy_cache_bypass`: 设置跳过代理缓存的条件。
     - 格式：`proxy_cache_bypass string ...;`
  8. `proxy_cache_key`: 设置代理缓存的键。
     - 格式：`proxy_cache_key string;`
  9. `proxy_cache_valid`: 设置代理缓存的有效期。
     - 格式：`proxy_cache_valid time;`
  10. `proxy_connect_timeout`: 设置代理连接的超时时间。
      - 格式：`proxy_connect_timeout time;`
  11. `proxy_headers_hash_bucket_size`: 设置用于存储代理头部的散列表桶的大小。
      - 格式：`proxy_headers_hash_bucket_size size;`
  12. `proxy_hide_header`: 设置隐藏响应头部。
      - 格式：`proxy_hide_header field;`
  13. `proxy_http_version`: 设置与后端服务器通信时使用的 HTTP 版本。
      - 格式：`proxy_http_version 1.0 | 1.1;`
  14. `proxy_ignore_headers`: 设置忽略的响应头部。
      - 格式：`proxy_ignore_headers field ...;`
  15. `proxy_intercept_errors`: 设置是否拦截错误响应。
      - 格式：`proxy_intercept_errors on | off;`
  16. `proxy_max_temp_file_size`: 设置临时文件的最大大小。
      - 格式：`proxy_max_temp_file_size size;`
  17. `proxy_method`: 设置与后端服务器通信时使用的 HTTP 方法。
      - 格式：`proxy_method method;`
  18. `proxy_next_upstream`: 设置在与上游服务器通信时的故障情况下，Nginx 会尝试的下一个上游服务器。
      - 格式：`proxy_next_upstream error | timeout | invalid_header | http_500 | http_502 | http_503 | http_504 | http_403 | http_404 | http_429 | non_idempotent | off ...;`
  19. `proxy_pass`: 设置要代理到的后端服务器的地址。
      - 格式：`proxy_pass URL;`
  20. `proxy_read_timeout`: 设置从后端服务器读取响应的超时时间。
      - 格式：`proxy_read_timeout time;`
  21. `proxy_redirect`: 设置代理重定向规则。
      - 格式：`proxy_redirect default | off | redirect replacement;`
  22. `proxy_request_buffering`: 设置是否启用请求缓冲。
      - 格式：`proxy_request_buffering on | off;`
  23. `proxy_send_lowat`: 设置低水位标记，以确保传输更少的数据。
      - 格式：`proxy_send_lowat size;`
  24. `proxy_send_timeout`: 设置向后端服务器发送请求的超时时间。
      - 格式：`proxy_send_timeout time;`
  25. `proxy_set_body`: 设置请求体。
      - 格式：`proxy_set_body string;`
  26. `proxy_set_header`: 设置请求头部。
      - 格式：`proxy_set_header field value;`
  27. `proxy_ssl_certificate`: 设置用于与后端服务器进行 SSL 握手的证书。
      - 格式：`proxy_ssl_certificate file;`
  28. `proxy_ssl_certificate_key`: 设置用于与后端服务器进行 SSL 握手的证书的密钥。
      - 格式：`proxy_ssl_certificate_key file;`
  29. `proxy_ssl_session_reuse`: 设置是否重用 SSL 会话。
      - 格式：`proxy_ssl_session_reuse on | off;`
  30. `proxy_ssl_trusted_certificate`: 设置用于验证后端服务器证书的信任链

### 四、**http_rewrite_module**: 提供了 URL 重写功能，允许对客户端请求的 URL 进行重写和修改。

- `http_rewrite_module` 模块提供了在 Nginx 中进行 URL 重写的功能，允许用户根据特定的规则修改请求的 URI。以下是该模块常用指令的介绍以及它们的指令格式：
  1. `rewrite`: 定义重写规则。
     - 格式：`rewrite regex replacement [flag];`
     - 示例：`rewrite ^/old-url$ /new-url last;`

  2. `return`: 执行重定向操作。
     - 格式：`return code [text | uri];`
     - 示例：`return 301 /new-location;`

  3. `if`: 在重写规则中添加条件判断。
     - 格式：`if (condition) { ... }`
     - 示例：`if ($request_uri ~* ^/old-url$) { return 301 /new-url; }`

  4. `set`: 设置变量值。
     - 格式：`set $variable value;`
     - 示例：`set $new_uri /new-location;`

  5. `break`: 中断处理当前请求，并继续执行后续的 Nginx 指令。
     - 格式：`break;`

  6. `last`: 中断处理当前请求，并将控制权交给下一个配置阶段。
     - 格式：`last;`

  7. `permanent`: 执行永久性重定向。
     - 格式：`permanent;`

  8. `redirect`: 执行临时性重定向。
     - 格式：`redirect code location;`

  9. `if_modified_since`: 根据请求头中的 If-Modified-Since 字段执行条件重定向。
     - 格式：`if_modified_since before;`

  10. `proxy_redirect`: 在反向代理中修改 Location 头字段的值。
      - 格式：`proxy_redirect default | off | redirect replacement;`

  11. `proxy_intercept_errors`: 设置是否拦截代理请求的错误响应。
      - 格式：`proxy_intercept_errors on | off;`

  12. `proxy_cache_bypass`: 设置跳过代理缓存的条件。
      - 格式：`proxy_cache_bypass string ...;`

  13. `proxy_hide_header`: 设置隐藏响应头字段。
      - 格式：`proxy_hide_header field;`

  14. `proxy_ignore_headers`: 设置忽略的响应头字段。
      - 格式：`proxy_ignore_headers field ...;`

  15. `proxy_redirect`: 设置代理重定向规则。
      - 格式：`proxy_redirect default | off | redirect replacement;`

  16. `proxy_set_body`: 设置请求体。
      - 格式：`proxy_set_body string;`

  17. `proxy_set_header`: 设置请求头字段。
      - 格式：`proxy_set_header field value;`

  18. `proxy_ssl_certificate`: 设置与后端服务器进行 SSL 握手所需的证书。
      - 格式：`proxy_ssl_certificate file;`

  19. `proxy_ssl_certificate_key`: 设置与后端服务器进行 SSL 握手所需的证书密钥。
      - 格式：`proxy_ssl_certificate_key file;`

  20. `proxy_ssl_session_reuse`: 设置是否重用 SSL 会话。
      - 格式：`proxy_ssl_session_reuse on | off;`

### 五、**http_access_module**: 提供了访问控制功能，允许对客户端请求进行访问控制和权限管理。

- `http_access_module` 模块提供了控制客户端访问权限的功能，允许管理员根据 IP 地址、请求头等条件来限制或允许客户端的访问。以下是该模块常用指令的介绍：
  1. `allow`: 允许特定 IP 地址或 IP 地址范围的访问。
     - 格式：`allow address | CIDR | all;`
     - 示例：`allow 192.168.1.0/24;`

  2. `deny`: 拒绝特定 IP 地址或 IP 地址范围的访问。
     - 格式：`deny address | CIDR | all;`
     - 示例：`deny 10.0.0.1;`

  3. `satisfy`: 指定满足 allow 和 deny 条件的方式。
     - 格式：`satisfy any | all;`
     - 示例：`satisfy any;`

  4. `auth_basic`: 启用 HTTP 基本认证。
     - 格式：`auth_basic "realm";`
     - 示例：`auth_basic "Restricted Area";`

  5. `auth_basic_user_file`: 指定存储用户名和密码的文件。
     - 格式：`auth_basic_user_file file;`
     - 示例：`auth_basic_user_file /etc/nginx/.htpasswd;`

  6. `limit_req`: 限制请求速率。
     - 格式：`limit_req zone=name [burst=number] [nodelay];`
     - 示例：`limit_req zone=one;`

  7. `limit_req_zone`: 定义请求速率限制的内存区域。
     - 格式：`limit_req_zone key zone=name:size rate=rate [sync];`
     - 示例：`limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;`

  8. `limit_conn`: 限制并发连接数。
     - 格式：`limit_conn zone=name [number];`
     - 示例：`limit_conn zone=addr;`

  9. `limit_conn_zone`: 定义并发连接数限制的内存区域。
     - 格式：`limit_conn_zone key zone=name:size;`
     - 示例：`limit_conn_zone $binary_remote_addr zone=addr:10m;`

  10. `proxy_pass`: 配置反向代理。
      - 格式：`proxy_pass URL;`
      - 示例：`proxy_pass http://backend.example.com;`

  11. `proxy_set_header`: 设置传递给后端服务器的请求头。
      - 格式：`proxy_set_header field value;`
      - 示例：`proxy_set_header X-Real-IP $remote_addr;`

  12. `proxy_hide_header`: 隐藏从后端服务器收到的响应头字段。
      - 格式：`proxy_hide_header field;`
      - 示例：`proxy_hide_header Server;`

  13. `proxy_ignore_headers`: 设置不处理的响应头字段。
      - 格式：`proxy_ignore_headers field ...;`
      - 示例：`proxy_ignore_headers X-Accel-Expires Expires Cache-Control;`

  14. `proxy_intercept_errors`: 设置是否拦截代理请求的错误响应。
      - 格式：`proxy_intercept_errors on | off;`
      - 示例：`proxy_intercept_errors on;`

  15. `proxy_cache_bypass`: 设置跳过代理缓存的条件。
      - 格式：`proxy_cache_bypass string ...;`
      - 示例：`proxy_cache_bypass $cookie_nocache $arg_nocache;`

  16. `proxy_cache`: 启用反向代理缓存。
      - 格式：`proxy_cache zone-name;`
      - 示例：`proxy_cache cache_zone;`

  17. `proxy_cache_valid`: 设置缓存的有效时间。
      - 格式：`proxy_cache_valid time;`
      - 示例：`proxy_cache_valid 200 302 10m;`

  18. `proxy_ssl_verify`: 设置是否验证后端服务器的 SSL 证书。
      - 格式：`proxy_ssl_verify on | off;`
      - 示例：`proxy_ssl_verify off;`

  19. `proxy_ssl_verify_depth`: 设置 SSL 证书验证深度。
      - 格式：`proxy_ssl_verify_depth number;`
      - 示例：`proxy_ssl_verify_depth 2;`

  20. `proxy_ssl_certificate`: 设置与后端服务器进行 SSL 握手所需的证书。
      - 格式：`proxy_ssl_certificate file;`
      - 示例：`proxy_ssl_certificate /etc/nginx/cert.pem;`

### 六、**http_log_module**: 提供了日志记录功能，允许记录客户端请求和服务器响应的日志信息。

- `http_log_module` 模块用于记录 HTTP 请求和响应的日志。以下是该模块常用指令的介绍：

  1. `access_log`: 指定访问日志文件的路径和格式。
     - 格式：`access_log path [format [buffer=size]];`
     - 示例：`access_log /var/log/nginx/access.log main;`

  2. `error_log`: 指定错误日志文件的路径和日志级别。
     - 格式：`error_log file [level];`
     - 示例：`error_log /var/log/nginx/error.log error;`

  3. `log_format`: 定义自定义的日志格式。
     - 格式：`log_format name [escape=default|json] string;`
     - 示例：`log_format main '$remote_addr - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent"';`

  4. `open_log_file_cache`: 开启日志文件缓存。
     - 格式：`open_log_file_cache max=N [inactive=time] [min_uses=N] [valid=time];`
     - 示例：`open_log_file_cache max=1000 inactive=20s;`

  5. `open_log_buffer`: 设置写入日志文件的缓冲区大小。
     - 格式：`open_log_buffer size;`
     - 示例：`open_log_buffer 64k;`

  6. `log_not_found`: 控制是否记录 404 错误。
     - 格式：`log_not_found on | off;`
     - 示例：`log_not_found off;`

  7. `log_subrequest`: 控制是否记录子请求。
     - 格式：`log_subrequest on | off;`
     - 示例：`log_subrequest on;`

  8. `types`: 定义 MIME 类型和文件扩展名之间的映射。
     - 格式：`types { ... }`
     - 示例：`types { text/plain txt; application/json json; }`

  9. `types_hash_max_size`: 设置 MIME 类型和文件扩展名之间的散列表大小。
     - 格式：`types_hash_max_size size;`
     - 示例：`types_hash_max_size 1024;`

  10. `types_hash_bucket_size`: 设置 MIME 类型和文件扩展名之间的散列表桶大小。
      - 格式：`types_hash_bucket_size size;`
      - 示例：`types_hash_bucket_size 64;`

  这些指令用于配置 Nginx 记录 HTTP 请求和响应的日志，可以根据实际需求来灵活配置。

### 七、**http_gzip_module**: 提供了 HTTP 响应内容的压缩功能，可以减小传输数据量，提高网站性能。

- `http_gzip_module` 模块用于在 Nginx 中启用 Gzip 压缩功能，以减少传输内容的大小，从而提高网站性能。以下是该模块常用指令的介绍：

  1. `gzip`: 启用或禁用 Gzip 压缩。
     - 格式：`gzip on | off;`
     - 示例：`gzip on;`

  2. `gzip_comp_level`: 设置 Gzip 压缩级别。
     - 格式：`gzip_comp_level level;`
     - 示例：`gzip_comp_level 6;`

  3. `gzip_types`: 设置要压缩的 MIME 类型。
     - 格式：`gzip_types mime-type ...;`
     - 示例：`gzip_types text/plain text/css application/json;`

  4. `gzip_min_length`: 设置触发 Gzip 压缩的最小文件大小。
     - 格式：`gzip_min_length length;`
     - 示例：`gzip_min_length 1000;`

  5. `gzip_buffers`: 设置用于压缩数据的内存缓冲区数量和大小。
     - 格式：`gzip_buffers number size;`
     - 示例：`gzip_buffers 4 32k;`

  6. `gzip_http_version`: 设置启用 Gzip 压缩的 HTTP 协议版本。
     - 格式：`gzip_http_version 1.0 | 1.1;`
     - 示例：`gzip_http_version 1.1;`

  7. `gzip_proxied`: 设置哪些类型的连接可以触发 Gzip 压缩。
     - 格式：`gzip_proxied off | expired | no-cache | no-store | private | no_last_modified | no_etag | auth | any;`
     - 示例：`gzip_proxied any;`

  8. `gzip_disable`: 设置禁用 Gzip 压缩的 User-Agent 字符串。
     - 格式：`gzip_disable "string";`
     - 示例：`gzip_disable "MSIE [1-6]\.";`

  这些指令允许您灵活地配置 Nginx 的 Gzip 压缩功能，以优化网站的性能并减少传输内容的大小。

### 八、**http_headers_module**: 提供了 HTTP 头部操作功能，可以添加、修改、删除 HTTP 头部。

- `http_headers_module` 模块允许在 Nginx 配置中设置和修改 HTTP 头部。以下是该模块常用指令的介绍：

  1. `add_header`: 添加一个新的 HTTP 头部或修改现有的 HTTP 头部。
     - 格式：`add_header name value [always];`
     - 示例：`add_header X-Frame-Options "SAMEORIGIN";`

  2. `expires`: 设置缓存过期时间，用于指示客户端在何时可以重新获取资源。
     - 格式：`expires [modified] time;`
     - 示例：`expires 1d;`

  3. `expires_max`: 设置缓存的最大过期时间，用于指示客户端在何时必须重新获取资源。
     - 格式：`expires_max time;`
     - 示例：`expires_max 365d;`

  4. `expires_modified`: 根据资源的最后修改时间来设置缓存过期时间。
     - 格式：`expires_modified time;`
     - 示例：`expires_modified 1y;`

  5. `add_trailer`: 添加一个新的 HTTP trailer 头部。
     - 格式：`add_trailer name value;`
     - 示例：`add_trailer X-Foo "bar";`

  6. `add_trailer_once`: 在响应头部中添加指定的 HTTP trailer 头部，但是只在第一次发送响应时添加。
     - 格式：`add_trailer_once name value;`
     - 示例：`add_trailer_once X-Foo "bar";`

  这些指令可以帮助您在 Nginx 配置中设置和修改 HTTP 头部，从而控制缓存、安全策略等方面的行为，并定制化您的 Web 服务器行为。

### 九、**http_fastcgi_module**: 提供了 FastCGI 支持，允许将客户端请求转发给 FastCGI 进程处理。

- `http_fastcgi_module` 模块用于配置 Nginx 与 FastCGI 后端之间的通信。它提供了一系列指令，用于配置 FastCGI 参数、连接池管理、超时设置等。以下是该模块常用指令的介绍：

  1. `fastcgi_pass`: 指定 FastCGI 后端服务器的地址和端口。
     - 格式：`fastcgi_pass address;`
     - 示例：`fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;`

  2. `fastcgi_param`: 设置发送到 FastCGI 后端的参数。
     - 格式：`fastcgi_param parameter value;`
     - 示例：`fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;`

  3. `fastcgi_index`: 设置索引文件。
     - 格式：`fastcgi_index index;`
     - 示例：`fastcgi_index index.php;`

  4. `fastcgi_buffers`: 设置用于保存 FastCGI 响应的内存缓冲区数量和大小。
     - 格式：`fastcgi_buffers number size;`
     - 示例：`fastcgi_buffers 8 4k;`

  5. `fastcgi_buffer_size`: 设置每个 FastCGI 缓冲区的大小。
     - 格式：`fastcgi_buffer_size size;`
     - 示例：`fastcgi_buffer_size 4k;`

  6. `fastcgi_read_timeout`: 设置从 FastCGI 后端读取响应的超时时间。
     - 格式：`fastcgi_read_timeout time;`
     - 示例：`fastcgi_read_timeout 30s;`

  7. `fastcgi_send_timeout`: 设置发送请求到 FastCGI 后端的超时时间。
     - 格式：`fastcgi_send_timeout time;`
     - 示例：`fastcgi_send_timeout 30s;`

  8. `fastcgi_connect_timeout`: 设置连接到 FastCGI 后端的超时时间。
     - 格式：`fastcgi_connect_timeout time;`
     - 示例：`fastcgi_connect_timeout 5s;`

  这些指令可以帮助您配置 Nginx 与 FastCGI 后端之间的通信，以便有效地处理动态内容，如 PHP、Python 等。

### 十、**stream_core_module**: 提供了 TCP 和 UDP 代理功能，允许 Nginx 作为 TCP/UDP 代理服务器。

- `stream_core_module` 模块提供了 TCP 和 UDP 代理功能，允许 Nginx 作为 TCP/UDP 代理服务器。以下是该模块的一些常用指令及其功能：

  1. `listen`: 指定监听的端口和地址。
     - 示例：`listen 8080;`

  2. `proxy_pass`: 指定代理服务器的地址。
     - 示例：`proxy_pass 127.0.0.1:8000;`

  3. `proxy_protocol`: 启用或禁用代理协议。
     - 示例：`proxy_protocol on;`

  4. `ssl_preread`: 启用 SSL 预读功能，用于在 SSL/TLS 握手之前根据客户端的请求内容进行路由。
     - 示例：`ssl_preread on;`

  5. `proxy_timeout`: 设置与后端服务器建立连接的超时时间。
     - 示例：`proxy_timeout 30s;`

  6. `proxy_connect_timeout`: 设置与后端服务器建立连接的超时时间。
     - 示例：`proxy_connect_timeout 5s;`

  7. `proxy_buffer_size`: 设置缓冲区的大小。
     - 示例：`proxy_buffer_size 4k;`

  8. `proxy_buffers`: 设置缓冲区的数量和大小。
     - 示例：`proxy_buffers 8 4k;`

  通过这些指令，您可以配置 Nginx 作为 TCP 或 UDP 代理服务器，用于处理传入的 TCP 或 UDP 数据流量，并将其转发到相应的后端服务器。

### 十一、**stream_ssl_module**: 提供了 TCP 和 UDP 的 SSL/TLS 支持，允许在 TCP/UDP 代理中启用 SSL/TLS 加密功能。

- `stream_ssl_module` 模块用于配置 Nginx 作为 TCP 和 UDP 代理服务器时的 SSL/TLS 功能。它允许您在代理 TCP 或 UDP 流量时对 SSL/TLS 连接进行加密和解密。以下是该模块的一些常用指令及其功能：

  1. `ssl_certificate`: 指定 SSL 证书文件的路径。
     - 示例：`ssl_certificate /path/to/certificate.crt;`

  2. `ssl_certificate_key`: 指定 SSL 证书密钥文件的路径。
     - 示例：`ssl_certificate_key /path/to/privatekey.key;`

  3. `ssl_protocols`: 指定 SSL/TLS 协议版本。
     - 示例：`ssl_protocols TLSv1.2 TLSv1.3;`

  4. `ssl_ciphers`: 指定 SSL/TLS 使用的加密算法。
     - 示例：`ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";`

  5. `ssl_prefer_server_ciphers`: 启用服务器端优先的 SSL 加密算法。
     - 示例：`ssl_prefer_server_ciphers on;`

  6. `ssl_session_cache`: 设置 SSL 会话缓存的类型和大小。
     - 示例：`ssl_session_cache shared:SSL:10m;`

  7. `ssl_session_timeout`: 设置 SSL 会话的超时时间。
     - 示例：`ssl_session_timeout 10m;`

  8. `ssl_stapling`: 启用 OCSP Stapling 功能。
     - 示例：`ssl_stapling on;`

  9. `ssl_stapling_verify`: 启用 OCSP Stapling 验证功能。
     - 示例：`ssl_stapling_verify on;`

  通过这些指令，您可以配置 Nginx 作为 TCP 或 UDP 代理服务器时的 SSL/TLS 加密和证书相关的设置，以确保数据在传输过程中的安全性和完整性。
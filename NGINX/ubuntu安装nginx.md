手动安装Nginx是在Ubuntu上安装Nginx的一种方法，可以使用最新版本的Nginx源代码进行编译和安装。以下是在Ubuntu上手动安装Nginx的详细步骤。

1. 下载Nginx源代码

在Nginx的官网上下载最新版本的Nginx源代码。可以使用以下命令来下载：

```
wget https://nginx.org/download/nginx-1.20.1.tar.gz
```

2. 安装编译工具和依赖库

在编译和安装Nginx之前，需要先安装编译工具和Nginx依赖的库，例如PCRE、Zlib、OpenSSL等。可以使用以下命令来安装：

```
sudo apt-get update
sudo apt-get install build-essential zlib1g-dev libpcre3 libpcre3-dev libssl-dev
```

3. 解压并编译Nginx

将下载的Nginx源代码解压缩到指定目录中，例如：

```
tar -zxvf nginx-1.20.1.tar.gz -C /usr/local/src/
```

进入解压后的Nginx源代码目录，并使用`./configure`命令来生成Makefile文件，例如：

```
cd /usr/local/src/nginx-1.20.1/
./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-http_v2_module
```

该命令将Nginx安装到/usr/local/nginx目录下，并启用了SSL和HTTP/2模块。

使用make命令来编译Nginx，例如：

```
make
```

编译完成后，可以使用make install命令来安装Nginx：

```
sudo make install
```

4. 启动Nginx

安装完成后，可以使用以下命令来启动Nginx：

```
sudo /usr/local/nginx/sbin/nginx
```

如果启动成功，可以在浏览器中输入服务器IP地址或域名来访问Nginx服务器。Nginx默认的网站根目录是/usr/local/nginx/html/，可以将网页文件放在该目录下。

5. 设置Nginx开机自启动

为了使Nginx在服务器启动时自动启动，可以将Nginx添加到系统服务中。可以使用以下命令将Nginx添加到系统服务：

```
sudo nano /etc/systemd/system/nginx.service
```

并将以下内容添加到文件中：

```
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/usr/local/src/nginx-1.20.1/logs/nginx.pid
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s stop
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

保存并退出文件后，使用以下命令重新加载系统服务文件：

```
sudo systemctl daemon-reload
```

接下来，可以使用以下命令启用Nginx服务并设置开机自启动：

```
sudo systemctl enable nginx
sudo systemctl start nginx
```

这样，Nginx就会在服务器启动时自动启动，并且可以在浏览器中访问网站。

总结

手动安装Nginx是一种在Ubuntu上安装Nginx的方法，可以使用最新版本的Nginx源代码进行编译和安装。手动安装Nginx需要进行多个步骤，包括下载Nginx源代码、安装编译工具和依赖库、解压并编译Nginx、启动Nginx以及设置Nginx开机自启动等。如果需要对Nginx进行更多的配置和定制，可以编辑Nginx的配置文件进行配置。
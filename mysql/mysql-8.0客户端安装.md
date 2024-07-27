看起来您的系统中没有找到可用的 `mysql-community-client` 软件包。这可能是因为您尚未正确配置 MySQL 社区存储库或者该软件包在存储库中不可用。

请确保已正确安装了 MySQL 社区存储库，并且已更新存储库索引。然后再次尝试安装 MySQL 客户端软件包。

如果您尚未安装 MySQL 社区存储库，请按照以下步骤进行安装：

1. 下载 MySQL 社区存储库 RPM 包：

```bash
wget https://dev.mysql.com/get/mysql80-community-release-el7-11.noarch.rpm
```

2. 安装 MySQL 社区存储库 RPM 包：

```bash
sudo rpm -ivh mysql80-community-release-el7-11.noarch.rpm
```

3. 更新存储库索引：

```bash
sudo yum update
```

4. 现在再次尝试安装 MySQL 客户端软件包：

```bash
sudo yum install mysql-community-client
```

这应该可以帮助您安装 MySQL 8.0 客户端到您的系统中。
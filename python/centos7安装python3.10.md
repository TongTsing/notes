出现 “No package python310 available” 的错误是因为默认的 CentOS 7 仓库中没有 `python310` 这个包。要解决这个问题，可以通过以下几种方式来安装 Python 3.10：

### 方法 1：使用 SCL（Software Collections）

CentOS 7 支持 Software Collections (SCL)，它允许在系统上安装和使用多个软件版本，包括较新的 Python 版本。

1. 安装 SCL：
   ```bash
   sudo yum install centos-release-scl
   ```

2. 安装 Python 3.10：
   CentOS 7 默认的 SCL 中可能不包含 Python 3.10，但你可以尝试安装稍旧的 Python 版本，例如 3.6 或 3.8：
   ```bash
   sudo yum install rh-python38
   ```

3. 启用并使用 Python 3.8：
   ```bash
   scl enable rh-python38 bash
   python3 --version
   ```

### 方法 2：手动下载并编译安装 Python 3.10

如果你必须使用 Python 3.10，手动编译是一个可行的办法：

1. 安装依赖：
   ```bash
   sudo yum groupinstall "Development Tools"
   sudo yum install wget openssl-devel bzip2-devel libffi-devel
   ```

2. 下载 Python 3.10 源码：
   ```bash
   wget https://www.python.org/ftp/python/3.10.0/Python-3.10.0.tgz
   ```

3. 解压并编译：
   ```bash
   tar xzf Python-3.10.0.tgz
   cd Python-3.10.0
   ./configure --enable-optimizations
   make altinstall
   ```

4. 验证安装：
   ```bash
   python3.10 --version
   ```

### 方法 3：使用 `pyenv` 安装 Python 3.10

`pyenv` 是一个方便的工具，可以用来安装和管理多个 Python 版本。

1. 安装 `pyenv` 所需的依赖：
   ```bash
   sudo yum install -y gcc zlib-devel bzip2 bzip2-devel readline-devel sqlite sqlite-devel openssl-devel xz xz-devel libffi-devel git
   ```

2. 安装 `pyenv`：
   ```bash
   curl https://pyenv.run | bash
   ```

3. 将 `pyenv` 添加到你的 shell 中：
   在 `~/.bashrc` 文件中添加以下内容：
   ```bash
   export PATH="$HOME/.pyenv/bin:$PATH"
   eval "$(pyenv init --path)"
   eval "$(pyenv init -)"
   eval "$(pyenv virtualenv-init -)"
   ```
   然后重新加载 shell:
   ```bash
   source ~/.bashrc
   ```

4. 使用 `pyenv` 安装 Python 3.10：
   ```bash
   pyenv install 3.10.0
   pyenv global 3.10.0
   ```

5. 验证安装：
   ```bash
   python --version
   ```

通过这些方法，你可以在 CentOS 7 上成功安装 Python 3.10。

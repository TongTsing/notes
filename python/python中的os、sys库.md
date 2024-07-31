`os` 库和 `sys` 库是 Python 标准库中两个非常重要的模块，它们分别用于处理不同类型的操作。以下是对这两个库的详细介绍及其各自对应的操作：

### `os` 库

`os` 库用于与操作系统交互，提供了许多与文件系统、进程管理和环境变量相关的操作。它主要用于执行操作系统级别的任务。以下是 `os` 库的一些常见功能：

1. **文件和目录操作**：
    - 创建、删除、重命名文件和目录：
      ```python
      os.mkdir('new_dir')  # 创建目录
      os.remove('file.txt')  # 删除文件
      os.rename('old_name.txt', 'new_name.txt')  # 重命名文件
      os.rmdir('dir_name')  # 删除目录
      ```
    - 获取目录内容：
      ```python
      os.listdir('dir_name')  # 列出目录内容
      ```
    - 改变当前工作目录：
      ```python
      os.chdir('dir_name')  # 改变当前工作目录
      ```

2. **路径操作**：
    - 获取文件或目录的绝对路径：
      ```python
      os.path.abspath('file.txt')
      ```
    - 检查文件或目录是否存在：
      ```python
      os.path.exists('file.txt')
      ```
    - 分割路径和扩展名：
      ```python
      os.path.split('dir_name/file.txt')  # 分割路径
      os.path.splitext('file.txt')  # 分割扩展名
      ```

3. **环境变量**：
    - 获取和设置环境变量：
      ```python
      os.getenv('HOME')  # 获取环境变量
      os.environ['HOME'] = '/new/home'  # 设置环境变量
      ```

4. **执行系统命令**：
    - 运行系统命令：
      ```python
      os.system('ls -la')  # 在类Unix系统上列出目录内容
      ```

5. **进程管理**：
    - 获取当前进程ID：
      ```python
      os.getpid()
      ```

### `sys` 库

`sys` 库用于访问与 Python 解释器紧密相关的变量和函数。它主要用于操作 Python 运行时环境。以下是 `sys` 库的一些常见功能：

1. **命令行参数**：
    - 获取命令行参数：
      ```python
      import sys
      print(sys.argv)  # 命令行参数列表
      ```

2. **标准输入、输出和错误**：
    - 重定向标准输出和标准错误：
      ```python
      import sys
      sys.stdout = open('output.txt', 'w')  # 重定向标准输出到文件
      sys.stderr = open('error.txt', 'w')  # 重定向标准错误到文件
      ```

3. **解释器路径**：
    - 获取 Python 解释器的路径和模块搜索路径：
      ```python
      import sys
      print(sys.executable)  # Python 解释器的路径
      print(sys.path)  # 模块搜索路径
      ```

4. **退出程序**：
    - 退出 Python 程序并返回一个状态码：
      ```python
      import sys
      sys.exit(0)  # 正常退出
      sys.exit(1)  # 异常退出
      ```

5. **版本信息**：
    - 获取 Python 版本信息：
      ```python
      import sys
      print(sys.version)  # Python 版本信息
      print(sys.version_info)  # Python 版本信息（详细）
      ```

6. **运行时信息**：
    - 获取 Python 运行时的相关信息，如递归限制、垃圾收集器状态等：
      ```python
      import sys
      print(sys.getrecursionlimit())  # 获取递归限制
      sys.setrecursionlimit(1000)  # 设置递归限制
      print(sys.getrefcount(object))  # 获取对象的引用计数
      ```

### 总结

- `os` 库主要用于执行操作系统级别的任务，如文件和目录操作、路径操作、环境变量管理和进程管理。
- `sys` 库主要用于访问和操作与 Python 解释器相关的变量和函数，如命令行参数、标准输入输出和错误、解释器路径、退出程序、版本信息和运行时信息。

通过结合使用这两个库，可以有效地管理和控制 Python 程序的运行环境以及与操作系统的交互。
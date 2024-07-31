[python] 在Python中，可以使用 `signal` 模块来捕获和处理信号。信号是在UNIX系统中用于进程间通信的一种机制，可以让一个进程向另一个进程发送通知。常见的信号包括 `SIGINT`（通常是按Ctrl+C产生的）、`SIGTERM`（终止进程）、`SIGHUP`（挂起）、`SIGUSR1` 和 `SIGUSR2`（用户自定义信号）等。

以下是如何捕获和处理信号的步骤：

### 步骤 1: 导入 `signal` 模块
首先需要导入 `signal` 模块。
```python
import signal
import time
```

### 步骤 2: 定义信号处理函数
定义一个函数来处理捕获到的信号。这个函数会在信号到达时被调用。
```python
def signal_handler(signum, frame):
    print(f"Signal {signum} received, frame: {frame}")
```

### 步骤 3: 使用 `signal.signal` 注册信号处理函数
使用 `signal.signal` 函数将信号与处理函数关联起来。
```python
signal.signal(signal.SIGINT, signal_handler)  # 捕获 Ctrl+C 产生的 SIGINT 信号
signal.signal(signal.SIGTERM, signal_handler)  # 捕获终止信号
```

### 步骤 4: 保持程序运行以等待信号
通常你需要保持程序运行，以便能够捕获信号。可以使用 `time.sleep` 或者一个永远循环来实现。
```python
print("Running... Press Ctrl+C to exit.")
try:
    while True:
        time.sleep(1)
except KeyboardInterrupt:
    print("Program interrupted by user")
```

### 完整示例代码
以下是一个完整的示例程序，它演示了如何捕获和处理 `SIGINT` 和 `SIGTERM` 信号：
```python
import signal
import time

# 定义信号处理函数
def signal_handler(signum, frame):
    print(f"Signal {signum} received, frame: {frame}")
    if signum == signal.SIGINT:
        print("Handling SIGINT (Ctrl+C)")
    elif signum == signal.SIGTERM:
        print("Handling SIGTERM")

# 注册信号处理函数
signal.signal(signal.SIGINT, signal_handler)  # 捕获 Ctrl+C 产生的 SIGINT 信号
signal.signal(signal.SIGTERM, signal_handler)  # 捕获终止信号

# 保持程序运行以等待信号
print("Running... Press Ctrl+C to exit.")
try:
    while True:
        time.sleep(1)
except KeyboardInterrupt:
    print("Program interrupted by user")
```

### 注意事项
- `signal` 模块只能在主线程中使用，在子线程中使用会导致 `ValueError` 异常。
- 有些信号在不同的操作系统上可能不可用。例如，Windows系统上不支持 `SIGKILL` 和 `SIGSTOP` 信号。

通过以上方法，你可以在Python程序中捕获并处理各种信号，实现进程间的通信和控制。
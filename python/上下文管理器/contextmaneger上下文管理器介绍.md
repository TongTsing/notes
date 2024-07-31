Python 的 `contextmanager` 是一个用来简化上下文管理的工具。上下文管理器的主要目的是在代码块执行前后自动管理资源，例如文件、网络连接等。常见的上下文管理器有 `with` 语句，用于处理文件的打开和关闭操作。`contextmanager` 来自 `contextlib` 模块，用于将一个普通的函数转换为上下文管理器，使得它可以和 `with` 语句一起使用。

### 使用示例

首先，导入 `contextmanager`:

```python
from contextlib import contextmanager
```

然后，可以定义一个函数并使用 `@contextmanager` 装饰器。这个函数必须包含一个 `yield` 语句。`yield` 语句之前的代码会在进入上下文时执行，`yield` 语句之后的代码会在退出上下文时执行。

### 示例：文件操作上下文管理器

```python
from contextlib import contextmanager

@contextmanager
def open_file(name, mode):
    f = open(name, mode)
    try:
        yield f
    finally:
        f.close()
```

使用这个自定义的上下文管理器：

```python
with open_file('test.txt', 'w') as f:
    f.write('Hello, world!')
```

### 工作原理

- `open_file` 函数打开文件，并将文件对象赋给变量 `f`。
- 在 `yield f` 处，将文件对象 `f` 暴露给 `with` 语句内部的代码块。
- `with` 语句内部的代码块执行完毕后，执行 `finally` 块，确保文件被关闭。

### 另一个示例：时间测量上下文管理器

```python
import time
from contextlib import contextmanager

@contextmanager
def timer():
    start = time.time()
    try:
        yield
    finally:
        end = time.time()
        print(f'Time taken: {end - start} seconds')

# 使用自定义的时间测量上下文管理器
with timer():
    # 模拟耗时操作
    time.sleep(2)
```

在这个示例中：

- 进入上下文时记录开始时间。
- `yield` 暴露上下文管理器给 `with` 语句内部的代码块。
- 代码块执行完毕后，记录结束时间并计算耗时。

### 优点

- **简洁**：减少了样板代码，使得资源管理更加简洁。
- **安全**：确保资源无论在正常执行还是发生异常时都能正确释放。
- **可读性**：提高代码的可读性和维护性。

通过使用 `contextmanager` 装饰器，可以方便地定义自己的上下文管理器，从而在需要时自动管理资源，保证代码的整洁和安全。
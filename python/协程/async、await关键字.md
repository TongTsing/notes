在 Python 中，`async` 和 `await` 是用于编写异步代码的重要关键字。它们使得编写异步程序变得更简单和直观。

### 1. `async` 关键字

- **定义协程**: 使用 `async def` 定义一个协程函数。协程是可以被挂起和恢复的函数，允许其他任务在等待时运行。

```python
async def my_coroutine():
    print("This is a coroutine.")
```

### 2. `await` 关键字

- **暂停协程**: 在协程中使用 `await` 来调用另一个协程。这会暂停当前协程的执行，直到被 `await` 的协程完成。

```python
async def my_coroutine():
    print("Start")
    await asyncio.sleep(1)  # 暂停 1 秒
    print("End")
```

### 使用示例

下面是一个完整的示例，展示如何使用 `async` 和 `await` 来编写异步代码：

```python
import asyncio

async def say_hello():
    print("Hello")
    await asyncio.sleep(1)  # 模拟异步操作
    print("World")

async def main():
    await say_hello()  # 调用协程

if __name__ == "__main__":
    asyncio.run(main())  # 运行事件循环
```

### 关键点

- **非阻塞**: 使用 `async` 和 `await` 的代码是非阻塞的，这意味着在等待某个操作完成时，其他任务可以继续运行。
- **事件循环**: `asyncio.run()` 启动事件循环，运行协程。
- **组合使用**: 可以在一个协程中调用多个 `await` 来并发执行多个任务，使用 `asyncio.gather()` 可以同时运行多个协程。

### 总结

`async` 和 `await` 关键字使得 Python 的异步编程更加清晰和易于理解，特别适合处理 I/O 密集型操作，如网络请求、文件操作等。通过这些关键字，程序可以在等待响应时继续执行其他任务，提高了效率。
在 Python 的 `threading` 模块中，`Condition` 对象用于实现线程之间的复杂同步。`Condition` 对象包含一个锁（`Lock` 或 `RLock`），以及一组条件变量。线程可以等待某些条件满足后再继续执行。

### 基本用法

1. **创建 Condition 对象**：
   ```python
   import threading
   condition = threading.Condition()
   ```

2. **使用 `with` 语句来获取 Condition 的锁**：
   ```python
   with condition:
       # 进入 with 块后 condition 锁被自动获取
       # 可以在此块中调用 condition 的 wait 和 notify 方法
   ```

3. **线程等待某个条件**：
   ```python
   with condition:
       condition.wait()  # 当前线程会释放 condition 的锁并进入等待状态
       # 当其他线程调用 notify 或 notify_all 时，该线程会被唤醒
   ```

4. **线程通知其他等待线程条件已满足**：
   ```python
   with condition:
       condition.notify()  # 唤醒一个等待该 condition 的线程
       # 或者使用 condition.notify_all() 唤醒所有等待该 condition 的线程
   ```

### 示例：生产者-消费者问题
下面是一个使用 `Condition` 对象来解决生产者-消费者问题的例子：

```python
import threading
import time
import random

class ProducerConsumer:
    def __init__(self):
        self.condition = threading.Condition()
        self.queue = []
        self.max_items = 10

    def producer(self):
        while True:
            item = random.randint(1, 100)
            with self.condition:
                while len(self.queue) >= self.max_items:
                    self.condition.wait()  # 队列满时等待
                self.queue.append(item)
                print(f'Produced: {item}')
                self.condition.notify()  # 通知消费者

            time.sleep(random.random())

    def consumer(self):
        while True:
            with self.condition:
                while not self.queue:
                    self.condition.wait()  # 队列为空时等待
                item = self.queue.pop(0)
                print(f'Consumed: {item}')
                self.condition.notify()  # 通知生产者

            time.sleep(random.random())

# 创建生产者-消费者对象
pc = ProducerConsumer()

# 创建线程
producer_thread = threading.Thread(target=pc.producer)
consumer_thread = threading.Thread(target=pc.consumer)

# 启动线程
producer_thread.start()
consumer_thread.start()

# 等待线程完成
producer_thread.join()
consumer_thread.join()
```

### 解释
1. **ProducerConsumer 类**：包含生产者和消费者方法。
2. **producer 方法**：生成随机数并将其添加到队列中。当队列满时，生产者会等待。每次生产一个新项目后，它会通知消费者。
3. **consumer 方法**：从队列中取出一个项目并消费它。当队列为空时，消费者会等待。每次消费一个项目后，它会通知生产者。
4. **Condition 对象**：用于在线程间同步生产和消费的操作。

通过这种方式，`Condition` 对象可以很好地管理多线程环境中复杂的同步问题。
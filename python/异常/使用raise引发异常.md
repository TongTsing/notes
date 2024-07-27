[python] `raise` 是Python中用于抛出异常的关键字。它可以让你在程序中引发一个异常，以便在某些条件下中止程序执行并进入异常处理流程。

以下是 `raise` 关键字的一些常见用法：

### 1. 直接引发一个异常
```python
raise Exception("Something went wrong!")
```
这个例子中，程序会引发一个 `Exception` 异常，并显示消息 "Something went wrong!"。

### 2. 引发指定类型的异常
你可以引发Python内置的任意类型的异常，比如 `ValueError`、`TypeError` 等。
```python
raise ValueError("Invalid value!")
```

### 3. 从一个现有的异常引发新的异常
在处理一个异常的过程中，可以引发一个新的异常，保留原始异常的上下文信息。
```python
try:
    1 / 0
except ZeroDivisionError as e:
    raise ValueError("There was an error with the value") from e
```
这种情况下，`ValueError` 将包含原始的 `ZeroDivisionError` 作为其上下文信息。

### 4. 重新引发捕获的异常
在 `except` 块中，使用 `raise` 可以重新引发当前捕获的异常。
```python
try:
    1 / 0
except ZeroDivisionError:
    print("Caught division by zero")
    raise
```
这里捕获了 `ZeroDivisionError` 异常，并在打印消息后重新引发相同的异常。

### 5. 引发自定义异常
你可以定义自己的异常类，然后使用 `raise` 引发这些自定义异常。
```python
class MyCustomError(Exception):
    pass

raise MyCustomError("This is a custom error!")
```

### 示例代码
综合以上用法，以下是一个综合示例：
```python
class NegativeValueError(Exception):
    """Custom exception for negative values."""
    pass

def check_value(value):
    if value < 0:
        raise NegativeValueError("Value cannot be negative!")
    elif value == 0:
        raise ValueError("Value cannot be zero!")
    else:
        print("Value is positive.")

try:
    check_value(-1)
except NegativeValueError as e:
    print(f"Caught custom error: {e}")
except ValueError as e:
    print(f"Caught value error: {e}")
```
在这个示例中，我们定义了一个自定义异常 `NegativeValueError` 并在 `check_value` 函数中使用 `raise` 引发它。然后我们在 `try` 块中调用 `check_value`，并在 `except` 块中捕获并处理不同的异常。

通过这些示例，你可以更好地理解 `raise` 关键字在Python中的用法以及如何在你的代码中有效地处理异常。
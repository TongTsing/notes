在 Python 中，`__add__` 和 `__iadd__` 是特殊方法（也称为魔术方法或双下划线方法），用于定义对象的加法行为。这两个方法主要用于运算符重载，具体来说，`__add__` 用于定义加法运算符 `+` 的行为，而 `__iadd__` 用于定义原位加法运算符 `+=` 的行为。它们之间的主要区别在于处理方式和返回结果的不同。

### `__add__` 方法

`__add__` 方法用于定义当使用 `+` 运算符进行加法运算时的行为。它返回一个新的对象，而不会修改调用该方法的对象本身。

**示例：**

```python
class MyNumber:
    def __init__(self, value):
        self.value = value

    def __add__(self, other):
        return MyNumber(self.value + other.value)

    def __repr__(self):
        return f'MyNumber({self.value})'

num1 = MyNumber(10)
num2 = MyNumber(20)
result = num1 + num2
print(result)  # 输出: MyNumber(30)
print(num1)    # 输出: MyNumber(10)
print(num2)    # 输出: MyNumber(20)
```

在这个示例中，`num1 + num2` 调用了 `num1` 的 `__add__` 方法，并返回一个新的 `MyNumber` 对象，原始的 `num1` 和 `num2` 对象未被修改。

### `__iadd__` 方法

`__iadd__` 方法用于定义当使用 `+=` 运算符进行原位加法运算时的行为。它通常会修改调用该方法的对象本身，并返回该对象。

**示例：**

```python
class MyNumber:
    def __init__(self, value):
        self.value = value

    def __iadd__(self, other):
        self.value += other.value
        return self

    def __repr__(self):
        return f'MyNumber({self.value})'

num1 = MyNumber(10)
num2 = MyNumber(20)
num1 += num2
print(num1)  # 输出: MyNumber(30)
print(num2)  # 输出: MyNumber(20)
```

在这个示例中，`num1 += num2` 调用了 `num1` 的 `__iadd__` 方法，`num1` 对象的值被修改并返回自身。

### 区别总结

1. **方法名称**：
    - `__add__` 用于 `+` 运算符。
    - `__iadd__` 用于 `+=` 运算符。

2. **返回结果**：
    - `__add__` 返回一个新的对象，原始对象不变。
    - `__iadd__` 通常返回被修改后的原始对象。

3. **对象修改**：
    - `__add__` 不修改调用该方法的对象本身。
    - `__iadd__` 通常会修改调用该方法的对象本身。

4. **用法场景**：
    - 使用 `__add__` 适用于需要创建新对象的加法操作。
    - 使用 `__iadd__` 适用于需要在原地修改对象的加法操作。
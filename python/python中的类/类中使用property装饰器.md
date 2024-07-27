Python 的 `property` 装饰器是一个内置函数，它为类属性提供了一种简洁的方式来定义访问器（getter）、修改器（setter）和删除器（deleter）方法。通过 `property` 装饰器，可以控制对实例属性的访问，同时使代码更加清晰和易读。

以下是 `property` 装饰器的详细介绍和用法：

### 基本用法

假设我们有一个类，其中包含一个私有属性 `_x`。我们希望通过公共属性 `x` 来访问和修改这个私有属性，同时添加一些控制逻辑。我们可以使用 `property` 装饰器来实现这一点。

```python
class MyClass:
    def __init__(self, x):
        self._x = x

    @property
    def x(self):
        """获取属性值"""
        return self._x

    @x.setter
    def x(self, value):
        """设置属性值并添加控制逻辑"""
        if value < 0:
            raise ValueError("Value cannot be negative")
        self._x = value

    @x.deleter
    def x(self):
        """删除属性"""
        del self._x

# 使用示例
obj = MyClass(10)
print(obj.x)  # 输出: 10
obj.x = 20    # 设置新值
print(obj.x)  # 输出: 20

# 尝试设置一个无效值
try:
    obj.x = -10  # 将引发 ValueError
except ValueError as e:
    print(e)  # 输出: Value cannot be negative

# 删除属性
del obj.x
```

### `property` 装饰器的详细解释

1. **`@property`**:
    - 定义一个只读属性。通过这个装饰器，方法 `x` 成为一个 getter 方法。

2. **`@x.setter`**:
    - 定义一个 setter 方法，用于设置属性值。这个装饰器的名字必须与 getter 方法的名字相同（即 `x`）。

3. **`@x.deleter`**:
    - 定义一个 deleter 方法，用于删除属性。这个装饰器的名字也必须与 getter 方法的名字相同（即 `x`）。

### 为什么使用 `property` 装饰器？

- **数据封装**：隐藏属性的内部实现，通过公开的 getter 和 setter 方法来控制对属性的访问。
- **数据验证**：在 setter 方法中添加验证逻辑，确保属性值的合法性。
- **简洁和可读性**：与传统的 getter 和 setter 方法相比，使用 `property` 装饰器使代码更加简洁和易读。

### 示例：定义一个带有属性验证的类

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        return self._radius

    @radius.setter
    def radius(self, value):
        if value <= 0:
            raise ValueError("Radius must be positive")
        self._radius = value

    @property
    def area(self):
        return 3.14159 * (self._radius ** 2)

# 使用示例
c = Circle(5)
print(c.radius)  # 输出: 5
print(c.area)    # 输出: 78.53975
c.radius = 10
print(c.area)    # 输出: 314.159

# 尝试设置一个无效的半径
try:
    c.radius = -5  # 将引发 ValueError
except ValueError as e:
    print(e)  # 输出: Radius must be positive
```

在上面的示例中，`Circle` 类使用 `property` 装饰器定义了 `radius` 和 `area` 属性，其中 `radius` 属性带有验证逻辑，而 `area` 属性是只读的。这样可以确保在设置半径时，始终是正值，并且可以通过 `area` 属性动态计算圆的面积。

通过使用 `property` 装饰器，可以使类的设计更加合理，代码更加简洁和直观。
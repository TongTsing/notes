当我们处理集合（例如列表）中的元素时，Python提供了一些内置函数来简化常见的操作。这些函数包括`map`、`filter`和`reduce`。

1. `map(function, iterable)`
   `map`函数将一个函数应用于给定可迭代对象（例如列表）中的每个元素，并返回一个新的可迭代对象，其中包含应用函数后的结果。新的可迭代对象的长度与原始可迭代对象相同。

   示例：
   ```python
   numbers = [1, 2, 3, 4, 5]
   squared = map(lambda x: x ** 2, numbers)
   print(list(squared))  # 输出: [1, 4, 9, 16, 25]
   ```

   在上面的示例中，`map`函数将lambda函数应用于`numbers`列表中的每个元素，计算其平方，并返回一个新的可迭代对象`[1, 4, 9, 16, 25]`。

2. `filter(function, iterable)`
   `filter`函数用于根据给定函数的条件过滤可迭代对象中的元素，并返回一个新的可迭代对象，其中包含满足条件的元素。

   # 注释：如果参数x传入function后function返回非0，则把x记录为符合要求，添加到序列中

   示例：
   ```python
   numbers = [1, 2, 3, 4, 5]
   evens = filter(lambda x: x % 2 == 0, numbers)
   print(list(evens))  # 输出: [2, 4]
   ```

   在上面的示例中，`filter`函数使用lambda函数过滤出`numbers`列表中的偶数，返回一个新的可迭代对象`[2, 4]`。

3. `reduce(function, iterable[, initializer])`
   `reduce`函数在Python 3中已从内置函数中移除，它位于`functools`模块中。`reduce`函数对给定的可迭代对象中的元素应用函数，并返回一个单个的值。

   示例：
   ```python
   from functools import reduce
   
   numbers = [1, 2, 3, 4, 5]
   product = reduce(lambda x, y: x * y, numbers)
   print(product)  # 输出: 120
   ```

   在上面的示例中，`reduce`函数使用lambda函数将`numbers`列表中的元素逐个相乘，最终返回一个值`120`，即1 * 2 * 3 * 4 * 5。

请注意，使用`map`、`filter`和`reduce`时，可以使用lambda函数提供简短的匿名函数，也可以使用命名函数作为参数传递。这些函数对于处理集合中的元素非常有用，可以提供简洁且可读性强的代码。
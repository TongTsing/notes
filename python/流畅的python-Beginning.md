# 流程的python

## 一、迭代器相关知识点

- 可迭代的对象有个 \_\_iter\_\_ 方法，每次都实例化一个新的迭代器；而迭代器要实现 \_\_next\_\_ 方法，
  返回单个元素，此外还要实现 \_\_iter\_\_ 方法，返回迭代器本身。
  可迭代的对象、迭代器和生成器 ｜ 339
  因此，迭代器可以迭代，但是可迭代的对象不是迭代器

- 所有生成器都是迭代器，因为生成器完全实现了迭代器接口；这意味着生成器函数，可以不使用迭代器的典型实现方式（"\_\_iter__" + "\_\_next\_\_"）；

- 可迭代的对象不可以独立进行for循环， 因为for循环实际上是先进行 iter(x) 然后 开始执行next（x）; 执行next（）需要x是一个迭代器；

- \_\_iter\_\_函数的最终目的是返回一个可迭代迭代器（实现了\_\_iter\_\_和\_\_next\_\_的实例对象），而有\_\_iter\_\_方法的对象称为可迭代对象；

- 用于过滤的生成器函数![image-20221109143808099](D:\pictures\typora_image\image-20221109143808099.png)

  ```python
  >>> def vowel(c):
  ... return c.lower() in 'aeiou'
  ...
  >>> list(filter(vowel, 'Aardvark'))
  ['A', 'a', 'a']
  >>> import itertools
  >>> list(itertools.filterfalse(vowel, 'Aardvark'))
  ['r', 'd', 'v', 'r', 'k']
  >>> list(itertools.dropwhile(vowel, 'Aardvark'))
  ['r', 'd', 'v', 'a', 'r', 'k']
  >>> list(itertools.takewhile(vowel, 'Aardvark'))
  ['A', 'a']
  >>> list(itertools.compress('Aardvark', (1,0,1,1,0,1)))
  ['A', 'r', 'd', 'a']
  >>> list(itertools.islice('Aardvark', 4))
  ['A', 'a', 'r', 'd']
  >>> list(itertools.islice('Aardvark', 4, 7))
  ['v', 'a', 'r']
  >>> list(itertools.islice('Aardvark', 1, 7, 2))
  ['a', 'd', 'a']
  ```

  ![image-20221109144132694](D:\pictures\typora_image\image-20221109144132694-16680013447401.png)

  ```python
  >>> sample = [5, 4, 2, 8, 7, 6, 3, 0, 9, 1]
  >>> import itertools
  >>> list(itertools.accumulate(sample)) # ➊
  [5, 9, 11, 19, 26, 32, 35, 35, 44, 45]
  >>> list(itertools.accumulate(sample, min)) # ➋
  [5, 4, 2, 2, 2, 2, 2, 0, 0, 0]
  >>> list(itertools.accumulate(sample, max)) # ➌
  [5, 5, 5, 8, 8, 8, 8, 8, 9, 9]
  >>> import operator
  >>> list(itertools.accumulate(sample, operator.mul)) # ➍
  [5, 20, 40, 320, 2240, 13440, 40320, 0, 0, 0]
  >>> list(itertools.accumulate(range(1, 11), operator.mul))
  [1, 2, 6, 24, 120, 720, 5040, 40320, 362880, 3628800] # ➎
  ```

## 二、else语句：不仅仅是if - else;

​				**总体来说，else在if、for、while、try正常结束的情况下调用**

流程控制语句：if、for、while、try和function；finnally语句无论try有没有捕获到异常，都会执行

## 三、上下文管理器

​		上下文管理器“对象”存在的目的在于：管理with语句，就像迭代器的存在是为了管理for语句一样；
​		with语句的目的是简化try/finnally模式。这种模执行式用于保证一段代码执行完成后执行某项操作；即便那段代码由于异常、return语句或者sys.exit()调用而中止；也会执行指定的操作。finally子句中的代码通常用于释放重要的资源，或者还原临时变更的状态。

- 在for语句中，要求in之后应该是一个迭代器：1.实现了\_\_iter\_\_ + \_\_next\_\_ 两个方法的对象；
- 在with语句中，要求在with之后应该是一个上下文管理器：1.实现了\_\_enter\_\_和\_\_exit\_\_两个方法的对象
- 在调用顺序上:for语句会先调用iter(x)方法返回一个迭代器，之后调用next()方法取出迭代器的值；而with语句会先调用上下文管理器的__enter\_\_方法，并在with语句正常/异常结束后调用上下文管理器的\_\_exit\_\_(self, exc\_type, exc_value, traceback)方法；


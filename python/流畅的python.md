# 流程的python

## 一、迭代器相关知识点

- 可迭代的对象有个 \_\_iter\_\_ 方法，每次都实例化一个新的迭代器；而迭代器要实现 \_\_next\_\_ 方法，
  返回单个元素，此外还要实现 \_\_iter\_\_ 方法，返回迭代器本身。
  可迭代的对象、迭代器和生成器 ｜ 339
  因此，迭代器可以迭代，但是可迭代的对象不是迭代器

- 所有生成器都是迭代器，因为生成器完全实现了迭代器接口；这意味着生成器函数，可以不使用迭代器的典型实现方式（"\_\_iter__" + "\_\_next\_\_"）；

- 可迭代的对象不可以独立进行for循环， 因为for循环实际上是先进行 iter(x) 然后 开始执行next（x）; 执行next（）需要x是一个迭代器；

- \_\_iter\_\_函数的最终目的是返回一个可迭代迭代器（实现了\_\_iter\_\_和\_\_next\_\_的实例对象），而有\_\_iter\_\_方法的对象称为可迭代对象；

- 用于过滤的生成器函数![image-20221109143808099](D:\OneDrive - Bergen Community College\小笔头\typora_image\image-20221109143808099.png)

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

  ![image-20221109144132694](D:\OneDrive - Bergen Community College\小笔头\typora_image\image-20221109144132694.png)

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

- 在for语句中，要求in之后应该是一个迭代器对象：1.实现了\_\_iter\_\_ + \_\_next\_\_ 两个方法的对象；
- 在with语句中，要求在with之后应该是一个上下文管理器：1.实现了\_\_enter\_\_和\_\_exit\_\_两个方法的对象 

## 四、python中的装饰器

​		装饰器是可调用的对象，其参数是另一个函数（被装饰的函数）。装饰器可能会处理被装
饰的函数，然后把它返回，或者将其替换成另一个函数或可调用对象。

### 从参数化的装饰器认识装饰器：

```python
def logging(level1, level2):
    print("==========logging============")
    print("logging Items:{0}".format(locals()))
    def outwrapper(func):
        print("================outwrapper================")
        print("func Items:{0}".format(locals()))
        print("free values:{}".format(func.__code__.co_freevars))
        def wrapper(*args, **kwargs):
            print("================wrapper================")
            print("func Items:{0}".format(locals()))
            print("[{0}]: enter {1}()".format(level1, func.__name__))
            print("[{0}]: enter {1}()".format(level2, func.__name__))
            print("free values:{}".format(func.__code__.co_freevars))
            print("item of *args:{0}; **kwargs:{1}".format(args, kwargs))
            print("================wrapper================")
            return func(*args, **kwargs)
        print("================outwrapper================")
        return wrapper
    print("===========loging============")
    return outwrapper

@logging(level1="l1", level2="l2")
def hello(a, b, c):
    print(a, b, c)
    
hello("hello,","good","morning")

-----------------------------输出----------------------------


==========logging============
func Items:{'level': 'INFO'}
===========loging============

================outwrapper================
func Items:{'func': <function hello at 0x000001CF56CE89D0>, 'level': 'INFO'}
================outwrapper================

================wrapper================
func Items:{'args': ('hello,', 'good', 'morning'), 'kwargs': {}, 'func': <function hello at 0x000001CF56CE89D0>, 'level': 'INFO'}
[INFO]: enter hello()
item of *args:hello,; **kwargs:good
================wrapper================
hello, good morning
```

实例中装饰器的函数调用结构为 logging->outwrapper->wrapper；

装饰器的运行过程：

1.   首先执行logging(...)返回outwrapper；
2. 执行@outwrapper

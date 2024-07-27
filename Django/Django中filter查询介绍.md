在Django中，`filter()` 方法是查询集（QuerySet） API 中的一部分，用于从数据库中过滤数据。它允许你通过一个或多个条件来筛选模型中的对象。`filter()` 方法返回一个新的查询集，其中包含满足条件的所有对象。

以下是一些常见的使用方法：

### 1. 基本使用方法

假设你有一个简单的模型 `Person`，如下所示：

```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
    age = models.IntegerField()
    email = models.EmailField()
```

1. **按字段精确匹配**

   ```python
   from myapp.models import Person

   # 查询姓氏为 'Smith' 的所有人
   people = Person.objects.filter(last_name='Smith')
   ```

2. **按多个字段精确匹配**

   ```python
   # 查询姓氏为 'Smith' 且年龄为 30 的所有人
   people = Person.objects.filter(last_name='Smith', age=30)
   ```

### 2. 使用查询表达式

Django 提供了许多查询表达式来实现复杂的查询条件。以下是一些常见的查询表达式及其使用示例：

1. **`exact`**：精确匹配（默认）

   ```python
   people = Person.objects.filter(age__exact=30)
   ```

2. **`iexact`**：不区分大小写的精确匹配

   ```python
   people = Person.objects.filter(first_name__iexact='john')
   ```

3. **`contains`**：包含，区分大小写

   ```python
   people = Person.objects.filter(first_name__contains='oh')
   ```

4. **`icontains`**：包含，不区分大小写

   ```python
   people = Person.objects.filter(first_name__icontains='oh')
   ```

5. **`gt`、`gte`、`lt`、`lte`**：大于、大于等于、小于、小于等于

   ```python
   people = Person.objects.filter(age__gt=25)  # 年龄大于 25
   people = Person.objects.filter(age__gte=25) # 年龄大于等于 25
   people = Person.objects.filter(age__lt=25)  # 年龄小于 25
   people = Person.objects.filter(age__lte=25) # 年龄小于等于 25
   ```

6. **`in`**：在一个范围内

   ```python
   people = Person.objects.filter(age__in=[25, 30, 35])
   ```

7. **`startswith` 和 `istartswith`**：以指定字符串开头，区分大小写和不区分大小写

   ```python
   people = Person.objects.filter(first_name__startswith='J')
   people = Person.objects.filter(first_name__istartswith='j')
   ```

8. **`endswith` 和 `iendswith`**：以指定字符串结尾，区分大小写和不区分大小写

   ```python
   people = Person.objects.filter(first_name__endswith='n')
   people = Person.objects.filter(first_name__iendswith='N')
   ```

### 3. 使用 Q 对象进行复杂查询

当你需要进行复杂的查询（例如，OR 条件）时，可以使用 `Q` 对象：

```python
from django.db.models import Q

# 查询姓氏为 'Smith' 或年龄大于 30 的所有人
people = Person.objects.filter(Q(last_name='Smith') | Q(age__gt=30))

# 查询姓氏为 'Smith' 且年龄小于 25 或名字中包含 'John' 的所有人
people = Person.objects.filter((Q(last_name='Smith') & Q(age__lt=25)) | Q(first_name__icontains='John'))
```

### 4. 排序和限制结果

你可以对查询集进行排序和限制结果数量：

1. **排序**：

   ```python
   people = Person.objects.filter(last_name='Smith').order_by('age')  # 按年龄升序排序
   people = Person.objects.filter(last_name='Smith').order_by('-age') # 按年龄降序排序
   ```

2. **限制结果数量**：

   ```python
   people = Person.objects.filter(last_name='Smith')[:10]  # 只取前 10 个结果
   ```

使用这些方法和技巧，你可以灵活地从数据库中查询所需的数据。
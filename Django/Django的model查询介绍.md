好的，下面是每种查询的示例数据和对应的结果数据。假设我们有一个 `Person` 模型，数据如下：

### 示例数据

```python
from myapp.models import Person

# 清空表
Person.objects.all().delete()

# 插入示例数据
Person.objects.create(first_name='John', last_name='Smith', age=25, email='john.smith@example.com')
Person.objects.create(first_name='Jane', last_name='Doe', age=30, email='jane.doe@example.com')
Person.objects.create(first_name='Alice', last_name='Johnson', age=22, email='alice.johnson@example.com')
Person.objects.create(first_name='Bob', last_name='Smith', age=20, email='bob.smith@example.com')
Person.objects.create(first_name='Charlie', last_name='Brown', age=35, email='charlie.brown@example.com')
```

### 查询示例及结果

1. **`all()`**

   ```python
   all_people = Person.objects.all()
   ```

   **结果**:
   ```
   [<Person: John Smith>, <Person: Jane Doe>, <Person: Alice Johnson>, <Person: Bob Smith>, <Person: Charlie Brown>]
   ```

2. **`filter()`**

   ```python
   smiths = Person.objects.filter(last_name='Smith')
   ```

   **结果**:
   ```
   [<Person: John Smith>, <Person: Bob Smith>]
   ```

3. **`exclude()`**

   ```python
   non_smiths = Person.objects.exclude(last_name='Smith')
   ```

   **结果**:
   ```
   [<Person: Jane Doe>, <Person: Alice Johnson>, <Person: Charlie Brown>]
   ```

4. **`get()`**

   ```python
   try:
       person = Person.objects.get(email='john.smith@example.com')
   except Person.DoesNotExist:
       print("No person with this email exists.")
   except Person.MultipleObjectsReturned:
       print("Multiple people with this email found.")
   ```

   **结果**:
   ```
   <Person: John Smith>
   ```

5. **`count()`**

   ```python
   num_people = Person.objects.count()
   ```

   **结果**:
   ```
   5
   ```

6. **`aggregate()`**

   ```python
   from django.db.models import Avg, Max, Min, Sum

   average_age = Person.objects.aggregate(Avg('age'))
   max_age = Person.objects.aggregate(Max('age'))
   min_age = Person.objects.aggregate(Min('age'))
   total_age = Person.objects.aggregate(Sum('age'))
   ```

   **结果**:
   ```
   average_age: {'age__avg': 26.4}
   max_age: {'age__max': 35}
   min_age: {'age__min': 20}
   total_age: {'age__sum': 132}
   ```

7. **`order_by()`**

   ```python
   ordered_by_age = Person.objects.order_by('age')
   ```

   **结果**:
   ```
   [<Person: Bob Smith>, <Person: Alice Johnson>, <Person: John Smith>, <Person: Jane Doe>, <Person: Charlie Brown>]
   ```

8. **`distinct()`**

   ```python
   distinct_last_names = Person.objects.values('last_name').distinct()
   ```

   **结果**:
   ```
   [{'last_name': 'Smith'}, {'last_name': 'Doe'}, {'last_name': 'Johnson'}, {'last_name': 'Brown'}]
   ```

9. **`first()` 和 `last()`**

   ```python
   first_person = Person.objects.first()
   last_person = Person.objects.last()
   ```

   **结果**:
   ```
   first_person: <Person: John Smith>
   last_person: <Person: Charlie Brown>
   ```

10. **`exists()`**

    ```python
    has_adults = Person.objects.filter(age__gte=18).exists()
    ```

    **结果**:
    ```
    True
    ```

11. **`values()` 和 `values_list()`**

    ```python
    names = Person.objects.values('first_name', 'last_name')
    names_list = Person.objects.values_list('first_name', 'last_name')
    ```

    **结果**:
    ```
    names: [{'first_name': 'John', 'last_name': 'Smith'}, {'first_name': 'Jane', 'last_name': 'Doe'}, {'first_name': 'Alice', 'last_name': 'Johnson'}, {'first_name': 'Bob', 'last_name': 'Smith'}, {'first_name': 'Charlie', 'last_name': 'Brown'}]
    names_list: [('John', 'Smith'), ('Jane', 'Doe'), ('Alice', 'Johnson'), ('Bob', 'Smith'), ('Charlie', 'Brown')]
    ```

12. **Q 对象**

    ```python
    from django.db.models import Q

    complex_query = Person.objects.filter(Q(last_name='Smith') | Q(age__lt=18))
    ```

    **结果**:
    ```
    [<Person: John Smith>, <Person: Bob Smith>]
    ```

13. **`select_related()` 和 `prefetch_related()`**

    假设有一个 `Address` 模型和一个 `Hobby` 模型，并且 `Person` 模型通过外键和多对多字段与这些模型相关联。

    ```python
    people_with_addresses = Person.objects.select_related('address').all()
    people_with_hobbies = Person.objects.prefetch_related('hobbies').all()
    ```

    **结果**:
    - `select_related()` 和 `prefetch_related()` 是用于优化查询，具体返回的数据与普通查询相同，但执行速度更快，减少了数据库查询次数。

14. **`create()`**

    ```python
    new_person = Person.objects.create(
        first_name='David',
        last_name='Clark',
        age=28,
        email='david.clark@example.com'
    )
    ```

    **结果**:
    ```
    <Person: David Clark>
    ```

15. **`update()`**

    ```python
    Person.objects.filter(last_name='Doe').update(age=35)
    ```

    **结果**:
    - 所有 `last_name` 为 `Doe` 的人的 `age` 更新为 `35`。

16. **`delete()`**

    ```python
    Person.objects.filter(last_name='Doe').delete()
    ```

    **结果**:
    - 所有 `last_name` 为 `Doe` 的人被删除。

17. **`raw()`**

    ```python
    people = Person.objects.raw('SELECT * FROM myapp_person WHERE age > %s', [30])
    ```

    **结果**:
    ```
    [<Person: Jane Doe>, <Person: Charlie Brown>]
    ```

这些示例展示了如何使用Django的查询集方法来执行各种查询操作，并列出了示例数据和相应的查询结果。
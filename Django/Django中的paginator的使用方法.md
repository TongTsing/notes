```python
from django.core.paginator import Paginator
from .models import User  # 假设你的用户模型在一个名为models.py的文件中

def user_list(request):
    user_list = User.objects.all()
    paginator = Paginator(user_list, 10)
    page = paginator.get_page(1)

    print(page.has_next())                  # 检查是否有下一页，输出: True
    print(page.has_previous())              # 检查是否有上一页，输出: False
    print(page.next_page_number())          # 返回下一页的页码，输出: 2
    print(page.previous_page_number())      # 返回上一页的页码，输出: 1
    print(page.number())                    # 返回当前页的页码，输出: 1
    print(page.paginator)                   # 返回Paginator对象，输出: <django.core.paginator.Paginator object at 0x7f874d5cb940>
    print(page.object_list)                 # 返回当前页的对象列表，输出: <QuerySet [<User: user1>, <User: user2>, ...]>
    print(page.start_index())               # 返回当前页第一个对象的索引（从1开始），输出: 1
    print(page.end_index())                 # 返回当前页最后一个对象的索引，输出: 10
    print(page.has_other_pages())           # 检查是否有其他页（除了当前页），输出: True
    print(page.paginator.num_pages)         # 返回总页数，输出: 10
    print(page.paginator.count)             # 返回总对象数，输出: 100
    print(len(page))                        # 返回当前页对象列表的长度，输出: 10

```


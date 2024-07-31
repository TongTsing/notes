在 SQL 中，文本匹配通常用于从文本列中查找、筛选或提取特定模式的文本。以下是一些常见的文本匹配方法及其用途、用法和示例：

1. **LIKE 运算符**：
   - 用途：用于在文本列中进行简单的模式匹配。
   - 用法：`LIKE` 运算符结合通配符（`%`表示零个或多个字符，`_`表示一个字符）来匹配文本。
   - 示例：查找所有以 "John" 开头的姓名：
     ```sql
     SELECT * FROM employees WHERE name LIKE 'John%';
     ```

2. **REGEXP/RLIKE 函数**：
   - 用途：用于在文本列中使用正则表达式进行模式匹配。
   - 用法：`REGEXP` 或 `RLIKE` 函数接受一个正则表达式作为参数，并返回匹配的结果。
   - 示例：查找所有包含 "gmail.com" 或 "yahoo.com" 的邮箱地址：
     ```sql
     SELECT * FROM users WHERE email REGEXP 'gmail\.com|yahoo\.com';
     ```

3. **SUBSTRING 函数**：
   - 用途：用于从文本列中提取子字符串。
   - 用法：`SUBSTRING` 函数接受字符串、起始位置和长度等参数，并返回提取的子字符串。
   - 示例：从 URL 列中提取域名：
     ```sql
     SELECT SUBSTRING(url, LOCATE('//', url) + 2, LOCATE('/', url, LOCATE('//', url) + 2) - LOCATE('//', url) - 2) AS domain FROM websites;
     ```

4. **CONCAT 函数**：
   - 用途：用于连接文本列或字符串。
   - 用法：`CONCAT` 函数接受多个字符串参数，并返回连接后的结果。
   - 示例：连接姓和名以生成全名：
     ```sql
     SELECT CONCAT(first_name, ' ', last_name) AS full_name FROM employees;
     ```

5. **TRIM 函数**：
   - 用途：用于去除文本列中的空格或其他指定字符。
   - 用法：`TRIM` 函数接受字符串和要去除的字符类型参数，并返回去除指定字符后的结果。
   - 示例：去除姓名列中的首尾空格：
     ```sql
     SELECT TRIM(name) AS trimmed_name FROM users;
     ```

这些是一些常见的 SQL 中用于文本匹配的方法，它们可以用于从文本列中查找、筛选、提取和处理文本数据。

`SUBSTRING` 和 `SUBSTRING_INDEX` 是 SQL 中用于处理字符串的函数，它们在提取子字符串方面有不同的用途和用法。

### SUBSTRING 函数：
- 用途：从一个字符串中提取子字符串。
- 语法：在大多数 SQL 数据库中，`SUBSTRING` 函数的语法如下：
  ```sql
  SUBSTRING(string_expression, start_position [, length])
  ```
  - `string_expression`：要从中提取子字符串的原始字符串。
  - `start_position`：子字符串的起始位置。如果为正数，则表示从字符串的起始位置开始计数；如果为负数，则表示从字符串末尾倒数计数。
  - `length`：可选参数，表示要提取的子字符串的长度。如果省略，则返回从起始位置到字符串末尾的所有字符。

### SUBSTRING_INDEX 函数：
- 用途：从一个字符串中提取子字符串，根据指定的分隔符进行切割。
- 语法：在大多数 SQL 数据库中，`SUBSTRING_INDEX` 函数的语法如下：
  ```sql
  SUBSTRING_INDEX(string, delimiter, count)
  ```
  - `string`：要进行切割的原始字符串。
  - `delimiter`：分隔符，用于指定切割的位置。
  - `count`：指定返回的子字符串的个数，如果为正数，则从字符串的开头开始切割；如果为负数，则从字符串的末尾开始切割。

### 示例：
假设有一个名为 `user_submit` 的表，其中有一个 `profile` 列存储了用户的个人信息。现在我们想从 `profile` 列中提取年龄信息。

示例查询：
```sql
SELECT SUBSTRING_INDEX(SUBSTRING_INDEX(profile, ',', -2), ',', 1) AS age FROM user_submit;
```

在这个示例中，`SUBSTRING_INDEX` 函数首先从 `profile` 列中提取出逗号分隔的倒数第二个元素，然后再次使用 `SUBSTRING_INDEX` 函数从这个子字符串中提取出逗号分隔的第一个元素，即年龄信息。
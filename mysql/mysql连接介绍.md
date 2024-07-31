以下是 MySQL 中常见连接类型的语法和对应的集合操作：

1. 内连接（Inner Join）：
   - 语法：
     ```sql
     SELECT 列名 FROM 表1 INNER JOIN 表2 ON 连接条件;
     ```
   - 集合操作：内连接返回两个表中符合连接条件的交集，类似于条件连接操作。

2. 外连接（Outer Join）：
   - 左外连接（Left Join）：
     - 语法：
       ```sql
       SELECT 列名 FROM 表1 LEFT JOIN 表2 ON 连接条件;
       ```
     - 集合操作：左外连接返回左表的所有行，以及与右表匹配的行，如果没有匹配的行，则右表的列将包含 NULL 值。（对应集合的右连接操作）
   - 右外连接（Right Join）：
     - 语法：
       ```sql
       SELECT 列名 FROM 表1 RIGHT JOIN 表2 ON 连接条件;
       ```
     - 集合操作：右外连接返回右表的所有行，以及与左表匹配的行，如果没有匹配的行，则左表的列将包含 NULL 值。（对应集合的右连接）
   - 全外连接（Full Join）：
     - 语法：
       ```sql
       SELECT 列名 FROM 表1 FULL JOIN 表2 ON 连接条件;
       ```
     - 集合操作：全外连接返回左表和右表的所有行，如果没有匹配的行，则对应的列将包含 NULL 值。

3. 自然连接（Natural Join）：
   - 语法：
     ```sql
     SELECT 列名 FROM 表1 NATURAL JOIN 表2;
     ```
   - 集合操作：自然连接会自动使用两个表中相同列名进行连接，类似于交集操作。

4. 交叉连接（Cross Join）：
   - 语法：
     ```sql
     SELECT 列名 FROM 表1 CROSS JOIN 表2;
     ```
   - 集合操作：交叉连接返回两个表的笛卡尔积，即两个表中所有可能的组合。

以上是常见连接类型的语法和对应的集合操作，可以根据具体的业务需求选择合适的连接方式。
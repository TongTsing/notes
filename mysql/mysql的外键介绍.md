在 MySQL 中，创建外键（Foreign Key）以及进行相应的数据增删改查操作的步骤如下：

### 创建外键

假设我们有两个表：`users` 和 `orders`。每个订单记录都属于一个用户，因此我们将在 `orders` 表中创建一个外键来引用 `users` 表。

```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_date DATE NOT NULL,
    amount DECIMAL(10, 2) NOT NULL,
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE ON UPDATE CASCADE
);
```

在上述代码中：
- `user_id` 是 `orders` 表中的外键，它引用 `users` 表的 `id` 字段。
- `ON DELETE CASCADE` 表示如果 `users` 表中的某个用户被删除，所有与该用户关联的订单记录也将被删除。
- `ON UPDATE CASCADE` 表示如果 `users` 表中的某个用户的 `id` 被更新，所有与该用户关联的订单记录的 `user_id` 也将自动更新。

### 插入数据

插入数据时，先插入父表（`users` 表），然后插入子表（`orders` 表）。

```sql
-- 插入用户数据
INSERT INTO users (username, email) VALUES ('Alice', 'alice@example.com');
INSERT INTO users (username, email) VALUES ('Bob', 'bob@example.com');

-- 插入订单数据
INSERT INTO orders (order_date, amount, user_id) VALUES ('2024-06-13', 99.99, 1);
INSERT INTO orders (order_date, amount, user_id) VALUES ('2024-06-14', 149.99, 2);
```

### 查询数据

查询带有外键关系的数据时，可以使用 `JOIN` 操作。

```sql
-- 查询所有订单及其对应的用户信息
SELECT orders.id AS order_id, orders.order_date, orders.amount, users.username, users.email
FROM orders
JOIN users ON orders.user_id = users.id;
```

### 更新数据

更新外键引用时，如果有 `ON UPDATE CASCADE` 选项，则相关的外键字段也会更新。

```sql
-- 更新用户数据
UPDATE users SET username = 'Alice Smith' WHERE id = 1;

-- 更新订单数据
UPDATE orders SET amount = 199.99 WHERE id = 1;
```

### 删除数据

删除父表数据时，如果有 `ON DELETE CASCADE` 选项，则相关的外键记录也会删除。

```sql
-- 删除用户数据
DELETE FROM users WHERE id = 1;  -- 这将同时删除 user_id 为 1 的所有订单
```

### 代码示例总结

这里是所有操作的完整代码示例：

```sql
-- 创建表
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_date DATE NOT NULL,
    amount DECIMAL(10, 2) NOT NULL,
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE ON UPDATE CASCADE
);

-- 插入数据
INSERT INTO users (username, email) VALUES ('Alice', 'alice@example.com');
INSERT INTO users (username, email) VALUES ('Bob', 'bob@example.com');
INSERT INTO orders (order_date, amount, user_id) VALUES ('2024-06-13', 99.99, 1);
INSERT INTO orders (order_date, amount, user_id) VALUES ('2024-06-14', 149.99, 2);

-- 查询数据
SELECT orders.id AS order_id, orders.order_date, orders.amount, users.username, users.email
FROM orders
JOIN users ON orders.user_id = users.id;

-- 更新数据
UPDATE users SET username = 'Alice Smith' WHERE id = 1;
UPDATE orders SET amount = 199.99 WHERE id = 1;

-- 删除数据
DELETE FROM users WHERE id = 1;
```

通过这些步骤，你可以在 MySQL 中创建外键，并进行相应的数据增删改查操作。
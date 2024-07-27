在MySQL中创建一对一关系的两张表通常有两种方式：

1. **使用主键作为外键**：在一张表中将外键设为另一张表的主键。
2. **使用唯一外键**：在一张表中使用外键并对其设置唯一约束。

这里是这两种方法的示例。

### 方法 1：使用主键作为外键

假设我们有两张表：`users` 和 `user_profiles`。每个用户都有一个唯一的配置文件。

```sql
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) NOT NULL
);

CREATE TABLE user_profiles (
    profile_id INT PRIMARY KEY,
    user_id INT,
    address VARCHAR(255),
    phone VARCHAR(20),
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
);
```

在这个例子中：

- `users` 表有一个 `user_id` 作为主键。
- `user_profiles` 表有一个 `profile_id` 作为主键，并且 `user_id` 作为外键且是唯一的。

### 方法 2：使用唯一外键

另一种方法是将 `user_profiles` 表中的 `user_id` 列设置为唯一：

```sql
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) NOT NULL
);

CREATE TABLE user_profiles (
    profile_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNIQUE,
    address VARCHAR(255),
    phone VARCHAR(20),
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
);
```

在这个例子中：

- `users` 表有一个 `user_id` 作为主键。
- `user_profiles` 表中 `user_id` 是唯一的并且是外键，确保每个 `user_profiles` 记录只能对应一个 `users` 记录。

### 插入和查询数据

#### 插入数据

插入用户和其配置文件数据：

```sql
INSERT INTO users (username) VALUES ('Alice'), ('Bob');

INSERT INTO user_profiles (user_id, address, phone) VALUES 
(1, '123 Main St', '555-1234'), 
(2, '456 Elm St', '555-5678');
```

#### 查询数据

查询用户及其配置文件信息：

```sql
SELECT u.username, p.address, p.phone
FROM users u
JOIN user_profiles p ON u.user_id = p.user_id;
```

通过这些方法，你可以在MySQL中创建和管理一对一关系的表。使用哪种方法取决于具体的需求和设计选择。
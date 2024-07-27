在MySQL中创建两个多对多关系的表需要使用一个中间表（也称为联接表或关联表）来存储表之间的关系。这里是一个具体的示例，假设我们有两个表 `students` 和 `courses`，表示学生和课程，每个学生可以参加多个课程，每个课程可以有多个学生。

### 步骤 1：创建主表

首先，创建 `students` 和 `courses` 表：

```sql
CREATE TABLE students (
    student_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL
);

CREATE TABLE courses (
    course_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(100) NOT NULL
);
```

### 步骤 2：创建中间表

接下来，创建一个中间表 `student_courses` 来存储学生和课程之间的关系：

```sql
CREATE TABLE student_courses (
    student_id INT,
    course_id INT,
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
    FOREIGN KEY (course_id) REFERENCES courses(course_id) ON DELETE CASCADE
);
```

### 解释

- `student_id` 和 `course_id` 是外键，分别引用 `students` 和 `courses` 表中的主键。
- `PRIMARY KEY (student_id, course_id)` 确保每个学生和每个课程的组合是唯一的。
- `FOREIGN KEY` 约束确保引用的完整性，并且 `ON DELETE CASCADE` 选项表示当学生或课程被删除时，相关的记录也会从中间表中删除。

### 插入数据

插入示例数据：

```sql
INSERT INTO students (name) VALUES ('Alice'), ('Bob'), ('Charlie');
INSERT INTO courses (title) VALUES ('Math'), ('Science'), ('History');

INSERT INTO student_courses (student_id, course_id) VALUES 
(1, 1), (1, 2), 
(2, 1), (2, 3), 
(3, 2), (3, 3);
```

### 查询数据

查询某个学生参加的所有课程：

```sql
SELECT s.name, c.title
FROM students s
JOIN student_courses sc ON s.student_id = sc.student_id
JOIN courses c ON sc.course_id = c.course_id
WHERE s.name = 'Alice';
```

查询某个课程的所有学生：

```sql
SELECT c.title, s.name
FROM courses c
JOIN student_courses sc ON c.course_id = sc.course_id
JOIN students s ON sc.student_id = s.student_id
WHERE c.title = 'Math';
```

通过这种方法，你可以在MySQL中创建两个多对多关系的表，并使用中间表来管理它们之间的关系。
在 MongoDB 中，可以使用一系列比较运算符来执行查询操作，这些比较运算符可以用于比较文档中字段的值。以下是 MongoDB 中常用的比较运算符：

1. **相等：**
   - `$eq`：等于
   - `$ne`：不等于

   ```javascript
   db.collection.find({ "age": { "$eq": 30 } }); // 查找年龄等于30的文档
   db.collection.find({ "status": { "$ne": "active" } }); // 查找状态不等于"active"的文档
   ```

2. **比较大小：**
   - `$gt`：大于
   - `$gte`：大于等于
   - `$lt`：小于
   - `$lte`：小于等于

   ```javascript
   db.collection.find({ "age": { "$gt": 25, "$lt": 35 } }); // 查找年龄在25和35之间的文档
   ```

3. **范围查询：**
   - `$in`：匹配数组中任意值
   - `$nin`：不匹配数组中任何值

   ```javascript
   db.collection.find({ "status": { "$in": ["active", "pending"] } }); // 查找状态为"active"或"pending"的文档
   ```

4. **存在性检查：**
   - `$exists`：检查字段是否存在

   ```javascript
   db.collection.find({ "name": { "$exists": true } }); // 查找具有名字字段的文档
   ```

5. **正则表达式：**
   - `$regex`：使用正则表达式匹配字段值

   ```javascript
   db.collection.find({ "name": { "$regex": "^J" } }); // 查找名字以字母J开头的文档
   ```

6. **逻辑运算符：**
   - `$and`：逻辑与
   - `$or`：逻辑或
   - `$not`：逻辑非
   - `$nor`：与逻辑或相反

   ```javascript
   db.collection.find({ "$or": [{ "age": { "$lt": 18 } }, { "age": { "$gt": 65 } }] }); // 查找年龄小于18或大于65的文档
   ```

这些比较运算符可以帮助您根据特定条件过滤和查询 MongoDB 中的文档数据。
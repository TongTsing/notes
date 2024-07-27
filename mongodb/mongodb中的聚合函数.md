MongoDB 的聚合框架提供了强大的功能，允许您对文档进行各种操作，如分组、筛选、计数、计算总和、平均值等。以下是 MongoDB 中常用的聚合函数和操作：

1. **\$match：** 用于筛选文档，类似于查询操作中的条件筛选。
   ```javascript
   db.collection.aggregate([
     { $match: { status: "A" } } // 筛选状态为"A"的文档
   ]);
   ```

2. **\$group：** 用于对文档进行分组操作，通常与聚合函数一起使用。
   ```javascript
   db.collection.aggregate([
     { $group: { _id: "$status", total: { $sum: 1 } } } // 按状态分组，并计算每个状态的文档总数
   ]);
   ```

3. **\$sort：** 用于对文档进行排序操作。
   ```javascript
   db.collection.aggregate([
     { $sort: { age: 1 } } // 按年龄升序排序
   ]);
   ```

4. **\$project：** 用于重新定义输出文档中的字段，也可用于添加新字段。
   ```javascript
   db.collection.aggregate([
     { $project: { name: 1, age: 1 } } // 只包含名字和年龄字段
   ]);
   ```

5. **\$limit：** 用于限制结果集中返回的文档数量。
   ```javascript
   db.collection.aggregate([
     { $limit: 10 } // 返回前10个文档
   ]);
   ```

6. **\$skip：** 用于跳过结果集中的指定数量的文档。
   ```javascript
   db.collection.aggregate([
     { $skip: 10 } // 跳过前10个文档
   ]);
   ```

7. **\$unwind：** 用于展开数组字段，将数组字段中的每个元素作为单独的文档输出。
   ```javascript
   db.collection.aggregate([
     { $unwind: "$tags" } // 将数组字段tags展开为单独的文档
   ]);
   ```

8. **\$lookup：** 用于从另一个集合中进行关联查询操作。
   ```javascript
   db.collection.aggregate([
     {
       $lookup:
         {
           from: "anotherCollection",
           localField: "field",
           foreignField: "anotherField",
           as: "output"
         }
    }
   ]);
   ```

这些是 MongoDB 中常用的聚合函数和操作，可以帮助您对文档数据进行更复杂的处理和分析。
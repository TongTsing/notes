

# MongoDB使用指南

## 一、数据库操作：

### 1、新建数据库

```sql
use db_name;
```

### 2、查看当前存在的数据库

```sql
show dbs;
```



### 3、选择要使用的数据库、查看当前使用的数据库77

```sql
> use runoob
switched to db runoob
# 查看当前使用的数据库
> db
runoob
> 
```

### 4、删除数据库

```sqlite
db.dropDatabase()
```

## 二、集合操作

###  xxxxxxxxxx [root@test ~]# /etc/init.d/mysqld restartStopping mysqld: [ OK ]Starting mysqld: [ OK ]shell

- **参数说明**：

  - **name**：要创建的集合名称
  - **options**：可选参数，指定有关内存大小及

- **opstions参数**：

  - **（布尔型）capped**： （可选）如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。 **当该值为 true 时，必须指定 size 参数。**
  - **（布尔型）autoindexid**：3.2 之后不再支持该参数。（可选）如为 true，自动在 _id 字段创建索引。默认为 false。
  - **（数值）size**： （可选）为固定集合指定一个最大值，即字节数。 **如果 capped 为 true，也需要指定该字段。**
  - **（数值）max**：（可选）指定固定集合中包含文档的最大数量。

- **补充： 在mongodb中不需要手动创建集合也可以进行数据的写入；当发行对不存在于数据库中的集合进行写入时候，mongodb会自动创建集合**

- 示例：

  ```sql
  # 选择/创建要使用的数据库
  > use test
  switched to db test
  > db.createCollection("runoob")
  { "ok" : 1 }
  ```

### 2. 查看当前使用数据库已有集合：show collections/show tables

```sql
> db
test
> show tables
t1
> 
```

### 3. 插入文档：db.collection_name.insertOne()/db.collection_name.insertMany()



```sql
db.collection.insertMany(
   [ <document 1> , <document 2>, ... ],
   {
      writeConcern: <document>,
      ordered: <boolean>
   }
)
```

**参数说明**：

- **document**：要写入的文档。
- **writeConcern**：写入策略，默认为 1，即要求确认写操作，0 是不要求。
- **ordered**：指定是否按顺序写入，默认 true，按顺序写入。

示例：

```sql
> db.t1.insertMany([{"name": "tq", "age": 18}],{writeConcern: 0,ordered: true})
{
	"acknowledged" : true,
	"insertedIds" : [
		ObjectId("62dbdca06ad5650db541d282")
	]
}
> 
```

### 3. 更新文档：db.collection.update(<querypolice>, <updateContent>, <updatepolice>)/ db.collection.save(<documentContent>, writeConcern)

```sql
db.collection.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)
```

**参数说明：**

- **query** : update的查询条件，类似sql update查询内where后面的。
- **update** : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
- **upsert** : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
- **multi** : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
- **writeConcern** :可选，抛出异常的级别。

**示例**：

```

```

**示例：**

```sql
> db.t1.find().pretty()
{ "_id" : ObjectId("62d63a34f20679ec16cd00cf"), "name" : "tq", "age" : 23 }
{ "_id" : 1, "name" : "tq", "sex" : "male", "age" : "11" }
{
	"_id" : ObjectId("62dbdca06ad5650db541d282"),
	"name" : "update_test2",
	"age" : "NaM",
	"sex" : "male"
}
> db.t1.save({"_id" : ObjectId("62dbdca06ad5650db541d282"), "name" : "update_test3", "age" : "NaM", "sex" : "male"})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.t1.find().pretty()
{ "_id" : ObjectId("62d63a34f20679ec16cd00cf"), "name" : "tq", "age" : 23 }
{ "_id" : 1, "name" : "tq", "sex" : "male", "age" : "11" }
{
	"_id" : ObjectId("62dbdca06ad5650db541d282"),
	"name" : "update_test3",
	"age" : "NaM",
	"sex" : "male"
}
> 

```


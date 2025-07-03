title: mongoDB 基础知识
author: Laiyong Wang
tags:
  - mongoDB
categories: []
date: 2024-07-15 10:37:00
---
### 基本概念

- **数据库（Database）**：是集合的容器，相当于关系型数据库中的数据库。
- **集合（Collection）**：数据库中的一组文档，相当于关系型数据库中的表。
- **文档（Document）**：集合中的一条记录，相当于关系型数据库表中的一行，不同的文档之间不必有相同的结构。
- **字段（Field）**：文档中的键值对，相当于关系型数据库中的列。一个字段可以是一个 JSON 对象或数组。

### 基本命令

- **显示所有数据库**：`show databases` 或 `show dbs`
- **切换到某个数据库**：`use <dbname>`
- **显示当前使用中的数据库名称**：`db`
- **显示当前数据库中的所有集合**：`show collections`
- **删除集合**：`db.collectionName.drop()`
- **删除当前数据库**：`db.dropDatabase()`
- **退出 mongosh 会话**：`exit`

### 创建和插入数据
- **插入一个新文档**：
  ```javascript
  db.user.insertOne({ name: "王" })
  ```
- **插入多个新文档**：
  ```javascript
  db.user.insertMany([{ name: "来" }, { name: "勇" }])
  ```
- **先插入一百条数据供查询使用**：
  ```javascript
  for (var i=1;i<=100;i++) db.user.insertOne({name:i,age:i,group:Math.floor(i/10)})
  ```

### 查询数据

- **查询所有文档**：
  ```javascript
  db.users.find()
  ```
- **根据条件查询文档**：
  ```javascript
  db.users.find({ name: "1" })
  ```
- **查询并只返回指定字段**：
  ```javascript
  db.users.find({ name: "2" }, { age: 1})
  ```
- **查询满足条件的第一条记录**：
  ```javascript
  db.users.findOne({ age: 12 })
  ```
- **返回满足条件的记录数量**：
  ```javascript
  db.users.countDocuments({ group: 3 })
  ```

### 常用聚合操作

- **计算总和**：`$sum`
```
//按照 group 分组
db.user.aggregate([ { $group: { _id: "$group", totalAge: { $sum: "$age" } } }] );
//不分组
db.user.aggregate([ { $group: { _id: null, totalAge: { $sum: "$age" } } }] );
```
- **计算平均值**：`$avg`
```
//按照 group 分组
db.user.aggregate([ { $group: { _id: "$group", totalAge: { $sum: "$age" } } }] );
//不分组
db.user.aggregate([ { $group: { _id: null, totalAge: { $sum: "$age" } } }] );
```
- **获取最小值**：`$min`
```
//按照 group 分组
db.user.aggregate([ { $group: { _id: "$group", minAge: { $min: "$age" } } }] );
//不分组
db.user.aggregate([ { $group: { _id: null, minAge: { $min: "$age" } } }] );
```
- **获取最大值**：`$max`
```
db.user.aggregate([ { $group: { _id: "$group", maxAge: { $max: "$age" } } }] );
db.user.aggregate([ { $group: { _id: null, maxAge: { $max: "$age" } } }] );
```
### 更新数据

- **更新满足条件的所有文档**：
  ```javascript
  db.users.updateMany({ name: "34" }, { $set: { age: 30 } })
  ```
- **更新满足条件的第一个文档**：
  ```javascript
  db.users.updateOne({ name: "45" }, { $set: { age: 30 } })
  ```
- **替换满足条件的第一个文档**：
  ```javascript
  db.users.replaceOne({ name: "12" }, { name: "34", age: 30 })
  ```

### 删除数据

- **删除满足条件的所有文档**：
  ```javascript
  db.users.deleteMany({ group: "4" })
  ```
- **删除满足条件的第一个文档**：
  ```javascript
  db.users.deleteOne({ name: "45" })
  ```

### 过滤条件

- **不等于**：`$ne`
```
db.user.find({ age: { $ne: 25 } });
```
- **等于**：`$eq`
```
db.user.find({ age: { $eq: 25 } });
```
- **大于 / 大于等于**：`$gt` / `$gte`
```
db.user.find({ age: { $gt: 25 } });
```
- **小于 / 小于等于**：`$lt` / `$lte`
```
db.user.find({ age: { $lt: 25 } });
```
- **在指定值列表中**：`$in`
```
db.user.find({ age: { $in: [25, 30, 35] } });
```
- **不在指定值列表中**：`$nin`
```
db.user.find({ age: { $nin: [25, 30, 35] } });
```
- **并且**：`$and`
```
db.user.find({ $and: [{ age: { $gt: 20 } }, { age: { $lt: 30 } }] });
```
- **或者**：`$or`
```
db.user.find({ $or: [{ age: { $gt: 20 } }, { age: { $lt: 30 } }] });
```
- **取反**：`$not`
```
db.user.find({ age: { $not: { $gt: 25 } } });
```
- **字段是否存在**：`$exists`
```
db.user.find({ age: { $exists: true } });
```
- **字段间比较**：`$expr`
```
db.user.find({ $expr: { $gt: ["$name", "$age"] } });
```

### 高级查询操作

- **更新文档的特定字段**：`$set`
- **递增或递减字段值**：`$inc`
- **重命名字段**：`$rename`
- **删除字段**：`$unset`
- **将值加入数组（允许重复）**：`$push`
- **从数组中移除值**：`$pull`
- **将值加入数组（不允许重复）**：`$addToSet`
- **排序**：`sort`
- **限定返回文档数量**：`limit`
- **跳过文档数量**：`skip`
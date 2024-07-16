title: mongodb基础知识
author: Laiyong Wang
date: 2023-03-12 14:43:06
tags:
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
  db.users.insertOne({ name: "老杨" })
  ```
- **插入多个新文档**：
  ```javascript
  db.users.insertMany([{ name: "李四" }, { name: "王五" }])
  ```

### 查询数据

- **先插入一百条数据供查询使用**：
  ```javascript
  for (var i=1;i<=100;i++) db.user.insertOne({name:i,age:12})
  ```


- **查询所有文档**：
  ```javascript
  db.users.find()
  ```
- **根据条件查询文档**：
  ```javascript
  db.users.find({ name: "老杨" })
  ```
- **查询并只返回指定字段**：
  ```javascript
  db.users.find({ name: "老杨" }, { name: 1, email: 1 })
  ```
- **查询满足条件的第一条记录**：
  ```javascript
  db.users.findOne({ level: 1 })
  ```
- **返回满足条件的记录数量**：
  ```javascript
  db.users.countDocuments({ name: "老杨" })
  ```

### 聚合操作

聚合用于处理数据并返回计算结果，例如求和、平均值等。

- **计算总和**：`$sum`
- **计算平均值**：`$avg`
- **获取最小值**：`$min`
- **获取最大值**：`$max`
- **将值加入数组**：`$push`
- **获取第一个文档**：`$first`
- **获取最后一个文档**：`$last`

### 更新数据

- **更新满足条件的所有文档**：
  ```javascript
  db.users.updateMany({ name: "老杨" }, { $set: { age: 30 } })
  ```
- **更新满足条件的第一个文档**：
  ```javascript
  db.users.updateOne({ name: "老杨" }, { $set: { age: 30 } })
  ```
- **替换满足条件的第一个文档**：
  ```javascript
  db.users.replaceOne({ name: "老杨" }, { name: "老杨", age: 30 })
  ```

### 删除数据

- **删除满足条件的所有文档**：
  ```javascript
  db.users.deleteMany({ name: "老杨" })
  ```
- **删除满足条件的第一个文档**：
  ```javascript
  db.users.deleteOne({ name: "老杨" })
  ```

### 过滤条件

- **不等于**：`$ne`
- **等于**：`$eq`
- **大于 / 大于等于**：`$gt` / `$gte`
- **小于 / 小于等于**：`$lt` / `$lte`
- **在指定值列表中**：`$in`
- **不在指定值列表中**：`$nin`
- **并且**：`$and`
- **或者**：`$or`
- **取反**：`$not`
- **字段是否存在**：`$exists`
- **字段间比较**：`$expr`

### 高级查询操作

- **保存文档（替换或插入）**：
  ```javascript
  db.users.save({ _id: ObjectId("..."), name: "老杨", age: 30 })
  ```
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

### 示例代码

```javascript
// 插入文档
db.users.insertOne({ name: "老杨", age: 28 })

// 查询文档
db.users.find({ age: { $gt: 20 } })

// 更新文档
db.users.updateOne({ name: "老杨" }, { $set: { age: 29 } })

// 删除文档
db.users.deleteOne({ name: "老杨" })
```
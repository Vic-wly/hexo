title: mongoDB的索引类型
author: Laiyong Wang
tags: []
categories: []
date: 2024-07-15 18:28:00
---
### 1. 单字段索引（Single Field Index）

单字段索引用于一个字段的查询。

示例：
```javascript
db.collection.createIndex({ age: 1 });
```
这将在 `age` 字段上创建一个升序索引。如果要创建降序索引，可以将 `1` 替换为 `-1`。

### 2. 复合索引（Compound Index）

复合索引用于多个字段的查询。

示例：
```javascript
db.collection.createIndex({ age: 1, name: -1 });
```
这将在 `age` 字段和 `name` 字段上创建一个复合索引，`age` 字段按升序排列，`name` 字段按降序排列。

### 3. 多键索引（Multikey Index）

多键索引用于数组字段的查询。MongoDB 会为数组中的每个元素创建一个索引条目。

示例：
```javascript
db.collection.createIndex({ tags: 1 });
```
假设 `tags` 字段是一个数组，如 `["mongodb", "database"]`，MongoDB 会为每个元素创建一个索引条目。

### 4. 文本索引（Text Index）

文本索引用于文本字段的全文搜索。

示例：
```javascript
db.collection.createIndex({ content: "text" });
```
这将在 `content` 字段上创建一个文本索引，允许进行全文搜索。

### 5. 地理空间索引（Geospatial Index）

地理空间索引用于存储地理空间坐标的查询。

示例：
```javascript
db.collection.createIndex({ location: "2dsphere" });
```
假设 `location` 字段存储地理空间数据，MongoDB 会为其创建一个 2dsphere 索引，支持地理空间查询。

### 6. 哈希索引（Hashed Index）

哈希索引用于等值查询。

示例：
```javascript
db.collection.createIndex({ user_id: "hashed" });
```
这将在 `user_id` 字段上创建一个哈希索引，适用于等值查询。

### 7. 唯一索引（Unique Index）

唯一索引确保索引字段的值在集合中是唯一的。

示例：
```javascript
db.collection.createIndex({ email: 1 }, { unique: true });
```
这将在 `email` 字段上创建一个唯一索引，确保每个文档的 `email` 值是唯一的。

### 8. 部分索引（Partial Index）

部分索引用于仅索引符合特定条件的文档。

示例：
```javascript
db.collection.createIndex(
  { age: 1 },
  { partialFilterExpression: { status: "active" } }
);
```
这将在 `age` 字段上创建一个部分索引，仅索引 `status` 为 `"active"` 的文档。

### 9. 稀疏索引（Sparse Index）

稀疏索引仅索引包含索引字段的文档。

示例：
```javascript
db.collection.createIndex({ age: 1 }, { sparse: true });
```
这将在 `age` 字段上创建一个稀疏索引，仅索引包含 `age` 字段的文档。

### 索引管理

#### 查看索引
要查看集合中的所有索引，可以使用 `getIndexes` 方法：

```javascript
db.collection.getIndexes();
```

#### 删除索引
要删除集合中的索引，可以使用 `dropIndex` 方法：

```javascript
db.collection.dropIndex("index_name");
```

要删除集合中的所有索引，可以使用 `dropIndexes` 方法：

```javascript
db.collection.dropIndexes();
```
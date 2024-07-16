title: mongo 使用基础
author: Laiyong Wang
date: 2022-08-24 14:02:58
tags:
---
多用于约束较少 || 频繁修改结构的实体

实际使用时，尽量只做插入，别做更新
查询：根据条件虚查找出最后一条记录即可
```
db.wanglaiyong.findOne({$query:{'name':'王来勇'},$orderby:{$natural:-1}})
```
优点：根据条件查询，返回所有结果，可以清晰的知道那个时间点对该数据进行了什么操作，而且自带备份（基于这点，所以做日志非常完美）

常用知识：
Bson 跟 json 类似但又不一致
```
//插入
db.wanglaiyong.insert(Bson)
db.wanglaiyong.save(Bson)//可指定 _id ,不指定的话，跟 insert 一样
//删除
//第二个参数，选填{justOne: <boolean>,writeConcern: <Bson>,collation:<Bson>}
db.wanglaiyong.remove({'name':'王来勇'});删除所有匹配的
db.wanglaiyong.deleteOne({'name':'王来勇'});删除第一个匹配的
db.wanglaiyong.deleteMany({'name':'王来勇'});删除所有匹配的
//更新
db.wanglaiyong.update(
   <query>,//匹配条件
   <update>,//更新内容的json
   {
     upsert: <boolean>,//未匹配是否插入新数据，默认false
     multi: <Bson>,
     writeConcern: <Bson>
   }
)
//查询
db.collection.find(query, projection)；
db.collection.find({'name':'王来勇'})；//第二个参数默认省略
db.collection.findOne({'name':'王来勇'})；
//创建索引
db.wanglaiyong.createIndex({"name":1,"school":-1})；//1 为指定按升序创建索引， -1 为指定序按降序来创建索引，多个key则为组合索引
```
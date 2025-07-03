title: kratos打印sql日志
author: Laiyong Wang
tags: []
categories: []
date: 2024-10-15 12:11:00
---
开启debug即可

代码里可以这样写
```
	DB := repo.data.Db.Debug()
	querySql := DB.DistributeCustom.Query().Where(where...)
	querySql.Offset(int((page - 1) * pageSize)).Limit(int(pageSize))
    
```
不要这样写,看着开启了debug，实则返回的未赋值，无效

```
	repo.data.Db.Debug()
	querySql := repo.data.Db.DistributeCustom.Query().Where(where...)
	querySql.Offset(int((page - 1) * pageSize)).Limit(int(pageSize))

```
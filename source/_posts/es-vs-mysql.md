title: ES的的分页查询和mysql的分页查询
author: Laiyong Wang
date: 2025-06-20 09:00:07
tags:
---
### 常规分页
- es
通过 from 指定起始位置，size 指定每页数量（如 from=10000, size=10）
深度分页时（如 from > 10000），每个分片需查询 from + size 条数据，协调节点汇总排序后返回指定范围，导致内存与 CPU 暴增

- mysql
1、 LIMIT + OFFSET
OFFSET 值较大时需扫描并丢弃大量数据，性能骤降（如 OFFSET 100000 需扫描前10万行）
2、逻辑分页（应用层分页）
查询全量数据到应用层，通过代码切片分页，浪费内存

### 优化方案
- es 
1、滚动查询
原理：首次查询生成快照并返回 scroll_id，后续基于此 ID 分批获取数据（如每次 1000 条）。
2、Search After 接力查询
原理：基于上一页最后一条数据的排序值（如时间戳 + ID）定位下一页起始位置。

- mysql
1、游标分页
原理：记录上一页最后一条数据的标识（如ID、时间戳），下次查询直接定位

2、覆盖索引（Covering Index）
原理：查询字段全部包含在索引中，避免回表查询。
不适合字段多的查询情况

3、延迟关联（Deferred Join）
原理：先通过子查询快速定位主键，再关联原表获取完整数据
```
SELECT * FROM orders 
INNER JOIN (
    SELECT id FROM orders 
    ORDER BY create_time DESC 
    LIMIT 100000, 10
) AS tmp USING(id);
```
4、预计算分页（空间换时间）
原理：先通过子查询快速定位主键，再关联原表获取完整数据
类似于中间件的思想：
1、创建辅助表存储每页的起止ID
2、查询时直接定位范围
















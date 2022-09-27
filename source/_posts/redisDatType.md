title: redis 数据类型及应用场景
author: Laiyong Wang
date: 2022-08-23 22:31:35
tags:
---
##### 字符串（string）
- 释义
String类型是Redis能与键关联的最简单的数据类型，它是Memcached当中仅有的数据类型，因此可以很快地被初学者学习。
Redis的Key名称也是一个字符串，当我们使用字符串类型作为其对应的值时，我们可以根据Key名称来查找映射对应的值。
Redis字符串是二进制安全的，这意味着一个Redis字符串能包含任意类型的数据，例如： 一张JPEG格式的图片或者一个序列化的Ruby对象。
一个字符串类型的值最多能存储512MB的内容。
- 应用场景
高速缓存HTML片段或者页面（常用）
高速缓存关系型数据库查询的数据结果（常用）
高速缓存会话控制数据（常用）
分布式锁，防止重复提交（常用）
存储设置固定格式的字符串序列（例如：时间序列）
统计网站访问者数量
每天注册用户数
限制API在某一时段的访问次数（常用）
用户签到（对字符串的位处理：bitmap）（常用）
统计活跃用户（对字符串的位处理：bitmap）（常用）
用户在线状态（对字符串的位处理：bitmap）（常用）
##### 哈希（hash）
- 释义
Redis 中的 Hash 类型可以看成是具有 String key 和 String value 的 map 容器。
该类型<b>非常适合存储对象信息</b>。
每一个 Hash 可以存储 4294967295 个键值对
- 应用场景
会员信息
用户购物车数据

##### 列表（list）
- 释义
Redis列表是简单的字符串列表，按照插入顺序排序。
你可以添加一个元素到列表的头部（左边）或者尾部（右边）。
一个列表最多可以包含 232 - 1 个元素 (4294967295, 每个列表超过40亿个元素)
- 应用场景
粉丝列表
时间轴（Timeline）
最新文章
消息队列

##### 集合（set）
- 释义
Redis 的 Set 是 String 类型的无序集合。
集合成员是唯一的，这就意味着集合中不能出现重复的数据。
集合中最大的成员数为 232 - 1 (4294967295, 每个集合可存储40多亿个成员)
- 应用场景
共同好友
好友推荐
##### 有序集合类型（zset）
- 释义
Redis 有序集合和集合一样，也是string类型元素的集合，且不允许重复的成员。
不同的是每个元素都会关联一个double类型的分数。
Redis正是通过分数来为集合中的成员进行从小到大的排序。
有序集合的成员是唯一的,但分数(score)却可以重复。
集合中最大的成员数为 232 - 1 (4294967295, 每个集合可存储40多亿个成员)
- 应用场景
排行榜
##### 订阅与发布
Redis 通过 PUBLISH 、 SUBSCRIBE 等命令实现了订阅与发布模式
这个功能提供两种信息机制， 分别是订阅/发布到频道和订阅/发布到模式

实现信息到交换机交换机再分发到队列的命令

##### HyperLogLog
用做基数统计，意思是不重复元素的总个数

##### Geospatial(不会读，正常就说 GEO)
一个 key ，内有多个成员，保存经纬度
redis基于该类型，提供了经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作
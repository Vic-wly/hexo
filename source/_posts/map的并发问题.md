title: map的并发问题
author: Laiyong Wang
tags:
  - go
categories: []
date: 2025-03-29 07:10:00
---
### 问题
map在扩容时有并发问题
(map扩容时)A协程在桶中读数据时，B协程驱逐了这个桶,A协程会找不到数据，或者读到错误数据

### 解决方案
给map加锁
使用sync.map（使用了两个map，分离了扩容问题，查改使用read，新增使用dirty）

### sync.map的结构

有四个mu、read、dirty、misses

正常读写，从read读写即可，扩容则从dirty走，read的amended=true，misses+1，当misses的值=len(dirty) 直接替换read

![upload successful](/images/pasted-55.png)

删除不是直接删除key，而是将指针设为nil，
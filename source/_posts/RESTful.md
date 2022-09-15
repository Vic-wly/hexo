title: RESTful
author: Laiyong Wang
date: 2022-08-20 16:01:40
tags:
---
#### REST 释义
一种软件设计风格，全称为 Representational State Transfer，直译为表现层状态转移，或许可以解释为用 URL 定位资源，用 HTTP 动词描述操作

白话文就是，相同的 url，不同方式进行请求，GET 用来获取资源，POST 用来新建资源，PUT 用来更新资源，DELETE 用来删除资源

#### RESTful api
正常来说，接口无非就是增删改查，符合 REST 标准即可认为是 RESTful api
嗯，不管对不对，反正我是这样理解的

#### RESTful 架构
- 每一个 URI 代表一种资源
- 客户端和服务器之间，传递这种资源的某种表现层
- 客户端通过四个 HTTP 动词，对服务器端资源进行操作，实现 "表现层状态转化"

#### PS
不要一个接口干很多事，一个接口就是一个资源，每个接口尽量颗粒度最小，就算是批量的，也尽量循环处理，考虑并发问题和死锁问题


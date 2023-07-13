title: restFul 的几种类型
author: Laiyong Wang
date: 2023-02-23 11:25:27
tags:
---
### GET
安全、幂等；
用于获取资源；

### POST
非安全、非幂等；
用于创建子资源；

### PUT
非安全、幂等；
用于创建、更新资源；

### DELETE
非安全、幂等；
删除资源；

### PATCH
非安全、幂等；
用于创建、更新资源，于PUT类似，区别在于PATCH代表部分更新；
后来提出的接口方法，使用时可能去要验证客户端和服务端是否支持；

### PUT 与 PATCH 的区别
PUT的定义：Replace(Create or Update)，创建或者更新，就算是更新也是将所有的字段全部更新一边
PATCH 修改已知数据，而且是修改部分信息


title: kratos学习记录
author: Laiyong Wang
tags:
  - go
  - kratos
categories: []
date: 2024-10-09 17:11:00
---
文档： https://go-kratos.dev/docs/getting-started/start
视频： https://www.bilibili.com/video/BV1t3411h7uA

## 从0到1写一个接口：从表里查数据并返回
#### ent 生成对于的数据库文件
make ent tables=distribute_data_from_day
#### 编写proto文件，然后
make api 生成对于的 pb.go 文件
#### 使用chatgpt生成对应的UC和repo文件
这个是为了开发统一模版进行开发

#### biz和service文件编写
service文件主要是为了实现 proto 文件里写的方法

biz文件主要是实现servicer里调用的方法，功能是对应的业务逻辑，

#### biz文件里的方法住查询更新和组装

调用uc（usecase）文件里面的方法 
uc（usecase）文件调用repo文件里的方法

uc文件和repo文件，是写好第一个，之后让chatgpt按照第一个再依据表结构自动创建即可

主要有四个方法 GetSingle 、 GetList 、 Create 、 Update

#### 将对应的 service 文件写的服务加到 service.go 文件内，biz 文件写的加到biz.go文件内

#### make wire 生成对应的文件

#### 继续在biz文件里完善代码逻辑
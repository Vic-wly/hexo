title: mysql 允许用户远程登录
author: Laiyong Wang
tags:
  - mysql
categories: []
date: 2022-02-08 14:20:00
---
mysql 允许某个用户远程登录

eg:root用户
```
mysql>use mysql;

mysql>update user set host='%' where user='root' AND host='localhost';

mysql>FLUSH PRIVILEGES;
```
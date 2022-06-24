title: mysql 允许用户远程登录
author: Laiyong Wang
date: 2022-02-08 14:20:18
tags:
---
mysql 允许某个用户远程登录

eg:root用户
```
mysql>use mysql;

mysql>update user set host='%' where user='root' AND host='localhost';

mysql>FLUSH PRIVILEGES;
```
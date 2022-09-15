title: mac 环境更新 php.ini 并重启服务
author: Laiyong Wang
date: 2022-09-12 23:54:39
tags:
---
#### 查看基础信息
```
php --ini
```

![upload successful](/images/pasted-17.png)
#### 编辑 php.ini 信息
```
/usr/local/etc/php/8.0/php.ini
```
#### 重启 php 服务
```
brew services restart php@8.0
```
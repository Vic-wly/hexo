title: mac 环境下 composer 更新、降级、更换镜像
author: Laiyong Wang
date: 2022-09-13 08:25:03
tags:
---
#### PS：本文档因安装yii2失败而产出
#### 更新至最新版本
```
composer self-update
```
#### 回退版本
因为 composer 安装 yii2 总是超时失败，搜索问题是需要安装
```
composer global require "fxp/composer-asset-plugin:~1.4.5"
```
但是我的 composer 在2.*，安装不了，所以需要回退版本
```
//回退版本（版本默认为1.10.26）
composer self-update --1
```
#### 更换镜像
```
//网址可选
composer config -g repo.packagist composer https://packagist.phpcomposer.com
```
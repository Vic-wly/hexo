title: mac 环境 git 版本升级
author: Laiyong Wang
date: 2022-09-12 21:39:19
tags:
---
##### 升级
```
brew install git
//或
brew upgrade git
```
此时只是最新版本的 git 安装了，需要切换
##### 切换最新版本
```
brew link --overwrite git
```

title: mac 命令行设置代理进行科学上网
author: Laiyong Wang
tags:
  - mac
  - vpn
categories: []
date: 2022-10-09 16:58:00
---
#### mac 电脑科学上网时，使用命令行，会发现命令行内的操作并没有科学上网，需要配置下

```
//查看电脑的公网ip
curl cip.cc
//配置
git config --global --unset http.proxy
git config --global --unset https.proxy
export http_proxy="http://127.0.0.1:1080"
export https_proxy="http://127.0.0.1:1080"
source ~/.zshrc
//我的端口是 1080，需要看自己科学上网设置监听的端口是多少

//开启
proxy
//关闭
unproxy
```
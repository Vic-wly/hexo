title: ssh 免密登录
author: Laiyong Wang
date: 2022-01-05 10:17:14
tags:
---
### 客户端生成公私钥
本地客户端生成公私钥：（一路回车默认即可）
``` bash
ssh-keygen
```

上面这个命令会在用户目录.ssh文件夹下创建公私钥

``` bash
cd ~/.ssh && ls -al
```

id_rsa （私钥） 

id_rsa.pub (公钥)

### 上传公钥到服务器
这里测试用的服务器地址为：42.192.144.242
用户为：root
``` bash
ssh-copy-id -i ~/.ssh/id_rsa.pub root@42.192.144.242
```

上面这条命令是写到服务器上的ssh目录下去了
``` bash
cd ~/.ssh && cat authorized_keys
```
可以看到客户端写入到服务器的 id_rsa.pub （公钥）内容。

### 测试免密登录
客户端通过ssh连接远程服务器，就可以免密登录了。
``` bash
ssh root@42.192.144.242
```


原文链接：https://blog.csdn.net/jeikerxiao/article/details/84105529
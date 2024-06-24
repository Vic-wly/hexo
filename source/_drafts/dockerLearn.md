title: docker 基础（边学习边整理下常用的知识点）
author: Laiyong Wang
date: 2022-10-14 13:12:20
tags:
---
#### 工具 mac 、 brew
#### 安装
```
brew install --cask --appdir=/Applications docker
//去启动台打开软件
//命令行输入
docker --version
//如下图所示即成功
```

![upload successful](/images/pasted-21.png)

#### 镜像的基础操作
```
//查看镜像
docker images

//下载镜像
docker pull centos

//删除镜像
docker rmi -f <镜像 ID>

//导入镜像
cat centos.tar | docker import - cnetos:v1

//导出容器
docker export <容器 ID>/<容器 name> > centos.tar

```

#### 容器的基础操作
```

//查看运行的容器
docker ps

//查看所有的容器，无论正在运行还是停止
docker ps -a

//删除容器
docker rm -f <容器 ID>/<容器 name>

//删除所有处于终止状态的容器
docker container prune

//停止一个容器
docker stop <容器 ID>/<容器 name>

//重启一个停止的容器
docker restart <容器 ID>/<容器 name>

//使用 centos 镜像 的 /bin/echo 命令执行 "Hello world"
docker run centos /bin/echo "Hello world"

//进入 centos 的容器内，进去后就相当于是 centos 系统，-i: 交互式操作，-t: 终端。
docker run  -it centos /bin/bash

//后台运行docker服务
docker run -d --name centos-test centos /bin/bash

//进入容器
docker exec -it <容器 ID>/<容器 name> /bin/bash
```
#### docker 应用
```

```


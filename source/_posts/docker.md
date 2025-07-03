title: 学习 docker，开个文章记录下遇到的问题和知识点
author: Laiyong Wang
tags:
  - docker
categories: []
date: 2022-05-29 12:25:00
---
###### 我学习时与 chatGPT 的问答，学习中你遇到的问题可能我也遇到过，供参考：https://chatgpt.com/share/a2b2ea6f-b73f-4cf2-b970-026bf566c2db
#### 工具以及学习途径
MAC、英特尔处理器
官网下载软件：https://desktop.docker.com/mac/main/amd64/Docker.dmg
菜鸟教程：https://www.runoob.com/docker/docker-tutorial.html

#### 问题
  - 终端报错：zsh: command not found: docker
  原因是没有配置环境变量
  配置环境变量：
  ```
//DOCKER_PATH 路径填自己的
export DOCKER_PATH="/Applications/Docker.app/Contents/Resources/bin"
export PATH=".\$PATH:$DOCKER_PATH"
  ```
  运行命令使环境变量立即生效：
  ```
source /etc/profile
  ```
  - 容器无法关闭
  执行下方的命令，可以查看 pid ，然后 kill -9 pid，强制结束进程
  ```
  echo $(docker inspect --format '{{ .State.Pid }}' <容器ID>)
  ```
  若是还是无法关闭或者无法找到进程，重启docker.........
#### 知识点
  - docker run :在容器内运行一个应用程序
  ```
  docker run centos /bin/echo "Hello world"
  ```
  参数解析：
  1、docker: Docker 的二进制执行文件。就是配置的环境变量目录下的执行文件
  2、run: 与前面的 docker 组合来运行一个容器。
  3、centos 指定要运行的镜像，Docker 首先从本地主机上查找镜像是否存在，如果不存在，Docker 就会从镜像仓库 Docker Hub 下载公共镜像。
  4、/bin/echo "Hello world": 在启动的容器里执行的命令
  
![upload successful](/images/pasted-45.png)
- 直接拉取镜像
```
docker pull centos
```
- 启动容器并默认不进入容器
```
docker run -itd --name centos-test centos /bin/bash
```
  参数说明：
  1、-i: 交互式操作。
  2、-t: 终端。
  3、centos: centos 镜像。
  4、/bin/bash：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 /bin/bash。
  5、-d 默认不会进入容器，想要进入容器需要使用指令 docker exec
  6、--name 给开启的容器取个名字
- 常用命令
  docker images 列出本地主机上的镜像
  docker inspect <容器 ID> 查看容器配置信息
  docker ps -a 显示所有的容器，包括未运行的
  docker exec 进入容器，且命令退出容器终端，但不会导致容器停止
  docker start <容器 ID>  重启容器
  docker stop <容器 ID>  停止容器
  docker export centos-test > centos.tar  导出本地centos-test容器
  cat centos.tar | docker import - wanglaiyong 从容器快照文件中再导入为镜像，以下实例将快照文件 centos.tar 导入到镜像 wanglaiyong
  
![upload successful](/images/pasted-46.png)
#### 安装 nginx
```
docker run --name nginx-test -p 8080:80 -d nginx
```
参数解释：
  --name nginx-test：容器名称。
  -p 8080:80： 端口进行映射，将本地 8080 端口映射到容器内部的 80 端口。
  -d nginx： 设置容器在在后台一直运行。
  
![upload successful](/images/pasted-47.png)
#### 安装 mysql
- 拉取官方的最新版本的镜像
```
docker pull mysql:latest
```
- 运行 mysql 容器
```
$ docker run -itd --name my-mysql -p 3309:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
```
参数说明:
1、 -p 3309:3306 ：映射容器服务的 3306 端口到宿主机的 3309 端口，外部主机可以直接通过 宿主机ip:3309 访问到 MySQL 的服务。
2、MYSQL_ROOT_PASSWORD=123456：设置 MySQL 服务 root 用户的密码。
- 进入容器并连接mysql

![upload successful](/images/pasted-49.png)
#### 安装 php + nginx
- 拉取镜像 我项目的 php 版本是 7.3
```
docker pull php:7.3-fpm
```
启动php

```
docker run --name  myphp-fpm -v ~/nginx/www:/www  -d php:7.3-fpm
// --name myphp-fpm : 将容器命名为 myphp-fpm。
// -v ~/nginx/www:/www : 将主机中项目的目录 www 挂载到容器的 /www
```

新增配置文件
/Users/wanglaiyong/nginx/conf/conf.d/default.conf
```
server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm index.php;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location ~ \.php$ {
        fastcgi_pass   php:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /www/$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```
配置说明：
1、php:9000: 表示 php-fpm 服务的 URL，下面我们会具体说明。
2、/www/: 是 myphp-fpm 中 php 文件的存储路径，映射到本地的 ~/nginx/www 目录。

- 启动 nginx
```
docker run --name my-nginx -p 8083:80 -d \
    -v ~/nginx/www:/usr/share/nginx/html:ro \
    -v ~/nginx/conf/conf.d:/etc/nginx/conf.d:ro \
    --link myphp-fpm:php \
    nginx
```
参数说明：
1、 -p 8083:80: 端口映射，把 nginx 中的 80 映射到本地的 8083 端口。因为本地80端口已经被我的apache使用
2、 ~/nginx/www: 是本地 html 文件的存储目录，/usr/share/nginx/html 是容器内 html 文件的存储目录。
3、 ~/nginx/conf/conf.d: 是本地 nginx 配置文件的存储目录，/etc/nginx/conf.d 是容器内 nginx 配置文件的存储目录。
4、 --link myphp-fpm:php: 把 myphp-fpm 的网络并入 nginx，并通过修改 nginx 的 /etc/hosts，把域名 php 映射成 127.0.0.1，让 nginx 通过 php:9000 访问 php-fpm。

- 在 ~/nginx/www 目录下创建 index.php
```
<?php
echo phpinfo();
?>
```
- 浏览器打开 http://127.0.0.1:8083

![upload successful](/images/pasted-48.png)
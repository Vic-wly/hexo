title: 将自己本地容器打包，一键部署环境（docker）
author: Laiyong Wang
tags:
  - docker
categories: []
date: 2024-05-30 00:21:00
---
###### 我学习时与 chatGPT 的问答，学习中你遇到的问题可能我也遇到过，供参考：https://chatgpt.com/share/a2b2ea6f-b73f-4cf2-b970-026bf566c2db
#### 安装 docker-compose
因命令行curl下载太慢，我直接去github下载文件
- 下载文件

下载地址：https://github.com/docker/compose/releases
- 将文件移动到 /usr/local/bin/ 目录并赋予执行权限

```
# 假设文件下载到 ~/Downloads 目录 ，文件名字请根据自己下载文件进行更改
sudo mv ~/Downloads/docker-compose-darwin-x86_64 /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
- 确认 Docker Compose 是否成功安装

```
docker-compose --version
```

![upload successful](/images/pasted-50.png)

#### 创建并编写配置文件
- 注意点
我会延续上一个文章：http://laiyong.wang/2024/05/29/docker
里面的mysql、nginx、php的配置和名称我都不会进行改变，但是因为命令会新创建，故需先全部暂停并删除
- 创建 docker-compose.yml
```
version: "2"

services:
  my-mysql:
    container_name: "my-mysql"
    ports:
      - "3309:3306"
    volumes:
      - /etc/localtime:/etc/localtime:ro # 设置容器和宿主机的时间同步，使用 'ro' 作为只读
    environment: # 自定义环境变量
      MYSQL_ROOT_PASSWORD: 123456
    image: mysql:latest

  myphp-fpm:
    container_name: "myphp-fpm"
    restart: always
    ports:
      - "9000:9000"
    volumes: //目录挂载
      - ~/nginx/www:/www
      - /etc/localtime:/etc/localtime:ro
    links:
      - "my-mysql"
    image: php:7.3-fpm

  my-nginx:
    container_name: "my-nginx"
    restart: always
    ports:
      - "8083:80"
    links:
      - "myphp-fpm:php" //这个点需要注意，我一开始配置错了，因为我nginx配置文件 default.conf 里是  fastcgi_pass   php:9000;故定义了一个名为 php 的链接指向 myphp-fpm 服务，
    volumes: //目录挂载
      - ~/nginx/www:/usr/share/nginx/html 
      - ~/nginx/conf/conf.d:/etc/nginx/conf.d
      - /etc/localtime:/etc/localtime:ro
    image: nginx:latest
```
#### 启动服务
执行下方的命令
```
docker-compose up -d
```
-d 是以后台模式运行，这意味着服务将在后台运行，而不会阻塞当前终端。
命令执行后将会做三件事：
1、根据 docker-compose.yml 文件中定义的服务启动相应的容器。
2、创建并启动服务的容器，并将它们连接到定义的网络中。
3、在后台模式下运行服务容器，这意味着容器将在后台持续运行，而不会占用当前终端。

![upload successful](/images/pasted-51.png)
#### 访问页面

![upload successful](/images/pasted-52.png)
#### 停止服务
执行下方的命令
```
docker-compose down
```
这个命令会停止并删除 docker-compose.yml 文件中定义的所有服务

![upload successful](/images/pasted-53.png)

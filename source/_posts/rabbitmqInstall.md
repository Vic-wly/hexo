title: 服务器安装 rabbitMQ 及使用方法，内含我服务器地址账号密码
author: Laiyong Wang
date: 2022-09-04 22:34:57
tags:
---
#### PS：我的服务器系统及版本：centos8
- 参考（rabbitmq官网）：https://www.rabbitmq.com/install-rpm.html
- 安装方式：yum
- 不用 EPEL 的 GPG-KEY 的话，可将 gpgcheck=1 改成 gpgcheck=0，免得安装的时候获取 GPG 密钥失败
##### 安装
- 新增 yum 源文件
```
# In /etc/yum.repos.d/rabbitmq.repo

##
## Zero dependency Erlang RPM
##

[rabbitmq_erlang]
name=rabbitmq_erlang
baseurl=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/rpm/el/8/$basearch
repo_gpgcheck=1
enabled=1
# Cloudsmith's repository key and RabbitMQ package signing key
gpgkey=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/gpg.E495BB49CC4BBE5B.key
       https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[rabbitmq_erlang-noarch]
name=rabbitmq_erlang-noarch
baseurl=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/rpm/el/8/noarch
repo_gpgcheck=1
enabled=1
# Cloudsmith's repository key and RabbitMQ package signing key
gpgkey=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/gpg.E495BB49CC4BBE5B.key
       https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[rabbitmq_erlang-source]
name=rabbitmq_erlang-source
baseurl=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/rpm/el/8/SRPMS
repo_gpgcheck=1
enabled=1
gpgkey=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/gpg.E495BB49CC4BBE5B.key
gpgcheck=0
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md


##
## RabbitMQ Server
##

[rabbitmq_server]
name=rabbitmq_server
baseurl=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/rpm/el/8/$basearch
repo_gpgcheck=1
enabled=1
# Cloudsmith's repository key and RabbitMQ package signing key
gpgkey=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/gpg.9F4587F226208342.key
       https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[rabbitmq_server-noarch]
name=rabbitmq_server-noarch
baseurl=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/rpm/el/8/noarch
repo_gpgcheck=1
enabled=1
# Cloudsmith's repository key and RabbitMQ package signing key
gpgkey=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/gpg.9F4587F226208342.key
       https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[rabbitmq_server-source]
name=rabbitmq_server-source
baseurl=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/rpm/el/8/SRPMS
repo_gpgcheck=1
enabled=1
gpgkey=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/gpg.9F4587F226208342.key
gpgcheck=0
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md
```
- 执行 yum 命令（按顺序执行）
```
yum -q makecache -y --disablerepo='*' --enablerepo='rabbitmq_erlang-noarch' --enablerepo='rabbitmq_server-noarch'

yum install socat logrotate -y

yum install --repo rabbitmq_erlang --repo rabbitmq_server-noarch erlang rabbitmq-server

```
- 启动服务
```
//系统启动时默认启动守护进程，以管理员身份运行
chkconfig rabbitmq-server on
//开启服务
/sbin/service rabbitmq-server start 
//查看状态
/sbin/service rabbitmq-server status 
//停止服务
/sbin/service rabbitmq-server stop
```
- 创建个用户并给予权限
```
//创建用户，回车后需要输入密码
rabbitmqctl add_user "wanglaiyong"
//给创建的用户加权限
rabbitmqctl set_permissions  "wanglaiyong"  ". *"  ".*"  ".*"
//服务器防火墙开启端口，然后重启下服务，即可客户端进行连接，不重启不知道行不行，没试，反正我直接重启了
```
##### 使用
- 参考（rabbitmq官网）
https://www.rabbitmq.com/getstarted.html
- 写个最简单的用于测试

1、composer 安装 php-amqplib
```
{
    "require": {
        "php-amqplib/php-amqplib": ">=3.0"
    }
}
//然后composer install
```

2、生产者 send.php
```
<?php
require_once __DIR__ . '/../vendor/autoload.php';
use PhpAmqpLib\Connection\AMQPStreamConnection;
use PhpAmqpLib\Message\AMQPMessage;

$connection = new AMQPStreamConnection('42.192.144.242', 5672, 'wanglaiyong', 'Friends12w3');
$channel = $connection->channel();

$channel->queue_declare('hello', false, false, false, false);

$msg = new AMQPMessage('Hello World!');
$channel->basic_publish($msg, '', 'hello');

echo " [x] Sent 'Hello World!'\n";

$channel->close();
$connection->close();
```
3、 消费者 receive.php

```
<?php
require_once __DIR__ . '/../vendor/autoload.php';
use PhpAmqpLib\Connection\AMQPStreamConnection;


$connection = new AMQPStreamConnection('42.192.144.242', 5672, 'wanglaiyong', 'Friends12w3');
$channel = $connection->channel();

$channel->queue_declare('hello', false, false, false, false);

echo " [*] Waiting for messages. To exit press CTRL+C\n";

$callback = function ($msg) {
    echo ' [x] Received ', $msg->body, "\n";
};

$channel->basic_consume('hello', '', false, true, false, false, $callback);

while ($channel->is_open()) {
    $channel->wait();
}
```
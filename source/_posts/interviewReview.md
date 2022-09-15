title: 面试总结
author: Laiyong Wang
date: 2022-08-29 16:50:42
tags:
---
#### PS：只记录没有回答上来的问题
##### 第一次面试 
rabbitmq 准备不足，干之
- RabbitMQ 中文文档 : http://rabbitmq.mr-ping.com
- rabbitmq 死信队列详解与使用 : https://blog.csdn.net/zhangcongyi420/article/details/100126666

- RabbitMQ 进阶学习 : https://blog.csdn.net/weixin_40792878/article/details/82720724

- RabbitMQ 之消息确认机制（事务 + Confirm） : https://blog.csdn.net/u013256816/article/details/55515234

- RabbitMQ 之消息持久化 : https://blog.csdn.net/u013256816/article/details/60875666

- RabbitMQ 之镜像队列 : https://blog.csdn.net/u013256816/article/details/71097186

1. rabbitmq 预读
可以理解为RabbitMQ Broker把未确认的消息批量推送到客户端，由客户端先缓存这些消息，然后投递到消费者中
避免一次消息一次投递，浪费 io 资源
参考链接：https://cloud.tencent.com/developer/article/1650067
2. 单位时间内，大量请求干到服务求， nginx 一开始很快，然后就比较慢，为什么
tcp 握手挥手需要时间。而且 nginx.conf 里 worker_processes * worker_connections 的总链接数被用完了也得等待释放

3. rabbitmq 的 connection channel有什么区别。这个题有点头疼，只知道一点，根本不敢说，说了再深问得炸，干脆说不知道，机智 boy
https://www.cnblogs.com/eleven24/p/10326718.html
4. rabbitmq 单条信息最大允许有多大
看 rabbitmq-common/include/rabbit.hrl 文件中的配置 define(MAX_MSG_SIZE, 536870912)
每个版本的默认值都不一样，3.7 2G，3.8 512mb，正常的运行环境中最好不要超过4mb
5. nginx php 对于高并发有哪些配置项
讲真的，看的非常头疼，而且还看不进去
- nginx
https://blog.51cto.com/linux2023/5017785?abTest=51cto
- php-fpm
https://zhuanlan.zhihu.com/p/404917300
nginx
```
worker_processes 8;//工作进程数，根据cpu多少核来决定
worker_connections 100；//单个工作进程可以允许同时建立外部连接的数量
multi_accept on;//如果使用的的系统是Linux，可使用"epoll"
```
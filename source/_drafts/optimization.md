title: 总结下最近的成果（2024/5.6-5.24）
author: Laiyong Wang
date: 2024-05-24 21:43:35
tags:
---
#### 健身 & 减肥
天天泡健身房半天，累的半死
刷脂6.4KG，肌肉轮廓已初步展现，想要六块腹肌，还得再来个6.4KG......

#### 娱乐
三国志战略版开荒完成且不到两周就已锁定霸业
dnf手游正在玩
英雄联盟一年多没玩，最近解馋了......

#### 学习
简单复习下操作系统、网络、redis、mysql、rabbitmq、php等基础知识,并记录与本博客

#### 针对这段时间的学习，结合实际工作，写点个人观点
绝大部分公司的服务，基本都是以业务驱动的，所以程序员及运维的工作就是为了让服务能稳定和高效的运行
1、首先是确定的是服务对象，B端还是C端，B端基本是数据量较小但需要稳定，C端基本是数据量大但要确保数据的最终一致性。这设计到 http 服务器的选择，稳定选 apache ，高效选 nginx。
2、针对B端，系统权限和日志必须要全，最好精确到每个用户的每次更新操作
3、C端用户，通常都是数据量大，可以参考这篇文章：http://laiyong.wang/2024/05/23/Concurrent
谈到这点，不得不说，dnf手游，可能因为数据量大导致掉线以及玩家物品丢失，数据没保障最终一致性，还有怪物名乱码等很多BUG，说明程序员和测试都不合格，圈钱游戏
4、针对第三点里的文章，
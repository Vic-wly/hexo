title: 死锁
author: Laiyong Wang
tags:
  - 锁
categories: []
date: 2024-05-11 15:12:00
---
#### 死锁的概念
  两个线程为了保护两个不同的共享资源而使用了两个互斥锁，那么这两个互斥锁应用不当的时候，可能会造成两个线程都在等待对方释放锁，在没有外力的作用下，这些线程会一直相互等待，就没办法继续运行，这种情况就是发生了死锁
  
#### 死锁产生
  死锁问题的产生是由两个或者以上线程并行执行的时候，争夺资源而互相等待造成的
  死锁只有同时满足以下四个条件才会发生：互斥条件、持有并等待条件、不可剥夺条件、环路等待条件
  解释起来好烦（来个百度百科吧）：https://baike.baidu.com/item/%E6%AD%BB%E9%94%81/2196938
  
#### 如何避免死锁
  要避免死锁问题，就是要破坏其中一个条件即可，最常用的方法就是使用资源有序分配法来破坏环路等待条件
  - 按照顺序或者层次加锁，避免出现循环等待的情况。
  - 设置获取锁的超时时间，超时后放弃获取锁，释放已占有的锁。
  - 使用并发工具类或者 Lock 接口代替 synchronized 关键词，提供更灵活的锁管理。
  - 降低锁的使用粒度，减少同步的代码块，提高资源的利用率。
  正常情况下写代码，使用相同的数据时，都按照尽量按照 ABCD 的顺序来写
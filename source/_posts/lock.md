title: 悲观锁 乐观锁 及 MVCC
author: Laiyong Wang
date: 2022-08-10 00:03:36
tags:
---
- 悲观锁
共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程
- 乐观锁
更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号机制和CAS算法实现

乐观锁适用于多读的应用类型，这样可以提高吞吐量。多写的场景下用悲观锁就比较合适

- MVCC
多版本并发控制，是为了在读取数据时不加锁来提高读取效率和并发性的一种手段
基于undolog、版本链、readview
这篇文章说的非常好
<https://segmentfault.com/a/1190000040957477?utm_source=sf-similar-article>
<https://segmentfault.com/a/1190000037557620>
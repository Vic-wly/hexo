title: 脏读、不可重复读、幻读以及解决办法
author: Laiyong Wang
tags:
  - mysql
  - mvcc
categories: []
date: 2024-05-27 19:34:00
---

脏读：读到其他事务未提交的数据；
不可重复读：前后读取的数据不一致；
幻读：前后读取的记录数量不一致。

上一篇文章写的有快照读实现的 mvcc：http://laiyong.wang/2024/05/27/mysql-readView
其实现的事务的隔离级别：
- 读已提交 可解决 脏读 问题
- 可重复读 可解决 不可重复读 问题
- 可重复读 解决了快照读（普通 select 语句）的幻读问题，但是当前读（select ... for update 等语句）的幻读问题未解决


当前读的定义：
mysql 里除了普通查询是快照读，其他都是当前读，比如 update、insert、delete，这些语句执行前都会查询最新版本的数据，然后再做进一步的操作

mysql如何解决当前读的幻读问题：
通过 next-key lock（记录锁+间隙锁）方式解决了幻读，因为当执行 select ... for update 语句的时候，会加上 next-key lock，如果有其他事务在 next-key lock 锁范围内插入了一条记录，那么这个插入语句就会被阻塞，无法成功插入，所以就很好了避免幻读问题

什么是间隙锁+记录锁？
举个例子： select name from wanglaiyong where id > 520 for update; 那么 id （520，+∞）就被加上锁了，这是个悲观锁，别的事务想插入啥的需要等待

上面说的都是避免幻读的原理，即使我们设置了可重复读的隔离级别，还是会产生幻读
- 快照读的幻读
事务A开启事务查询 id = 520 的数据，显示没有，事务 B 插入了一条 id = 520 的数据并提交，事务A 基于当前读的快照，再次查询，两次查询结果相同，都是没有，但是插入时会报错，因为数据库的约束问题，这也是一种幻读
- 当前读的幻读
跟快照读的幻读很类似
事务A开启事务查询 id = 520 的数据，显示没有，事务 B 插入了一条 id = 520 的数据并提交，事务A 再次查询，使用当前读，将会查询到数据，两次查询结果不一致，幻读了

- 怎么避免幻读
在开启事务之后，马上执行 select ... for update 这类当前读的语句，这样它会对记录加 next-key lock，从而避免其他事务插入一条新记录。



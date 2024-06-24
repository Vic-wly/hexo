title: mysql 的 readView 如何实现 mvcc 、读已提交、可重复读
author: Laiyong Wang
date: 2024-05-27 17:18:42
tags:
---
参考网站：
https://blog.csdn.net/h2503652646/article/details/117152307
https://blog.csdn.net/chijiandi/article/details/115115017
https://xiaolincoding.com/mysql/transaction/mvcc.html

在InnoDB引擎中，每行记录的后面会保存两个隐藏的列：trx_id、roll_pointer。它们的作用如下：
- trx_id：用于保存每次对该记录进行修改的事务的id。
- roll_pointer:存储一个指针，指向这条数据记录上一个版本的地址，可以通过它获取到该记录上一个版本的数据信息。


readView 里也有字段用于保存有关 trx_id 的数据
- m_ids ：指的是在创建 Read View 时，当前数据库中「活跃事务」的事务 id 列表，注意是一个列表，“活跃事务”指的就是，启动了但还没提交的事务。
- min_trx_id ：指的是在创建 Read View 时，当前数据库中「活跃事务」中事务 id 最小的事务，也就是 m_ids 的最小值。
- max_trx_id ：这个并不是 m_ids 的最大值，而是创建 Read View 时当前数据库中应该给下一个事务的 id 值，也就是全局事务中最大的事务 id 值 + 1；
- creator_trx_id ：指的是创建该 Read View 的事务的事务 id。

他们的关系如图所示：

![upload successful](/images/pasted-43.png)

一个事务去访问记录的时候，除了自己的更新记录总是可见之外，有这几种情况：
- 如果记录的 trx_id 值小于 Read View 中的 min_trx_id 值，表示这个版本的记录是在创建 Read View 前已经提交的事务生成的，所以该版本的记录对当前事务可见。
- 如果记录的 trx_id 值大于等于 Read View 中的 max_trx_id 值，表示这个版本的记录是在创建 Read View 后才启动的事务生成的，所以该版本的记录对当前事务不可见。
- 如果记录的 trx_id 值在 Read View 的 min_trx_id 和 max_trx_id 之间，需要判断 trx_id 是否在 m_ids 列表中：
  1、如果记录的 trx_id 在 m_ids 列表中，表示生成该版本记录的活跃事务依然活跃着（还没提交事务），所以该版本的记录对当前事务不可见。
  2、如果记录的 trx_id 不在 m_ids列表中，表示生成该版本记录的活跃事务已经被提交，所以该版本的记录对当前事务可见。
  
这种通过「版本链」来控制并发事务访问同一个记录时的行为就叫 MVCC（多版本并发控制）。

读已提交：每次查询时都会生成一个新的ReadView
可重复读：每次查询都复用第一次生成的ReadView
按照 ReadView 的访问规则去读取数据就实现了读已提交和可重复读。
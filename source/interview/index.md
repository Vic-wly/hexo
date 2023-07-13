title: 八股文
date: 2022-11-30 19:49:13
---
### 面试八股文
#### mysql
- 有哪些事务隔离级别，Mysql 的事务隔离级别是怎么实现的？
<https://zhuanlan.zhihu.com/p/117476959>
mvcc重点-快照（实现可重复读的关键）
当前事务内的更新，可以读到；
版本未提交，不能读到；
版本已提交，但是却在快照创建后提交的，不能读到；
版本已提交，且是在快照创建前提交的，可以读到；
表锁不等于所有行锁
间隙锁解决幻读
- 索引原理
<http://laiyong.wang/2022/11/29/mysql-Index/>
- 分库分表的策略，如果要按照分表字段以外的字段作为查询条件怎么办
<https://www.cnblogs.com/jobbible/p/10255907.html>
- MVCC 和间隙锁原理
<https://xie.infoq.cn/article/60ec650d1ecd72dfe48781ed5>
<https://blog.csdn.net/qq_42500831/article/details/123652307?utm_medium=distribute.pc_feed_404.none-task-blog-2~default~BlogCommendFromBaidu~Rate-1-123652307-blog-null.pc_404_mixedpudn&depth_1-utm_source=distribute.pc_feed_404.none-task-blog-2~default~BlogCommendFromBaidu~Rate-1-123652307-blog-null.pc_404_mixedpud>
- explain 的 type 字段有哪些
<https://www.weikeqin.com/2020/02/05/mysql-explain/>
- update 语句的执行流程，binlog 的作用和几种格式
<https://cloud.tencent.com/developer/article/1624168>(这篇文章很详细)
<https://cloud.tencent.com/developer/article/1533697>
<https://blog.51cto.com/u_15127534/4339193>
- 主从同步的原理和问题
<https://cuizb.top/myblog/article/1644849971>
- 发生死锁的原因以及如何解决
<https://cloud.tencent.com/developer/article/1483989>
<https://cloud.tencent.com/developer/article/1628870>
- 如何优化大 offset
<https://codeantenna.com/a/X6f1ZofXHV>
#### redis
- 缓存如何保证一致性
<http://kaito-kidd.com/2021/09/08/how-to-keep-cache-and-consistency-of-db/>
- 用过 redis 哪些数据结构，使用场景是什么
<https://zhuanlan.zhihu.com/p/145384563>
redis系列：<https://www.jianshu.com/p/28138a5371d0>
- redis 的 connect 和 pconnect 的区别，pconnect 有什么问题
connect：脚本结束之后连接就释放了。
pconnect：脚本结束之后连接不释放，连接保持在php-fpm进程中。
所以使用pconnect代替connect，可以减少频繁建立redis连接的消耗。占内存，若是redis不常用，非常浪费服务器资源
- redis 如何实现分布式锁，有什么问题
七种方案：<https://juejin.cn/post/6936956908007850014>
业务进程超时：<https://juejin.cn/post/7055904324542529550>
- redis 为什么用跳表实现有序集合？原理，用有序集合的场景
<https://juejin.cn/post/6962735884844138533>
- 主从同步的原理，哨兵和集群的区别
<https://zhuanlan.zhihu.com/p/62947738>
<https://blog.csdn.net/weixin_43889841/article/details/117483197>
- redis cluster 用的什么协议同步数据，哨兵的选举呢
参考上一个问题
- rdb 和 aof 的原理
<https://juejin.cn/post/7006619052453937160>
- 数据过期和淘汰策略
<https://www.51cto.com/article/712947.html>
- 缓存雪崩 击穿 穿透
<https://segmentfault.com/a/1190000022029639>
#### php
- php-fpm 的生命周期，创建进程方式，各自的优缺点
<https://www.abelzhou.com/php/php-fpm-lifespan/#>
- php 数组遍历为什么能保证有序
<https://segmentfault.com/a/1190000019964143>
- php 怎么实现的弱类型，怎么实现一个扩展
<https://blog.csdn.net/epwqgdnbrh/article/details/104749714>
- 常见魔术方法和函数(弟弟)
#### es（随便看看吧，我他妈又不用）
- 深度分页会有什么问题
- 倒排索引的原理
- lsm 树原理
#### rabbitMQ
<https://mp.weixin.qq.com/s?__biz=Mzg3OTU5NzQ1Mw==&mid=2247489667&idx=1&sn=910f851309f822b9473d08e7f6d4ab6c&chksm=cf035a61f874d377dd08c9303655afe7a385e9dc2421fe61a377ac156d62a01bfb48bb12ec1b&token=331236693&lang=zh_CN#rd>
#### kafka(这个随便看看，我是用rabbitMQ)
- kafka 的架构，大致储存结构
- 如果消费者数超过分区数会怎么样
- 怎么保证数据的可靠投递？
- 消费者的 offset 存在哪里？
- 如何通过 offset 定位消息？
- 时间轮的原理
- kafka 写入高性能的原因，sendfile 和 mmap 原理，为什么不用 splice（
#### 网络
- https 原理，tls 握手需要几个 rtt？
<https://www.51cto.com/article/642795.html>
- 浏览器访问某个网址的详细过程，四次挥手
<https://www.cnblogs.com/fundebug/p/what-happens-from-url-to-webpage.html>
- http2 和 quic 原理(不懂)
<https://toutiao.io/posts/hysaqw/preview>
- 分布式系统
<https://www.atlassian.com/zh/microservices/microservices-architecture/distributed-architecture>
- 分布式事务怎么处理
<https://segmentfault.com/a/1190000040321750>
<https://cloud.tencent.com/developer/article/1806989>
- 简述 raft 原理
<https://juejin.cn/post/6875950902852157447>
- 分布式 id 的几种实现和优缺点
<https://www.cnblogs.com/mzq123/p/16840232.html>
- 降级 限流 熔断实现原理
<https://juejin.cn/post/6938377598762221598>
#### 其他
- 布隆过滤器的实现原理和使用场景
<https://zhuanlan.zhihu.com/p/294069121>
- 进程间通信有哪几种方式
<https://worktile.com/kb/ask/7774.html>
- 进程线程协程区别
<https://juejin.cn/post/6975852498393235487>
- lvs 原理，如何保证高可用
<https://segmentfault.com/a/1190000041048935>
- 502 504 什么原因，如何处理
<https://juejin.cn/post/6862138634087170062>
- 给你两个一模一样的玻璃球，求出 100 层楼哪一层开始玻璃球会被摔碎
<https://www.zhihu.com/question/31855632>
- 一致性 hash 原理，怎么解决节点少数据倾斜的问题
<https://developer.aliyun.com/article/938465>
- 限流
<https://juejin.cn/post/7145435951899574302>
#### 系统设计
- 设计秒杀系统，需要支持 100W 以上 QPS
<https://www.zhihu.com/question/20978066>
- 设计微博首页，需要拉取所有关注用户的最近 20 条微博
<https://phpmianshi.com/?id=30>
- 抢红包算法设计
<https://www.zhihu.com/question/22625187>
- 设计一个短链系统
<https://www.cnblogs.com/myshowtime/p/16316654.html>
#### 算法
- leetcode 中等
title: 说一下工作方面的东西
author: Laiyong Wang
date: 2024-05-25 13:01:02
tags:
---
常规程序员能接触到的基本就是服务器、网络、代码、数据库
非底层研发的主要工作：对数据的接收、发送及对数据的整合处理并可视化界面查看

所以我们的工作内容就是写代码对数据进行各种传输、处理、保存以及展示，这方面又涉及到如何稳定、安全且高效的对数据进行处理

- 数据传输：最好使用https，且传输内容需加密，具体加密解密看双方要求
- 数据处理：这方面涉及点较多，简单举几个：数据验证、数据转换、避免长事务、大数据可分批处理（但不能每批特别少数据，防止频繁的i/o）、处理时注意幂等性、采用合理的算法
- 数据保存：看数据的重要性，正常数据库保存即可，记得定期备份

设计到高并发、大数据的情况下，我们又该怎么办（通过chatGPT获取，写的的确比自己写的好...........）
- 架构设计
  微服务架构：将应用拆分成多个独立的服务，每个服务负责特定的功能，以便于扩展和维护。

  分布式系统：利用分布式系统架构，如分布式数据库、分布式缓存、消息队列等，提升系统的处理能力和容错能力。

- 数据库设计
  数据库分片：将数据分散存储在多个数据库实例中，减轻单个数据库的压力。可以使用水平分片（sharding）技术。
  读写分离：将数据库的读操作和写操作分离，读操作由只读副本处理，写操作由主数据库处理，以提高读写性能。
  索引优化：创建合理的索引，提高查询效率。注意避免过多的索引，以免影响写操作性能。
  批量操作：减少单次数据库操作的数据量，通过批量操作提升性能，减少数据库连接开销。

- 缓存策略
  使用缓存：在可能的地方使用缓存（如 Redis、Memcached），减轻数据库的负担。缓存热点数据和频繁访问的数据。
  缓存更新策略：设计有效的缓存更新策略（如过期时间、主动更新），确保数据的一致性和及时性。
  本地缓存：在应用程序中使用本地缓存，减少远程缓存的访问次数，提高性能。
  
- 并发控制
  无锁并发：尽量采用无锁数据结构和算法，减少锁的竞争。使用 CAS（Compare-And-Swap）等技术实现无锁并发控制。
  线程池：合理配置线程池，避免频繁创建和销毁线程，提升并发处理能力。
  异步编程：使用异步编程模型（如事件驱动、回调函数、Future/Promise），提高系统的并发处理能力和响应速度。


- 消息队列
  使用消息队列：引入消息队列（如 RabbitMQ、Kafka），解耦系统各部分，平衡负载，提升系统的扩展性和稳定性。
  异步处理：将耗时操作通过消息队列异步处理，减轻主流程的压力，提升系统的响应速度。

- 服务限流和降级
  限流：对高并发请求进行限流，防止系统过载。可以使用令牌桶、漏桶算法实现限流。
  熔断器：在高并发和大数据场景下，使用熔断器（如 Hystrix）保护系统，避免级联故障。
  服务降级：在系统负载过高时，自动降级部分服务，确保核心功能的可用性。

- 性能优化
  代码优化：优化代码逻辑，避免不必要的复杂度和重复计算。使用高效的数据结构和算法。
  内存管理：优化内存使用，避免内存泄漏和过度使用。合理使用对象池，减少频繁的对象创建和销毁。
  I/O优化：减少阻塞 I/O 操作，尽量使用异步 I/O 或非阻塞 I/O。优化网络传输，减少数据传输量。

- 分布式事务
  两阶段提交：在需要分布式事务的场景下，使用两阶段提交（2PC）协议确保数据的一致性。
  补偿事务：设计补偿事务机制，在事务失败时进行回滚操作，确保数据一致性。

- 监控和预警
  日志记录：详细记录系统日志，特别是错误日志和性能日志，便于问题排查和性能调优。
  性能监控：使用性能监控工具（如 Prometheus、Grafana）实时监控系统的性能指标，及时发现性能瓶颈和异常。
  预警系统：设置预警机制，在系统异常或性能指标异常时及时通知相关人员，进行快速响应和处理。

- 安全性
  数据加密：确保传输中的数据和存储的数据都经过加密处理，防止数据泄露和篡改。
  身份验证：对用户和系统组件进行严格的身份验证，防止未经授权的访问。
  权限控制：细粒度的权限控制，确保用户只能访问和操作其有权限的数据和功能。
  通过注意以上这些方面，可以有效地提高高并发和大数据场景下系统的性能、稳定性和安全性。


title: go 的 sync.Pool
author: Laiyong Wang
tags:
  - go
categories: []
date: 2024-06-24 17:22:00
---
### Poll
- 作用和效果
sync.Pool 数据类型用来保存一组可独立访问的临时对象，可以有效地减少新对象的申请，从而提高程序性能

1、sync.Pool 本身就是线程安全的，多个 goroutine 可以并发地调用它的方法存取对象
2、sync.Pool 不可在使用之后再复制使用。

- 解决了我的疑问
除了使用更多的goroutine来干更多的活，还可以使用 sync.Pool 去池化，有开源的 Worker Pool ，别自己去实现了

- sync.Pool 的坑和解决办法
	- 内存泄漏
    取出来的元素在使用的时候，我们可以往这个元素中增加大量的数据，这会导致底层的 byte slice 的容量可能会变得很大。这个时候，即使 Reset 再放回到池子中，这些 byte slice 的容量不会改变，所占的空间依然很大。而且，因为 Pool 回收的机制，这些大的 Buffer 可能不被回收，而是会一直占用很大的空间，这属于内存泄漏的问题。
    - 内存浪费
    池子中的 buffer 都比较大，但在实际使用的时候，很多时候只需要一个小的 buffer，这也是一种浪费现象
    可以将 buffer 池分成几层。首先，小于 512 byte 的元素的 buffer 占一个池子；其次，小于 1K byte 大小的元素占一个池子，以此类推

- GMP中的P与poll的关系（ChatGPT 的问答）

1. **`sync.Pool`使用了P的特性**：`sync.Pool`利用了每个`P`（Processor）的本地存储来实现高效的对象缓存。这意味着每个`P`有自己的本地对象池，当一个`P`需要一个对象时，它首先会尝试从它的本地池中获取，这样可以避免锁争用，提高性能。

2. **`P`不直接使用`sync.Pool`**：`P`本身是Go运行时调度器的一部分，负责管理可运行的goroutine队列，并在M（操作系统线程）之间进行负载均衡。`P`本身并不依赖于`sync.Pool`来完成它的调度任务。
title: go 的 channel
author: Laiyong Wang
tags:
  - go
categories: []
date: 2024-06-27 19:06:00
---
### 简单记录下，这个文章是给自己看的，使用方法和原理什么的请 google ........

### 用途
- 数据交流：当作并发的 buffer 或者 queue，解决生产者 - 消费者问题。多个 goroutine 可以并发当作生产者（Producer）和消费者（Consumer）。但是最好使用sync-poll
- 数据传递：一个 goroutine 将数据交给另一个 goroutine，相当于把数据的拥有权 (引用) 托付出去。
- 信号通知：一个 goroutine 可以将信号 (closing、closed、data ready 等) 传递给另一个或者另一组 goroutine 。
- 任务编排：可以让一组 goroutine 按照一定的顺序并发或者串行的执行，这就是编排的功能。
- 锁：利用 Channel 也可以实现互斥锁的机制。 容量为一的channel，如果不是为了超时机制，也不推荐

### 如何选择并发原语和 channel
- 共享资源的并发访问使用传统并发原语；
- 复杂的任务编排和消息传递使用 Channel；
- 消息通知机制使用 Channel，除非只想 signal 一个 goroutine，才使用 Cond；
- 简单等待所有任务的完成用 WaitGroup，Channel，也可以；
- 需要和 Select 语句结合，使用 Channel；
- 需要和超时配合时，使用 Channel 和 Context。

### panic 场景
- close 为 nil 的 chan；
- send 已经 close 的 chan；
- close 已经 close 的 chan。

### 主要参数
ring buffer 环形缓存区
receive queue 接收队列
send queue 发送队列
mutex 锁，保护channel
closed 

### 接收数据和发送数据的各种场景

- 接收传进来的值
直接发送：缓存没有，有协程等待，数据直接copy到目标变量并唤醒协程
放入缓存：无协程等待，放入缓存
休眠等待：缓存满了 接受队列无，将自己包装后放入发送队列，然后休眠

- 发送出去
发送队列有G，无缓存 直接从G拿
发送队列有G，有缓存 从缓存拿
发送队列无G，无缓存 阻塞
发送队列无G，有缓存 从缓存拿
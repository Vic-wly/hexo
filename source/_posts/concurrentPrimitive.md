title: go并发编程实战 - 基本并发原语，学习总结
author: Laiyong Wang
date: 2024-06-24 13:04:36
tags:
---
### 并发原语的种类及使用场景
- 种类
互斥锁 Mutex、读写锁 RWMutex、并发编排 WaitGroup、条件变量 Cond、Channel 等同步原语
- 使用场景
1、共享资源。并发地读写共享资源，会出现数据竞争的问题，需要 Mutex、RWMutex 这样的并发原语来保护。
2、任务编排。需要 goroutine 按照一定的规律执行，而 goroutine 之间有相互等待或者依赖的顺序关系，常使用 WaitGroup 或者 Channel 来实现。
3、消息传递。信息交流以及不同的 goroutine 之间的线程安全的数据交流，常使用 Channel 来实现。

### Mutex 互斥锁

- 正常模式
waiter 都是进入先入先出队列，被唤醒的 waiter 并不会直接持有锁，而是要和新来的 goroutine 进行竞争，都需要自旋来获取锁，源码里时循环四次。新来的 goroutine 有先天的优势，它们正在 CPU 中运行，可能它们的数量还不少，所以，在高并发情况下，被唤醒的 waiter 可能比较悲剧地获取不到锁，这时，它会被插入到队列的前面。如果 waiter 获取不到锁的时间超过阈值 1 毫秒，那么，这个 Mutex 就进入到了饥饿模式。
- 饥饿模式
Mutex 的拥有者将直接把锁交给队列最前面的 waiter。新来的 goroutine 不会尝试获取锁，即使看起来锁没有被持有，它也不会去抢，也不会 spin（自旋），它会乖乖地加入到等待队列的尾部
如何切换至正常模式：

	- waiter 已经是队列中的最后一个 waiter 了，没有其它的等待锁的 goroutine 了
    - 此 waiter 的等待时间小于 1 毫秒。
- 易错场景注意
	- Lock/Unlock 需要成对出现，没有成对出现，就意味着会出现死锁，正常使用defer 来 Unlock即可
    - 不能 Copy 已使用的 Mutex，因为在并发环境下，根本不知道要复制的 Mutex 状态是什么，因为要复制的 Mutex 是由其它 goroutine 并发访问的，状态可能总是在变化，可以使用 go vet main.go来检查
    - 重入，Mutex 不是可重入的锁
    - 死锁，四大特性：互斥、持有和等待、不可剥夺、环路等待
    
- 使用起来很简单，代码也很简单，写个demo
```
type Counter struct {
	sync.Mutex
	Count uint64
}

func main() {
	var counter Counter
	var wg sync.WaitGroup
	wg.Add(10)
	for i := 0; i < 10; i++ {
		go func() {
			defer wg.Done()
			for j := 0; j < 1000; j++ {
				counter.Lock()
				counter.Count++
				counter.Unlock()
			}
		}()
	}
	wg.Wait()
	fmt.Println(counter.Count)
}
```
### RWMutex 读写锁
- 简单解释下
RWMutex 在某一时刻只能由任意数量的 reader 持有，或者是只被单个的 writer 持有。
- RWMutex 结构体
```
type RWMutex struct {
  w           Mutex   // 互斥锁解决多个writer的竞争
  writerSem   uint32  // writer信号量
  readerSem   uint32  // reader信号量
  readerCount int32   // reader的数量
  readerWait  int32   // writer等待完成的reader的数量
}

const rwmutexMaxReaders = 1 << 30
```

- 易错场景注意
	- Lock 和 Unlock 的调用总是成对出现的，RLock 和 RUnlock 的调用也必须成对出现。Lock 和 RLock 多余的调用会导致锁没有被释放，可能会出现死锁，而 Unlock 和 RUnlock 多余的调用会导致 panic
    - 不可复制 类似 Mutex
    - 死锁

- 一个 demo 代码
```
func main() {
    var counter Counter
    for i := 0; i < 10; i++ { // 10个reader
        go func() {
            for {
                counter.Count() // 计数器读操作
                time.Sleep(time.Millisecond)
            }
        }()
    }

    for { // 一个writer
        counter.Incr() // 计数器写操作
        time.Sleep(time.Second)
    }
}
// 一个线程安全的计数器
type Counter struct {
    mu    sync.RWMutex
    count uint64
}

// 使用写锁保护
func (c *Counter) Incr() {
    c.mu.Lock()
    c.count++
    c.mu.Unlock()
}

// 使用读锁保护
func (c *Counter) Count() uint64 {
    c.mu.RLock()
    defer c.mu.RUnlock()
    return c.count
}
```
### WaitGroup：协同等待，任务编排利器
- 实现原理
sync.WaitGroup 没有使用channel，而是依赖于信号量来实现阻塞和同步机制。信号量提供了一种低开销、高效的同步手段，特别适合需要高性能的场景
- 基础方法
```
    func (wg *WaitGroup) Add(delta int) // 用来设置 WaitGroup 的计数值；
    func (wg *WaitGroup) Done()  // WaitGroup 的计数值减 1，其实就是调用了 Add(-1)；
    func (wg *WaitGroup) Wait()  // WaitGroup 的计数值减 1，其实就是调用了 Add(-1)；
```

- 注意点：
	- 计数器不能设置为负值
    - 预先设置 Add 的数字，别再子协程力写 Add方法
    - 前一个 Wait 还没结束不能重用 WaitGroup，

### Cond
没啥用啊，这个，不写了

### Once：一个简约而不简单的并发原语
- 使用场景
sync.Once 只暴露了一个方法 Do，你可以多次调用 Do 方法，但是只有第一次调用 Do 方法时 f 参数才会执行，这里的 f 是一个无参数无返回值的函数。
当且仅当第一次调用 Do 方法的时候参数 f 才会执行，即使第二次、第三次、第 n 次调用时 f 参数的值不一样，也不会被执行

- 总结
Once 常常用来初始化单例资源，或者并发访问只需初始化一次的共享资源，或者在测试的时候初始化一次测试资源。

- 注意点
防止产生错误，方法未执行成功
死锁
```
func main() {
    var once sync.Once
    once.Do(func() {
        once.Do(func() {
            fmt.Println("初始化")
        })
    })
}
```

- 实现源码

```
type Once struct {
    m    Mutex
    done uint32
}

func (o *Once) Do(f func()) {
    if atomic.LoadUint32(&o.done) == 1 {
        return
    }
    o.m.Lock()
    defer o.m.Unlock()
    if o.done == 0 {
        defer atomic.StoreUint32(&o.done, 1)
        f()
    }
}

```
### map：如何实现线程安全的map类型

- 常见错误
未初始化，直接赋值
并发读写，因为 map 对象不是线程（goroutine）安全的，并发读写的时候运行时会有检查，遇到并发问题就会导致 panic。

- 并发读写，如何处理 感觉面试会容易问
加锁和分片加锁这两种方案都比较常用，如果是追求更高的性能，分片加锁更好，因为它可以降低锁的粒度，进而提高访问此 map 对象的吞吐。如果并发性能要求不是那么高的场景，简单加锁方式更简单。

### sync.Map 线程安全的 map

这个 sync.Map 并不是用来替换内建的 map 类型的，它只能被应用在一些特殊的场景里。
- 使用场景
  以下两个场景中使用 sync.Map，会比使用 map+RWMutex 的方式，性能要好得多：
  - 只会增长的缓存系统中，一个 key 只写入一次而被读很多次，且删除很少
  - 多个 goroutine 为不相交的键集读、写和重写键值对。

- 实现原理
	- 空间换时间。通过冗余的两个数据结构（只读的 read 字段、可写的 dirty），来减少加锁对性能的影响。对只读字段（read）的操作不需要加锁。
	- 优先从 read 字段读取、更新、删除，因为对 read 字段的读取不需要锁。
	- 动态调整。miss 次数多了之后，将 dirty 数据提升为 read，避免总是从 dirty 中加锁读取。
	- double-checking。加锁之后先还要再检查 read 字段，确定真的不存在才操作 dirty 字段。
	- 延迟删除。删除一个键值只是打标记，只有在提升 dirty 字段为 read 字段的时候才清理删除的数据。
    
- 总结
Go 内置的 map 类型使用起来很方便，但是它有一个非常致命的缺陷，那就是它存在着并发问题，所以如果有多个 goroutine 同时并发访问这个 map，就会导致程序崩溃。所以 Go 官方 Blog 很早就提供了一种加锁的方法，还有后来提供了适用特定场景的线程安全的 sync.Map，还有第三方实现的分片式的 map，这些方法都可以应用于并发访问的场景。
title: go 的 atomic
author: Laiyong Wang
tags:
  - go
categories: []
date: 2024-06-27 18:51:00
---
go 语言中原子操作是硬件级的
`sync/atomic`包提供了底层的原子操作，用于在多个goroutine之间安全地操作共享变量。原子操作能够保证这些操作是不可分割的，即不会被其他操作打断，从而避免数据竞争（race condition）。

### 方法

`sync/atomic`包提供了一组函数，用于对数值类型（如整数和指针）进行原子操作。这些操作包括：

- `atomic.AddInt32`/`AddInt64`：原子地对整数进行加法操作。
- `atomic.LoadInt32`/`LoadInt64`：原子地读取整数值。
- `atomic.StoreInt32`/`StoreInt64`：原子地写入整数值。
- `atomic.SwapInt32`/`SwapInt64`：原子地交换整数值。
- `atomic.CompareAndSwapInt32`/`CompareAndSwapInt64`：原子地比较并交换整数值。

### 作用

- **线程安全**：保证对共享变量的操作是原子的，从而避免数据竞争。
- **高效**：比使用锁（如`sync.Mutex`）更加轻量级，适用于需要高频率操作的场景。

### 使用场景

在实际应用中，`sync/atomic`常用于计数器、状态标志等需要多goroutine安全访问的共享变量。

### 简单例子

以下是一个使用`sync/atomic`实现线程安全计数器的示例：

```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
)

func main() {
	var counter int64

	var wg sync.WaitGroup

	// 启动100个goroutine，每个增加1000次计数器
	for i := 0; i < 100; i++ {
		wg.Add(1)
		go func() {
			defer wg.Done()
			for j := 0; j < 1000; j++ {
				atomic.AddInt64(&counter, 1)
			}
		}()
	}

	// 等待所有goroutine完成
	wg.Wait()

	fmt.Println("Final counter value:", counter)
}
```

### 解释

1. **定义计数器**：
    - 使用 `int64` 类型的变量 `counter` 来存储计数值。

2. **启动多个 goroutine**：
    - 使用 `sync.WaitGroup` 来等待所有 goroutine 完成。
    - 启动 100 个 goroutine，每个 goroutine 执行 1000 次计数器增加操作。

3. **原子操作**：
    - 在每次增加计数器时，使用 `atomic.AddInt64(&counter, 1)` 保证操作的原子性。

4. **等待所有 goroutine 完成**：
    - 使用 `wg.Wait()` 等待所有 goroutine 完成操作。

5. **输出结果**：
    - 输出最终的计数器值。

这个例子展示了如何使用 `sync/atomic` 实现一个线程安全的计数器，确保在多个 goroutine 并发执行时没有数据竞争。
比 Mutex 简单很多，且不用 lock unlock
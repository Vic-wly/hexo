title: 为什么要有GMP调度解决了什么问题、如何实现协程并发，抢占式调度解决了什么问题
author: Laiyong Wang
tags:
  - GMP
  - go
categories: []
date: 2025-03-29 08:09:00
---

### 一、传统线程模型的痛点

• 线程创建成本高（默认栈MB级别）
• 线程切换需要内核态切换（微秒级）
• 难以支撑百万级并发


### 二、GMP 模型解决方案

#### 核心组件
```go
// Go runtime 关键结构（简化版）
type G struct { // Goroutine
    stack   []byte // 2KB 栈
    goid    int64
    // ... 状态字段
}

type M struct { // Machine（内核线程）
    curG      *G   // 当前运行的G
    p         *P   // 绑定的P
    // ... 系统线程信息
}

type P struct { // Processor（逻辑处理器）
    runq     [256]guintptr // 本地队列
    // ... 统计、缓存等信息
}
```

#### 调度流程示例
```go
func main() {
    // 初始化 P 的数量（默认等于CPU核心数）
    runtime.GOMAXPROCS(4)

    // 创建百万级goroutine
    for i := 0; i < 1_000_000; i++ {
        go func(id int) {
            fmt.Printf("Goroutine %d\n", id)
        }(i)
    }

    // 调度器行为：
    // 1. G 被放入 P 的本地队列
    // 2. M 从 P 获取 G 执行
    // 3. 当 P 本地队列满时，G 进入全局队列
}
```

**关键设计优势**：
1. **内存高效**：每个G初始仅2KB栈，可动态扩容
2. **快速切换**：用户态调度（纳秒级切换）
3. **负载均衡**：Work-stealing 机制（M会从其他P偷G）

---

### 三、抢占式调度解决的问题
#### 1. 协作式调度的缺陷（Go 1.13 之前）
```go
func main() {
    go func() {
        for {
            // 死循环不主动让出CPU
        }
    }()

    time.Sleep(time.Millisecond)
    fmt.Println("这行可能永远无法执行")
}
```

#### 2. 抢占式调度实现（Go 1.14+）
```go
// Go runtime 插入抢占检测
func checkPreempt() {
    if gp.preemptStop {
        mcall(preemptPark) // 触发调度
    }
}

// 编译器在循环中插入检查
// 原代码：
for {
    i++
}

// 编译后：
for {
    if needPreempt {
        checkPreempt() 
    }
    i++
}
```

**抢占触发场景**：
1. 系统调用执行时间过长
2. 函数调用时的栈扩张检查
3. 基于信号的强制抢占（sysmon监控线程）

---

### 四、完整机制总结表
| 机制              | 解决问题                          | 实现方式                                 | 性能影响               |
|-------------------|----------------------------------|----------------------------------------|-----------------------|
| GMP 模型          | 传统线程高开销问题                | 用户态协程 + 逻辑处理器队列              | 纳秒级切换            |
| Work-stealing     | 各P之间负载不均衡                 | M优先从本地队列获取，空时偷其他P的任务   | 提升CPU利用率         |
| 协作式调度        | 简单并发场景                      | 函数调用时主动让出CPU                    | 存在调度延迟          |
| 抢占式调度        | 死循环导致调度阻塞                | 系统监控线程+编译器插入检查点            | 增加约1%运行时开销    |
| 网络轮询器        | 网络IO阻塞导致线程浪费            | 单独线程处理epoll/kqueue                | 提升IO密集型性能      |

---

### 五、最佳实践建议
1. **控制Goroutine生命周期**
   ```go
   // 使用context控制超时
   ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
   defer cancel()
   
   go func(ctx context.Context) {
       select {
       case <-ctx.Done():
           return // 及时退出
       // ... 
       }
   }(ctx)
   ```

2. **避免过度并发**
   ```go
   // 使用worker pool模式
   pool := make(chan struct{}, runtime.NumCPU()*2)
   for task := range tasks {
       pool <- struct{}{}
       go func(t Task) {
           defer func() { <-pool }()
           process(t)
       }(task)
   }
   ```

3. **监控调度器状态**
   ```go
   // 输出调度器信息
   func logSchedStats() {
       var stats runtime.MemStats
       runtime.ReadMemStats(&stats)
       fmt.Printf("Goroutines: %d\n", runtime.NumGoroutine())
       fmt.Printf("OS Threads: %d\n", runtime.ThreadCreateProfile(nil))
   }
   ```

通过这套机制，Go 在单进程内实现了百万级并发能力，同时保持亚毫秒级的调度延迟。理解这些底层原理有助于编写更高效可靠的并发程序。
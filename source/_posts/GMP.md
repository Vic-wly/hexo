title: GMP 模型
author: Laiyong Wang
date: 2024-06-12 19:25:29
tags:
---
Go 的 GMP 模型是 Go 语言的并发调度模型，它通过 Goroutine、M（操作系统线程）和 P（处理器）三个核心概念来实现高效的并发调度和执行。以下是对 GMP 模型的详细解释：

### 1. Goroutine (G)
- **定义**：Goroutine 是 Go 语言中的轻量级线程，类似于操作系统中的线程，但更加轻量和高效。
- **特点**：Goroutine 的创建和销毁开销非常小，能够在数百万数量级上运行而不会导致系统资源枯竭。
- **调度**：Goroutine 的调度由 Go 运行时自动管理，无需显式管理。

### 2. Machine (M)
- **定义**：M 代表实际的操作系统线程（也称为内核线程）。
- **职责**：M 执行 G 并由 Go 运行时管理。一个 M 可以执行多个 G，但在任意时间点，一个 M 只能执行一个 G。
- **上下文切换**：M 会在 G 之间进行上下文切换，但这种切换由 Go 运行时管理，比操作系统的线程切换开销更低。

### 3. Processor (P)
- **定义**：P 代表处理器，是一个逻辑处理单元，用于管理 G 的调度。
- **数量**：P 的数量由 `GOMAXPROCS` 环境变量设置，默认为机器的 CPU 核心数。
- **作用**：P 维护着一个本地的 G 队列，并负责将 G 分配给 M 执行。每个 P 只能绑定一个 M，但可以在不同 M 之间切换。

### GMP 模型的工作机制

1. **初始化**：Go 程序启动时，会根据 `GOMAXPROCS` 初始化 P 的数量，并创建相应数量的 M。
2. **调度**：
   - Goroutine 的创建和切换由 P 负责。P 会维护一个本地 G 队列，当一个 M 需要执行 G 时，会从 P 的本地队列中获取 G。
   - 如果本地队列为空，P 会从全局队列或者其他 P 的队列中窃取 G 来执行。
3. **执行**：
   - M 从 P 获取 G 后开始执行。
   - 如果 G 在执行过程中发生阻塞（如 IO 操作），M 会将 G 放回队列并尝试获取新的 G 来执行。
4. **抢占**：
   - Go 运行时会定期检查正在运行的 G，如果某个 G 执行时间过长，会被抢占并重新调度，以确保其他 G 有机会运行。

### GMP 模型的优点

- **高效调度**：GMP 模型通过逻辑处理单元（P）和工作窃取机制，实现了高效的 Goroutine 调度，避免了大量的操作系统线程上下文切换开销。
- **并发支持**：GMP 模型充分利用了多核 CPU 的优势，使 Goroutine 能够在多核 CPU 上并发执行，提高了程序的执行效率。
- **轻量级**：Goroutine 相对于操作系统线程更加轻量级，使得 Go 程序可以创建和管理大量并发任务而不显著增加系统负担。

### 示例代码

以下是一个简单的示例，展示了 Go 如何使用 Goroutine 实现并发计算：

```go
package main

import (
    "fmt"
    "runtime"
    "sync"
)

func main() {
    runtime.GOMAXPROCS(runtime.NumCPU()) // 设置 GOMAXPROCS 为 CPU 核心数

    var wg sync.WaitGroup

    // 启动多个 Goroutine
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func(i int) {
            defer wg.Done()
            fmt.Printf("Goroutine %d is running\n", i)
        }(i)
    }

    // 等待所有 Goroutine 结束
    wg.Wait()
    fmt.Println("All Goroutines finished")
}
```

### 总结

GMP 模型是 Go 语言实现高效并发的核心机制，通过 Goroutine、M（机器线程）和 P（处理器）三个核心概念，实现了轻量级、高效的并发调度。Goroutine 的轻量级和 GMP 模型的高效调度使得 Go 在处理高并发任务时表现出色，非常适合构建高性能的并发应用。

### 使用 P 的优点
  - 每个 P 维护一个本地 Goroutine 队列，不用一直去全局队列拿 G ，减少了锁争用，提升了调度效率。
  - 每个 P 绑定一个操作系统线程（M），P 负责 Goroutine 的调度，操作系统线程（M）只负责执行任务，减少了复杂的线程管理开销。
  - 窃取 G 的方式，也实现个 P 直接的负载均衡，最大限度的利用服务器性能
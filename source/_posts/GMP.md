title: go是单进程的，为啥可以使用服务器的八个核
author: Laiyong Wang
date: 2025-06-23 22:24:13
tags:
---
核心原因在于其调度器设计的 **GMP 模型**与操作系统线程机制的协同工作。以下是详细解释：

---

### **1. 进程与线程的本质**
- **进程是资源分配单位**：一个进程拥有独立的内存空间、文件描述符等资源，但**不直接执行代码**。
- **线程是执行单位**：线程是操作系统调度和分派 CPU 时间片的基本单元。一个进程可以创建多个线程，这些线程**共享进程的内存空间**，但由操作系统调度到不同 CPU 核心上并行执行。
- **多核并行原理**：操作系统会将进程中的多个线程分配到不同的 CPU 核心上运行，实现真正的并行（而非并发）。例如，八核服务器可同时运行 8 个线程。

---

### **2. Go 调度器的 GMP 模型如何利用多核**
Go 的调度器通过 **G（Goroutine）、M（Machine）、P（Processor）** 三层抽象，将用户级协程（Goroutine）高效映射到系统线程（M），并分配到 CPU 核心上执行：

| **组件** | **角色**               | **与多核的关系**                                                                 |
|----------|------------------------|---------------------------------------------------------------------------------|
| **M（Machine）** | 系统线程（内核线程）    | 由操作系统调度到 CPU 核心上运行。一个 M 绑定到一个物理核心时，即可利用该核心的计算资源。 |
| **P（Processor）** | 逻辑处理器             | 数量默认等于 CPU 核心数（如八核则 P=8）。每个 P 绑定一个 M，决定**同时活跃的线程数**（即并行能力）。 |
| **G（Goroutine）** | 用户级协程             | 由 P 调度到 M 上执行。大量 G 通过 P 分配到多个 M，最终由不同 CPU 核心并行执行。 |

#### **关键机制**
1. **P 的数量控制并行度**  
   - 通过 `GOMAXPROCS` 参数设置 P 的数量（默认 = CPU 核数）。  
   - 例如八核服务器默认创建 8 个 P，每个 P 绑定一个 M（系统线程），从而**同时占用 8 个 CPU 核心**。

2. **M 的动态扩展**  
   - 若一个 M 因系统调用（如 I/O）阻塞，Go 运行时会解绑该 M 与 P，并创建新的 M 接管 P，继续利用空闲 CPU 核心。  
   - 例如：M0 阻塞 → P 被转移给新创建的 M1 → M1 绑定到另一个 CPU 核心继续运行其他 G。

3. **负载均衡**  
   - **工作窃取（Work Stealing）**：当某个 P 的本地任务队列为空时，会从其他 P 的队列中“偷取”一半的 Goroutine，确保所有 CPU 核心均被充分利用。  
   - **全局队列（Global Queue）**：新创建的 G 优先放入 P 的本地队列，本地队列满时转移至全局队列，由空闲 P 获取。

---

### **3. 操作系统层面对多核的支持**
- **线程调度由内核管理**：操作系统内核的调度器负责将进程中的线程分配到物理 CPU 核心上。Go 的 M 直接对应内核线程，因此内核会自动将多个 M 分配到不同核心。
- **无需跨进程通信**：同一进程内的所有线程共享内存，Goroutine 通过 M 执行时，跨核心通信**无需进程间通信（IPC）的开销**，只需访问共享内存。

---

### 4. 性能优势示例

假设八核服务器运行 Go 程序：

1. 创建 8 个 P（逻辑处理器），每个 P 绑定一个 M（系统线程）。  
2. 操作系统将 8 个 M 分配到 8 个 CPU 核心上并行运行。  
3. 每个 M 不断从 P 的本地队列获取 G 执行：  
   - 若某个 G 阻塞（如网络请求），其绑定的 M 会释放 P，由新 M 接管 P 继续使用该核心。  
   - 空闲 P 通过工作窃取从其他 P 获取任务，避免核心闲置。  
---

### 总结
- **单进程 + 多线程**：Go 进程通过创建多个系统线程（M），由操作系统调度到不同 CPU 核心。  
- **GMP 调度器**：通过 P 控制并行度、动态调整 M 应对阻塞、工作窃取实现负载均衡，确保所有 CPU 核心始终高效运行。  
- **资源高效**：线程共享进程内存，避免跨进程通信开销；Goroutine 轻量级，支持海量并发任务。  

> 简单来说：Go 的调度器将用户级协程（G）通过逻辑处理器（P）绑定到系统线程（M），而操作系统自动将这些线程分配到多个 CPU 核心上，从而实现单进程跨八核并行。
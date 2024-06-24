title: php-fpm的主进程、子进程、nginx的工作进程还有工作进程的最大连接数之间的关系
author: Laiyong Wang
date: 2024-06-05 23:29:04
tags:
---
### PHP-FPM 进程模型

- **主进程（Master Process）**：负责管理子进程，接收和分配任务。
- **子进程（Worker Processes）**：实际处理请求的工作进程。

#### 配置参数

- `pm`：进程管理方式，有 `static`、`dynamic` 和 `ondemand` 三种模式。
  - `static`：固定数量的子进程。
  - `dynamic`：子进程数量动态调整，指定最小、启动和最大子进程数。
  - `ondemand`：按需启动子进程。
- `pm.max_children`：最大子进程数量（所有模式适用）。
- `pm.start_servers`：启动时的子进程数量（dynamic 模式适用）。
- `pm.min_spare_servers`：最小空闲子进程数量（dynamic 模式适用）。
- `pm.max_spare_servers`：最大空闲子进程数量（dynamic 模式适用）。

### Nginx 进程模型

- **Master Process**：管理工作进程（Worker Processes），不处理请求。
- **Worker Processes**：处理实际请求，数量通常设置为服务器 CPU 核心数或其倍数。

#### 配置参数

- `worker_processes`：Nginx 工作进程数量。
- `worker_connections`：每个工作进程允许的最大连接数。
- `multi_accept`：是否尽可能多地接受连接。

### 工作原理和关系

1. **请求流程**：
   - **Nginx** 接收客户端请求并通过 FastCGI 方式将动态请求转发给 PHP-FPM 处理。
   - **PHP-FPM** 子进程处理请求并将结果返回给 Nginx。
   - **Nginx** 将响应结果返回给客户端。

2. **连接和并发**：
   - **Nginx 工作进程数**（`worker_processes`）决定并发处理能力。每个工作进程处理的连接数由 `worker_connections` 决定。
   - **Nginx 最大并发连接数** = `worker_processes` × `worker_connections`。
   - **PHP-FPM 最大并发处理数** = `pm.max_children`。

3. **资源利用**：
   - Nginx 进程数和连接数设置影响 Nginx 的并发处理能力。
   - PHP-FPM 子进程数量影响 PHP 脚本的并发处理能力。
   - 如果 PHP-FPM 子进程数低于 Nginx 并发连接数，可能出现瓶颈，影响性能。

### 优化建议

1. **Nginx 配置**：
   ```nginx
   worker_processes auto;  # 自动根据 CPU 核心数配置
   events {
       worker_connections 1024;  # 每个工作进程允许的最大连接数
       use epoll;  # 高效 I/O 模型
       multi_accept on;  # 尽可能多地接受连接
   }
   ```

2. **PHP-FPM 配置**：
   ```ini
   [www]
   pm = dynamic
   pm.max_children = 50
   pm.start_servers = 5
   pm.min_spare_servers = 5
   pm.max_spare_servers = 35
   ```

3. **结合调整**：
   - 确保 `pm.max_children` 足够高以处理 Nginx 的并发请求。
   - 调整 `worker_processes` 和 `worker_connections` 以匹配服务器资源和应用负载。
   - 监控服务器负载，使用压力测试工具优化配置。

### 关系总结

- Nginx 的 `worker_processes` 决定并发处理能力。
- 每个 Nginx 工作进程的 `worker_connections` 决定其最大连接数。
- PHP-FPM 的 `pm.max_children` 决定可以并发处理的 PHP 请求数。
- Nginx 处理请求后，通过 FastCGI 转发给 PHP-FPM，PHP-FPM 子进程处理请求并返回结果。

合理配置 Nginx 和 PHP-FPM，平衡并发连接和请求处理能力，可以显著提高服务器性能和稳定性。
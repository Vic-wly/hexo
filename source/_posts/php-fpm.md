title: php-fpm 如何更快，处理更可能多的并发请求
author: Laiyong Wang
date: 2024-06-05 22:21:41
tags:
---
#### 总结，有关配置的有：
- 调整php-fpm的模式为 Dynamic ，子进程的数量可以动态调整
- 调整超时时间，防止脚本一直占用资源
- 增加 listen.backlog 的值，防止请求因为超过tcp连接数而被拒绝
- 增加文件描述符限制，以处理更多并发连接，因为每个连接和文件操作都会占用一个文件描述符。
- nginx 调整工作进程数量和每个工作进程的最大连接数，工作进程数量取决于服务器的 cpu 内核数，每个工作进程的最大连接数基于实际需求和服务器的能力还有据系统的文件描述符限制来设置
- 启用 FastCGI 缓存以减少对 PHP-FPM 的请求次数
#### 1. 调整 `pm` 模式和参数

`pm`（进程管理）模式有三种：`static`、`dynamic` 和 `ondemand`。常用的是 `dynamic` 模式，它允许根据需求动态调整进程数量。

- **`pm.max_children`**: 设置 PHP-FPM 可以创建的最大子进程数量。
- **`pm.start_servers`**: 在启动时创建的子进程数量。
- **`pm.min_spare_servers`**: 保持在运行中的最少空闲子进程数量。
- **`pm.max_spare_servers`**: 保持在运行中的最大空闲子进程数量。
- **`pm.max_requests`**: 每个子进程在处理这么多请求后会被重启，以防止内存泄漏。

示例配置：
```ini
pm = dynamic
pm.max_children = 50
pm.start_servers = 10
pm.min_spare_servers = 5
pm.max_spare_servers = 20
pm.max_requests = 500
```

#### 2. 调整 `request_terminate_timeout`

设置请求超时时间，以防止长时间运行的脚本占用资源：
```ini
request_terminate_timeout = 30s
```

#### 3. 增加 `listen.backlog`

增加 `listen.backlog`（设置服务器在接收新的 TCP 连接请求时的等待队列长度。当服务器的连接请求超过处理能力时，这个参数决定了有多少连接可以在队列中等待，而不被立即拒绝） 的值，默认值是 -1，以处理更多的排队请求：
```ini
listen.backlog = 511
```

### 系统和网络优化

#### 1. 调整系统文件描述符限制

增加文件描述符限制，以处理更多并发连接：
```bash
ulimit -n 65535
```
在 `/etc/security/limits.conf` 中添加：
```plaintext
* soft nofile 65535
* hard nofile 65535
```

#### 2. 调整内核参数

在 `/etc/sysctl.conf` 中添加或修改以下参数：
```plaintext
net.core.somaxconn = 65535
net.core.netdev_max_backlog = 65535
net.ipv4.tcp_max_syn_backlog = 4096
net.ipv4.ip_local_port_range = 1024 65535
```
然后执行 `sysctl -p` 使其生效。

### Web 服务器优化（以 Nginx 为例）

#### 1. 调整工作进程数量

根据 CPU 核心数量调整 `worker_processes`：
```nginx
worker_processes auto;
```

#### 2. 调整工作连接数

设置每个工作进程的最大连接数：
```nginx
events {
    worker_connections 1024;
}
```

#### 3. 启用缓存

启用 FastCGI 缓存以减少对 PHP-FPM 的请求次数：
```nginx
fastcgi_cache_path /var/cache/nginx levels=1:2 keys_zone=FASTCGI_CACHE:100m inactive=60m;
fastcgi_cache_key "$scheme$request_method$host$request_uri";

server {
    ...
    location ~ \.php$ {
        fastcgi_cache FASTCGI_CACHE;
        fastcgi_cache_valid 200 301 302 60m;
        fastcgi_pass unix:/run/php/php7.4-fpm.sock;
        ...
    }
}
```

### 硬件和架构优化

#### 1. 使用负载均衡

使用负载均衡器（如 Nginx 或 HAProxy）分发请求到多个 PHP-FPM 实例，提升整体处理能力。

#### 2. 垂直扩展和水平扩展

- **垂直扩展**: 增加服务器的 CPU、内存等资源。
- **水平扩展**: 添加更多的服务器，分担负载。

#### 3. 使用反向代理缓存

使用反向代理缓存（如 Varnish 或 Nginx 缓存）缓存静态内容和频繁访问的动态内容，减轻 PHP-FPM 的负载。

### 数据库和应用层优化

#### 1. 数据库优化

确保数据库查询高效，使用索引，优化慢查询。

#### 2. 使用 OPCache 缓存

确保启用了 OPCache（PHP 自带）以缓存 PHP 字节码，减少解析和编译的开销：
```ini
opcache.enable=1
opcache.memory_consumption=128
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=4000
opcache.revalidate_freq=2
```

#### 3. 应用代码优化

- 减少不必要的计算和数据处理。
- 使用合适的数据结构和算法。
- 避免 N+1 查询问题。

通过以上多方面的优化，可以显著提升 PHP-FPM 的性能和并发处理能力，确保在高并发情况下系统的稳定运行。
title: 'nginx  负载均衡 '
author: Laiyong Wang
tags:
  - nginx
  - ''
categories: []
date: 2024-06-15 16:18:00
---
### 简单实例

#### 配置代码

```
http {
    upstream backend {
        server laiyong1.wang;
        server laiyong2.wang;
        server laiyong3.wang;
    }

    server {
        listen 80;
        server_name laiyong.wang;

        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```

#### 配置解释

- `upstream backend`：定义一个名为 `backend` 的上游服务器组，其中包括 `laiyong1.wang`、`laiyong2.wang` 和 `laiyong3.wang`。
- `server`：配置一个服务器块，监听 80 端口，并且将所有请求转发到 `backend` 上游服务器组。
- `proxy_pass http://backend`：将请求代理到 `backend` 上游服务器组。
- `proxy_set_header`：设置代理请求头，确保客户端的真实 IP 和请求头信息被转发到上游服务器。

要配置一个复杂的负载均衡设置，可以涉及多种策略和高级功能，比如健康检查、SSL 终止、缓存等。以下是一个综合示例，展示了如何在 Nginx 中配置一个复杂的负载均衡场景：

### 复杂实例

#### 配置代码

```
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Gzip settings
    gzip on;
    gzip_disable "msie6";

    upstream backend {
        # 使用加权轮询
        server laiyong1.wang weight=5;
        server laiyong2.wang weight=3;
        server laiyong3.wang weight=2;
        
        # 健康检查配置
        health_check interval=10s fails=3 passes=2;
    }

    server {
        listen 80;
        server_name laiyong.wang;

        # 重定向所有HTTP请求到HTTPS
        return 301 https://$host$request_uri;
    }

    server {
        listen 443 ssl http2;
        server_name laiyong.wang;

        # SSL 证书配置
        ssl_certificate /etc/nginx/ssl/nginx.crt;
        ssl_certificate_key /etc/nginx/ssl/nginx.key;
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers HIGH:!aNULL:!MD5;
        
        # SSL 缓存配置
        ssl_session_cache shared:SSL:10m;
        ssl_session_timeout 10m;

        # 使用缓存提高性能
        proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m max_size=10g inactive=60m use_temp_path=off;

        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;

            # 启用缓存
            proxy_cache my_cache;
            proxy_cache_valid 200 302 10m;
            proxy_cache_valid 404 1m;

            # 时间设置
            proxy_connect_timeout 60s;
            proxy_send_timeout 60s;
            proxy_read_timeout 60s;
            send_timeout 60s;
        }

        location /status {
            stub_status on;
            access_log off;
            allow 127.0.0.1; # 只允许本地访问
            deny all;
        }
    }
}
```

### 配置解释

- **基本设置和安装**：定义了基本的 Nginx 设置，如用户、工作进程数、日志文件位置等。
- **日志格式**：定义了访问日志的格式。
- **文件发送和连接管理**：启用了 `sendfile`、`tcp_nopush` 和 `tcp_nodelay` 来优化文件发送和连接管理。
- **gzip 压缩**：启用了 gzip 压缩以减少传输数据量。
- **上游服务器组 `backend`**：
  - 使用加权轮询，将更多请求分配给权重较高的服务器。
  - 配置了健康检查，每 10 秒检查一次服务器状态，连续 3 次失败后认为服务器不可用，连续 2 次成功后认为服务器恢复。
- **HTTP 到 HTTPS 重定向**：将所有 HTTP 请求重定向到 HTTPS。
- **SSL 终止**：
  - 配置了 SSL 证书和密钥。
  - 定义了 SSL 协议和密码套件。
  - 配置了 SSL 会话缓存和超时时间。
- **代理缓存**：
  - 配置了缓存路径、缓存区域、最大缓存大小和缓存失效时间。
  - 启用了代理缓存，并为不同的 HTTP 状态码设置了不同的缓存时间。
- **超时设置**：配置了代理连接、发送和读取超时时间。
- **状态监控**：启用了 `stub_status` 模块，用于查看 Nginx 的状态信息，仅允许本地访问。


### 验证配置

检查 Nginx 配置文件语法是否正确：

```sh
sudo nginx -t
```

如果输出 `syntax is ok` 和 `test is successful`，则表示配置文件没有语法错误。

### 重新加载 Nginx 配置

编辑完配置文件后，保存并重新加载 Nginx 配置：

```sh
sudo nginx -s reload
```

通过这种复杂的 Nginx 负载均衡配置，可以实现高性能、可用性高的服务器架构，支持 SSL 终止、缓存和健康检查等高级功能，提高系统的整体性能和可靠性。






### 负载均衡策略


#### 轮询（默认）

轮询是 Nginx 默认的负载均衡策略，即将请求依次分配到每个服务器。

```nginx
upstream backend {
    server laiyong1.wang;
    server laiyong2.wang;
    server laiyong3.wang;
}
```

#### 加权轮询

加权轮询可以根据服务器的权重分配请求，权重越高，分配的请求越多。

```nginx
upstream backend {
    server laiyong1.wang weight=3;
    server laiyong2.wang weight=2;
    server laiyong3.wang weight=1;
}
```

#### 最少连接数

将请求分配给当前活动连接数最少的服务器。

```nginx
upstream backend {
    least_conn;
    server laiyong1.wang;
    server laiyong2.wang;
    server laiyong3.wang;
}
```

#### IP 哈希

将相同客户端 IP 的请求始终分配给同一台服务器。

```nginx
upstream backend {
    ip_hash;
    server laiyong1.wang;
    server laiyong2.wang;
    server laiyong3.wang;
}
```

### 留个问题

一个请求的生命周期，其过程和数据流转是怎样流转的的以及使用的什么协议
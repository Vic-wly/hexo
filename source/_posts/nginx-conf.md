title: nginx.conf php & hexo 配置
author: Laiyong Wang
date: 2022-10-18 10:09:00
tags:
---
#### PHP环境，记得先运行php-fpm
```
//路径自己看着改
/usr/local/php8/sbin/php-fpm -R
```

```
server {
    listen       80;
    server_name  localhost;
    root /www;

    location / {
        index index.php index.html index.htm;
    }

    #匹配以 .php 结尾的所有请求
    location ~ .php$ {
        #把所有的php请求转发给本机的9000端口，即php-fpm的服务。
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        #这个fastcgi_params文件我们一般都不需要改动，用默认的就好
        include fastcgi_params;
        #$document_root ：Nginx自带变量，表示主目录的位置
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi.conf;
    }
}
```
#### hexo 配置，需要反向代理至4000端口
```
server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html/hexo/public;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
            index index.php index.html index.htm;
            proxy_pass  http://127.0.0.1:4000;
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
```
---
title: Nginx入门基础知识
date: 2024-10-31 23:41:59
tags: [笔记, linux,Nginx]
categories: [软件技术]
---

最近Nginx部署用的多点，学过的tomcat仅仅适合用于java项目。

# 简介

> 开源的高性能 HTTP 服务器和反向代理服务器
>
> IMAP/POP3 邮件代理服务器

## 特点

1. 高并发处理能力
2. 反向代理和负载均衡
3. 动静分离
4. 高效的静态文件服务
5. 支持多种协议

## 反向代理

反向代理充当客户端和后端服务器之间的中间层。它的作用是接收客户端的请求，转发给后端服务器处理，然后将处理结果返回给客户端。使用反向代理的主要目的是提高系统性能、安全性和可扩展性。Nginx 和 Apache 等服务器软件通常被配置为反向代理。

### 单个服务器使用反向代理

可以用于分发请求、缓存静态资源、隐藏后端服务等

**优势：**

**安全性**：隐藏后端服务器的 IP，防止直接攻击。

**可扩展性**：在需要扩展时，可以在同一 Nginx 配置下代理多个应用或服务。

**性能优化**：通过缓存静态资源和 SSL 卸载，提升整体性能。

### 多个服务器使用反向代理

后端服务器不需要直接连接互联网，只要它们和反向代理服务器（例如 Nginx）在同一个内网中。

### 优点

1. **安全性**
    - 后端服务器的 IP 地址不会暴露在公网上，降低了受到外部攻击的风险。
    - 可以通过 Nginx 过滤和控制访问，进一步增强安全性。
2. **负载均衡和容错**
    - Nginx 可以使用负载均衡策略在内网中将请求分发到多台服务器上，提高系统的整体并发能力。
    - 通过健康检查，Nginx 可以检测后端服务器的状态，自动跳过故障服务器，提高系统容错能力。
3. **简化配置**
    - 后端服务器只需与反向代理服务器（Nginx）进行通讯，而不需要考虑公网的 IP 和 DNS 配置，简化了网络配置。
    - 通过内网 IP 直接访问，减少了外部因素的影响，通信效率更高。

---

# Nginx配置案例



## 1. 静态网站服务

```nginx
# 定义一个虚拟主机(server块)
server {
    # 监听80端口(HTTP)
    listen 80;
    
    # 配置服务器名称，可以匹配多个域名
    server_name example.com www.example.com;
    
    # 设置网站根目录路径
    root /var/www/example.com;
    
    # 指定默认索引文件，按顺序尝试
    index index.html index.htm;
    
    # 主location块，处理所有请求
    location / {
        # 尝试按顺序寻找文件：先找精确URI，再找URI/目录，最后返回404
        try_files $uri $uri/ =404;
    }
    
    # 匹配静态资源文件(不区分大小写 ~*)
    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        # 设置缓存过期时间为30天
        expires 30d;
        
        # 关闭访问日志记录(静态资源访问不记录)
        access_log off;
        
        # 开启gzip压缩(需要nginx配置了gzip模块)
        gzip_static on;
    }
    
    # 自定义错误页面
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
}
```

## 2. 反向代理

```nginx
# 定义API服务的虚拟主机
server {
    listen 80;
    server_name api.example.com;
    
    # 客户端请求体最大大小
    client_max_body_size 10m;
    
    # API接口的location配置
    location / {
        # 将请求代理到本地的3000端口服务
        proxy_pass http://localhost:3000;
        
        # 设置传递给后端的重要HTTP头信息
        proxy_set_header Host $host;              # 原始主机头
        proxy_set_header X-Real-IP $remote_addr;  # 客户端真实IP
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; # 代理链IP
        
        # 超时设置(单位：秒)
        proxy_connect_timeout 60;  # 连接后端超时
        proxy_read_timeout 90;     # 读取响应超时
        proxy_send_timeout 90;     # 发送请求超时
        
        # WebSocket支持配置
        proxy_http_version 1.1;    # 使用HTTP/1.1协议
        proxy_set_header Upgrade $http_upgrade;      # 升级头
        proxy_set_header Connection "upgrade";       # 连接类型
        
        # 禁用代理缓冲
        proxy_buffering off;
    }
    
    # 健康检查端点
    location /health {
        proxy_pass http://localhost:3000/health;
        access_log off;  # 不记录健康检查的访问日志
    }
}
```

## 3. 负载均衡

```nginx
# 定义名为backend的上游服务器组
upstream backend {
    # 负载均衡算法(默认是轮询)
    # 可选：least_conn(最少连接)、ip_hash(IP哈希)、hash(自定义哈希)等
    
    # 后端服务器1，weight表示权重(越高分配越多请求)
    server backend1.example.com weight=3;
    
    # 后端服务器2，默认weight=1
    server backend2.example.com;
    
    # 备用服务器，只有当其他服务器都不可用时才启用
    server backend3.example.com backup;
    
    # 连接保持配置
    keepalive 32;  # 保持的连接数
    
    # 健康检查参数(需要nginx plus或第三方模块)
    # health_check interval=5s fails=3 passes=2;
}

server {
    listen 80;
    server_name app.example.com;
    
    # 启用gzip压缩
    gzip on;
    gzip_types text/plain text/css application/json application/javascript;
    
    location / {
        # 将请求代理到上游服务器组
        proxy_pass http://backend;
        
        # 设置必要的头信息
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # 当后端返回特定状态码时的处理
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
        
        # 连接超时设置
        proxy_connect_timeout 2s;
    }
    
    # 负载均衡状态监控页面(需要nginx status模块)
    location /nginx_status {
        stub_status on;
        access_log off;
        allow 192.168.1.0/24;  # 只允许内网IP访问
        deny all;
    }
}
```

## 4. HTTPS安全配置

```nginx
# HTTP强制跳转HTTPS
server {
    listen 80;
    server_name secure.example.com;
    
    # 301永久重定向到HTTPS版本
    return 301 https://$host$request_uri;
    
    # 也可以这样写：
    # location / {
    #     rewrite ^(.*)$ https://$host$1 permanent;
    # }
}

# HTTPS服务器配置
server {
    listen 443 ssl http2;  # 启用HTTP/2协议
    server_name secure.example.com;
    
    # SSL证书路径
    ssl_certificate /etc/letsencrypt/live/secure.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/secure.example.com/privkey.pem;
    
    # 启用OCSP Stapling提高SSL性能
    ssl_stapling on;
    ssl_stapling_verify on;
    
    # SSL协议配置(禁用不安全的旧版本)
    ssl_protocols TLSv1.2 TLSv1.3;
    
    # SSL加密套件配置
    ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384';
    ssl_prefer_server_ciphers on;
    
    # SSL会话缓存设置
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 1d;
    
    # HSTS头(强制浏览器使用HTTPS)
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;
    
    # 其他安全头
    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options DENY;
    add_header X-XSS-Protection "1; mode=block";
    
    # 网站根目录配置
    root /var/www/secure.example.com;
    index index.html;
    
    location / {
        try_files $uri $uri/ =404;
    }
    
    # 禁止访问隐藏文件
    location ~ /\. {
        deny all;
        access_log off;
        log_not_found off;
    }
}
```

## 5. 高级URL重写

```nginx
server {
    listen 80;
    server_name blog.example.com;
    
    root /var/www/blog;
    
    # 默认索引文件(先尝试PHP，再尝试HTML)
    index index.php index.html index.htm;
    
    # 重定向旧博客路径到新路径(301永久重定向)
    location /old-blog {
        # $request_uri包含原始请求的URI和参数
        return 301 /blog$request_uri;
        
        # 也可以使用rewrite实现：
        # rewrite ^/old-blog(.*)$ /blog$1 permanent;
    }
    
    # 博客主路径配置
    location /blog {
        # 重写URL：去掉/blog前缀
        # break标志表示停止后续rewrite处理
        rewrite ^/blog/(.*)$ /$1 break;
        
        # 尝试寻找文件：先找精确URI，再找目录，最后交给index.php处理
        try_files $uri $uri/ /index.php?$args;
    }
    
    # PHP处理配置
    location ~ \.php$ {
        # 包含FastCGI默认参数
        include fastcgi_params;
        
        # 连接到PHP-FPM套接字
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        
        # 设置SCRIPT_FILENAME参数
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        
        # FastCGI缓存配置(可选)
        fastcgi_cache my_cache;
        fastcgi_cache_valid 200 301 302 1h;
        fastcgi_cache_use_stale error timeout invalid_header updating;
    }
    
    # 禁止访问敏感文件
    location ~* (\.env|\.git|\.svn) {
        deny all;
        access_log off;
        log_not_found off;
    }
    
    # 静态文件缓存设置
    location ~* \.(?:ico|css|js|gif|jpe?g|png|webp)$ {
        expires 365d;
        access_log off;
        add_header Cache-Control "public";
    }
}
```


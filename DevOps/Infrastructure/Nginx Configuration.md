---
id: dev-055-0000-0000-0000-000000000039
title: Nginx Configuration
language: markdown
tags: [devops, infrastructure, nginx, web-server]
selection: null
isPinned: false
timestamp: 1781800000017
---
# Nginx Configuration

**Links**: [[Docker Containers]] | [[Kubernetes Basics]] | [[Web Security]] | [[Caching Strategies]] | [[Rate Limiting]] | [[HTTP Protocol]]

## What is Nginx?

High-performance web server, reverse proxy, load balancer, and HTTP cache.

## Reverse Proxy

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

## Static Files

```nginx
server {
    root /var/www/html;
    index index.html;
    location / {
        try_files $uri $uri/ /index.html;
    }
    location ~* \.(css|js|jpg|png)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

## Load Balancing

```nginx
upstream backend {
    least_connections;
    server 10.0.0.1:8000 weight=3;
    server 10.0.0.2:8000;
}
```

## SSL/TLS

```nginx
listen 443 ssl http2;
ssl_certificate /etc/ssl/certs/example.com.pem;
ssl_certificate_key /etc/ssl/private/example.com.key;
ssl_protocols TLSv1.2 TLSv1.3;
```

**Next**: [[Kubernetes Basics]] — Container orchestration
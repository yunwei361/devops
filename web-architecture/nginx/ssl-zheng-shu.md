# SSL 证书

### 节点规划

```
LB: 192.168.31.19

Web01: 192.168.31.21
```



### 案例 - LB 加密，Web01 不加密

#### LB nginx 配置

```nginx
upstream www_pools {
    server 192.168.31.21:80;
}

# 重定向HTTP到HTTPS
server {
    listen 80;
    server_name yunwei361.com www.yunwei361.com;

    return 301 https://$host$request_uri;
}

# HTTPS服务器块
server {
    listen 443 ssl;
    server_name yunwei361.com www.yunwei361.com;

    # SSL KEY
    ssl_certificate /etc/nginx/ssl_keys/yunwei361.pem;
    ssl_certificate_key /etc/nginx/ssl_keys/yunwei361.key;

    # SSL配置
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384';
    ssl_session_timeout 10m;
    ssl_session_cache shared:SSL:10m;

    location / {
        proxy_pass http://www_pools;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-Ip $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

#### Web01 nginx 配置

```nginx
server {
    listen       80;
    server_name  yunwei361.com www.yunwei361.com;
    root   /usr/share/nginx/html;

    access_log /var/log/nginx/www.access.log main;
    error_log /var/log/nginx/www.error.log notice;

    location / {
        index  index.html index.htm;
    }
}
```



### 案例 - LB 加密，Web01 也加密

#### LB nginx 配置

```nginx
upstream www_pools {
    server 192.168.31.21:443;
}

# 重定向HTTP到HTTPS
server {
    listen 80;
    server_name yunwei361.com www.yunwei361.com;

    return 301 https://$host$request_uri;
}

# HTTPS服务器块
server {
    listen 443 ssl;
    server_name yunwei361.com www.yunwei361.com;

    # SSL KEY
    ssl_certificate /etc/nginx/ssl_keys/yunwei361.pem;
    ssl_certificate_key /etc/nginx/ssl_keys/yunwei361.key;

    # SSL配置
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384';
    ssl_session_timeout 10m;
    ssl_session_cache shared:SSL:10m;

    location / {
        proxy_pass https://www_pools;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-Ip $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

#### Web01 nginx 配置

```nginx
server {
    listen 443 ssl;
    server_name yunwei361.com www.yunwei361.com;
    root   /usr/share/nginx/html;

    access_log /var/log/nginx/www.access.log main;
    error_log /var/log/nginx/www.error.log notice;

    # SSL KEY
    ssl_certificate /etc/nginx/ssl_keys/yunwei361.pem;
    ssl_certificate_key /etc/nginx/ssl_keys/yunwei361.key;

    # SSL配置
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384';
    ssl_session_timeout 10m;
    ssl_session_cache shared:SSL:10m;


    location / {
        index  index.html index.htm;
    }
}
```

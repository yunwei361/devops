# 负载均衡

### 负载均衡方法的种类

官方文档：[https://nginx.org/en/docs/http/load\_balancing.html](https://nginx.org/en/docs/http/load\_balancing.html)

Nginx 支持多种负载均衡方法，常见的包括：

* round-robin
* weight
* least\_conn
* ip\_hash
* url\_hash（第三方）
* fail（第三方）

#### round-robin（轮询，Nginx默认的配置）

轮询算法会将每个新的连接请求分配到下一个服务器，如果所有服务器都为down，则请求会分配给down后的服务器

```
upstream myapp1 {
    server 192.168.1.11:80;
    server 192.168.1.12:80;
    server 192.168.1.13:80;
}
```

#### weight（加权轮询）

可以给不同的服务器设置不同的权重

```
upstream myapp1 {
    # 后端按照 3:2:1 的比例处理请求
    server 192.168.1.11:80 weight=3;
    server 192.168.1.12:80 weight=2;
    server 192.168.1.13:80;
}
```

#### least\_conn（最少连接）

将新的连接请求分配到连接数最少的服务器

```
upstream myapp1 {
    least_conn;
    server 192.168.1.11:80;
    server 192.168.1.12:80;
    server 192.168.1.13:80;
}
```

#### ip\_hash（IP哈希）

根据客户端IP地址进行哈希计算，同一个客户端IP地址总是被映射到同一个服务器

```
upstream myapp1 {
    ip_hash;
    server 192.168.1.11:80;
    server 192.168.1.12:80;
    server 192.168.1.13:80;
}
```

#### url\_hash（URL哈希）

根据客户端URL地址进行哈希计算，同一个URL地址总是被映射到同一个服务器

```
upstream myapp1 {
    hash $request_uri;
    server 192.168.1.11:80;
    server 192.168.1.12:80;
    server 192.168.1.13:80;
}
```

#### fair

根据服务器端的响应时间来分配请求，响应时间短的优先分配

```
upstream myapp1 {
    fair;
    server 192.168.1.11:80;
    server 192.168.1.12:80;
    server 192.168.1.13:80;
}
```



### 案例演示（轮询）

#### 服务器规划

```
SLB: 192.168.31.19

web01: 192.168.31.21
web02: 192.168.31.22
```

#### 负载均衡服务器的 nginx 配置（192.168.31.19）

```nginx
upstream www_pool {
    server 192.168.31.21:80;
    server 192.168.31.22:80;
}

server {
    listen       80;
    server_name  www.yunwei361.com;

    access_log /var/log/nginx/www.access.log main;
    error_log /var/log/nginx/www.error.log notice;

    location / {
        proxy_pass http://www_pool;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-Ip $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

#### 后端 web01 服务器的 nginx 配置（192.168.31.21）

```nginx
server {
    listen       80;
    server_name  www.yunwei361.com;
    root   /usr/share/nginx/html;

    access_log /var/log/nginx/www.access.log main;
    error_log /var/log/nginx/www.error.log notice;

    location / {
        index  index.html index.htm;
    }
}
```

#### 后端 web02 服务器的 nginx 配置（192.168.31.22）

```nginx
server {
    listen       80;
    server_name  www.yunwei361.com;
    root   /usr/share/nginx/html;

    access_log /var/log/nginx/www.access.log main;
    error_log /var/log/nginx/www.error.log notice;

    location / {
        index  index.html index.htm;
    }
}
```


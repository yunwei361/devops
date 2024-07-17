# 反向代理

### 服务器规划

```
SLB: 192.168.31.19

web: 192.168.31.21
```



### 反向代理服务器的 nginx 配置（192.168.31.19）

```nginx
server {
    listen       80;
    server_name  proxy.yunwei361.com;

    access_log /var/log/nginx/proxy.access.log main;
    error_log /var/log/nginx/proxy.error.log notice;

    location / {
        proxy_pass http://192.168.31.21;
        # 传递 Host 头
        proxy_set_header Host $http_host;
        # 记录用户的真实IP；
        # 当有多层代理的时候，使用 $proxy_add_x_forwarded_for 会记录每层代理的IP；相当于记录了多个 $remote_addr
        proxy_set_header X-Forwarded-For $remote_addr;
    }
}
```



### 后端 web 服务器的 nginx 配置（192.168.31.21）

```nginx
server {
    listen       80;
    server_name  proxy.yunwei361.com;
    root   /usr/share/nginx/html/proxy;

    access_log /var/log/nginx/proxy.access.log main;
    error_log /var/log/nginx/proxy.error.log notice;

    location / {
        index  index.html index.htm;
    }
}
```

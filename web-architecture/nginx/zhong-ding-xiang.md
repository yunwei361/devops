# 重定向

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>



### return

官方文档：[https://nginx.org/en/docs/http/ngx\_http\_rewrite\_module.html#return](https://nginx.org/en/docs/http/ngx\_http\_rewrite\_module.html#return)

### if

官方文档：[https://nginx.org/en/docs/http/ngx\_http\_rewrite\_module.html#if](https://nginx.org/en/docs/http/ngx\_http\_rewrite\_module.html#if)

#### set

官方文档：[https://nginx.org/en/docs/http/ngx\_http\_rewrite\_module.html#set](https://nginx.org/en/docs/http/ngx\_http\_rewrite\_module.html#set)



### 案例1 - 允许指定请求访问

#### 网站只允许 GET、POST 请求访问，其它方式禁止访问；网站维护时返回503

```nginx
server {
    listen       80;
    server_name  www.yunwei361.com;
    root   /usr/share/nginx/html;

    access_log /var/log/nginx/www.access.log main;
    error_log /var/log/nginx/www.error.log notice;
    
    # 定义 flag 变量，用于网站维护状态，0代表正常，1代表维护；网站维护时将0改为1即可
    set $flag 0;
    if ( $flag = 1 ) {
        return 503;
    }

    # 非 GET、POST 请求，则返回 403
    if ( $request_method !~ "GET|POST" ) {
        return 403;
    }

    location / {
        index  index.html index.htm;
    }
}
```

#### 测试

```bash
# 使用 HEAD 方式进行访问
curl -I www.yunwei361.com
HTTP/1.1 403 Forbidden
Server: nginx/1.24.0
Date: Tue, 28 May 2024 09:02:38 GMT
Content-Type: text/html
Content-Length: 153
Connection: keep-alive
```



### 案例2 - http 重定向至 https

{% hint style="info" %}
下方 server 配置仅为重定向配置，还需搭配 https 的 server 配置，才能实现 https 访问
{% endhint %}

#### return 方式

```nginx
server {
    listen       80;
    server_name  www.yunwei361.com;

    access_log /var/log/nginx/www.access.log main;
    error_log /var/log/nginx/www.error.log notice;

    return 301 https://$server_name$request_uri;
}
```

#### rewrite 方式

```nginx
server {
    listen       80;
    server_name  www.yunwei361.com;

    access_log /var/log/nginx/www.access.log main;
    error_log /var/log/nginx/www.error.log notice;

    # permanent 为 301 永久重定向，不加则默认返回 302
    rewrite ^(.*)$ https://$server_name$1 permanent;
}
```

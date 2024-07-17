# 日志、禁止指定网段访问、缓存时长

### test.conf

```nginx
server {
    listen 80;
    server_name test.yunwei361.com;
    root /usr/share/nginx/html/test;

    access_log  /var/log/nginx/test.access.log main;
    error_log /var/log/nginx/test.error.log notice;

    location / {
        index index.html index.htm;
    }
    location /admin/ {
	# 允许172.31.45.0/24 网段访问
        allow 172.31.45.0/24;
	# 拒绝所有
	deny all;
    }

    # 设置缓存时长
    location ~* \.(html|css|js)$ {
        # max 的时长为10年
        expires max;
    }
    location ~* \.(jpg|jpeg|png|gif|bmp)$ {
        expires 1h;
    }
}
```



### 指定网段访问测试

```bash
# 直接访问报 403
curl http://test.yunwei361.com/admin/
<html>
<head><title>403 Forbidden</title></head>
<body>
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.24.0</center>
</body>
</html>


# 手动指定 hosts 解析后正常
curl -L -H "Host: test.yunwei361.com" http://172.31.45.149/admin/
admin page


curl -vL -H "Host: test.yunwei361.com" http://172.31.45.149/admin/
* processing: http://172.31.45.149/admin/
*   Trying 172.31.45.149:80...
* Connected to 172.31.45.149 (172.31.45.149) port 80
> GET /admin/ HTTP/1.1
> Host: test.yunwei361.com
> User-Agent: curl/8.2.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Server: nginx/1.24.0
< Date: Tue, 21 May 2024 10:14:57 GMT
< Content-Type: text/html
< Content-Length: 11
< Last-Modified: Tue, 21 May 2024 10:03:45 GMT
< Connection: keep-alive
< ETag: "664c7181-b"
< Accept-Ranges: bytes
< 
admin page
* Connection #0 to host 172.31.45.149 left intact
```



### 缓存时长查看

<figure><img src="../../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>


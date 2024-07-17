# 显示目录内容、显示时间、人类可读文件大小、中文字符

### dir\_index.conf

```nginx
server{
    listen 80;
    server_name test.yunwei361.com;
    root /usr/share/nginx/html/test/svip;

    # 没有 index.html 时，列出目录内容
    autoindex on;
    # 显示本地时区时间，而非 UTC 时间
    autoindex_localtime on;
    # 显示文件大小为人类易读格式
    autoindex_exact_size off;
    # 配置 utf8 编码后显示中文不乱码
    charset utf8;

    location / {
        index index.html index.htm;
    }
}
```



### 访问

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

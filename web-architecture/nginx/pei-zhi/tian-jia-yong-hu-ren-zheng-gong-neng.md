# 添加用户认证功能

### 安装认证软件

```bash
yum install httpd-tools -y

# 创建文件并添加用户，此处添加的用户为 用户名：will 密码：123456
# -c 创建文件
htpasswd -bc /etc/nginx/passwd_user will 123456

# 添加用户
# 第二次如果还加 -c 则会清空文件
htpasswd -b /etc/nginx/passwd_user admin admin

# 新版本无需修改文件权限和属主，即可直接使用；
# 老版本若遇到不生效时可排查权限问题

# 查看
cat /etc/nginx/passwd_user
```



### auth.conf

```nginx
server {
    listen 80;
    server_name test.yunwei361.com;
    root /usr/share/nginx/html/test;

    autoindex on;
    autoindex_localtime on;
    autoindex_exact_size off;
    charset utf8;

    location / {
        index index.html index.htm;
    }

    # 访问 test.yunwei361.com/svip/ 就会触发认证
    location /svip/ {
	auth_basic "请输入密码：";
	auth_basic_user_file /etc/nginx/passwd_user;
    }

}
```



### 访问

<figure><img src="../../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

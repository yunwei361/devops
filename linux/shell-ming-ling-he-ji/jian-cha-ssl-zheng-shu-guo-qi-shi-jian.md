# 检查 SSL 证书过期时间

### 检查已建立网站的证书

```bash
curl -Lvs https://www.qq.com -o /dev/null |& grep "expire date"
# 输出：
*  expire date: Feb  4 23:59:59 2025 GMT

# curl 选项解释
# -L 重定向
# -v 显示过程
# -s 安静模式，不显示进度条和错误信息
# -o 响应信息重定向
# ｜& 把错误输出也通过管道符传递
```

#### 计算证书剩余有效时间

```bash
# 直接取出过期时间
curl -Lvs https://www.qq.com -o /dev/null |& awk -F ': ' '/expire date/ {print $2}'
# 输出：
Feb  4 23:59:59 2025 GMT

# 将取出的时间转换为秒
date -d "Feb  4 23:59:59 2025 GMT" +%s

# 今天的秒
date +%s

# 将取出结果转换的秒和今天的秒进行相应计算即可得出离过期还剩的天数
echo "(1738713599-1717139305)/60/60/24" | bc
```



### 检查证书文件

```bash
openssl x509 -in /etc/nginx/ssl_keys/yunwei361.pem -noout -dates
# 输出：
notBefore=May 30 00:00:00 2024 GMT
notAfter=Aug 27 23:59:59 2024 GMT


# /etc/nginx/ssl_keys/yunwei361.pem 是本地证书公钥文件

# openssl x509：调用OpenSSL命令行工具来处理X.509证书
# -in /etc/nginx/ssl_keys/yunwei361.pem：指定输入文件，即包含证书的文件路径
# -noout：不输出证书的内容，仅显示指定的信息
# -dates：显示证书的起始日期（Not Before）和截止日期（Not After）

# 组合起来，这个命令会读取 /etc/nginx/ssl_keys/yunwei361.pem 文件，并仅显示证书的有效期，不输出其他信息。
```

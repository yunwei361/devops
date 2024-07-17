# proxychains（让 Linux 命令可以使用 socks 代理）

### 安装

```bash
yum install epel-release -y

yum install proxychains-ng -y
```



### 配置

```bash
vim /etc/proxychains.conf

# 按照代理列表的顺序进行连接，列表有一个有效的代理就行
dynamic_chain

# [ProxyList] 代理列表下方加代理服务器
socks5 192.168.31.2 7890
```



### 使用

```bash
# 命令前加 proxychains4 即可
proxychains4 curl -I https://www.google.com
```

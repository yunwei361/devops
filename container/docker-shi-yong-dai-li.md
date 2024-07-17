# Docker 使用代理

### 命令

```bash
# 1. 查看 Docker service 文件位置
systemctl cat docker

# 2. 在[Service]下方添加代理（注意：Docker 无法使用 socks 代理）
Environment="HTTP_PROXY=http://192.168.31.2:7890"
Environment="HTTPS_PROXY=http://192.168.31.2:7890"

# 3. 重启 Docker 服务
systemctl daemon-reload
systemctl restart docker
```

<figure><img src="../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

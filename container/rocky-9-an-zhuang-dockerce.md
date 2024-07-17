# Rocky 9 安装 Docker-CE



```bash
wget -O /etc/yum.repos.d/docker-ce.repo https://download.docker.com/linux/centos/docker-ce.repo

sudo sed -i 's+download.docker.com+mirrors.cloud.tencent.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo

yum makecache

yum install docker-ce -y

cat > /etc/docker/daemon.json << EOF
{
   "registry-mirrors": [
   "https://mirror.ccs.tencentyun.com",
   "https://2cky5149.mirror.aliyuncs.com"
  ]
}
EOF

systemctl start docker
systemctl enable docker
systemctl status docker

```


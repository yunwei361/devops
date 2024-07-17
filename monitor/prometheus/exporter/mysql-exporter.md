# MySQL Exporter

### 环境准备

#### MySQL Docker 启动

```bash
docker run --name mysql8 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8
```

#### 安装 MySQL 客户端

```bash
yum install mariadb -y
```

#### 创建 MySQL 的 exporter 用户

```sql
# mysql -u root -p123456 -h 127.0.0.1

# 本地访问时使用此用户
CREATE USER 'exporter'@'localhost' IDENTIFIED BY '123456' WITH MAX_USER_CONNECTIONS 3;
GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'exporter'@'localhost';

# 使用 Docker 容器部署 exporter 时，访问使用的此用户；否则会报连接拒绝或权限拒绝
CREATE USER 'exporter'@'172.%' IDENTIFIED BY '123456' WITH MAX_USER_CONNECTIONS 3;
GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'exporter'@'172.%';

select user,host from mysql.user;
```



### 部署 exporter

#### 配置文件准备

```bash
cat > my_exporter.conf << EOF
[client]
host = 192.168.31.22
user = exporter
password = 123456
EOF
```

#### Docker 方式部署

> **DockerHub:**
>
> https://github.com/prometheus/mysqld\_exporter

```bash
docker pull prom/mysqld-exporter

docker run -d \
  --name mysqld-exporter \
  --restart unless-stopped \
  -p 9104:9104 \
  -v `pwd`/my_exporter.conf:/etc/my_exporter.conf \
  -e "DATA_SOURCE_NAME='exporter:123456@(127.0.0.1:3306)/'" \
  prom/mysqld-exporter \
  --config.my-cnf=/etc/my_exporter.conf
```

#### 访问

```bash
curl -s 192.168.31.22:9104/metrics | head
```

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

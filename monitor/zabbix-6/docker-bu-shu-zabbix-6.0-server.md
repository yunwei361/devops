# Docker 部署 Zabbix 6.0 server

该示例演示了如何运行支持 MySQL 数据库的 Zabbix 服务器、基于 Nginx Web 服务器的 Zabbix Web 界面和 Zabbix Java 网关。



### 创建专用于 Zabbix 组件容器的网络

```bash
docker network create --subnet 172.20.0.0/16 --ip-range 172.20.240.0/20 zabbix-net
```



### 启动空 MySQL 服务器实例

```bash
docker run --name mysql-server -t \
             -e MYSQL_DATABASE="zabbix" \
             -e MYSQL_USER="zabbix" \
             -e MYSQL_PASSWORD="zabbix_pwd" \
             -e MYSQL_ROOT_PASSWORD="root_pwd" \
             -e TZ=Asia/Shanghai \
             --network=zabbix-net \
             --restart unless-stopped \
             -d mysql:8.0 \
             --character-set-server=utf8 --collation-server=utf8_bin \
             --default-authentication-plugin=mysql_native_password
```



### 启动 Zabbix Java 网关实例

```bash
docker run --name zabbix-java-gateway -t \
             --network=zabbix-net \
             -e TZ=Asia/Shanghai \
             --restart unless-stopped \
             -d zabbix/zabbix-java-gateway:alpine-6.0-latest
```



### 启动 Zabbix 服务器实例并将该实例与创建的 MySQL 服务器实例链接

```bash
docker run --name zabbix-server-mysql -t \
             -e TZ=Asia/Shanghai \
             -e DB_SERVER_HOST="mysql-server" \
             -e MYSQL_DATABASE="zabbix" \
             -e MYSQL_USER="zabbix" \
             -e MYSQL_PASSWORD="zabbix_pwd" \
             -e MYSQL_ROOT_PASSWORD="root_pwd" \
             -e ZBX_JAVAGATEWAY="zabbix-java-gateway" \
             --network=zabbix-net \
             -p 10051:10051 \
             --restart unless-stopped \
             -d zabbix/zabbix-server-mysql:alpine-6.0-latest
```

{% hint style="info" %}
Zabbix server instance exposes 10051/TCP port (Zabbix trapper) to host machine.
{% endhint %}



### 启动 Zabbix Web 界面并将实例与创建的 MySQL 服务器和 Zabbix 服务器实例链接

```bash
docker run --name zabbix-web-nginx-mysql -t \
             -e TZ=Asia/Shanghai \
             -e ZBX_SERVER_HOST="zabbix-server-mysql" \
             -e DB_SERVER_HOST="mysql-server" \
             -e MYSQL_DATABASE="zabbix" \
             -e MYSQL_USER="zabbix" \
             -e MYSQL_PASSWORD="zabbix_pwd" \
             -e MYSQL_ROOT_PASSWORD="root_pwd" \
             --network=zabbix-net \
             -p 80:8080 \
             --restart unless-stopped \
             -d zabbix/zabbix-web-nginx-mysql:alpine-6.0-latest
```

{% hint style="info" %}
Zabbix web interface instance exposes 80/TCP port (HTTP) to host machine.
{% endhint %}

#### 访问 Zabbix Web 对应的 IP 和暴露的端口（账号：Admin 密码：zabbix）

<figure><img src="../../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

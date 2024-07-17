# 常用服务部署

### Redis 5.0

```bash
docker pull redis:5.0
 
docker run --name redis5.0 --restart always -d \
  -v /data/docker_data/redis:/data \
  -p 6379:6379 \
  redis:5.0 \
  redis-server --save 60 1 --loglevel warning
```



### MySQL 8.0

```bash
docker pull mysql:8.0
 
docker run --name mysql8.0 --restart always -d \
  -v /data/docker_data/mysql:/var/lib/mysql \
  -p 3306:3306 \
  -p 33060:33060 \
  -e MYSQL_ROOT_PASSWORD=sensors_2024. \
  mysql:8.0
```



### Kafka 3.7.0

```bash
docker pull bitnami/kafka:3.7.0
 
# 需将要挂载的宿主机目录属主和属组设置为"id"为 1001 的用户，此处添加"kafka"用户，并指定 uid、gid 为1001
useradd kafka -u 1001
 
# 新版kafka不需要zookeeper（2.8.0版本后就不依赖zk了）
docker run --name kafka3.7.0 --restart always -d --hostname kafka-server \
    -v /data/docker_data/kafka:/bitnami/kafka \
    -p 9092:9092 \
    -p 9093:9093 \
    -e KAFKA_CFG_NODE_ID=0 \
    -e KAFKA_CFG_PROCESS_ROLES=controller,broker \
    -e KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093 \
    -e KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT \
    -e KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@kafka-server:9093 \
    -e KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER \
    bitnami/kafka:3.7.0
```

{% hint style="info" %}
经测试，Docker 服务连接 kafka 可能会报 unknow host，只需在 docker run 启动相关容器时添加如下参数即可（添加后可在 /etc/hosts 中查看到解析）

\--add-host kafka-server:192.168.1.10
{% endhint %}



### Nacos 2.3.1

```bash
docker pull nacos/nacos-server:v2.3.1
 
docker run --name nacos2.3.1 --restart always -d \
  -e MODE=standalone \
  -v /data/docker_data/nacos:/home/nacos/data \
  -p 8848:8848 \
  -p 9848:9848 \
  -p 9849:9849 \
  nacos/nacos-server:v2.3.1
 
# 此处将容器里的 /home/nacos/data 做持久化操作
```



### Jenkins 2.440.2-lts

```bash
docker pull jenkins/jenkins:2.440.2-lts
 
# 修改宿主机所需数据持久化目录的属主和属组为容器中jenkins用户对应的属主和属组
chown -R 1000:1000 /data/docker_data/jenkins/jenkins_home
 
docker run --name jenkins2.440.2 --restart always -d \
  -p 8080:8080 \
  -p 50000:50000 \
  -v /data/docker_data/jenkins/jenkins_home:/var/jenkins_home \
  jenkins/jenkins:2.440.2-lts
 
# 容器启动后访问宿主机ip:8080即可打开jenkins的web页面
# 推荐安装默认的插件，还需安装插件：maven、gitee、ssh、docker
```

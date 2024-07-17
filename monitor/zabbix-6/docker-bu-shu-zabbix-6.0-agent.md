# Docker 部署 Zabbix 6.0 agent

### 主机为非 server 端所在主机

```bash
docker run --name zabbix-agent2 \
  -e TZ=Asia/Shanghai \
  -e ZBX_HOSTNAME="Zabbix server" \
  -e ZBX_SERVER_HOST="192.168.31.19" \
  -e ZBX_SERVER_ACTIVE="192.168.31.19" \
  -p 10050:10050 \
  --init -d \
  zabbix/zabbix-agent2:6.0-ol-latest
```



### 主机为 server 端所在主机

指定 server 端 IP 时，需要指定为 docker network 中为 zabbix 单独创建的网络的 gateway（具体名称见部署 server 端时的命令）

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

```bash
docker run --name zabbix-agent2 \
  -e TZ=Asia/Shanghai \
  -e ZBX_HOSTNAME="Zabbix server" \
  -e ZBX_SERVER_HOST="172.20.240.0" \
  -e ZBX_SERVER_ACTIVE="172.20.240.0" \
  --network=zabbix-net \
  -p 10050:10050 \
  --init -d \
  zabbix/zabbix-agent2:6.0-ol-latest
```


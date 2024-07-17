# Prometheus

### Prometheus 的下载地址

> https://prometheus.io/download/

此次演示版本如下：

prometheus 2.37.6

```
wget https://github.com/prometheus/prometheus/releases/download/v2.37.6/prometheus-2.37.6.linux-amd64.tar.gz
```

node\_exporter 1.5.0

```
wget https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz
```

pushgateway 1.9.0

```
wget https://github.com/prometheus/pushgateway/releases/download/v1.9.0/pushgateway-1.9.0.linux-amd64.tar.gz
```

alertmanager 0.25.0

```
wget https://github.com/prometheus/alertmanager/releases/download/v0.25.0/alertmanager-0.25.0.linux-amd64.tar.gz
```



### 部署 Prometheus

```bash
# 获取程序文件
cd /tmp

wget https://github.com/prometheus/prometheus/releases/download/v2.37.6/prometheus-2.37.6.linux-amd64.tar.gz

tar xf prometheus-2.37.6.linux-amd64.tar.gz

mkdir -p /usr/local/prometheus

cp -afr /tmp/prometheus-2.37.6.linux-amd64/* /usr/local/prometheus/

```

```bash
# 使用 systemd 托管，写入 service 文件
cat > /etc/systemd/system/prometheus.service << EOF
[Unit]
Description="prometheus"
Documentation=https://prometheus.io/
After=network.target

[Service]
Type=simple

ExecStart=/usr/local/prometheus/prometheus  --config.file=/usr/local/prometheus/prometheus.yml --storage.tsdb.path=/usr/local/prometheus/data --web.enable-lifecycle --enable-feature=remote-write-receiver --query.lookback-delta=2m --web.enable-admin-api

Restart=on-failure
SuccessExitStatus=0
LimitNOFILE=65536
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=prometheus


[Install]
WantedBy=multi-user.target
EOF

```

```bash
# 启动
systemctl enable prometheus
systemctl start prometheus
systemctl status prometheus

```

```
# 启动参数解释
--config.file=/opt/prometheus/prometheus.yml
指定 Prometheus 的配置文件路径

--storage.tsdb.path=/opt/prometheus/data
指定 Prometheus 时序数据的硬盘存储路径

--web.enable-lifecycle
启用生命周期管理相关的 API，比如调用 /-/reload 接口就需要启用该项

--enable-feature=remote-write-receiver
启用 remote write 接收数据的接口，启用该项之后，categraf、grafana-agent 等 agent 就可以通过 /api/v1/write 接口推送数据给 Prometheus 

--query.lookback-delta=2m
即时查询在查询当前最新值的时候，只要发现这个参数指定的时间段内有数据，就取最新的那个点返回，这个时间段内没数据，就不返回了 

--web.enable-admin-api
启用管理性 API，比如删除时间序列数据的 /api/v1/admin/tsdb/delete_series 接口
```



### 访问：

> http://127.0.0.1:9090
>
> http://127.0.0.1:9090/metrics


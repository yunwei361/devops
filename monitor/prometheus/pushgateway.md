# Pushgateway

### 部署 Pushgateway

```bash
cd /tmp

wget https://github.com/prometheus/pushgateway/releases/download/v1.9.0/pushgateway-1.9.0.linux-amd64.tar.gz

tar xf pushgateway-1.9.0.linux-amd64.tar.gz -C /usr/local

cd /usr/local

ln -s pushgateway-1.9.0.linux-amd64 pushgateway

# 启动 pushgateway
nohup /usr/local/pushgateway/pushgateway &>> pushgateway.log &
```



### 修改 Prometheus 配置文件

```yaml
# vim prometheus.yml
# 路径：/usr/local/prometheus/prometheus.yml

# 在 scrape_configs 下新增 job_name
scrape_configs:
  - job_name: 'pushgateway'
    static_configs:
    - targets: ['192.168.31.23:9091']
```



### 重新加载配置

```bash
# 让 Prometheus 重新读取配置
kill -HUP `pidof prometheus`
```



### 访问 metrics

```bash
curl -s 192.168.31.23:9091/metrics
```



### 测试脚本推送

可将脚本设置为定时任务（分钟级）或者死循环（秒级），即可实现定时推送监控指标

```sh
cat get_cpu_metrics.sh 
#!/bin/bash

# 1.vars
job="pushgateway"
instance="192.168.31.23:9091"
pushgw="http://192.168.31.23:9091"
cores=$(lscpu | awk '/^CPU\(s\)/{print $2}')


# 2.commands
# 以下命令可实现推送自定义的监控指标（此处为获取 cpu 核心数）至 pushgateway
#   cpu_cores 为自定义的监控指标
#   @- 表示数据来自管道符
echo "cpu_cores $cores" | \
curl --data-binary @- ${pushgw}/metrics/job/${job}/instance/${instance}
```



### 查看

#### 浏览器查看

<figure><img src="../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

#### 命令行查看

```bash
curl 192.168.31.23:9091/metrics | grep cpu_cores
```

<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>


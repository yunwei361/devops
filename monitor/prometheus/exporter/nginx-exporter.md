# Nginx Exporter

### Docker 部署

> DockerHub:
>
> [https://hub.docker.com/r/nginx/nginx-prometheus-exporter](https://hub.docker.com/r/nginx/nginx-prometheus-exporter)

```bash
docker run --name nginx_exporter \
  -d -p 9113:9113 \
  nginx/nginx-prometheus-exporter:latest \
  --nginx.scrape-uri=http://192.168.31.22/nginx_status

# --nginx.scrape-uri 指定 stub_status 地址
```

<figure><img src="../../../.gitbook/assets/image (79).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

#### 修改 Prometheus 配置文件

```yaml
# prometheus.yml
scrape_configs:
  - job_name: "nginx_exporter"
    static_configs:
      - targets:
        - "192.168.31.22:9113"
```

#### 重载配置文件

```bash
kill -HUP `pidof prometheus`
```

#### 查看 Prometheus targets 页面

<figure><img src="../../../.gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>



### 二进制部署

> [https://github.com/nginxinc/nginx-prometheus-exporter](https://github.com/nginxinc/nginx-prometheus-exporter)


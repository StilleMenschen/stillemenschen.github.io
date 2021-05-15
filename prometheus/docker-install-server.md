# Docker 安装 Server

## 安装

这个示例将采集的客户端也一同部署在同一台服务器中

- 由于 Prometheus 对时间同步有要求，因此将宿主机的时区文件挂载到容器内
- 使用 Docker 网关桥接容器网络
- 开放 Prometheus 服务和 Exporter 服务端口
- 开放 Grafana 可视化服务端口
- 配置 Node Exporter 的目录访问权限

首先是 docker-compose 文件

```yml
version: "3.5"
services:
  prom-server:
    image: prom/prometheus:v2.26.0
    container_name: prom-server
    # 服务端注册端口
    ports:
      - "9090:9090"
    # 挂载 Prometheus 配置文件和宿主机的时区
    volumes:
      - "/etc/localtime:/etc/localtime:ro"
      - "/usr/local/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml"
    networks:
      prometheus-net:
        aliases:
          - prom-server
    depends_on:
      - prom-node-exporter
  prom-node-exporter:
    image: prom/node-exporter:v1.1.2
    container_name: prom-node-exporter
    # 开放特殊目录访问权限
    privileged: true
    # 客户端采集端口
    ports:
      - "9100:9100"
    volumes:
      - "/proc:/host/proc"
      - "/sys:/host/sys"
      - "/etc/localtime:/etc/localtime:ro"
    command:
      [
        "--path.procfs=/host/proc",
        "--path.sysfs=/host/sys",
        '--collector.filesystem.ignored-mount-points="^/(sys|proc|dev|host|etc)/?"'
      ]
    networks:
      prometheus-net:
        aliases:
          - prom-node-exporter
  # 可视化界面支持
  grafana-server:
    image: grafana/grafana:7.5.5
    container_name: grafana-server
    # 开放Web页面端口
    ports:
      - "3000:3000"
    # Web登录密码，账号是admin
    environment:
      GF_SECURITY_ADMIN_PASSWORD: "grafana"
    volumes:
      - "/etc/localtime:/etc/localtime:ro"
    networks:
      prometheus-net:
        aliases:
          - grafana-server

networks:
  prometheus-net:
    name: prometheus-net
```

接着是最基本的 Prometheus 配置文件（prometheus.yml）

```yml
global:
  scrape_interval: 30s # By default, scrape targets every 15 seconds.

  # Attach these labels to any time series or alerts when communicating with
  # external systems (federation, remote storage, Alertmanager).
  # external_labels:
  #   monitor: 'codelab-monitor'

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "node"

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 1m
    # 客户端配置中的 prom-node-exporter 实际是引用了Docker网关中容器的网络别名（域名）
    static_configs:
      - targets: ["localhost:9090", "prom-node-exporter:9100"]
```

可以在宿主机运行以下命令测试

```bash
# 采集客户端 Node Exporter
curl -sL http://localhost:9100/metrics | tail
# Prometheus 服务
curl -sL http://localhost:9090/api/v1/query?query=up | python -m json.tool
# Grafana 服务
curl -IL http://localhost:3000
```

在 Grafana 中配置好 Data Source 和 Dashboard 就可以以之间看了

## 参考文档

- Prometheus 安装 https://prometheus.io/docs/prometheus/latest/installation/
- Prometheus 入门 https://prometheus.io/docs/prometheus/latest/getting_started/
- Prometheus 监控 Linux 虚拟机 https://lixinkuan.blog.csdn.net/article/details/113631550
- Prometheus 监控 docker 容器 https://blog.csdn.net/lixinkuan328/article/details/107780118
- Grafana 仪表盘 https://grafana.com/grafana/dashboards

Last Modified 2021-05-15

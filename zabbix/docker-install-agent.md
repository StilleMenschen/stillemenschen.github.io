# Docker 安装 Zabbix Agent

## 安装

- 使用基于`alpine`的镜像
- 使用`root`用户运行
- 使用`host`网络模式直接绑定端口到宿主机，不使用 docker 的隔离网络

注意 compose 文件中的`192.168.1.1`和`10051`要替换成实际的`Zabbix Server`地址和端口

```yml
version: "3.3"
services:
  zabbix-agent:
    image: zabbix/zabbix-agent:alpine-5.2-latest
    container_name: zabbix-agent
    user: root
    privileged: true
    environment:
      ZBX_HOSTNAME: "zabbix-agent-node1"
      ZBX_SERVER_HOST: "192.168.1.1"
      ZBX_SERVER_PORT: "10051"
    # 直接将agent的10050端口绑定到宿主机
    network_mode: "host"
```

## 参考文档

- 官方 Docker 安装参考 https://www.zabbix.com/documentation/current/manual/installation/containers
- Agent 镜像 https://hub.docker.com/r/zabbix/zabbix-agent

Last Modified 2021-05-03

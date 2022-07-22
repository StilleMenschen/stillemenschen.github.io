# Docker 安装 Zabbix Server

## 安装

- 使用基于`alpine`的镜像
- 使用`MySQL`数据库，指定`UTF-8`字符集
- 定义多个服务连接到同一个 Docker 桥接网络，使用网络别名（aliases）相互访问
- 开放`10051`服务注册端口到宿主机
- 开放`80`监控服务的 Web 页面端口到宿主机
- 添加一个`Agent`客户端到宿主机

```yml
version: "3.3"
services:
  # MySQL数据库
  zabbix-mysql-server:
    image: mysql:8.0.22
    container_name: zabbix-mysql-server
    command:
      [
        "--character-set-server=utf8",
        "--collation-server=utf8_bin",
        "--default-authentication-plugin=mysql_native_password",
      ]
    restart: unless-stopped
    tty: true
    environment:
      MYSQL_DATABASE: "zabbix"
      MYSQL_USER: "zabbix"
      MYSQL_PASSWORD: "zabbix_pwd"
      MYSQL_ROOT_PASSWORD: "root_pwd"
    networks:
      zabbix-net:
        aliases:
          - zabbix-mysql-server
  # Java网关
  zabbix-java-gateway:
    image: zabbix/zabbix-java-gateway:alpine-5.2-latest
    container_name: zabbix-java-gateway
    restart: unless-stopped
    tty: true
    depends_on:
      - zabbix-mysql-server
    networks:
      zabbix-net:
        aliases:
          - zabbix-java-gateway
  # 主服务
  zabbix-server-mysql:
    image: zabbix/zabbix-server-mysql:alpine-5.2-latest
    container_name: zabbix-server-mysql
    restart: unless-stopped
    tty: true
    environment:
      DB_SERVER_HOST: "zabbix-mysql-server"
      MYSQL_DATABASE: "zabbix"
      MYSQL_USER: "zabbix"
      MYSQL_PASSWORD: "zabbix_pwd"
      MYSQL_ROOT_PASSWORD: "root_pwd"
      ZBX_JAVAGATEWAY: "zabbix-java-gateway"
    # 开放服务注册端口给客户端注册监听
    ports:
      - "10051:10051"
    networks:
      zabbix-net:
        aliases:
          - zabbix-server-mysql
    depends_on:
      - zabbix-mysql-server
      - zabbix-java-gateway
  # nginx服务
  zabbix-web-nginx-mysql:
    image: zabbix/zabbix-web-nginx-mysql:alpine-5.2-latest
    container_name: zabbix-web-nginx-mysql
    restart: unless-stopped
    tty: true
    environment:
      ZBX_SERVER_HOST: "zabbix-server-mysql"
      DB_SERVER_HOST: "zabbix-mysql-server"
      MYSQL_DATABASE: "zabbix"
      MYSQL_USER: "zabbix"
      MYSQL_PASSWORD: "zabbix_pwd"
      MYSQL_ROOT_PASSWORD: "root_pwd"
      PHP_TZ: "Asia/Shanghai"
    # 开放Web服务端口
    ports:
      - "80:8080"
    volumes:
      - "/etc/localtime:/etc/localtime:ro"
    networks:
      zabbix-net:
        aliases:
          - zabbix-web-nginx-mysql
    depends_on:
      - zabbix-mysql-server
      - zabbix-java-gateway
      - zabbix-server-mysql
  # 本机客户端
  zabbix-agent:
    image: zabbix/zabbix-agent:alpine-5.2-latest
    container_name: zabbix-agent
    user: root
    privileged: true
    environment:
      ZBX_HOSTNAME: "zabbix-agent"
      ZBX_SERVER_HOST: "zabbix-server-mysql"
      ZBX_SERVER_PORT: "10051"
    networks:
      zabbix-net:
        aliases:
          - zabbix-agent

networks:
  # 可能存在子网冲突的情况，视情况修改
  zabbix-net:
    ipam:
      config:
        - subnet: 172.22.0.0/16
```

>使用此 compose 文件安装服务时，默认的 Web 服务登录账号密码为`Admin/zabbix`，如果默认密码有变更，可关
>注[Zabbix 登录配置参考页](https://www.zabbix.com/documentation/current/manual/quickstart/login)

>如果宿主机上有其它正在运行的 docker 容器，自定义的网卡子网（网段）可能会存在冲突的情况，需要找出与宿主机和当前服务器网
>络环境中不冲突的网段进行设置

## 参考文档

- 官方 Docker 安装参考 https://www.zabbix.com/documentation/current/manual/installation/containers
- 基于 MySQL 的服务镜像 https://hub.docker.com/r/zabbix/zabbix-server-mysql/
- Java 网关镜像 https://hub.docker.com/r/zabbix/zabbix-java-gateway/
- 基于 MySQL 和 Nginx 的 Web 服务镜像 https://hub.docker.com/r/zabbix/zabbix-web-nginx-mysql/
- Docker 官方 MySQL 镜像（不是 MySQL 官方） https://hub.docker.com/_/mysql

Last Modified 2021-05-03

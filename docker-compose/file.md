# docker-compose.yml

## 简介

Compose文件是一个YAML文件，用于定义服务，网络和卷。撰写文件的默认路径是`./docker-compose.yml`。服务定义包含应用于该服务启动的每个容器的配置，就像将命令行参数传递给`docker run`一样。同样，网络和卷定义类似于`docker network create`和`docker volume create`

与`docker run`一样，默认情况下，会尊重`Dockerfile`中指定的选项，例如`CMD`，`EXPOSE`，`VOLUME`，`ENV`；无需在`docker-compose.yml`中再次指定它们

# services

## build

简单指定一个上下文路径，默认读取指定路径下的`Dockerfile`
```
version: "3.9"
services:
  webapp:
    build: ./dir
```
如果同时指定`build`和`image`，则会以`image`中指定的名称和标签来命名构建的镜像
```
build: ./dir
image: webapp:tag
```

### 子参数
- `context` 构建上下文

```
build:
  context: ./dir
```
- `dockerfile` 自定义构建文件名

```
build:
  context: .
  dockerfile: Dockerfile-alternate
```
- `args` 定义构建参数

```
build:
  context: .
  args:
    buildno: 1
    gitcommithash: cdc3b19
```
```
build:
  context: .
  args:
    - buildno=1
    - gitcommithash=cdc3b19
```
- `cache_from` 引擎用于缓存解析的图像列表

```
build:
  context: .
  cache_from:
    - alpine:latest
    - corp/web_app:3.14
```
- `extra_hosts` 自定义域名映射

```
extra_hosts:
  - "somehost:162.242.195.82"
  - "otherhost:50.31.209.229"
```
- `labels` 定义构建元数据

```
build:
  context: .
  labels:
    com.example.description: "Accounting webapp"
    com.example.department: "Finance"
    com.example.label-with-empty-value: ""
```
```
build:
  context: .
  labels:
    - "com.example.description=Accounting webapp"
    - "com.example.department=Finance"
    - "com.example.label-with-empty-value"
```
- `network` 设置`RUN`指令的网络连接

```
build:
  context: .
  network: host
```
```
build:
  context: .
  network: custom_network_1
```
或者指定为`none`表示禁用自定义网络
- `shm_size` 设置此构建容器的`/dev/shm`分区的大小

```
build:
  context: .
  shm_size: '2gb'
```
```
build:
  context: .
  shm_size: 10000000
```
- `target` 自定义构建目标（多阶段构建）

```
build:
  context: .
  target: prod
```

## command

覆盖默认的命令
```
command: bundle exec thin -p 3000
```
```
command: ["bundle", "exec", "thin", "-p", "3000"]
```

## container_name

自定义容器名称
```
container_name: my-web-container
```

## devices

自定义设备映射
```
devices:
  - "/dev/ttyUSB0:/dev/ttyUSB0"
```

## depends_on

表达服务之间的依赖性
- 使用`docker-compose up`启动时，`db`和`redis`会先于`web`启动
- 使用`docker-compose up SERVICE`指定服务启动时，会自动引入依赖的服务，如`docker-compose up web`会自动引入`db`和`redis`
- 使用`docker-compose stop`停止时，`web`会先于`db`和`redis`停止
```
  version: "3.9"
  services:
    web:
      build: .
      depends_on:
        - db
        - redis
    redis:
      image: redis
    db:
      image: postgres
```

## dns

DNS服务IP
```
dns: 8.8.8.8
```
```
dns:
  - 8.8.8.8
  - 9.9.9.9
```

## dns_search

DNS服务域
```
dns_search: example.com
```
```
dns_search:
  - dc1.example.com
  - dc2.example.com
```

## entrypoint

覆盖镜像的默认`ENTRYPOINT`
```
entrypoint: /code/entrypoint.sh
```
```
entrypoint: ["php", "-d", "memory_limit=-1", "vendor/bin/phpunit"]
```

## env_file

环境变量文件
```
env_file: .env
```
```
env_file:
  - ./common.env
  - ./apps/web.env
  - /opt/runtime_opts.env
```
文件内容
```
# Set Rails/Rack environment
RACK_ENV=development
```

## environment

环境变量
```
environment:
  RACK_ENV: development
  SHOW: 'true'
  SESSION_SECRET:
```
```
environment:
  - RACK_ENV=development
  - SHOW=true
  - SESSION_SECRET
```

## extends

拓展本文件内已经声明过的服务
```
extends: web
```
拓展其它`docker-compose`文件中的服务配置，可参考[官方文档](https://docs.docker.com/compose/extends/#extending-services)
```
extends:
  file: common.yml
  service: webapp
```

## extra_hosts

域名解析
```
extra_hosts:
  - "somehost:162.242.195.82"
  - "otherhost:50.31.209.229"
```

## group_add

指定容器内的用户应成为其成员的其他组（按名称或ID）。要添加的容器和主机系统中都必须存在组
```
version: "2.4"
services:
  myservice:
    image: alpine
    group_add:
      - mail
```

## healthcheck

健康检查
```
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 1m30s
  timeout: 10s
  retries: 3
  start_period: 40s
```
`test`必须是字符串或列表。如果是列表，则第一项必须为`NONE`，`CMD`或`CMD-SHELL`。如果是字符串，则等效于指定`CMD-SHELL`后跟该字符串
```
test: ["CMD", "curl", "-f", "http://localhost"]
```
```
# test: ["CMD-SHELL", "curl -f http://localhost || exit 1"]
# or
test: curl -f https://localhost || exit 1
```
不检查
```
healthcheck:
  disable: true
```

## image

指定要从中启动容器的镜像。可以是`repository/tag`或部分`image id`
```
image: redis
```
```
image: ubuntu:18.04
```
```
image: tutum/influxdb
```
```
image: example-registry.com:4000/postgresql
```
```
image: a4bc65fd
```

## init

在容器内运行一个初始化程序，以转发信号并获取进程。将此选项设置为`true`可以为服务启用此功能
```
version: "2.4"
services:
  web:
    image: alpine:latest
    init: true
```

## labels

定义元数据
```
labels:
  com.example.description: "Accounting webapp"
  com.example.department: "Finance"
  com.example.label-with-empty-value: ""
```
```
labels:
  - "com.example.description=Accounting webapp"
  - "com.example.department=Finance"
  - "com.example.label-with-empty-value"
```

## logging

日志配置，可参考[官方文档](https://docs.docker.com/config/containers/logging/configure/)
```
logging:
  driver: syslog
  options:
    syslog-address: "tcp://192.168.0.42:123"
```

## network_mode

网络模式，支持指定当前文件中的其它服务名或已有的容器
```
network_mode: "bridge"
```
```
network_mode: "host"
```
```
network_mode: "none"
```
```
network_mode: "service:[service name]"
```
```
network_mode: "container:[container name/id]"
```

## networks

此配置将会引用顶级`networks`节点下的网络
```
services:
  some-service:
    networks:
     - some-network
     - other-network
```

### 子参数
- `aliases` 服务在网络中的别名（类似域名）
```
  services:
    some-service:
      networks:
        some-network:
          aliases:
            - alias1
            - alias3
        other-network:
          aliases:
            - alias2
```
- `ipv4_address`或`ipv6_address` 自定义网络地址（容器的网络环境内）
> 如果需要IPv6寻址，则必须设置`enable_ipv6`选项，并且必须使用版本`2.x`的Compose文件
```
  version: "3.9"
  services:
    app:
      image: nginx:alpine
      networks:
        app_net:
          ipv4_address: 172.16.238.10
          ipv6_address: 2001:3984:3989::10

  networks:
    app_net:
      ipam:
        driver: default
        config:
          - subnet: "172.16.238.0/24"
          - subnet: "2001:3984:3989::/64"
```

## pid

与宿主机共享`PID`地址空间，以此标志启动的容器可以访问和操作裸机名称空间中的其他容器，反之亦然
```
pid: "host"
```
与当前文件中的其它服务或已有的容器共享`PID`地址空间
```
pid: "container:custom_container_1"
```
```
pid: "service:foobar"
```

## pids_limit

`PID`地址空间的数量限制，设置`-1`表示不限制
```
pids_limit: 10
```

## platform

服务的目标运行平台
```
platform: osx
```
```
platform: windows/amd64
```
```
platform: linux/arm64/v8
```

## ports

宿主机与容器内端口映射
> 端口映射与`network_mode: host`不兼容
```
ports:
  - "3000"
  - "3000-3005"
  - "8000:8000"
  - "9090-9091:8080-8081"
  - "49100:22"
  - "127.0.0.1:8001:8001"
  - "127.0.0.1:5000-5010:5000-5010"
  - "6060:6060/udp"
  - "12400-12500:1240"
```
> `12400-12500:1240`表示在宿主机中指定的端口范围`12400`到`12500`之间随机选取一个绑定到容器的`1240`端口

## profiles

类似`Spring Boot`的配置文件切换，使用此选项可以选择性的启动服务以便调试，如`docker-compose --profile debug up`启动`debug`的相关服务
```
profiles: ["frontend", "debug"]
profiles:
  - frontend
  - debug
```

## restart

重启容器策略
```
restart: "no"
restart: always
restart: on-failure
restart: unless-stopped
```

## secrets

指定机密信息文件，可参考[官方文档](https://docs.docker.com/engine/swarm/secrets/)，机密文件解密后放在容器内的`/run/secrets/<secret_name>`下
```
version: "3.9"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    secrets:
      - my_secret
      - my_other_secret
secrets:
  my_secret:
    file: ./my_secret.txt
  my_other_secret:
    external: true
```
长参数可以自定义机密文件解密后的文件权限和所有者，以及在`/run/secrets/`中的文件名
```
version: "3.9"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    secrets:
      - source: my_secret
        target: redis_secret
        uid: '103'
        gid: '103'
        mode: 0440
secrets:
  my_secret:
    file: ./my_secret.txt
  my_other_secret:
    external: true
```

## stop_grace_period

指定在发送`SIGKILL`之前，如果容器无法处理`SIGTERM`（或使用`stop_signal`指定的任何停止信号）时，尝试停止该容器要等待的时间
```
stop_grace_period: 1s
```
```
stop_grace_period: 1m30s
```

## stop_signal

停止容器的信号量
```
stop_signal: SIGUSR1
```

## sysctls

指定内核参数
```
sysctls:
  net.core.somaxconn: 1024
  net.ipv4.tcp_syncookies: 0
```
```
sysctls:
  - net.core.somaxconn=1024
  - net.ipv4.tcp_syncookies=0
```

## tmpfs

在容器内挂载一个临时文件系统
```
tmpfs: /run
```
```
tmpfs:
  - /run
  - /tmp
```
长参数
```
- type: tmpfs
  target: /app
  tmpfs:
    size: 1000
```

## volumes

挂载卷
```
volumes:
  # Just specify a path and let the Engine create a volume
  - /var/lib/mysql

  # Specify an absolute path mapping
  - /opt/data:/var/lib/mysql

  # Path on the host, relative to the Compose file
  - ./cache:/tmp/cache

  # User-relative path
  - ~/configs:/etc/configs/:ro

  # Named volume
  - datavolume:/var/lib/mysql
```
长参数
- `type` 支持`volume`（卷）、`bind`（绑定）、`tmpfs`（临时文件系统）、`npipe`（命名管道）
- `source` 绑定宿主机的指定路径或者取顶级`volumes`中的卷
- `target` 挂载到容器内的目标路径
- `read_only` 设置卷是否为只读，即`true`
- `bind` 绑定选项
  - `propagation` 用于绑定的传播模式（权限）
- `volume` 卷选项
  - `nocopy` 创建卷时禁用从容器复制数据的标志
- `tmpfs` 临时文件系统选项
  - `size` tmpfs挂载的大小（以字节为单位）

```
version: "2.4"
services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - type: volume
        source: mydata
        target: /data
        volume:
          nocopy: true
      - type: bind
        source: ./static
        target: /opt/app/static

networks:
  webnet:

volumes:
  mydata:
```

Last Modified 2021-04-15

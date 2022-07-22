# docker-compose.yml

## 简介

Compose 文件是一个 YAML 文件，用于定义服务，网络和卷。撰写文件的默认路径是`./docker-compose.yml`。服务定义包含应用于该服
务启动的每个容器的配置，就像将命令行参数传递给`docker run`一样。同样，网络和卷定义类似
于`docker network create`和`docker volume create`

与`docker run`一样，默认情况下，会尊重`Dockerfile`中指定的选项，例如`CMD`，`EXPOSE`，`VOLUME`，`ENV`；无需
在`docker-compose.yml`中再次指定它们

# services

## build

简单指定一个上下文路径，默认读取指定路径下的`Dockerfile`

```yml
version: "3.9"
services:
  webapp:
    build: ./dir
```

如果同时指定`build`和`image`，则会以`image`中指定的名称和标签来命名构建的镜像

```yml
build: ./dir
image: webapp:tag
```

### 子参数

- `context` 构建上下文

```yml
build:
  context: ./dir
```

- `dockerfile` 自定义构建文件名

```yml
build:
  context: .
  dockerfile: Dockerfile-alternate
```

- `args` 定义构建参数

```yml
build:
  context: .
  args:
    buildno: 1
    gitcommithash: cdc3b19
```

```yml
build:
  context: .
  args:
    - buildno=1
    - gitcommithash=cdc3b19
```

- `cache_from` 引擎用于缓存解析的图像列表

```yml
build:
  context: .
  cache_from:
    - alpine:latest
    - corp/web_app:3.14
```

- `extra_hosts` 自定义域名映射

```yml
extra_hosts:
  - "somehost:162.242.195.82"
  - "otherhost:50.31.209.229"
```

- `labels` 定义构建元数据

```yml
build:
  context: .
  labels:
    com.example.description: "Accounting webapp"
    com.example.department: "Finance"
    com.example.label-with-empty-value: ""
```

```yml
build:
  context: .
  labels:
    - "com.example.description=Accounting webapp"
    - "com.example.department=Finance"
    - "com.example.label-with-empty-value"
```

- `network` 设置`RUN`指令的网络连接

```yml
build:
  context: .
  network: host
```

```yml
build:
  context: .
  network: custom_network_1
```

或者指定为`none`表示禁用自定义网络

- `shm_size` 设置此构建容器的`/dev/shm`分区的大小

```yml
build:
  context: .
  shm_size: "2gb"
```

```yml
build:
  context: .
  shm_size: 10000000
```

- `target` 自定义构建目标（多阶段构建）

```yml
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

```yml
container_name: my-web-container
```

## devices

自定义设备映射

```yml
devices:
  - "/dev/ttyUSB0:/dev/ttyUSB0"
```

## depends_on

表达服务之间的依赖性

- 使用`docker-compose up`启动时，`db`和`redis`会先于`web`启动
- 使用`docker-compose up SERVICE`指定服务启动时，会自动引入依赖的服务，如`docker-compose up web`会自动引入`db`和`redis`
- 使用`docker-compose stop`停止时，`web`会先于`db`和`redis`停止

```yml
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

DNS 服务 IP

```yml
dns: 8.8.8.8
```

```yml
dns:
  - 8.8.8.8
  - 9.9.9.9
```

## dns_search

DNS 服务域

```yml
dns_search: example.com
```

```yml
dns_search:
  - dc1.example.com
  - dc2.example.com
```

## entrypoint

覆盖镜像的默认`ENTRYPOINT`

```yml
entrypoint: /code/entrypoint.sh
```

```yml
entrypoint: ["php", "-d", "memory_limit=-1", "vendor/bin/phpunit"]
```

## env_file

环境变量文件

```yml
env_file: .env
```

```yml
env_file:
  - ./common.env
  - ./apps/web.env
  - /opt/runtime_opts.env
```

文件内容

```env
# Set Rails/Rack environment
RACK_ENV=development
```

## environment

环境变量

```yml
environment:
  RACK_ENV: development
  SHOW: "true"
  SESSION_SECRET:
```

```yml
environment:
  - RACK_ENV=development
  - SHOW=true
  - SESSION_SECRET
```

## extends

拓展本文件内已经声明过的服务

```yml
extends: web
```

拓展其它`docker-compose`文件中的服务配置，可参考[官方文档](https://docs.docker.com/compose/extends/#extending-services)

```yml
extends:
  file: common.yml
  service: webapp
```

## extra_hosts

域名解析

```yml
extra_hosts:
  - "somehost:162.242.195.82"
  - "otherhost:50.31.209.229"
```

## group_add

指定容器内的用户应成为其成员的其他组（按名称或 ID）。要添加的容器和主机系统中都必须存在组

```yml
version: "2.4"
services:
  myservice:
    image: alpine
    group_add:
      - mail
```

## healthcheck

健康检查

```yml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 1m30s
  timeout: 10s
  retries: 3
  start_period: 40s
```

`test`必须是字符串或列表。如果是列表，则第一项必须为`NONE`，`CMD`或`CMD-SHELL`。如果是字符串，则等效于指定`CMD-SHELL`后
跟该字符串

```yml
test: ["CMD", "curl", "-f", "http://localhost"]
```

```yml
# test: ["CMD-SHELL", "curl -f http://localhost || exit 1"]
# or
test: curl -f https://localhost || exit 1
```

不检查

```yml
healthcheck:
  disable: true
```

## image

指定要从中启动容器的镜像。可以是`repository/tag`或部分`image id`

```yml
image: redis
```

```yml
image: ubuntu:18.04
```

```yml
image: tutum/influxdb
```

```yml
image: example-registry.com:4000/postgresql
```

```yml
image: a4bc65fd
```

## init

在容器内运行一个初始化程序，以转发信号并获取进程。将此选项设置为`true`可以为服务启用此功能

```yml
version: "2.4"
services:
  web:
    image: alpine:latest
    init: true
```

## labels

定义元数据

```yml
labels:
  com.example.description: "Accounting webapp"
  com.example.department: "Finance"
  com.example.label-with-empty-value: ""
```

```yml
labels:
  - "com.example.description=Accounting webapp"
  - "com.example.department=Finance"
  - "com.example.label-with-empty-value"
```

## logging

日志配置，可参考[官方文档](https://docs.docker.com/config/containers/logging/configure/)

```yml
logging:
  driver: syslog
  options:
    syslog-address: "tcp://192.168.0.42:123"
```

## network_mode

网络模式，支持指定当前文件中的其它服务名或已有的容器

```yml
network_mode: "bridge"
```

```yml
network_mode: "host"
```

```yml
network_mode: "none"
```

```yml
network_mode: "service:[service name]"
```

```yml
network_mode: "container:[container name/id]"
```

## networks

此配置将会引用顶级`networks`节点下的网络

```yml
services:
  some-service:
    networks:
      - some-network
      - other-network
```

### 子参数

- `aliases` 服务在网络中的别名（类似域名）

```yml
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
  >如果需要 IPv6 寻址，则必须设置`enable_ipv6`选项，并且必须使用版本`2.x`的 Compose 文件

```yml
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

```yml
pid: "host"
```

与当前文件中的其它服务或已有的容器共享`PID`地址空间

```yml
pid: "container:custom_container_1"
```

```yml
pid: "service:foobar"
```

## pids_limit

`PID`地址空间的数量限制，设置`-1`表示不限制

```yml
pids_limit: 10
```

## platform

服务的目标运行平台

```yml
platform: osx
```

```yml
platform: windows/amd64
```

```yml
platform: linux/arm64/v8
```

## ports

宿主机与容器内端口映射

>端口映射与`network_mode: host`不兼容

```yml
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

类似`Spring Boot`的配置文件切换，使用此选项可以选择性的启动服务以便调试，如`docker-compose --profile debug up`启
动`debug`的相关服务

```yml
profiles: ["frontend", "debug"]
profiles:
  - frontend
  - debug
```

## secrets

指定机密信息文件，可参考[官方文档](https://docs.docker.com/engine/swarm/secrets/)，机密文件解密后放在容器内
的`/run/secrets/<secret_name>`下

```yml
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

```yml
version: "3.9"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    secrets:
      - source: my_secret
        target: redis_secret
        uid: "103"
        gid: "103"
        mode: 0440
secrets:
  my_secret:
    file: ./my_secret.txt
  my_other_secret:
    external: true
```

## stop_grace_period

指定在发送`SIGKILL`之前，如果容器无法处理`SIGTERM`（或使用`stop_signal`指定的任何停止信号）时，尝试停止该容器要等待的时
间

```yml
stop_grace_period: 1s
```

```yml
stop_grace_period: 1m30s
```

## stop_signal

停止容器的信号量

```yml
stop_signal: SIGUSR1
```

## sysctls

指定内核参数

```yml
sysctls:
  net.core.somaxconn: 1024
  net.ipv4.tcp_syncookies: 0
```

```yml
sysctls:
  - net.core.somaxconn=1024
  - net.ipv4.tcp_syncookies=0
```

## tmpfs

在容器内挂载一个临时文件系统

```yml
tmpfs: /run
```

```yml
tmpfs:
  - /run
  - /tmp
```

长参数

```yml
- type: tmpfs
  target: /app
  tmpfs:
    size: 1000
```

## volumes

挂载卷

```yml
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
  - `size` tmpfs 挂载的大小（以字节为单位）

```yml
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

## volumes_from

从另一个服务或容器挂载所有卷，可以选择指定只读访问（ro）或读写（rw）。如果未指定访问级别，则使用读写

```yml
volumes_from:
  - service_name
  - service_name:ro
  - container:container_name
  - container:container_name:rw
```

## restart

默认的重启策略为`no`，并且在任何情况下都不会重启容器。如果指定了`always`，则容器将始终重新启动。如果退出代码指示失败时错
误，则失败时策略将重新启动容器

```yml
restart: no
```

```yml
restart: always
```

```yml
restart: on-failure
```

```yml
restart: unless-stopped
```

# services 常规参数

参数与`docker run`中的选项一致

## 计算资源限制

```yml
cpu_count: 2
cpu_percent: 50
cpus: 0.5
cpu_shares: 73
cpu_quota: 50000
cpu_period: 20ms
cpuset: 0,1
```

## 用户

```yml
user: postgresql
```

## 工作目录

```yml
working_dir: /code
```

## 域名

```yml
domainname: foo.com
```

## 域

```yml
hostname: foo
```

## 共享内存

```yml
ipc: host
```

## 物理地址

```yml
mac_address: 02:42:ac:11:65:43
```

## 内存资源限制

```yml
mem_limit: 1000000000
memswap_limit: 2000000000
mem_reservation: 512m
```

## 访问权限

```yml
privileged: true
```

## 内存溢出控制

```yml
oom_score_adj: 500
oom_kill_disable: true
```

## 容器内文件系统只读

```yml
read_only: true
```

## /dev/shm 大小

```yml
shm_size: 64M
```

## 保持 STDIN 打开

```yml
stdin_open: true
```

## 伪终端

```yml
tty: true
```

# volumes

## driver

指定该卷应使用哪个卷驱动程序。默认情况下`Docker Engine`配置为使用任何驱动器，在大多数情况下为本地驱动程序。如果驱动程序
不可用，则当`docker-compose up`尝试创建卷时，引擎将返回错误

```yml
driver: foobar
```

## driver_opts

指定选项列表作为键值对，以传递给该卷的驱动程序

```yml
volumes:
  example:
    driver_opts:
      type: "nfs"
      o: "addr=10.40.0.199,nolock,soft,rw"
      device: ":/docker/example"
```

## external

如果设置为`true`，则指定此卷是在 Compose 之外创建的。`docker-compose up`不会尝试创建它，如果不存在，则会引发错误

```yml
version: "3.9"

services:
  db:
    image: postgres
    volumes:
      - data:/var/lib/postgresql/data

volumes:
  data:
    external: true
```

>对于该格式的`3.3`版及更低版本，外部不能与其他卷配置键（driver，driver_opts，labels）结合使用。对于`3.4`及更高版本，此
>限制不再存在

## labels

使用 Docker 标签将元数据添加到容器。您可以使用数组或字典

```yml
labels:
  com.example.description: "Database volume"
  com.example.department: "IT/Ops"
  com.example.label-with-empty-value: ""
```

```yml
labels:
  - "com.example.description=Database volume"
  - "com.example.department=IT/Ops"
  - "com.example.label-with-empty-value"
```

## name

为此卷设置一个自定义名称。名称字段可用于引用包含特殊字符的卷

```yml
version: "3.9"
volumes:
  data:
    name: my-app-data
```

它也可以与外部属性结合使用

```yml
version: "3.9"
volumes:
  data:
    external: true
    name: my-app-data
```

# networks

## driver

指定该网络应使用哪个驱动程序

```yml
driver: bridge
```

- `bridge` 桥接
- `overlay` 集群网络
- `host` or `none` 使用宿主机的网络，或者不使用网络。相当于`docker run --net=host`或`docker run --net=none`。仅在使
  用`docker stack`命令时使用。如果使用`docker-compose`命令，请改用`network_mode`

## enable_ipv6

启用`IPv6`寻址，仅限`2.x`版本的 Compose 文件使用

## driver_opts

传递给卷驱动程序的参数

```yml
driver_opts:
  foo: "bar"
  baz: 1
```

## attachable

仅在驱动程序设置为`overlay`时使用。如果设置为`true`，则除了服务之外，独立容器还可以连接到此网络。如果独立容器连接
到`overlay`网络，则它可以与也从其他 Docker 守护程序附加到`overlay`网络的服务和独立容器进行通信

## ipam

指定自定义`IPAM`配置（子网划分）。这是一个具有多个属性的对象，每个属性都是可选的

- `driver` 自定义 IPAM 驱动程序，而不是默认值。
- `config` 具有零个或多个 config 块的列表，每个块包含以下任一键
  - `subnet` `CIDR`格式的子网，代表一个网段
  - `ip_range` 从中分配容器 IP 的 IP 范围
  - `gateway` 主子网的 IPv4 或 IPv6 网关
  - `axu_addresses` 网络驱动程序使用的辅助 IPv4 或 IPv6 地址，作为从主机名到 IP 的映射
- `config` 特定于驱动程序的选项作为键值映射

```yml
ipam:
  driver: default
  config:
    - subnet: 172.28.0.0/16
      ip_range: 172.28.5.0/24
      gateway: 172.28.5.254
      aux_addresses:
        host1: 172.28.1.5
        host2: 172.28.1.6
        host3: 172.28.1.7
  options:
    foo: bar
    baz: "0"
```

## internal

默认情况下，Docker 还将桥接网络连接到它，以提供外部连接。如果要创建外部隔离的网络，可以将此选项设置为`true`

## labels

使用 Docker 标签将元数据添加到容器。您可以使用数组或字典

```yml
labels:
  com.example.description: "Database volume"
  com.example.department: "IT/Ops"
  com.example.label-with-empty-value: ""
```

```yml
labels:
  - "com.example.description=Database volume"
  - "com.example.department=IT/Ops"
  - "com.example.label-with-empty-value"
```

## external

如果设置为`true`，则指定此网络是在 Compose 之外创建的。`docker-compose up`不会尝试创建它，如果不存在，则会引发错误

```yml
version: "3.9"

services:
  proxy:
    build: ./proxy
    networks:
      - outside
      - default
  app:
    build: ./app
    networks:
      - default

networks:
  outside:
    external: true
```

## name

为此网络设置一个自定义名称。名称字段可用于引用包含特殊字符的网络

```yml
version: "3.9"
networks:
  network1:
    name: my-app-net
```

它也可以与外部属性结合使用

```yml
version: "3.9"
networks:
  network1:
    external: true
    name: my-app-net
```

# secrets

机密文件，默认位置在`/run/secrets/<secret_name>`（Windows 在`C:\ProgramData\Docker\secrets`），可参
考[官方文档](https://docs.docker.com/engine/swarm/secrets/)

- `file` 将使用指定路径中的文件内容创建`secret`
- `external` 如果设置为`true`，则指定此`secret`是在 Compose 之外创建的。`docker-compose up`不会尝试创建它，如果不存在，
  则会引发未找到的错误
- `name` Docker 中的`secret`对象的名称。此字段可用于引用包含特殊字符的`secret`。在`3.5`版本中引入

```yml
secrets:
  my_first_secret:
    file: ./secret_data
  my_second_secret:
    external: true
```

`3.5`以及更高版本的 Compose 文件

```yml
secrets:
  my_first_secret:
    file: ./secret_data
  my_second_secret:
    external: true
    name: redis_secret
```

`3.4`以及更低版本的 Compose 文件

```yml
my_second_secret:
  external:
    name: redis_secret
```

Last Modified 2021-04-15

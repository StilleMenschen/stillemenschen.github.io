# docker-compose.yml

## 简介

Compose文件是一个YAML文件，用于定义服务，网络和卷。撰写文件的默认路径是`./docker-compose.yml`。服务定义包含应用于该服务启动的每个容器的配置，就像将命令行参数传递给`docker run`一样。同样，网络和卷定义类似于`docker network create`和`docker volume create`

与`docker run`一样，默认情况下，会尊重`Dockerfile`中指定的选项，例如`CMD`，`EXPOSE`，`VOLUME`，`ENV`；无需在`docker-compose.yml`中再次指定它们

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

### 子参数说明
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

## config


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

表达服务之间的依赖性。服务依赖项导致以下行为
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

Last Modified 2021-04-14

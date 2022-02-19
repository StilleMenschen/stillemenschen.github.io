# Linux 系统 Docker Compose 安装

- 指定以宿主机的 root 用户运行
- 获取官方最新版镜像
- 指定容器名称
- 映射容器内部端口到宿主机
- 环境变量（时区，NodeJS）
- 挂载卷（ssh，时区，JDK，Maven，NodeJS，主目录，备份目录）

```yml
version: "3.3"
services:
  jenkins1:
    user: root
    image: "jenkins/jenkins:latest"
    container_name: jenkins1
    ports:
      - "18888:8080"
      - "18889:50000"
    environment:
      TZ: "Asia/Shanghai"
      JAVA_OPTS: "-Duser.timezone=Asia/Shanghai"
      NODE_HOME: "/usr/local/node14.15/bin:/usr/local/node14.15/npm-global/bin"
    volumes:
      - "~/.ssh:/root/.ssh"
      - "/usr/share/zoneinfo/Asia/Shanghai:/etc/localtime"
      - "/usr/local/jdk1.8:/usr/local/jdk1.8"
      - "/usr/local/node14.15:/usr/local/node14.15"
      - "/usr/local/maven3:/usr/local/maven3"
      - "/usr/src/test/backup:/home/jenkins/backup"
      - "/usr/src/test/data:/var/jenkins_home"
```

Last Modified 2022-01-19

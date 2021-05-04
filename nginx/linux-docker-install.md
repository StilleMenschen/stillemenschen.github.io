# Linux 系统 Docker 安装

- 获取官方最新版镜像
- 指定容器名称
- 映射容器端口到宿主机
- 挂载卷（配置文件、静态文件目录、日志目录）

```yml
version: "3.3"
services:
  nignx1:
    image: nginx:latest
    container_name: nginx1
    ports:
      - "80:80"
    volumes:
      - "/usr/local/nginx/nginx.conf:/etc/nginx/nginx.conf"
      - "/usr/local/nginx/html:/usr/share/nginx/html"
      - "/usr/local/nginx/conf.d:/etc/nginx/conf.d"
      - "/usr/local/nginx/logs:/var/log/nginx"
```

Last Modified 2021-04-17

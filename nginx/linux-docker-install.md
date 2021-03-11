# Linux系统Docker安装

- 获取官方最新版镜像
- 指定容器名称
- 映射容器端口到宿主机
- 挂载卷（配置文件、静态文件目录、日志目录）

```yml
version: "3.9"
services:
  nignx1:
    image: nginx:latest
    container_name: nginx1
    ports:
      - "80:80"
    volumes:
      - "/usr/nginx/nginx.conf:/etc/nginx/nginx.conf"
      - "/usr/nginx/html:/usr/share/nginx/html"
      - "/usr/nginx/conf.d:/etc/nginx/conf.d"
      - "/usr/nginx/logs:/var/log/nginx"
```

Last Modified 2021-03-11
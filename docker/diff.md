# diff

## 简介

检查容器文件系统上文件或目录的更改

```
docker diff CONTAINER
```

列出自容器创建以来容器文件系统中已更改的文件和目录。跟踪三种不同类型的更改

<style>
table th:first-of-type {
    width: 20%;
}
</style>

| 符号 | 说明               |
| :--- | :----------------- |
| A    | 文件或目录是新增的 |
| D    | 文件或目录被删除   |
| C    | 文件或目录被修改   |

## 命令示例

检查 nginx 容器内的文件变化

```
$ docker diff 1fdfd1f54c1b

C /dev
C /dev/console
C /dev/core
C /dev/stdout
C /dev/fd
C /dev/ptmx
C /dev/stderr
C /dev/stdin
C /run
A /run/nginx.pid
C /var/lib/nginx/tmp
A /var/lib/nginx/tmp/client_body
A /var/lib/nginx/tmp/fastcgi
A /var/lib/nginx/tmp/proxy
A /var/lib/nginx/tmp/scgi
A /var/lib/nginx/tmp/uwsgi
C /var/log/nginx
A /var/log/nginx/access.log
A /var/log/nginx/error.log
```

Last Modified 2021-04-22

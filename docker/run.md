# run

## 简介

在新容器中运行命令

```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

docker run命令首先在指定映像上创建可写容器层，然后使用指定命令启动它。 也就是说，docker run等效于API`/containers/create`然后`/containers/(id)/start`。可以使用docker start重新启动已停止的容器，使其之前的所有更改保持不变。请参阅`docker ps -a`查看所有容器的列表。

## 选项

<style>
table th:first-of-type {
    width: 18%;
}
</style>

选项 | 说明
:- | :-
--add-host              | 添加自定义域名IP映射（`host:ip`）
--cidfile               | 将容器ID写入文件
-d, --detach            | 在后台运行容器并打印容器ID
--dns                   | 自定义DNS服务
--entrypoint            | 覆盖`image`的默认`ENTRYPOINT`
-e, -env                | 设置环境变量
--env-file              | 读入环境变量文件
--health-cmd            | 健康检查命令
--health-interval       | 健康检查时间间隔（ms、s、m、h）默认0s
--health-retries        | 连续出现多次故障需要报告不健康，默认3次
--health-timeout        | 健康检查超时时间（ms、s、m、h）默认0s
--health-start-period   | 开始运行健康检查重试倒计时之前，等待容器初始化的时间（ms|s|m|h）（默认为0s）
--help                  | 显示帮助信息
-h, --hostname          | 容器主机名
-i, --interactive       | 即使未连接`STDIN`，也应使其保持打开状态
--ip                    | 指定IPv4地址，如`172.16.1.2`
--ip6                   | 指定IPv6地址，如`2001:db8::33`
--link-local-ip         | 容器`IPv4/IPv6`链接到本地地址
--mac-address           | 容器的物理地址，如`92:d0:c6:0a:29:33`
--device                | 将主机设备添加到容器
--disable-content-trust	| 跳过`image`验证，默认`true`
--kernel-memory         | 核心内存限制（b、k、m、g）
-m, --memory            | 内存总大小限制（b、k、m、g）
--memory-reservatio     | 内存软性限制（b、k、m、g）
--memory-swap           | 内存限制加上交换空间的总限制，指定`-1`启用无限制交换空间
--memory-swappiness     | 用于设置容器的虚拟内存控制行为。值为`0~100`之间的整数
--name                  | 自定义容器名称
--no-healthcheck        | 禁用容器的所有健康检查
--oom-kill-disable      | 禁用内存溢出时杀死容器
--oom-score-adj         | 调整内存溢出杀死容器的控制行为，值为`-1000~1000`之间的整数
--privileged            | 赋予容器拓展权限
-p, --publish           | 公开容器内的端口到宿主机，如`80:80`、`80`、`127.0.0.1:80:80`
-P, --publish-all       | 指定所有公开的端口随机绑定到宿主机的任意端口
--pull                  | 拉取容器后再运行镜像，可以为`"always"`、`"missing"`、`"never"`，默认`"missing"`
--read-only             | 挂载的容器文件系统为只读
--restart               | 开启容器退出时自动重新启动策略，默认不自动重启
--rm                    | 命令执行结束时自动删除容器
--stop-signal           | 停止容器的信号，默认`SIGTERM`
--tmpfs                 | 挂载`tmpfs`目录
-t, --tty               | 分配伪`TTY`
-u, --user              | 用户名或UID（格式：`<name|uid>[:<group|gid>]`）
-v, --volume            | 绑定挂载卷
--volumes-from          | 从指定的容器挂载卷
-w, --workdir           | 容器内的工作目录

> 部分选项是通过键值对（`key=value`）来指定的
## 示例

### 运行容器并进入容器中

```bash
docker run --name test -i -t debian
```

### 运行容器并指定允许容器执行拓展权限，进入容器中的`bash`环境

```bash
docker run -i -t --privileged ubuntu bash
```

### 运行容器并指定工作目录

指定工作目录为`/opt`，进入容器命令行时，默认的上下文路径是容器指定的工作目录

```bash
docker run -w /opt -i -t debian pwd
```

### 运行容器并挂载宿主机的目录到容器中

```bash
docker run -v /usr/nginx:/opt/nginx -v /usr/nginx/nginx.conf:/opt/nginx/nginx.conf -i -t nginx ls -lAhFR /opt/nginx
```

### 运行容器并开放容器内的端口绑定到宿主机

```bash
docker run -p 127.0.0.1:80:8080/tcp debian bash
```

### 运行容器并指定环境变量

```bash
docker run -v /usr/share/zoneinfo/Asia/Shanghai:/etc/localtime -e TZ=Asia/Shanghai debian env
```

或者使用环境变量文件，以`kay=value`的形式，每行一个环境变量

```env
# 这是注释说明
TZ=Asia/Shanghai
JAVA_HOME=/path/to/java/home
```

然后执行命令指定环境变量文件名，如文件名为`debian.env`

```bash
docker run --env-file debian.env debian env
```

Last Modified 2021-04-11

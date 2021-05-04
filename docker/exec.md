# exec

## 简介

在正在运行的容器中运行新命令

```
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

使用`docker exec`启动的命令仅在容器的主进程（PID 1）运行时运行，如果重新启动容器，则该命令不会重新启动。

`COMMAND`将在容器的默认目录中运行。 如果基础映像在其 Dockerfile 中具有使用 WORKDIR 指令指定的自定义目录，则将使用该目录
。

`COMMAND`应该是可执行文件，链式命令或带引号的命令将不起作用。示例：`docker exec -ti my_container "echo a && echo b"`将不
起作用，但是`docker exec -ti my_container sh -c "echo a && echo b"`将起作用

## 选项

<style>
table th:first-of-type {
    width: 20%;
}
</style>

| 选项              | 说明                                                |
| :---------------- | :-------------------------------------------------- |
| -d, --detach      | 在后台运行命令                                      |
| --detach-keys     | 覆盖后台运行容器的键序列                            |
| -e, --env         | 指定环境变量                                        |
| --env-file        | 指定环境变量文件                                    |
| -i, --interactive | 即使未连接`STDIN`，也应使其保持打开状态             |
| --privileged      | 赋予命令扩展权限                                    |
| -t, --tty         | 分配伪 TTY                                          |
| -u, --user        | 用户名或`UID`（格式：`<name\|uid>[:<group\|gid>]`） |
| -w, --workdir     | 指定容器内的工作目录                                |

## 命令示例

首先创建一个容器并后台运行

```bash
docker run --name ubuntu_top -d ubuntu top -bH
```

创建一个文件到容器内

```bash
docker exec -d ubuntu_top touch /tmp/execWorks
```

通过`bash`访问容器内部并指定一个环境变量且改变工作目录

```bash
docker exec -it -e VAR1=123 -w /tmp/ ubuntu_top bash
```

进入到容器内之后，分别执行`pwd`、`ls -l`和`echo $VAR1`，可以发现刚刚通过`touch`创建的文件`execWorks`就在`/tmp`目录中，工
作目录变化了且环境变量与命令中指定的一致，值为`123`

```
root@14715fb054c8:/tmp# pwd
/tmp
root@14715fb054c8:/tmp# ls -l
total 0
-rw-r--r--. 1 root root 0 Apr 19 09:24 execWorks
root@14715fb054c8:/tmp# echo $VAR1
123
```

Last Modified 2021-04-19

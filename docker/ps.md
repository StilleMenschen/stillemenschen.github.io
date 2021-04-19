# ps

## 简介

列出容器

```
docker ps [OPTIONS]
```

## 选项

<style>
table th:first-of-type {
    width: 26%;
}
</style>

选项 | 说明
:- | :-
-a, --all    | 列出所有容器（默认只列出运行中的）
-f, --filter | 根据提供的条件过滤输出
--format     | 使用Go模板格式化输出容器信息
-n, --last   | 显示n个最后创建的容器（包括所有状态）
-l, --latest | 显示最新创建的容器（包括所有状态）
--no-trunc   | 不截断输出
-q, --quiet  | 只展示容器的ID
-s, --size   | 显示文件总大小


## 过滤器

过滤标志（`-f`或`--filter`）格式为`kay=value`，多个条件使用多个选项指定，如`--filter "foo=bar" --filter "bif=baz"`

过滤条件 | 说明
:- | :-
id                | 容器ID
name              | 容器名称
label             | 容器标签，代表键或键值对的任意字符串
exited            | 代表容器退出状态码的整数，仅在`--all`选项下生效
status            | 固定值：`created`，`restarting`， `running`， `removing`， `paused`， `exited`， `dead`
ancestor          | 筛选以某个镜像作为基础的容器，格式为：`<image-name>[:<tag>]`，`<image id>`，或 `<image@digest>`
before or since   | 过滤在指定容器创建之前或之后的容器
volume            | 过滤已经绑定挂载卷的容器
network           | 过滤连接到指定网络的正在运行中的容器
publish or expose | 过滤发布或公开给定端口的容器
health            | 根据健康检查状态过滤容器，固定值：`starting`，`healthy`，`unhealthy`，`none`
isolation         | 筛选Windows守护程序，固定值：`default`，`process`，`hyperv`
is-task           | 筛选作为`task`服务的容器，固定布尔值：`true`，`false`

1. 过滤名称（`name`）包含有指定字符串的所有容器
    ```bash
    docker ps -a --filter "name=nostalgic"
    ```

2. 过滤退出状态为`137`的所有容器
    ```bash
    docker ps -a --filter 'exited=137'
    ```

3. 过滤基于某个镜像启动的容器

    支持格式：`image`，`image:tag`，`image:tag@digest`，`short-id`，`full-id`。如果不指定`tag`，则默认为`latest`

    指定镜像名称
    ```bash
    docker ps --filter ancestor=ubuntu
    ```

    指定镜像名称和版本
    ```bash
    docker ps --filter ancestor=ubuntu:12.04.5
    ```

    指定镜像短ID
    ```bash
    docker ps --filter ancestor=d0e008c6cf02
    ```

4. 过滤占用指定端口的容器
    ```bash
    docker ps --filter publish=80
    ```
    ```bash
    docker ps --filter expose=8000-8080/tcp
    ```
    ```bash
    docker ps --filter publish=80/udp
    ```

## 格式化

格式化选项（`--format`）使用Go模板格式化输出容器信息，在格式化字符串最前面加上`table`可以指定以表格的方式输出，包含表头

格式化字段 | 说明
:- | :-
.ID         | 容器ID
.Image      | 镜像
.Command    | 命令
.CreatedAt  | 运行时间
.RunningFor | 当前运行状态
.Ports      | 端口映射
.State      | 运行状态，固定值：`created`，`running`，`exited`
.Status     | 健康检查状态
.Size       | 容器占用空间
.Names      | 容器名称
.Labels     | 容器所有标签
.Mounts     | 容器挂载卷
.Networks   | 容器网络

以表格形式格式化输出
```bash
docker ps -a --format "table {{.ID}} {{.Image}} {{.Command}} {{.CreatedAt}} {{.RunningFor}} {{.Ports}} {{.State}} {{.Status}} {{.Size}} {{.Names}} {{.Labels}} {{.Mounts}} {{.Networks}}"
```

自定义格式化输出
```bash
docker ps -a --no-trunc --format "{{.ID}}: {{.Image}}, {{.Names}}, {{.Networks}}, {{.Size}}, {{.CreatedAt}}\n{{.Command}}\n{{.RunningFor}}, {{.Ports}}, {{.State}}, {{.Status}}\n{{.Mounts}}\n"
```

Last Modified 2021-04-11

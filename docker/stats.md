# stats

## 简介

显示容器资源使用情况统计信息的实时流

```
docker stats [OPTIONS] [CONTAINER...]
```

docker stats 命令返回正在运行的容器的实时数据流。 要将数据限制到一个或多个特定容器，请指定一列用空格分隔的容器名称或
ID。 您可以指定一个停止的容器，但是停止的容器不返回任何数据

## 选项

<style>
table th:first-of-type {
    width: 20%;
}
</style>

| 选项        | 说明                                     |
| :---------- | :--------------------------------------- |
| -a, --all   | 展示所有容器（默认只展示运行中的）       |
| --format    | 使用给定的 Go 模板格式化输出             |
| --no-stream | 停用实时展示统计信息，仅获取第一次的结果 |
| --no-trunc  | 不要截断输出                             |

## 输出信息

| 输出                 | 说明                                       |
| :------------------- | :----------------------------------------- |
| CONTAINER ID 和 Name | 容器的 ID 和名称                           |
| CPU % 和 MEM %       | 容器正在使用的主机 CPU 和内存的百分比      |
| MEM USAGE / LIMIT    | 容器正在使用的总内存以及允许使用的总内存量 |
| NET I/O              | 容器通过其网络接口发送和接收的数据量       |
| BLOCK I/O            | 容器已从主机上的块设备读取和写入的数据量   |
| PIDs                 | 容器创建的进程或线程数                     |

## 格式化输出

使用`--format`可以指定 Go 模板格式化输出信息

| 字段       | 说明                              |
| :--------- | :-------------------------------- |
| .Container | 容器名称或 ID（用户输入）         |
| .Name      | 容器名称                          |
| .ID        | 容器 ID                           |
| .CPUPerc   | CPU 百分比                        |
| .MemUsage  | 内存使用情况                      |
| .NetIO     | 网络 IO                           |
| .BlockIO   | 块 IO                             |
| .MemPerc   | 内存百分比（在 Windows 上不可用） |
| .PIDs      | PID 数量（在 Windows 上不可用）   |

## 命令示例

1. 输出指定容器的 CPU 和内存使用率

   ```
   docker stats --all --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}" fervent_panini 5acfcb1b4fd1 drunk_visvesvaraya
   ```

2. 输出所有运行中容器的 CPU 使用率

   ```
   docker stats --format "{{.Container}}: {{.CPUPerc}}"
   ```

3. 以表格形式输出所有运行中容器的 CPU 和内存使用率
   ```
   docker stats --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}"
   ```

Last Modified 2021-04-22

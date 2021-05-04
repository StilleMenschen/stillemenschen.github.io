# history

## 简介

显示镜像构建的历史记录

```
docker history [OPTIONS] IMAGE
```

## 选项

<style>
table th:first-of-type {
    width: 20%;
}
</style>

| 选项        | 说明                                        |
| :---------- | :------------------------------------------ |
| --format    | 使用 Go 模板格式化输出                      |
| -H, --human | 以人类可读的格式输出大小和日期（默认 true） |
| --no-trunc  | 不截断输出                                  |
| -q, --quiet | 仅显示镜像 ID                               |

## 格式化输出

| 字段          | 说明                                                         |
| :------------ | :----------------------------------------------------------- |
| .ID           | 镜像 ID                                                      |
| .CreatedSince | 自镜像创建以来的时间，如果未指定`--human=true`，则显示时间戳 |
| .CreatedAt    | 创建镜像的时间戳                                             |
| .CreatedBy    | 创建镜像的命令行                                             |
| .Size         | 镜像大小                                                     |
| .Comment      | 镜像注释                                                     |

## 命令示例

1. 检查镜像指定不同的 TAG

   ```
   docker history jenkins/jenkins
   ```

   ```
   docker history jenkins/jenkins:2.289
   ```

   ```
   docker history nginx:latest
   ```

2. 格式化输出
   ```
   docker history --format "{{.ID}}: {{.CreatedSince}}" busybox
   ```
   ```
   docker history --format "table {{.ID}}\t{{.CreatedSince}}" busybox
   ```

Last Modified 2021-04-22

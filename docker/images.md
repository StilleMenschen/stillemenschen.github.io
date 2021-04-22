# images

## 简介

列出镜像
```
docker images [OPTIONS] [REPOSITORY[:TAG]]
```
默认的Docker镜像将显示所有顶级镜像，它们的存储库和标签以及它们的大小。

Docker镜像具有中间层，可通过允许缓存每个步骤来提高可重用性，减少磁盘使用量并加快docker构建。这些中间层默认情况下不显示

SIZE是图像及其所有父图像占用的累积空间。这也是docker保存镜像时创建的Tar文件内容所使用的磁盘空间

如果图像具有多个存储库名称或标签，则将多次列出该图像。该单个镜像（可通过其匹配的IMAGE ID识别）仅用完一次列出的SIZE

## 选项

<style>
table th:first-of-type {
    width: 20%;
}
</style>

选项 | 说明
:- | :-
-a, --all    | 显示所有镜像（默认隐藏中间镜像）
--digests    | 显示签名
-f, --filter | 根据提供的条件过滤输出
--format     | 使用Go模板格式化输出
--no-trunc   | 不要截断输出
-q, --quiet  | 仅显示镜像ID

## 过滤

过滤标志（`-f`或`--filter`）格式为`"key=value"`。如果有多个过滤器，则传递多个标志（例如`--filter "foo=bar" --filter "bif=baz"`）

当前支持的过滤器

- `dangling` 布尔值 - `true`或`false`
- `label` `label=<key>` 或 `label=<key>=<value>`
- `before` `<image-name>[:<tag>]`，`<image id>`或`<image@digest>` - 过滤在给定ID或引用之前创建的镜像
- `since` `<image-name>[:<tag>]`，`<image id>`或`<image@digest>` - 过滤在给定ID或引用之后创建的镜像
- `reference` 表达式或镜像参考 - 过滤参考与指定表达式匹配的镜像

## 格式化

字段 | 说明
:- | :-
.ID           | 镜像ID
.Repository   | 镜像仓库
.Tag          | 镜像标记
.Digest       | 镜像签名
.CreatedSince | 自创建镜像以来经过的时间
.CreatedAt    | 创建镜像的时间
.Size         | 镜像大小

## 命令示例

1. 过滤中间层镜像（仓库和标记显示为`<none>`）
    ```
    docker images --filter "dangling=true"
    ```
    批量删除中间层镜像
    ```
    docker rmi $(docker images -f "dangling=true" -q)
    ```

2. 过滤在指定镜像之前或之后创建的镜像
    ```
    docker images --filter "before=image1"
    ```
    ```
    docker images --filter "since=image3"
    ```

3. 按仓库名称（REPOSITORY）或标记（TAG）过滤镜像
    ```
    docker images --filter=reference='busy*:*libc'
    ```
    ```
    docker images --filter=reference='busy*:uclibc' --filter=reference='busy*:glibc'
    ```

4. 格式化输出
    ```
    docker images --format "{{.ID}}: {{.Repository}}"
    ```
    ```
    docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
    ```

Last Modified 2021-04-22

# pull

## 简介

从镜像仓库中提取镜像或存储库

```
docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```

您的大多数镜像都将在 Docker Hub 仓库的基础镜像之上创建

Docker Hub 包含许多预先构建的镜像，您可以拉取并尝试这些镜像，而无需定义和配置自己的镜像

## 选项

<style>
table th:first-of-type {
    width: 20%;
}
</style>

| 选项                    | 说明                             |
| :---------------------- | :------------------------------- |
| -a, --all-tags          | 下载存储库中所有标记的镜像       |
| --disable-content-trust | 跳过镜像验证                     |
| --platform              | 如果服务器支持多平台，则设置平台 |
| -q, --quiet             | 禁用详细信息输出                 |

## 命令示例

1. 根据仓库名称（REPOSITORY）或标记（TAG）或签名（DIGEST）拉取镜像

   ```
   docker pull debian
   ```

   ```
   docker pull debian:jessie
   ```

   ```
   docker pull ubuntu:14.04
   ```

   ```
   docker pull ubuntu@sha256:45b23dee08af5e43a7fea6c4cf9c25ccf269ee113168c19722f87876677c5cb2
   ```

2. 从不同的镜像仓库`myregistry.local:5000`中拉取镜像`testing/test-image`，Docker 默认会使用`https://`请求
   ```
   docker pull myregistry.local:5000/testing/test-image
   ```

Last Modified 2021-12-24

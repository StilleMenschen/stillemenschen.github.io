# inspect

## 简介

返回有关 Docker 对象（镜像、容器、网络等）的低级信息

```
docker inspect [OPTIONS] NAME|ID [NAME|ID...]
```

Docker inspect 提供了有关由 Docker 控制的构造的详细信息

默认情况下，docker inspect 将结果呈现在 JSON 数组中

## 选项

<style>
table th:first-of-type {
    width: 20%;
}
</style>

| 选项         | 说明                             |
| :----------- | :------------------------------- |
| -f, --format | 使用给定的 Go 模板格式化输出     |
| -s, --size   | 如果类型为容器，则显示文件总大小 |
| --type       | 返回指定类型的 JSON              |

## 命令示例

1. 获取容器 IP 地址
   ```bash
   docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $INSTANCE_ID
   ```
2. 获取容器 MAC 地址

   ```
   docker inspect --format='{{range .NetworkSettings.Networks}}{{.MacAddress}}{{end}}' $INSTANCE_ID
   ```

3. 获取容器 log 路径

   ```
   docker inspect --format='{{.LogPath}}' $INSTANCE_ID
   ```

4. 获取容器镜像名称

   ```
   docker inspect --format='{{.Config.Image}}' $INSTANCE_ID
   ```

5. 列出所有端口绑定

   ```
   docker inspect --format='{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' $INSTANCE_ID
   ```

6. 查找端口映射

   ```
   docker inspect --format='{{(index (index .NetworkSettings.Ports "80/tcp") 0).HostPort}}' $INSTANCE_ID
   ```

7. 获取容器挂载配置
   ```
   docker inspect --format='{{.Mounts}}' $INSTANCE_ID
   ```
   利用 python 格式化输出
   ```
   docker inspect -f '{{json .NetworkSettings.Ports}}' $INSTANCE_ID | python -m json.tool
   ```

Last Modified 2021-04-22

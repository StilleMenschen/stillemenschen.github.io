# cp

## 简介

在容器和本地文件系统之间复制文件/文件夹

```
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
```

## 说明

docker cp将`SRC_PATH`的内容复制到`DEST_PATH`。您可以从容器的文件系统复制到本地计算机，也可以从容器的文件系统复制到本地文件系统。如果为`SRC_PATH`或`DEST_PATH`指定了`-`，则还可以将tar归档从`STDIN`或`STDOUT`流式传输。容器可以是正在运行或已停止的容器。`SRC_PATH`或`DEST_PATH`可以是文件或目录。如果拷贝的文件在宿主机或容器中已经存在，则直接覆盖。

将容器中的某个文件输出到宿主机的标准输出流中

```bash
docker cp nginx1:/etc/bash.bashrc -
```

docker cp命令假定容器路径相对于容器的`/`（根）目录。这意味着提供开头`/`是可选的。该命令将`compassionate_darwin:/tmp/foo/myfile.txt`和`compassionate_darwin:tmp/foo/myfile.txt`视为相同的路径。本地机器路径可以是绝对值或相对值。该命令将本地计算机的相对路径解释为相对于运行docker cp的当前工作目录的相对路径。

- `SRC_PATH`是文件
  - `DEST_PATH`不存在：该文件将保存到在`DEST_PATH`中创建的文件
  - `DEST_PATH`不存在且以`/`结束：错误，目标目录必须存在
  - `DEST_PATH`存在且是一个文件：旧文件将会被新文件直接覆盖
  - `DEST_PATH`存在且是一个目录：`SRC_PATH`文件被拷贝到`DEST_PATH`目录下

- `SRC_PATH`是目录
  - `DEST_PATH`不存在：将`DEST_PATH`创建为目录，并将源目录的内容复制到该目录中
  - `DEST_PATH`存在且是一个文件：错误，不能将目录拷贝到文件中
  - `DEST_PATH`存在且是一个目录：
    - `SRC_PATH`不以`/`结尾。（即：斜杠后跟`.`）：源目录复制到该目录中
    - `SRC_PATH`以`/`结尾。（即：斜杠后跟`.`）：源目录的内容被复制到该目录中

无法复制某些系统文件，例如`/proc`，`/sys`，`/dev`，`tmpfs`下的资源以及用户在容器中创建的挂载点。但是，您仍然可以通过在docker exec中手动运行tar来复制此类文件。以下两个示例以不同的方式做相同的事情（考虑`SRC_PATH`和`DEST_PATH`是目录）

```bash
docker exec CONTAINER tar Ccf $(dirname SRC_PATH) - $(basename SRC_PATH) | tar Cxf DEST_PATH -
```
```bash
tar Ccf $(dirname SRC_PATH) - $(basename SRC_PATH) | docker exec -i CONTAINER tar Cxf DEST_PATH -
```

## 参数

参数 | 说明
:--- | :---
-a, --archive  | 存档模式（完整复制目录或文件的`UID/GID`信息）
-L, --follow-link | 跟踪并复制`SRC_PATH`符号链接指向的真实目录或文件

Last Modified 2021-03-27
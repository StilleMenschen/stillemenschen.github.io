# build

## 简介

从`Dockerfile`构建镜像
```
docker build [OPTIONS] PATH | URL | -
```
`docker build`命令从`Dockerfile`和`上下文`构建`Docker`镜像。构建的`上下文`是位于指定`PATH`或`URL`中的文件集。构建过程可以引用`上下文`中的任何文件。例如，您的构建可以使用`COPY`指令在`上下文`中引用文件

`URL`参数可以引用三种资源：Git存储库，预打包的tarball上下文和纯文本文件

## Git

当`URL`参数指向Git存储库的位置时，该存储库充当构建上下文。系统以递归方式获取存储库及其子模块。首先将存储库拉到本地主机上的临时目录中。成功之后，该目录将作为上下文发送到Docker守护程序。本地副本使您能够使用本地用户凭据，VPN等访问私有存储库

`Git URL`在其片段部分接受上下文配置，并用冒号`:`分隔。第一部分代表Git将检出的引用，可以是分支，标签或远程引用。第二部分表示存储库中的一个子目录，该子目录将用作构建上下文
```
docker build https://github.com/docker/rootfs.git#container:docker
```
下表表示所有有效的后缀及其构建上下文

构建上下文后缀 | 参考提交 | 构建上下文
:---|:---|:---
myrepo.git                   | refs/heads/master   | /
myrepo.git#mytag             | refs/tags/mytag     | /
myrepo.git#mybranch          | refs/heads/mybranch | /
myrepo.git#pull/42/head      | refs/pull/42/head   | /
myrepo.git#:myfolder         | refs/heads/master   | /myfolder
myrepo.git#master:myfolder   | refs/heads/master   | /myfolder
myrepo.git#mytag:myfolder    | refs/tags/mytag     | /myfolder
myrepo.git#mybranch:myfolder | refs/heads/mybranch | /myfolder

## Tarball

如果您将`URL`传递到远程`tarball`，则`URL`本身将发送到守护程序
```
docker build http://server/context.tar.gz
```
下载操作将在运行`Docker`守护程序的主机上执行，该主机不必与发出`build`命令的主机相同。`Docker`守护程序将获取`context.tar.gz`并将其用作构建上下文。Tarball上下文必须是符合标准`tar UNIX`格式的`tar`归档文件，并且可以使用`xz`，`bzip2`，`gzip`或`identity`（无压缩）格式中的任何一种进行压缩

## Text files

除了指定上下文，您还可以在URL中传递单个`Dockerfile`或通过`STDIN`将文件传递到管道中。要从`STDIN`传递`Dockerfile`
```
docker build - < Dockerfile
```
使用`Windows`上的`Powershell`，您可以运行
```
Get-Content Dockerfile | docker build -
```
如果使用`STDIN`或指定指向`纯文本文件`的`URL`，则系统会将内容放入名为`Dockerfile`的文件中，并且任何`-f`，`--file`选项都将被忽略。在这种情况下执行构建，不会有上下文

默认情况下，`docker build`命令将在构建上下文的根目录下查找`Dockerfile`。 `-f`，`--file`，选项使您可以指定要使用的替代文件的路径。在将同一组文件用于多个构建的情况下，这很有用。该路径必须是构建上下文中的文件。如果指定了相对路径，则将其解释为相对于上下文的根路径

在大多数情况下，最好将每个Dockerfile放在一个空目录中。 然后，仅将构建Dockerfile所需的文件添加到该目录。 为了提高构建的性能，您还可以通过向该目录添加[.dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)文件来排除文件和目录

## 参数

参数 | 说明
:--- | :---
--add-host     | 添加自定义主机到IP的映射（host:ip）
--build-arg    | 设置构建时变量
--cache-from   | 视为缓存源的图像
--compress     | 使用`gzip`压缩构建上下文
-f, --file     | `Dockerfile`的名称（默认为`PATH/Dockerfile`）
--force-rm     | 始终清理中间容器
--iidfile      | 将镜像ID写入文件
--isolation    | 容器隔离参数（`Linux`平台只支持`default`，`Windows`平台支持：`default`，`process`，`hyperv`）
--label        | 设置镜像的元数据
-m, --memory   | 内存限制
--memory-swap  | 内存限制加上交换空间的总限制，指定`-1`启用无限制交换空间
--network      | 在构建期间为`RUN`指令设置网络模式
--no-cache     | 构建时不使用缓存
--platform     | 如果服务器支持多平台，则设置平台
--pull         | 始终尝试提取镜像的较新版本
-q, --quiet    | 禁用生成输出和在成功时打印图像ID
--rm           | 成功构建后删除中间容器（默认`true`）
-t, --tag      | 名称或者`name:tag`格式的标签（可选）
--target       | 设置要构建的目标构建阶段

## 参考示例

1. 指定构建`Dockerfile`文件并以`tar`文件作为上下文构建

    ```
    docker build -t vieux/apache:2.0 -f ctx/Dockerfile http://server/ctx.tar.gz
    ```

2. 读取输入流中的文件作为构建上下文

    ```
    docker build - < Dockerfile
    ```
    ```
    docker build - < context.tar.gz
    ```
3. 特殊的`Dockerfile`文件名

    ```
    docker build -f Dockerfile.debug .
    ```
    ```
    curl example.com/remote/Dockerfile | docker build -f - .
    ```

4. 配置构建参数

    您可以在`Dockerfile`中使用`ENV`指令来定义变量值。这些值将保留在生成的镜像中。但是，持久性通常不是您想要的。用户希望根据在其上构建镜像的主机不同来配置不同的变量。

    一个很好的例子是`http_proxy`或用于拉取中间文件的源版本。ARG指令允许`Dockerfile`作者使用`--build-arg`标志定义用户可以在构建时设置的值：
    ```
    docker build --build-arg HTTP_PROXY=http://10.20.30.2:1234 --build-arg FTP_PROXY=http://40.50.60.5:4567 .
    ```
    该标志允许您传递在`Dockerfile`的`RUN`指令中像常规环境变量一样被访问的构建时变量。而且，这些值不会像`ENV`值那样保留在中间或最终镜像中。您必须为每个构建参数添加`--build-arg`

    您也可以使用不带值的`--build-arg`标志，在这种情况下，本地环境变量的值将传入正在构建的`Docker`容器中
    ```
    export HTTP_PROXY=http://10.20.30.2:1234
    docker build --build-arg HTTP_PROXY .
    ```

5. 域名映射

    ```
    docker build --add-host=docker:10.180.0.1 .
    ```

6. 目标构建阶段

    ```
    FROM debian AS build-env
    ...

    FROM alpine AS production-env
    ...
    ```
    引用第一个阶段`build-env`开始构建
    ```
    docker build -t mybuildimage --target build-env .
    ```

Last Modified 2021-04-04

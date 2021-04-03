# build

## 简介

从`Dockerfile`构建镜像
```
docker build [OPTIONS] PATH | URL | -
```
`docker build`命令从`Dockerfile`和`上下文`构建`Docker`镜像。构建的`上下文`是位于指定`PATH`或`URL`中的文件集。构建过程可以引用`上下文`中的任何文件。例如，您的构建可以使用`COPY`指令在`上下文`中引用文件

`URL`参数可以引用三种资源：`Git存储库`，`预打包的tarball上下文`和`纯文本文件`

Last Modified 2021-04-03

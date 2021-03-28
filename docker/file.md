# Dockerfile

## 简介

Docker可以通过阅读Dockerfile中的指令来自动构建映像。Dockerfile是一个文本文档，其中包含用户可以在命令行上调用以组装映像的所有命令。使用`docker build`的用户可以创建自动执行的构建，该构建可以连续执行多个命令行指令。

`docker build`命令从`Dockerfile`和上下文构建映像。构建的上下文是位于指定位置`PATH`或`URL`的文件集。PATH是本地文件系统上的目录，而`URL`是一个`Git`存储库位置。上下文路径是递归处理的（按目录下的子目录和目录中的文件逐个读入）。因此，`PATH`包括任何子目录，而`URL`包括存储库及其子模块。

构建是由Docker守护程序而不是CLI运行的。构建过程要做的第一件事是将整个上下文（递归）发送到守护程序。在大多数情况下，最好以空目录作为上下文，并将`Dockerfile`保留在该目录中。仅添加构建`Dockerfile`所需的文件。

> <span style="color: red">警告：不要将根目录`/`用作`PATH`，因为它会导致构建执行时将硬盘驱动器的全部内容传输到Docker守护程序。</span>

## 基本格式

指令`INSTRUCTION`虽然不区分大小写，但是作为一种约定，最好全部使用大写。

```Dockerfile
# Comment line
INSTRUCTION arguments
```

`Dockerfile`默认只会解析以`#`开头的行作为注释，如果行开头是有效的指令，则后续的`#`不会生效为注释

```Dockerfile
# Comment line
RUN echo 'we are running some # of cool things'
```

环境变量（使用`ENV`指令声明），也可以在指令中使用，通过`$variable_name`或者`${variable_name}`来表示

环境变量语法也支持一些标准的`bash`修饰符，如下所示:

- `${variable:-word}`表示如果变量为空（没有定义）则取`word`作为变量的替代值，否则取变量本身的值
- `${variable:+word}`表示如果变量非空（已经定义），则取`word`作为变量的替代值，否则取空值

`word`可以是任意字符表示的字符串，如果需要正常显示为一个字符串而不是被解析为变量，使用转义字符表示，如`\$foo`或`\${foo}`将分别转换为`$foo`和`${foo}`文字

简单示例

```Dockerfile
FROM busybox
ENV FOO=/bar
WORKDIR ${FOO}   # WORKDIR /bar
ADD . $FOO       # ADD . /bar
COPY \$FOO /quux # COPY $FOO /quux
```

支持使用环境变量的指令：`ADD`，`COPY`，`ENV`，`EXPOSE`，`FROM`，`LABEL`，`STOPSIGNAL`，`USER`，`VOLUME`，`WORKDIR`，`ONBUILD（与上述受支持的指令之一结合使用时）`


## .dockerignore

在docker CLI将上下文路径中的文件和目录发送到docker守护程序之前，它将在上下文的根目录中查找名为`.dockerignore`的文件。如果此文件存在，则CLI会修改上下文以排除与其中的模式匹配的文件和目录。这有助于避免不必要地将较大或敏感的文件和目录发送到守护程序，并避免使用`ADD`或`COPY`将它们添加到镜像中。

CLI将`.dockerignore`文件解释为以换行符分隔的模式列表，类似于`Unix shell`的文件组。匹配文件时，上下文的根被认为是工作目录和根目录。例如，模式`/foo/bar`和`foo/bar`都在`PATH`的`foo`子目录中或位于`URL`的`Git`存储库的根目录中排除名为`bar`的文件或目录。两者都不排除其他任何东西。

如果`.dockerignore`文件中的行以第1列中的`#`开头，则该行将被视为注释，并且在CLI解释之前将被忽略。

这是一个示例`.dockerignore`文件：

```
# comment
*/temp*
*/*/temp*
temp?
```

规则 | 说明
:--- | :---
`#comment`            | 注释
`*/temp*`             | 在根的任何直接子目录中排除名称以`temp`开头的文件和目录。例如，排除纯文件`/somedir/temporary.txt`，以及目录`/somedir/temp`
`*/*/temp*`           | 从根以下两级的任何子目录中排除以`temp`开头的文件和目录。例如，排除`/somedir/subdir/temporary.txt`
`temp?`               | 排除名称开头为`temp`并在后面跟有一个字符的根目录中的文件和目录。例如，排除`/tempa`和`/tempb`
`!filename` or `!pattern` | 在规则前面加上`!`字符表示忽略`pattern`匹配或忽略`filename`文件

## FORM

```Dcokerfile
FROM [--platform=<platform>] <image> [AS <name>]
```
```Dcokerfile
FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]
```
```Dcokerfile
FROM [--platform=<platform>] <image>[@<digest>] [AS <name>]
```

`FORM`是每个`Dockerfile`开头的第一个指令，这个指令将指定基于哪一个`image`（镜像）来制作一个新的镜像，`ARG`指令是唯一一个可以写在`FORM`指令之前的指令，指定一些参数用于`FROM`指令选取`image`

`FROM`可以在单个`Dockerfile`中多次出现，以创建多个`image`或将一个构建阶段用作对另一个构建阶段的依赖。只需在每个新的`FROM`指令之前记录一次提交输出的最后一个`image`ID。每个FROM指令清除由先前指令创建的任何状态

在`FROM`引用多平台`image`的情况下，可选的`--platform`标志可用于指定`image`的平台。例如`linux/amd64`，`linux/arm64`或`windows/amd64`。默认情况下，使用构建请求的目标平台。可以在此标志的值中使用全局构建参数，例如，自动平台`ARG`允许您将指令执行强制到本机构建平台（`--platform=$BUILDPLATFORM`），并使用它来交叉编译到目标平台内部

在`FORM`指令之前使用`ARG`指令：

```Dockerfile
ARG  CODE_VERSION=latest
FROM base:${CODE_VERSION}
CMD  /code/run-app

FROM extras:${CODE_VERSION}
CMD  /code/run-extras
```

在`FROM`之前声明的`ARG`在新构建阶段开始后不再生效。因此，`FROM`之后的任何指令都不能使用它。要使用在第一个`FROM`之前声明的`ARG`的默认值，请使用`ARG`指令，在新的构建阶段内部指定同名的参数，但不需要为参数指定值

```Dockerfile
ARG VERSION=latest
FROM busybox:$VERSION
ARG VERSION
RUN echo $VERSION > image_version
```

Last Modified 2021-03-28

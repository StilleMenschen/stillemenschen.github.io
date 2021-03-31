# Dockerfile

## 简介

Docker可以通过阅读Dockerfile中的指令来自动构建映像。Dockerfile是一个文本文档，其中包含用户可以在命令行上调用以组装映像的所有命令。使用`docker build`的用户可以创建自动执行的构建，该构建可以连续执行多个命令行指令。

`docker build`命令从`Dockerfile`和上下文构建映像。构建的上下文是位于指定位置`PATH`或`URL`的文件集。PATH是本地文件系统上的目录，而`URL`是一个`Git`存储库位置。上下文路径是递归处理的（按目录下的子目录和目录中的文件逐个读入）。因此，`PATH`包括任何子目录，而`URL`包括存储库及其子模块。

构建是由Docker守护程序而不是CLI运行的。构建过程要做的第一件事是将整个上下文（递归）发送到守护程序。在大多数情况下，最好以空目录作为上下文，并将`Dockerfile`保留在该目录中。仅添加构建`Dockerfile`所需的文件。

> <span style="color: red">警告：不要将根目录`/`用作`PATH`，因为它会导致构建执行时将硬盘驱动器的全部内容传输到Docker守护程序。</span>

## 基本格式

指令`INSTRUCTION`虽然不区分大小写，但是作为一种约定，最好全部使用大写。
```
# Comment line
INSTRUCTION arguments
```
`Dockerfile`默认只会解析以`#`开头的行作为注释，如果行开头是有效的指令，则后续的`#`不会生效为注释
```
# Comment line
RUN echo 'we are running some # of cool things'
```
环境变量（使用`ENV`指令声明），也可以在指令中使用，通过`$variable_name`或者`${variable_name}`来表示

环境变量语法也支持一些标准的`bash`修饰符，如下所示:

- `${variable:-word}`表示如果变量为空（没有定义）则取`word`作为变量的替代值，否则取变量本身的值
- `${variable:+word}`表示如果变量非空（已经定义），则取`word`作为变量的替代值，否则取空值

`word`可以是任意字符表示的字符串，如果需要正常显示为一个字符串而不是被解析为变量，使用转义字符表示，如`\$foo`或`\${foo}`将分别转换为`$foo`和`${foo}`文字

简单示例
```
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
```
ARG  CODE_VERSION=latest
FROM base:${CODE_VERSION}
CMD  /code/run-app

FROM extras:${CODE_VERSION}
CMD  /code/run-extras
```
在`FROM`之前声明的`ARG`在新构建阶段开始后不再生效。因此，`FROM`之后的任何指令都不能使用它。要使用在第一个`FROM`之前声明的`ARG`的默认值，请使用`ARG`指令，在新的构建阶段内部指定同名的参数，但不需要为参数指定值
```
ARG VERSION=latest
FROM busybox:$VERSION
ARG VERSION
RUN echo $VERSION > image_version
```

## RUN

`RUN`指令有两种执行方式

- `RUM <command>`（shell 命令，此命令在终端里执行，`Linux`默认`/bin/sh -c`，`Windows`默认`cmd /S /C`）
- `RUN ["executable", "param1", "param2"]`（使用JSON数组的形式表示可执行命令和命令参数）

`RUN`指令将在当前镜像顶部的新层中执行任何命令，并提交结果。生成的提交镜像将用于`Dockerfile`中的下一步操作

执行的shell命令比较长时，可以使用`\`换行，如下
```
RUN /bin/bash -c 'source $HOME/.bashrc; \
echo $HOME'
```
如果使用JSON数组形式表示命令，则必须是执行shell命令才可以使用环境变量，否则环境变量将被解析为普通的字符串，如下使用JSON数组的形式调用shell命令执行并且可以正常解析环境变量
```
RUN [ "sh", "-c", "echo $HOME" ]
```

## CMD

`CMD`指令有以下三种方式执行

- `CMD ["executable","param1","param2"]`使用JSON数组表示命令和命令的参数
- `CMD ["param1","param2"]`里面的参数将会作为`ENTRYPOINT`执行命令的默认参数
- `CMD command param1 param2`执行shell命令

在`Dockerfile`中只能有一个`CMD`指令，如果指定了多个，则以文件中最后一个`CMD`指令生效，如果`CMD`指令中没有指明可执行程序，而只是指定了参数，那么还需要再指定一个`ENTRYPOINT`

如果使用`CMD`来为`ENTRYPOINT`指定参数，则这两个指令都必须以JSON数组的形式来编写

如果在`CMD`指令中执行shell，默认情况下会使用`/bin/sh -c`来执行。在没有shell环境的情况下，必须以JSON数组来表示命令，且必须提供可执行程序的完整路径，如：
```
FROM ubuntu
CMD ["/usr/bin/wc","--help"]
```

## ENV

`ENV`指令可以为容器环境或后续执行的指令提供自定义的环境变量，环境变量以`<key>`对应`<value>`的形式表示
```
ENV <key>=<value> ...
```
如果要在`<value>`中使用空格，可以使用`"`双引号或者`\`转义字符
```
ENV MY_NAME="John Doe"
ENV MY_DOG=Rex\ The\ Dog
ENV MY_CAT=fluffy
```

## ADD

```
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

> `--chown`参数仅在Linux操作系统中生效

`ADD`指令可以复制目录、文件或者远程`URL`中的文件，`<src>`允许指定多个，多个`<src>`的情况下，如果是目录或者文件，它们将会按当前构建的上下文路径来解析

`<src>`支持Go模板的文件语法[filepath.Match](http://golang.org/pkg/path/filepath#Match)

模糊路径匹配，使用`*`表示一个或多个字母
```
ADD hom* /mydir/
ADD *hom /mydir/
ADD hom/a* /mydir/
```
单字符匹配，使用`?`表示一个任意字母
```
ADD hom?.txt /mydir/
ADD home/aa?.txt /mydir/
```
如果`<dest>`不是以`/`开头（即根路径），则会参考当前的工作路径解析，如下示例实际会被解析为`<WORKDIR>/relativeDir/`
```
ADD test.txt relativeDir/
```
如果使用具体的用户名或组名而不是用户ID或用户组ID，则docker会查找容器内的文件系统中`/etc/passwd`和`/etc/group`两个文件来确定具体的`UID`和`GID`，权限示例
```
ADD --chown=55:mygroup files* /somedir/
ADD --chown=bin files* /somedir/
ADD --chown=1 files* /somedir/
ADD --chown=10:11 files* /somedir/
```

> 如果容器内的文件系统中不存在`/etc/passwd`或`/etc/group`文件，当`--chown`中使用具体的用户名或组名时，会导致`ADD`指令执行失败，而使用数字的`UID`和`GID`不会访问容器进行检查，`ADD`指令可以正常执行

`ADD`指令遵循以下原则
- `<src>`必须是基于上下文路径中的目录或文件，类似`ADD ../something /something`的方式是无效的，因为在`docker build`开始前，当前的上下文路径将首先发送到`docker`的守护进程，再按`Dockerfile`进行构建
- 如果`<src>`是URL，且`<dest>`不以`/`结尾，则直接下载URL中的文件并复制到`<dest>`
- 如果`<src>`是URL，并且`<dest>`以`/`结尾，则从URL推断文件名，并将文件下载到`<dest>/<filename>`。例如，添加`http://example.com/foobar /`将创建文件`/foobar`。该URL必须具有非平凡的路径，以便在这种情况下可以找到适当的文件名（如`http://example.com`将不起作用）
- 如果`<src>`是采用公认压缩格式（identity, gzip, bzip2 或 xz）的本地`tar`归档文件，则将其解压缩为目录。来自远程URL的资源不会被解压缩。复制或解压缩目录时，其行为与tar -x相同
> 如果一个空的文件恰好以`.tar.gz`结尾，则不会被解压缩，而是被简单地拷贝到容器中，也不会出现解压缩的错误信息
- 如果`<src>`是目录，则将复制目录中的所有内容到`<dest>`，包括文件系统元数据
- 如果`<src>`是任何其他类型的文件，则将其与元数据一起单独复制。在这种情况下，如果`<dest>`以`/`结束，则它将被视为目录，并且`<src>`的内容将写入`<dest>/base(<src>)`。
- 如果直接或由于使用通配符而指定了多个`<src>`资源，则`<dest>`必须是目录，并且必须以`/`结尾
- 如果`<dest>`不以斜杠结尾，则它将被视为常规文件，并且`<src>`的内容将写入`<dest>`
- 如果`<dest>`不存在，它将与路径中所有缺少的目录一起创建

## COPY

```
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]
```
`COPY`指令与`ADD`指令类似，但`COPY`指令不支持下载URL中的文件，也不支持解压缩使用`tar`归档的文件，只有拷贝`<src>`文件或目录到容器内的功能，参考`ADD`指令说明

## ENTRYPOINT

`exec`形式
```
ENTRYPOINT ["executable", "param1", "param2"]
```
`shell`形式
```
ENTRYPOINT command param1 param2
```
`ENTRYPOINT`允许您配置将作为可执行文件运行的容器，`Dockerfile`应该指定`CMD`或`ENTRYPOINT`命令中的至少一个，使用容器作为可执行文件时，应定义`ENTRYPOINT`

`docker run <image>`的命令行参数将附加在`exec`形式`ENTRYPOINT`的所有元素之后，并将覆盖使用`CMD`指定的所有元素。这允许将参数传递给入口点，即`docker run <image> -d`将`-d`参数传递给入口点。您可以使用`docker run --entrypoint`标志覆盖`ENTRYPOINT`指令

`shell`形式可防止使用任何`CMD`或运行命令行参数，但具有以下缺点：`ENTRYPOINT`将作为`/bin/sh -c`的子命令启动，该子命令不传递信号。这意味着可执行文件将不是容器的`PID 1`，并且不会接收`Unix`信号，因此您的可执行文件将不会从`docker stop <container>`接收到`SIGTERM`

> 只有`Dockerfile`中的最后一条`ENTRYPOINT`指令才会生效

下表显示了针对不同的`ENTRYPOINT/CMD`组合执行的命令

`/` | `No ENTRYPOINT` | `ENTRYPOINT exec_entry p1_entry` | `ENTRYPOINT ["exec_entry", "p1_entry"]`
:--- | :--- | :--- | :---
`No CMD` | error, not allowed | /bin/sh -c exec_entry p1_entry | exec_entry p1_entry
`CMD ["exec_cmd", "p1_cmd"]` | exec_cmd p1_cmd | /bin/sh -c exec_entry p1_entry | exec_entry p1_entry exec_cmd p1_cmd
`CMD ["p1_cmd", "p2_cmd"]` | p1_cmd p2_cmd | /bin/sh -c exec_entry p1_entry | exec_entry p1_entry p1_cmd p2_cmd
`CMD exec_cmd p1_cmd` | /bin/sh -c exec_cmd p1_cmd | /bin/sh -c exec_entry p1_entry | exec_entry p1_entry /bin/sh -c exec_cmd p1_cmd

> 如果从基本`image`定义了`CMD`，则设置`ENTRYPOINT`会将`CMD`重置为空值。在这种情况下，必须在当前`image`中定义`CMD`以具有值

参数覆盖示例

构建一个镜像，名称为`top`，指定入口点和额外命令参数
```
FROM ubuntu
ENTRYPOINT ["top", "-b"]
CMD ["-c"]
```
运行镜像`top`，并指定一个额外参数`-H`
```
$ docker run -it --name test  top -H

top - 08:25:00 up  7:27,  0 users,  load average: 0.00, 0.01, 0.05
Threads:   1 total,   1 running,   0 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.1 us,  0.1 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem:   2056668 total,  1616832 used,   439836 free,    99352 buffers
KiB Swap:  1441840 total,        0 used,  1441840 free.  1324440 cached Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
    1 root      20   0   19744   2336   2080 R  0.0  0.1   0:00.04 top
```
查看镜像内的程序，可以看到用`CMD`指令指定的`-c`参数被忽略了，而是使用run所指定的`-H`参数
```
$ docker exec -it test ps aux

USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  2.6  0.1  19752  2352 ?        Ss+  08:24   0:00 top -b -H
root         7  0.0  0.1  15572  2164 ?        R+   08:25   0:00 ps aux
```

## VOLUME

```
VOLUME ["/data"]
```
`VOLUME`指令创建具有指定名称的安装点，并将其标记为保存来自本机主机或其他容器的外部安装的卷。该值可以是`JSON`数组，`VOLUME ["/var/log/"]`或具有多个参数的纯字符串，例如`VOLUME /var/log`或`VOLUME /var/log /var/db`
```
FROM ubuntu
RUN mkdir /myvol
RUN echo "hello world" > /myvol/greeting
VOLUME /myvol
```
该`Dockerfile`生成一个映像，该镜像使`docker run`在`/myvol`中创建一个新的挂载点，并将`greeting`文件复制到新创建的卷中。

## USER

指定实际用户名或用户组
```
USER <user>[:<group>]
```
指定用户ID或组ID
```
USER <UID>[:<GID>]
```

> 如果没有为用户声明组，则该镜像（或后续说明）将以`root`组作为该用户的所属组运行，Windows系统中，必须先使用`net user`创建一个实际的用户，才能使用该指令声明

## WORKDIR

```
WORKDIR /path/to/workdir
```

`WORKDIR`指令为`Dockerfile`中跟随它的所有`RUN`，`CMD`，`ENTRYPOINT`，`COPY`和`ADD`指令设置工作目录。如果`WORKDIR`不存在，即使以后的`Dockerfile`指令中未使用它也将被创建

`WORKDIR`指令可在`Dockerfile`中多次使用。如果提供了相对路径，则它将相对于上一个`WORKDIR`指令的路径。例如
```
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
```
最终`pwd`命令会输出`a/b/c`

`WORKDIR`指令可以解析以前使用`ENV`设置的环境变量。您只能使用在`Dockerfile`中显式设置的环境变量。例如
```
ENV DIRPATH=/path
WORKDIR $DIRPATH/$DIRNAME
RUN pwd
```
最终`pwd`命令会输出`/path/$DIRNAME`

Last Modified 2021-03-31

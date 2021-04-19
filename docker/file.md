# Dockerfile

## 简介

Docker可以通过阅读`Dockerfile`中的指令来自动构建镜像。`Dockerfile`是一个文本文档，其中包含用户可以在命令行上调用以组装镜像的所有命令。使用`docker build`的用户可以创建自动执行的构建，该构建可以连续执行多个命令行指令。

`docker build`命令从`Dockerfile`和上下文构建镜像。构建的上下文是位于指定位置`PATH`或`URL`的文件集。`PATH`是本地文件系统上的目录，而`URL`是一个`Git`存储库位置。上下文路径是递归处理的（按目录下的子目录和目录中的文件逐个读入）。因此，`PATH`包括任何子目录，而`URL`包括存储库及其子模块。

构建是由Docker守护程序而不是CLI运行的。构建过程要做的第一件事是将整个上下文（递归）发送到守护程序。在大多数情况下，最好以空目录作为上下文，并将`Dockerfile`保留在该目录中。仅添加构建`Dockerfile`所需的文件。

> <span style="color: red">警告：不要将根目录`/`用作`PATH`，因为它会导致构建执行时将硬盘驱动器的全部内容传输到Docker守护程序。</span>

## 基本格式

指令`INSTRUCTION`虽然不区分大小写，但是作为一种约定，最好全部使用大写。
```
# Comment line
INSTRUCTION arguments
```
`Dockerfile`默认只会解析以`#`开头的行作为注释，如果行开头是有效的指令，则后续的`#`不会生效为注释
```docker
# Comment line
RUN echo 'we are running some # of cool things'
```
环境变量（使用`ENV`指令声明），也可以在指令中使用，通过`$variable_name`或者`${variable_name}`来表示

环境变量语法也支持一些标准的`bash`修饰符，如下所示:

- `${variable:-word}`表示如果变量为空（没有定义）则取`word`作为变量的替代值，否则取变量本身的值
- `${variable:+word}`表示如果变量非空（已经定义），则取`word`作为变量的替代值，否则取空值

`word`可以是任意字符表示的字符串，如果需要正常显示为一个字符串而不是被解析为变量，使用转义字符表示，如`\$foo`或`\${foo}`将分别转换为`$foo`和`${foo}`文字

简单示例
```docker
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
<style>
table th:first-of-type {
    width: 24%;
}
</style>

规则 | 说明
:- | :-
`#comment`            | 注释
`*/temp*`             | 在根的任何直接子目录中排除名称以`temp`开头的文件和目录。例如，排除纯文件`/somedir/temporary.txt`，以及目录`/somedir/temp`
`*/*/temp*`           | 从根以下两级的任何子目录中排除以`temp`开头的文件和目录。例如，排除`/somedir/subdir/temporary.txt`
`temp?`               | 排除名称开头为`temp`并在后面跟有一个字符的根目录中的文件和目录。例如，排除`/tempa`和`/tempb`
`!filename` or `!pattern` | 在规则前面加上`!`字符表示忽略`pattern`匹配或忽略`filename`文件

## FORM

```
FROM [--platform=<platform>] <image> [AS <name>]
```
```
FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]
```
```
FROM [--platform=<platform>] <image>[@<digest>] [AS <name>]
```

`FORM`是每个`Dockerfile`开头的第一个指令，这个指令将指定基于哪一个`image`（镜像）来制作一个新的镜像，`ARG`指令是唯一一个可以写在`FORM`指令之前的指令，指定一些参数用于`FROM`指令选取`image`

`FROM`可以在单个`Dockerfile`中多次出现，以创建多个`image`或将一个构建阶段用作对另一个构建阶段的依赖。只需在每个新的`FROM`指令之前记录一次提交输出的最后一个`image`ID。每个FROM指令清除由先前指令创建的任何状态

在`FROM`引用多平台`image`的情况下，可选的`--platform`标志可用于指定`image`的平台。例如`linux/amd64`，`linux/arm64`或`windows/amd64`。默认情况下，使用构建请求的目标平台。可以在此标志的值中使用全局构建参数，例如，自动平台`ARG`允许您将指令执行强制到本机构建平台（`--platform=$BUILDPLATFORM`），并使用它来交叉编译到目标平台内部

在`FORM`指令之前使用`ARG`指令：
```docker
ARG  CODE_VERSION=latest
FROM base:${CODE_VERSION}
CMD  /code/run-app

FROM extras:${CODE_VERSION}
CMD  /code/run-extras
```
在`FROM`之前声明的`ARG`在新构建阶段开始后不再生效。因此，`FROM`之后的任何指令都不能使用它。要使用在第一个`FROM`之前声明的`ARG`的默认值，请使用`ARG`指令，在新的构建阶段内部指定同名的参数，但不需要为参数指定值
```docker
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
```docker
RUN /bin/bash -c 'source $HOME/.bashrc; \
echo $HOME'
```
如果使用JSON数组形式表示命令，则必须是执行shell命令才可以使用环境变量，否则环境变量将被解析为普通的字符串，如下使用JSON数组的形式调用shell命令执行并且可以正常解析环境变量
```docker
RUN [ "sh", "-c", "echo $HOME" ]
```

## CMD

`CMD`指令有以下三种方式执行

- `CMD ["executable","param1","param2"]`使用JSON数组表示命令和命令的参数
- `CMD ["param1","param2"]`里面的参数将会作为`ENTRYPOINT`执行命令的默认参数
- `CMD command param1 param2`执行shell命令

在`Dockerfile`中只能有一个`CMD`指令，如果指定了多个，则以文件中最后一个`CMD`指令生效，如果`CMD`指令中没有指明可执行程序，而只是指定了参数，那么还需要再指定一个`ENTRYPOINT`

如果使用`CMD`来为`ENTRYPOINT`指定参数，则这两个指令**都必须以JSON数组的形式来编写**

如果在`CMD`指令中执行shell，默认情况下会使用`/bin/sh -c`来执行。在没有shell环境的情况下，必须以JSON数组来表示命令，且必须提供可执行程序的完整路径，如：
```docker
FROM ubuntu
CMD ["/usr/bin/wc","--help"]
```

## ENV

`ENV`指令可以为容器环境或后续执行的指令提供自定义的环境变量，环境变量以`<key>`对应`<value>`的形式表示
```
ENV <key>=<value> ...
```
如果要在`<value>`中使用空格，可以使用`"`双引号或者`\`转义字符
```docker
ENV MY_NAME="John Doe"
ENV MY_DOG=Rex\ The\ Dog
ENV MY_CAT=fluffy
```

## ADD

```
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

> `--chown`选项仅在Linux操作系统中生效

`ADD`指令可以复制目录、文件或者远程`URL`中的文件，`<src>`允许指定多个，多个`<src>`的情况下，如果是目录或者文件，它们将会按当前构建的上下文路径来解析

`<src>`支持Go模板的文件语法[filepath.Match](http://golang.org/pkg/path/filepath#Match)

模糊路径匹配，使用`*`表示一个或多个字母
```docker
ADD hom* /mydir/
ADD *hom /mydir/
ADD hom/a* /mydir/
```
单字符匹配，使用`?`表示一个任意字母
```docker
ADD hom?.txt /mydir/
ADD home/aa?.txt /mydir/
```
如果`<dest>`不是以`/`开头（即根路径），则会参考当前的工作路径解析，如下示例实际会被解析为`<WORKDIR>/relativeDir/`
```docker
ADD test.txt relativeDir/
```
如果使用具体的用户名或组名而不是用户ID或用户组ID，则docker会查找容器内的文件系统中`/etc/passwd`和`/etc/group`两个文件来确定具体的`UID`和`GID`，权限示例
```docker
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

`docker run <image>`的命令行选项将附加在`exec`形式`ENTRYPOINT`的所有元素之后，并将覆盖使用`CMD`指定的所有元素。这允许将参数传递给入口点，即`docker run <image> -d`将`-d`选项传递给入口点。您可以使用`docker run --entrypoint`标志覆盖`ENTRYPOINT`指令

`shell`形式可防止使用任何`CMD`或运行命令行参数，但具有以下缺点：`ENTRYPOINT`将作为`/bin/sh -c`的子命令启动，该子命令不传递信号。这意味着可执行文件将不是容器的`PID 1`，并且不会接收`Unix`信号，因此您的可执行文件将不会从`docker stop <container>`接收到`SIGTERM`

> 只有`Dockerfile`中的最后一条`ENTRYPOINT`指令才会生效

下表显示了针对不同的`ENTRYPOINT/CMD`组合执行的命令

`/` | `No ENTRYPOINT` | `ENTRYPOINT exec_entry p1_entry` | `ENTRYPOINT ["exec_entry", "p1_entry"]`
:- | :- | :- | :-
`No CMD` | error, not allowed | /bin/sh -c exec_entry p1_entry | exec_entry p1_entry
`CMD ["exec_cmd", "p1_cmd"]` | exec_cmd p1_cmd | /bin/sh -c exec_entry p1_entry | exec_entry p1_entry exec_cmd p1_cmd
`CMD ["p1_cmd", "p2_cmd"]` | p1_cmd p2_cmd | /bin/sh -c exec_entry p1_entry | exec_entry p1_entry p1_cmd p2_cmd
`CMD exec_cmd p1_cmd` | /bin/sh -c exec_cmd p1_cmd | /bin/sh -c exec_entry p1_entry | exec_entry p1_entry /bin/sh -c exec_cmd p1_cmd

> 如果从基本`image`定义了`CMD`，则设置`ENTRYPOINT`会将`CMD`重置为空值。在这种情况下，必须在当前`image`中定义`CMD`以具有值

参数覆盖示例

构建一个镜像，名称为`top`，指定入口点和额外命令参数
```docker
FROM ubuntu
ENTRYPOINT ["top", "-b"]
CMD ["-c"]
```
运行镜像`top`，并指定一个额外选项`-H`
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
查看镜像内的程序，可以看到用`CMD`指令指定的`-c`选项被忽略了，而是使用run所指定的`-H`选项
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
```docker
FROM ubuntu
RUN mkdir /myvol
RUN echo "hello world" > /myvol/greeting
VOLUME /myvol
```
该`Dockerfile`生成一个镜像，该镜像使`docker run`在`/myvol`中创建一个新的挂载点，并将`greeting`文件复制到新创建的卷中。

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
```docker
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
```
最终`pwd`命令会输出`a/b/c`

`WORKDIR`指令可以解析以前使用`ENV`设置的环境变量。您只能使用在`Dockerfile`中显式设置的环境变量。例如
```docker
ENV DIRPATH=/path
WORKDIR $DIRPATH/$DIRNAME
RUN pwd
```
最终`pwd`命令会输出`/path/$DIRNAME`

## ARG

`ARG`指令定义了一个变量，用户可以在构建时使用`--build-arg <varname>=<value>`标志使用`docker build`命令将其传递给构建器。如果用户指定了未在`Dockerfile`中定义的构建参数，则构建会输出警告
```docker
FROM busybox
ARG user1
ARG buildno
# ...
```
带有默认值，如果`ARG`指令具有默认值，并且在构建时未传递任何值，则构建器将使用默认值
```docker
FROM busybox
ARG user1=someuser
ARG buildno=1
# ...
```

> <span style="color: red">不建议使用构建时变量来传递诸如github密钥，用户凭据等机密信息。构建时变量值对于任何使用docker history命令查看镜像的用户都是可见的</span>


`ARG`变量定义从`Dockerfile`中定义的行开始生效，而不是该参数在命令行被指定或在其他地方使用了该变量开始生效。例如，考虑以下Dockerfile
```docker
FROM busybox
USER ${user:-some_user}
ARG user
USER $user
# ...
```
使用命令构建
```bash
docker build --build-arg user=what_user .
```
第2行的`USER`为`some_user`，因为在随后的第3行中定义了`user`变量。第4行的`USER`评估为用户定义的`what_user`，并且在命令行中传递了`what_user`值。在通过ARG指令对其进行定义之前，任何在`ARG`定义之前使用该变量都会返回一个空字符串

`ARG`指令在定义它的构建阶段结束时超出使用范围。要在多个阶段使用`ARG`，每个阶段都必须包含`ARG`指令
```docker
FROM busybox
ARG SETTINGS
RUN ./run/setup $SETTINGS

FROM busybox
ARG SETTINGS
RUN ./run/other $SETTINGS
```
您可以使用`ARG`或`ENV`指令来指定`RUN`指令可用的变量。使用`ENV`指令定义的环境变量始终会覆盖`ARG`指令定义同名的变量。

`Docker`具有一组预定义的`ARG`变量，您可以在`Dockerfile`中使用它们而无需相应的`ARG`指令
```
HTTP_PROXY
http_proxy
HTTPS_PROXY
https_proxy
FTP_PROXY
ftp_proxy
NO_PROXY
no_proxy
```
预定义的`ARG`变量在使用`docekr history`查看镜像时，默认被排除输出，但是如果在`Dockerfile`中使用`ARG`定义了与预定义变量同名的变量，则变量定义会保留在`docker history`查看镜像的构建历史中

## ONBUILD

当将镜像用作另一个构建的基础时，`ONBUILD`指令会在镜像上添加一个触发指令，以便稍后执行。该触发器将在子级镜像构建的上下文中执行，就像它已被插入到子级`Dockerfile`中的`FROM`指令之后一样

> 不允许使用链接的`ONBUILD`指令，如`ONBUILD ONBUILD`

使用以下`Dockerfile`构建一个父级镜像运行一个`Spring Boot`程序
```docker
FROM openjdk:8
WORKDIR /usr/src/myapp
ONBUILD COPY [ "application.yml", "/usr/src/myapp/config/" ]
ONBUILD COPY [ "springbootdemo-0.0.1-SNAPSHOT.jar", "/usr/src/myapp/app.jar" ]
ENV SERVER_PORT=8080
CMD [ "java", "-jar", "app.jar", "--server.port=${SERVER_PORT}"]
```
构建此镜像作为父级
```bash
docker build -t app-parent .
```
编写一个子级镜像引用刚刚构建的镜像，并修改启动的服务端口，由于父级已经声明过拷贝的指令，子级的`Dockerfile`不需要再写一次，只需要准备好对应名称的`jar`文件和`yml`配置文件
```docker
FROM app-parent
ENV SERVER_PORT=8081
```
构建此镜像作为继承父级的子级，然后运行子级镜像
```bash
docker build -t app-child .
docker run -it --name child-instance --rm app-child
```
查看容器内的目录，可以看到已经有`app.jar`和`application.yml`这两个文件
```
$ docker exec -it child-instance ls -RAhFl
app.jar

./config:
application.yml
```

## STOPSIGNAL

```
STOPSIGNAL signal
```
`STOPSIGNAL`指令设置将被发送到容器退出的系统调用信号。该信号可以是与内核`syscall`表中的位置匹配的有效无符号数字（例如9），也可以是格式为`SIGNAME`的信号名称（例如`SIGKILL`）

## HEALTHCHECK

`HEALTHCHECK`有两种形式表示

- `HEALTHCHECK [OPTIONS] CMD command`（通过在容器内运行命令来检查容器的运行状况）
- `HEALTHCHECK NONE`（禁用从父级镜像继承的任何健康检查）

`HEALTHCHECK`指令告诉`Docker`如何测试容器以检查其是否仍在工作。这样可以检测到诸如Web服务器陷入无限循环并且无法处理新连接的情况，即使服务器进程仍在运行

在`CMD`之前可以指定的选项包括以下，时间单位支持：`ms`（毫秒），`s`（秒），`m`（分钟），`h`（小时）

* `--interval=DURATION` (检测间隔时间，默认值: 30s)
* `--timeout=DURATION` (检测命令运行的超时时长，默认值: 30s)
* `--start-period=DURATION` (开始运行健康检查重试倒计时之前，等待容器初始化的时间，默认值: 0s)
* `--retries=N` (连续出现`N`次故障需要报告不健康，默认值: 3)

如果容器中启用了健康检查，则检查的状态包含以下三种

- `starting`（初始化）
- `healthy`（健康检查通过）
- `unhealthy`（经过一定次数的连续故障之后）

运行状况检查将首先在容器启动后的间隔秒数内运行，然后在之前每次检查完成后的间隔秒数内运行；如果单次检查花费的时间超过超时秒数，则认为检查失败；当重新尝试健康检查连续多次失败后，才能将容器视为`unhealthy`

命令的退出状态指示容器的健康状态。可能的值为

- 0: success - 容器健康且可以使用
- 1: unhealthy - 容器无法正常工作
- 2: reserved - 请勿使用此退出代码

如下表示每间隔5分钟检查站点首页在3s内可以被访问到，否则返回不正常
```docker
HEALTHCHECK --interval=5m --timeout=3s \
  CMD curl -f http://localhost/ || exit 1
```

## SHELL

```
SHELL ["executable", "parameters"]
```
`SHELL`指令允许覆盖用于命令的`shell`形式的默认`shell`。在Linux上，默认外壳程序是`["/bin/sh"，"-c"]`，在`Windows`上，默认外壳程序是`["cmd"，"/S"，"/C"]`。必须在Dockerfile中以JSON数组形式编写`SHELL`指令

以下是一个基于`windows server`镜像的`Dockerfile`示例
```docker
FROM microsoft/windowsservercore

# 使用 cmd /S /C 执行 echo default
RUN echo default

# 使用 cmd /S /C powershell -command 执行 Write-Host default
RUN powershell -command Write-Host default

# 使用 powershell -command 执行 Write-Host hello
SHELL ["powershell", "-command"]
RUN Write-Host hello

# 使用 cmd /S /C 执行 echo hello
SHELL ["cmd", "/S", "/C"]
RUN echo hello
```

Last Modified 2021-04-11

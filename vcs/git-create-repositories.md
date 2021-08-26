# 创建 Git 仓库

## init

初始化仓库

```
git init [-q | --quiet] [--bare] [--template=<template_directory>]
          [--separate-git-dir <git dir>] [--object-format=<format>]
          [-b <branch-name> | --initial-branch=<branch-name>]
          [--shared[=<permissions>]] [directory]
```

### 选项

<style>
table th:first-of-type {
    width: 24%;
}
</style>

| 选项                                                 | 说明                     |
| :--------------------------------------------------- | :----------------------- |
| -q, --quiet                                          | 安静模式，仅输出错误信息 |
| -b \<branch-name\>, --initial-branch=\<branch-name\> | 指定初始化时的主分支名称 |

### Git 目录结构

- config 文件 - 所有项目特定的配置设置都存储在其中

> Git 在您当前使用的任何存储库的 Git 目录 (.git/config) 中的配置文件中查找配置值。这些值特定于该单个存储库。例如，假设您
> 设置 Git 的全局配置使用您的个人电子邮件地址。如果您希望将工作电子邮件用于特定项目而不是个人电子邮件，则该更改将添加到
> 此文件中。

- description 文件 - 这个文件只被 GitWeb 程序使用，所以我们可以忽略它
- hooks 目录- 这是我们可以放置客户端或服务器端脚本的地方，我们可以使用它们来挂钩 Git 的不同生命周期事件
- info 目录 - 包含全局排除文件
- objects 目录 - 该目录将存储我们所做的所有提交
- refs 目录 - 该目录包含指向提交的指针（基本上是“分支”和“标签”）

### 例子

1. 创建一个 /path/to/my/codebase/.git 目录
2. 将所有现有文件添加到索引中
3. 将原始状态记录为历史上的第一次提交

```bash
cd /path/to/my/codebase
git init      # (1)
git add .     # (2)
git commit    # (3)
```

## clone

克隆仓库

```
git clone [--template=<template_directory>]
          [-l] [-s] [--no-hardlinks] [-q] [-n] [--bare] [--mirror]
          [-o <name>] [-b <name>] [-u <upload-pack>] [--reference <repository>]
          [--dissociate] [--separate-git-dir <git dir>]
          [--depth <depth>] [--[no-]single-branch] [--no-tags]
          [--recurse-submodules[=<pathspec>]] [--[no-]shallow-submodules]
          [--[no-]remote-submodules] [--jobs <n>] [--sparse]
          [--filter=<filter>] [--] <repository>
          [<directory>]
```

### 选项

| 选项                                             | 说明                                                               |
| :----------------------------------------------- | :----------------------------------------------------------------- |
| -q, --quiet                                      | 安静模式，仅输出错误信息                                           |
| -v, --verbose                                    | 指定初始化时的主分支名称                                           |
| --progress                                       | 除非指定了`--quiet`选项，否则展示处理进度                          |
| --bare                                           | 仅克隆仓库内容，不创建与远程仓库的分支`refs/remotes/origin/`关联   |
| --mirror                                         | 与`--bare`类似，但还会跟踪远程仓库的分支变化并跟随更新             |
| -o \<name\>, --origin \<name\>                   | 使用不同的来源，覆盖`clone.defaultRemoteName`配置项                |
| -b \<branch-name\>, --branch=\<branch-name\>     | 指定克隆的分支名称                                                 |
| -c \<key\>=\<value\>, --config \<key\>=\<value\> | 指定初始化之后的 Git 配置项                                        |
| --depth \<depth\>                                | 浅克隆，仅克隆指定次数的变更历史记录                               |
| --[no-]single-branch                             | 仅克隆通过`--branch`指定的分支的浅克隆，忽略其它分支的更新历史记录 |
| --no-tags                                        | 不克隆标签                                                         |

### URL

Git URL 支持多种协议，SSH 协议支持指定登录服务器的账号名

- ssh://[user@]host.xz[:port]/path/to/repo.git/
- git://host.xz[:port]/path/to/repo.git/
- http[s]://host.xz[:port]/path/to/repo.git/
- ftp[s]://host.xz[:port]/path/to/repo.git/

> HTTP 协议还支持直接在 URL 中指定账户和密码，如 `https://user:password@repositores.com/path/to/repo.git` 但要注意用户名和密码出现了特殊字符时要先使用 URL 编码，如账户名是一个邮箱，则邮箱的 `@` 符号要经过 URL 编码，如 `user%40example.net` 表示 `user@example.net`

### 例子

1. 克隆一个指定的分支并且只跟踪指定的分支的提交历史

   ```bash
   git clone -b V1.0 --single-branch git@www.example.com:path/to/repo.git
   ```

2. 克隆到一个指定的空文件夹

   ```bash
   git clone http://192.168.1.1:9527/path/to/repo.git /usr/local/src/repo/
   ```

## status

仓库状态

```
git status [<options>…​] [--] [<pathspec>…​]
```

### 选项

| 选项                 | 说明   |
| :------------------- | :---------------------- |
| -s, --short          | 展示较少的信息 |
| -b, --branch         | 展示分支 |
| --long               | 展示更多信息 |
| -v, --verbose        | 除了展示变更的文件外，还展示变更的文件内容，指定两次此选项还会展示尚未暂存的更改 |
| --ignored[=\<mode\>] | 也展示被忽略的文件，mode 支持的值为<br>traditional - 显示忽略的文件和目录，除非指定了 --untracked-files=all，在这种情况下显示忽略目录中的单个文件<br>no - 不显示被忽略的文件<br>matching - 显示与忽略模式匹配的忽略文件和目录。 |
| --renames, --no-renames | 文件被重命名检测 |
| --find-renames[=\<n\>] | 打开重命名检测 |
| \<pathspec\> | 指定路径的变化状态（路径描述支持相对路径和通配符） |

> 一般路径通配符最常用的是`*`和`?`，`*`表示零个或多个字符，`?`表示零个或一个字符；还有一种特殊的`**`表示，这个表示仅在使用路径分隔符时有效，如`**/a`代表任意目录中的一个子目录a，
> `/opt/**/abc.conf`代表/opt目录中任意深度层级下的文件abc.conf，而`/a/b/**`代表目录/a/b下的任意深度的文件和目录

### 例子

1. 查看指定前缀路径下的文件变化状态

   ```bash
   git status config*
   ```

2. 查看所有变更的文件且包含忽略的文件，以简短的方式展示

   ```bash
   git status --ignored=traditional -s
   ```

Last Modified 2021-08-26

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

>Git 在您当前使用的任何存储库的 Git 目录 (.git/config) 中的配置文件中查找配置值。这些值特定于该单个存储库。
>例如，假设您设置 Git 的全局配置使用您的个人电子邮件地址。如果您希望将工作电子邮件用于特定项目而不是个人电子邮件，
>则该更改将添加到此文件中。

- description 文件 - 这个文件只被 GitWeb 程序使用，所以我们可以忽略它
- hooks 目录- 这是我们可以放置客户端或服务器端脚本的地方，我们可以使用它们来挂钩 Git 的不同生命周期事件
- info 目录 - 包含全局排除文件
- objects 目录 - 该目录将存储我们所做的所有提交
- refs 目录 - 该目录包含指向提交的指针（基本上是“分支”和“标签”）

### 例子

1. 创建一个 /path/to/my/codebase/.git 目录
2. 将所有现有文件添加到暂存区中
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

>HTTP 协议还支持直接在 URL 中指定账户和密码，如 `https://user:password@repositores.com/path/to/repo.git` 但要注意用户名
>和密码出现了特殊字符时要先使用 URL 编码，如账户名是一个邮箱，则邮箱的 `@` 符号要经过 URL 编码，如 `user%40example.net`
>表示 `user@example.net`

### 例子

1. 克隆一个指定的分支并且只跟踪指定的分支的提交历史

   ```bash
   git clone -b V1.0 --single-branch git@www.example.com:path/to/repo.git
   ```

2. 克隆到一个指定的空文件夹

   ```bash
   git clone http://192.168.1.1:9527/path/to/repo.git /usr/local/src/repo/
   ```

3. 浅克隆后设置可以获取所有的分支

   ```bash
   git clone --depth 100 git@www.example.com/repository.git
   cd repository
   git config remote.origin.fetch '+refs/heads/*:refs/remotes/origin/*'
   git fetch --all
   ```

## commit

记录对存储库的更改

```
git commit [-a | --interactive | --patch] [-s] [-v] [-u<mode>] [--amend]
           [--dry-run] [(-c | -C | --fixup | --squash) <commit>]
           [-F <file> | -m <msg>] [--reset-author] [--allow-empty]
           [--allow-empty-message] [--no-verify] [-e] [--author=<author>]
           [--date=<date>] [--cleanup=<mode>] [--[no-]status]
           [-i | -o] [--pathspec-from-file=<file> [--pathspec-file-nul]]
           [-S[<keyid>]] [--] [<pathspec>…​]
```

### 选项

| 选项                                       | 说明                                                                                                                                                                                                                                    |
| :----------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -a, --all                                  | 自动暂存已修改和删除的文件，没有跟踪的新文件不受影响                                                                                                                                                                                    |
| -p, --patch                                | 使用交互式补丁选择界面来选择要提交的更改                                                                                                                                                                                                |
| -C \<commit\>, --reuse-message=\<commit\>  | 获取现有的提交对象，并在创建提交时重用日志消息和作者信息（包括时间戳）                                                                                                                                                                  |
| -c \<commit\>, --reedit-message=\<commit\> | 与 `-C` 类似，但会调用编辑器                                                                                                                                                                                                            |
| --branch                                   | 以简短格式显示分支和跟踪信息                                                                                                                                                                                                            |
| --author=\<author\>                        | 覆盖提交作者。 使用标准 A U Thor \<author@example.com\>格式指定显式作者。 否则 \<author\>被假定为一个模式，用于搜索该作者的现有提交（即 rev-list --all -i --author=<author>）； 然后从找到的第一个此类提交中复制提交作者。            |
| --date=\<date\>                            | 覆盖提交中使用的作者日期                                                                                                                                                                                                                |
| -m \<msg\>, --message=\<msg\>              | 使用给定的 \<msg\>作为提交消息。如果给出了多个 -m 选项，它们的值将连接为单独的段落                                                                                                                                                     |
| -e, --edit                                 | 合并时可以进一步编辑生成的消息                                                                                                                                                                                                          |
| --amend                                    | 重新修改最后一次的提交信息                                                                                                                                                                                                              |
| -u[\<mode\>], --untracked-files[=\<mode\>] | 显示未跟踪的文件，\<mode\>支持的值<br>no - 不显示未跟踪的文件<br>normal - 显示未跟踪的文件和目录<br>all - 还显示未跟踪目录中的单个文件                                                                                                 |
| -v, --verbose                              | 在提交消息模板底部显示 HEAD 提交和将提交的内容之间的统一差异，通过提醒提交有哪些更改来帮助用户描述提交。请注意，此差异输出的行没有以 # 为前缀。如果指定两次，则另外显示将提交的内容与工作树文件之间的统一差异，即对跟踪文件的未暂存更改 |
| -q, --quiet                                | 安静模式，仅输出错误信息                                                                                                                                                                                                                |
| --dty-run                                  | 不创建提交，而是显示要提交的路径列表，具有将保留未提交的本地更改的路径以及未跟踪的路径                                                                                                                                                  |
| --status                                   | 使用编辑器准备提交消息时，将`git status`的输出包含在提交消息模板中                                                                                                                                                                      |
| --no-status                                | 使用编辑器准备默认提交消息时，不在提交消息模板中包含`git status`的输出                                                                                                                                                                  |
| \<pathspec\>…​                             | 在命令行上给出 pathspec 时，提交与 pathspec 匹配的文件的内容，而不记录已添加到暂存区中的更改                                                                                                                                              |


### 例子

1. 仅提交指定文件的变化

   ```bash
   git add index.html
   git commit index.html -m "Change index.html file"
   ```

2. 提交当前所有已追踪的发生变化或被删除的文件（未追踪的文件不会受到影响）

   ```bash
   git commit -a -m "change many file"
   ```

## status

仓库状态

```
git status [<options>…​] [--] [<pathspec>…​]
```

### 选项

| 选项                    | 说明                                                                                                                                                                                                                            |
| :---------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| -s, --short             | 展示较少的信息                                                                                                                                                                                                                  |
| -b, --branch            | 展示分支                                                                                                                                                                                                                        |
| --long                  | 展示更多信息                                                                                                                                                                                                                    |
| -v, --verbose           | 除了展示变更的文件外，还展示变更的文件内容，指定两次此选项还会展示尚未暂存的更改                                                                                                                                                |
| --ignored[=\<mode\>]    | 也展示被忽略的文件，mode 支持的值为<br>traditional - 显示忽略的文件和目录，除非指定了 --untracked-files=all，在这种情况下显示忽略目录中的单个文件<br>no - 不显示被忽略的文件<br>matching - 显示与忽略模式匹配的忽略文件和目录。 |
| --renames, --no-renames | 文件被重命名检测                                                                                                                                                                                                                |
| --find-renames[=\<n\>]  | 打开重命名检测                                                                                                                                                                                                                  |
| \<pathspec\>…​          | 指定路径的变化状态（路径描述支持相对路径和通配符）                                                                                                                                                                              |

>一般路径通配符最常用的是`*`和`?`，`*`表示零个或多个字符，`?`表示零个或一个字符；还有一种特殊的`**`表示，这个表示仅在使
>用路径分隔符时有效，如`**/a`代表任意目录中的一个子目录 a， `/opt/**/abc.conf`代表/opt 目录中任意深度层级下的文件
>abc.conf，而`/a/b/**`代表目录/a/b 下的任意深度的文件和目录

### 例子

1. 查看指定前缀路径下的文件变化状态

   ```bash
   git status config*
   ```

2. 查看所有变更的文件且包含忽略的文件，以简短的方式展示

   ```bash
   git status --ignored=traditional -s
   ```

Last Modified 2022-09-06

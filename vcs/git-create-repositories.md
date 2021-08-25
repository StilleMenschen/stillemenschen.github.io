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

## status

仓库状态

Last Modified 2021-08-25

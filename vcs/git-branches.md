# Git 仓库分支

## tag

创建、列出、删除或验证使用 GPG 签名的标签对象

```
git tag [-a | -s | -u <keyid>] [-f] [-m <msg> | -F <file>] [-e]
        <tagname> [<commit> | <object>]
git tag -d <tagname>…​
git tag [-n[<num>]] -l [--contains <commit>] [--no-contains <commit>]
        [--points-at <object>] [--column[=<options>] | --no-column]
        [--create-reflog] [--sort=<key>] [--format=<format>]
        [--merged <commit>] [--no-merged <commit>] [<pattern>…​]
git tag -v [--format=<format>] <tagname>…​
```

### 选项

<style>
table th:first-of-type {
    width: 24%;
}
</style>

| 选项                                | 说明                                                                                          |
| :---------------------------------- | :-------------------------------------------------------------------------------------------- |
| -a, --annotate                      | 制作一个无符号的注释标记对象                                                                  |
| -f, --force                         | 替换已存在的同名称标记                                                                        |
| -d, --delete                        | 删除已存在的标记                                                                              |
| -n\<num\>                           | 使用 `-l, --list` 选项时指定展示的行数\<num\>                                                 |
| -l, --list                          | 列表展示所有标记                                                                              |
| -i, --ignore-case                   | 过滤或排序时忽略大小写                                                                        |
| --column[=\<options\>], --no-column | 单列展示标记                                                                                  |
| --contains [\<commit\>]             | 只有列出包含指定提交的标记                                                                    |
| --no-contains [\<commit\>]          | 只有列出不包含指定提交的标记                                                                  |
| --points-at \<object\>              | 仅列出给定对象的标记                                                                          |
| -m \<msg\>, --message=\<msg\>       | 指定标记消息，需要同时指定`-a`或`-s`选项或者指定`-u <keyid>`                                  |
| -F \<file\>, --file=\<file\>        | 从给定文件中读取消息，使用`-`表示读取标准输入，需要同时指定`-a`或`-s`选项或者指定`-u <keyid>` |
| -e, --edit                          | 允许进一步编辑使用了`-a`或`-m`读取的消息                                                      |
| \<tagname\>                         | 要新增、修改或删除的标记名称                                                                  |
| \<commit\>, \<object\>              | 要添加标记的提交或对象，如过不指定则表示取当前`HEAD`                                          |

## branch

列出、创建或删除分支

```
git branch [--color[=<when>] | --no-color] [--show-current]
        [-v [--abbrev=<n> | --no-abbrev]]
        [--column[=<options>] | --no-column] [--sort=<key>]
        [--merged [<commit>]] [--no-merged [<commit>]]
        [--contains [<commit>]] [--no-contains [<commit>]]
        [--points-at <object>] [--format=<format>]
        [(-r | --remotes) | (-a | --all)]
        [--list] [<pattern>…​]
git branch [--track | --no-track] [-f] <branchname> [<start-point>]
git branch (--set-upstream-to=<upstream> | -u <upstream>) [<branchname>]
git branch --unset-upstream [<branchname>]
git branch (-m | -M) [<oldbranch>] <newbranch>
git branch (-c | -C) [<oldbranch>] <newbranch>
git branch (-d | -D) [-r] <branchname>…​
git branch --edit-description [<branchname>]
```

| 选项                                | 说明                                                                         |
| :---------------------------------- | :--------------------------------------------------------------------------- |
| -d, --delete                        | 删除已存在的标记                                                             |
| -D                                  | 类似`--delete --force`                                                       |
| -f, --force                         | 强制执行，即使分支名称已经存在，配合 `-c`、`-d`或`-m`选项                    |
| -m, --move                          | 重命名/移动分支和对应的 reflog                                               |
| -M                                  | 类似`--move --force`                                                         |
| -c, --copy                          | 复制分支和对应的 reflog                                                      |
| -C                                  | 类似`--copy --force`                                                         |
| -i, --ignore-case                   | 过滤或排序时忽略大小写                                                       |
| --column[=\<options\>], --no-column | 单列展示分支                                                                 |
| -r, --remotes                       | 列出远程分支或删除远程分支（配合`-d`选项）                                   |
| -a, --all                           | 列出远程和本地的所有分支                                                     |
| -l, --list                          | 列出分支，配合 \<pattern\> 可以过滤分支，如`git branch --list 'maint-*'`     |
| --show-current                      | 显示当前分支，在分离`HEAD`的情况下不会有任何输出                             |
| -v, -vv, --verbose                  | 在列表模式下展示每个`HEAD`的 sha1 和提交标题行，以及上游分支的关系（如果有） |
| -q, --quiet                         | 创建或删除分支时仅输出错误消息                                               |
| --contains [\<commit\>]             | 仅列出包含指定提交的分支                                                     |
| --no-contains [\<commit\>]          | 列出不包含指定提交的分支                                                     |
| --merged [\<commit\>]               | 仅列出合并了该提交的分支                                                     |
| --no-merged [\<commit\>]            | 列出未合并了该提交的分支                                                     |
| \<branchname\>                      | 新分支名称                                                                   |
| \<start-point\>                     | 新分支的起始点，可以指定分支、标签或提交的 ID，默认为当前`HEAD`              |
| \<oldbranch\>                       | 旧分支名称，用于重命名或拷贝分支                                             |
| \<newbranch\>                       | 新分支名称，用于重命名或拷贝分支                                             |
| --points-at \<object\>              | 仅列出与给定对象有关联的分支                                                 |

### 例子

1. 检出分支到新分支并切换

   ```bash
   git branch my2.6.14 v2.6.14
   git switch my2.6.14
   ```

   > 也可以使用 `checkout -b my2.6.14 v2.6.14` 替代上面的两个命令

2. 列出符合条件的分支

   ```bash
   git branch -r -l '<remote>/<pattern>'
   git for-each-ref 'refs/remotes/<remote>/<pattern>'
   ```

## checkout

切换分支或恢复工作树文件

```
git checkout [-q] [-f] [-m] [<branch>]
git checkout [-q] [-f] [-m] --detach [<branch>]
git checkout [-q] [-f] [-m] [--detach] <commit>
git checkout [-q] [-f] [-m] [[-b|-B|--orphan] <new_branch>] [<start_point>]
git checkout [-f|--ours|--theirs|-m|--conflict=<style>] [<tree-ish>] [--] <pathspec>…​
git checkout [-f|--ours|--theirs|-m|--conflict=<style>] [<tree-ish>] --pathspec-from-file=<file> [--pathspec-file-nul]
git checkout (-p|--patch) [<tree-ish>] [--] [<pathspec>…​]
```

## merge

将两个或多个开发历史连接在一起

```
git merge [-n] [--stat] [--no-commit] [--squash] [--[no-]edit]
        [--no-verify] [-s <strategy>] [-X <strategy-option>] [-S[<keyid>]]
        [--[no-]allow-unrelated-histories]
        [--[no-]rerere-autoupdate] [-m <msg>] [-F <file>] [<commit>…​]
git merge (--continue | --abort | --quit)
```

Last Modified 2021-08-29

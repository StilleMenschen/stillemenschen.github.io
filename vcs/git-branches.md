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

### 选项

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
| -l, --list                          | 列出分支，配合 \<pattern\>可以过滤分支，如`git branch --list 'maint-*'`     |
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

   >也可以使用 `checkout -b my2.6.14 v2.6.14` 替代上面的两个命令

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

### 选项

| 选项                                      | 说明                                                                                                                                                |
| :---------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------- |
| -q, --quiet                               | 安静模式，仅输出错误信息                                                                                                                            |
| --progress, --no-progress                 | 除非指定了`--quiet`选项，否则展示处理进度                                                                                                           |
| --ours, --theirs                          | 发生冲突时，选择保留本地的（ours）还是保留远端的（theirs）                                                                                          |
| -f, --force                               | 切换分支时，即使暂存区或工作树与 HEAD 不同，也继续执行。用于丢弃本地更改。从暂存区中检出路径时，不会因未合并的条目而失败；相反，未合并的条目将被忽略    |
| -b \<new_branch\>                         | 创建一个新分支并从指定的提交点开始，默认情况下\<start_point\>为当前 HEAD                                                                            |
| -B \<new_branch\>                         | 创建一个新分支并从指定的提交点开始，默认情况下\<start_point\>为当前 HEAD，如果分支已经存在则将其重置到指定的提交点                                  |
| -l                                        | 创建新分支的 reflog                                                                                                                                 |
| --orphan \<new_branch\>                   | 创建一个新分支并从指定的提交点开始，且不包含任何提交记录，默认情况下\<start_point\>为当前 HEAD                                                      |
| -m, --merge                               | 如果检出的分支与当前工作树和要检出的分支一个或多个文件存在差异时则执行合并，合并存在冲突时将保留冲突的文件，需要使用 `git add` 标记已解决冲突的路径 |
| --conflict=\<style\>                      | 与 `--merge` 选项相同，但改变了冲突块的显示方式，可能的值为 merge（默认）和 diff3（除了 merge 样式显示的内容外，还显示原始内容）                    |
| --overwrite-ignore, --no-overwrite-ignore | 切换分支时静默覆盖被忽略的文件（默认）当新分支包含被忽略的文件时，使用 `--no-overwrite-ignore`中止操作                                              |

## merge

将两个或多个开发历史连接在一起

```
git merge [-n] [--stat] [--no-commit] [--squash] [--[no-]edit]
        [--no-verify] [-s <strategy>] [-X <strategy-option>] [-S[<keyid>]]
        [--[no-]allow-unrelated-histories]
        [--[no-]rerere-autoupdate] [-m <msg>] [-F <file>] [<commit>…​]
git merge (--continue | --abort | --quit)
```

### 选项

| 选项                                        | 说明                                                                                                                                                                                                                                   |
| :------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| --commit, --no-commit                       | 合并且提交                                                                                                                                                                                                                             |
| --edit, -e, --no-edit                       | 合并时可以进一步编辑生成的消息，如果指定了 `--no-edit` 则直接使用生成的消息                                                                                                                                                            |
| --stat, -n, --no-stat                       | 合并结束后显示对比差异                                                                                                                                                                                                                 |
| --squash, --no-squash                       | 生成工作树和暂存区状态，就像真正的合并发生一样（合并信息除外），但实际上不进行提交、移动 HEAD 或记录 $GIT_DIR/MERGE_HEAD（导致下一个 git commit 命令创建一个 合并提交）。 这允许您在当前分支之上创建单个提交，其效果与合并另一个分支相同 |
| -q, --quiet                                 | 安静模式，仅输出错误信息                                                                                                                                                                                                               |
| -v, --verbose                               | 详细处理过程                                                                                                                                                                                                                           |
| --progress, --no-progress                   | 查看或关闭进度                                                                                                                                                                                                                         |
| -m \<msg\>                                  | 合并的消息                                                                                                                                                                                                                             |
| -s \<strategy\>, --strategy=\<strategy\>    | 指定合并策略，支持的值 resolve、recursive、octopus（默认）、ours、subtree                                                                                                                                                                      |
| -X \<option\>, --strategy-option=\<option\> | 传递给合并策略的选项                                                                                                                                                                                                                   |
| --abort                                     | 中止当前的冲突解决过程，并尝试重建合并前的状态                                                                                                                                                                                         |
| --continue                                  | 在 `git merge` 由于冲突而停止后，您可以通过运行 `git merge --continue` 来结束合并                                                                                                                                                      |
| \<commit\>…​                                | 指定多个要合并的提交，若没有指定则合并当前分支配置为用作其上游的远程跟踪分支                                                                                                                                                           |

### 例子

1. 合并分支 fixes 和 enhancements 到当前分支之上，使用 octopus 合并策略

   ```bash
   git merge fixes enhancements
   ```

2. 合并分支 obsolete 到当前分支之上，使用 ours 合并策略

   ```bash
   git merge -s ours obsolete
   ```

3. 将分支 maint 合并到当前分支中，但不自动执行提交

   ```bash
   git merge --no-commit maint
   ```

## 附录

| Commit-ish/Tree-ish       | Examples                                   |
| :------------------------ | :----------------------------------------- |
| 1. \<sha1\>               | `dae86e1950b1277e545cee180551750029cfe735` |
| 2. \<describeOutput\>     | `v1.7.4.2-679-g3bee7fb`                    |
| 3. \<refname\>            | `master, heads/master, refs/heads/master`  |
| 4. \<refname\>@{\<date\>} | `master@{yesterday}, HEAD@{5 minutes ago}` |
| 5. \<refname\>@{\<n\>}    | `master@{1}`                               |
| 6. @{\<n\>}               | `@{1}`                                     |
| 7. @{-\<n\>}              | `@{-1}`                                    |
| 8. \<refname\>@{upstream} | `master@{upstream}, @{u}`                  |
| 9. \<rev\>^               | `HEAD^, v1.5.1^0`                          |
| 10. \<rev\>~\<n\>         | `master~3`                                 |
| 11. \<rev\>^{\<type\>}    | `v0.99.8^{commit}`                         |
| 12. \<rev\>^{}            | `v0.99.8^{}`                               |
| 13. \<rev\>^{/\<text\>}   | `HEAD^{/fix nasty bug}`                    |
| 14. :/\<text\>            | `:/fix nasty bug`                          |

| Tree-ish only        | Examples                                |
| :------------------- | :-------------------------------------- |
| 15. \<rev\>:\<path\> | `HEAD:README, :README, master:./README` |

| Tree-ish?           | Examples             |
| :------------------ | :------------------- |
| 16. :\<n\>:\<path\> | `:0:README, :README` |

Last Modified 2021-09-05

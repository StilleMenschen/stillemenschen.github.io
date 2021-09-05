# Git 提交处理

## revert

还原一些现有的提交

```
git revert [--[no-]edit] [-n] [-m parent-number] [-s] [-S[<keyid>]] <commit>…​
git revert (--continue | --skip | --abort | --quit)
```

### 选项

<style>
table th:first-of-type {
    width: 24%;
}
</style>

| 选项                                        | 说明                                                                                                                                                                                                                               |
| :------------------------------------------ | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| \<commit\>…​                                | 要进行恢复的提交                                                                                                                                                                                                                   |
| -e, --edit                                  | 提交还原前可以进一步编辑生成的提交消息                                                                                                                                                                                             |
| --no-edit                                   | 使用此选项，`git revert`将不会启动提交消息编辑器                                                                                                                                                                                   |
| -n, --no-commit                             | 通常，该命令会自动创建一些带有提交日志消息的提交，说明哪些提交已被还原。此标志应用将命名提交还原到您的工作树和暂存区所需的更改，但不进行提交。此外，使用此选项时，您的暂存区不必与 HEAD 提交匹配。恢复是针对暂存区的开始状态完成的 |
| -s \<strategy\>, --strategy=\<strategy\>    | 指定合并策略，支持的值 resolve、recursive、octopus（默认）、ours、subtree                                                                                                                                                          |
| -X \<option\>, --strategy-option=\<option\> | 传递给合并策略的选项                                                                                                                                                                                                               |

### 冲突解决

| 选项       | 说明                                         |
| :--------- | :------------------------------------------- |
| --continue | 在冲突的文件解决冲突后，继续操作合并         |
| --skip     | 跳过产生冲突的提交，继续处理剩下未处理的提交 |
| --quit     | 放弃当前的操作，且不会回滚提交状态           |
| --abort    | 取消操作并回滚到操作前的提交状态             |

### 例子

1. 还原 HEAD 中最后第四次提交指定的更改，并使用还原的更改创建新提交

   ```bash
   git revert HEAD~3
   ```

2. 将提交所做的更改从 master 中的倒数第五次提交（包括）恢复到 master 中的倒数第三次提交（包括），但不要使用恢复的更改创
   建任何提交。恢复只修改工作树和暂存区

   ```bash
   git revert -n master~5..master~2
   ```

## reset

将当前 HEAD 重置为指定状态

```
git reset [-q] [<tree-ish>] [--] <pathspec>…​
git reset [-q] [--pathspec-from-file=<file> [--pathspec-file-nul]] [<tree-ish>]
git reset (--patch | -p) [<tree-ish>] [--] [<pathspec>…​]
git reset [--soft | --mixed [-N] | --hard | --merge | --keep] [-q] [<commit>]
```

### 选项

| 选项                          | 说明                                                                                                                   |
| :---------------------------- | :--------------------------------------------------------------------------------------------------------------------- |
| -q, --quiet, --no-quiet       | 安静，只报告错误                                                                                                       |
| --pathspec-from-file=\<file\> | Pathspec 在 \<file\> 而不是命令行参数中传递。 如果 \<file\> 正好是`-`则读取标准输入。 Pathspec 元素由 LF 或 CR/LF 分隔 |
| --pathspec-file-nul           | 仅对 `--pathspec-from-file` 有意义。 Pathspec 元素用 NUL 字符分隔，所有其他字符都按字面意思（包括换行符和引号）        |
| --                            | 不要将更多参数解释为选项                                                                                               |
| \<pathspec\>…​                | 限制受操作影响的路径                                                                                                   |

### 重置模式

| 选项    | 说明                                                                                                                                                                                         |
| :------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| --soft  | 还原更改到暂存区                                                                                                                                                                             |
| --mixed | 保留更改的文件但未标记为提交（暂存）                                                                                                                                                         |
| --hard  | 重置暂存区和工作树。自 \<commit\> 以来对工作树中跟踪文件的任何更改都将被丢弃                                                                                                                 |
| --merge | 重置暂存区并更新工作树中 \<commit\> 和 HEAD 之间不同的文件，但保留暂存区和工作树之间不同的文件（即具有尚未添加的更改）。如果 \<commit\> 和暂存区之间不同的文件具有未暂存的更改，则重置将中止 |
| --keep  | 重置暂存区条目并更新工作树中 \<commit\> 和 HEAD 之间不同的文件。 如果 \<commit\> 和 HEAD 之间的文件有本地更改，则重置将中止                                                                  |

> 尽量少使用 `reset`，如果确定需要执行重置提交，可以先将当前的 HEAD 拉一个新分支暂时保留更改，再做重置操作

## cherry-pick

应用一些现有提交引入的更改到其它分支

```
git cherry-pick [--edit] [-n] [-m parent-number] [-s] [-x] [--ff]
                  [-S[<keyid>]] <commit>…​
git cherry-pick (--continue | --skip | --abort | --quit)
```

### 选项

| 选项                                        | 说明                                                                                                                                                                                                                               |
| :------------------------------------------ | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| \<commit\>…​                                | 要应用到当前工作树的现有提交                                                                                                                                                                                                       |
| -e, --edit                                  | 提交还原前可以进一步编辑生成的提交消息                                                                                                                                                                                             |
| -n, --no-commit                             | 通常，该命令会自动创建一些带有提交日志消息的提交，说明哪些提交已被还原。此标志应用将命名提交还原到您的工作树和暂存区所需的更改，但不进行提交。此外，使用此选项时，您的暂存区不必与 HEAD 提交匹配。恢复是针对暂存区的开始状态完成的 |
| -x                                          | 在记录提交时，在原始提交消息中附加一行“(cherry pick from commit ... )”，以表明此更改是从哪个提交中挑选出来的。这仅适用于没有冲突的樱桃选择                                                                                         |
| -s \<strategy\>, --strategy=\<strategy\>    | 指定合并策略，支持的值 resolve、recursive、octopus（默认）、ours、subtree                                                                                                                                                          |
| -X \<option\>, --strategy-option=\<option\> | 传递给合并策略的选项                                                                                                                                                                                                               |

### 例子

1. 在 master 分支的 HEAD 应用由提交引入的更改，并使用此更改创建新提交

   ```bash
   git cherry-pick master
   ```

2. 应用由 master 的祖先而不是 HEAD 的所有提交引入的更改来生成新的提交

   ```bash
   git cherry-pick ..master
   git cherry-pick ^HEAD master
   ```

3. 应用由 maint 或 next 的祖先的所有提交引入的更改，但不是 master 或其任何祖先。请注意，后者并不意味着 maint 和 master
   和 next 之间的一切；具体来说，如果 maint 包含在 master 中，则不会使用 maint

   ```bash
   git cherry-pick maint next ^master
   git cherry-pick maint master..next
   ```

4. 应用 master 指向的第五个和第三个最后提交引入的更改，并使用这些更改创建 2 个新提交

   ```bash
   git cherry-pick master~4 master~2
   ```

5. 将 master 指向的第二个最后提交和 next 指向的最后一个提交引入的更改应用于工作树和暂存区，但不要使用这些更改创建任何提交

   ```bash
   git cherry-pick -n master~1 next
   ```

6. 如果历史是线性的并且 HEAD 是 next 的祖先，则更新工作树并推进 HEAD 指针以匹配下一个。否则，将那些在 next 但不是 HEAD 中的提交引入的更改应用到当前分支，为每个新更改创建一个新提交

   ```bash
   git cherry-pick --ff ..next
   ```

7. 将涉及 README 的主分支上的所有提交引入的更改应用于工作树和暂存区，因此可以检查结果并在合适的情况下将其制成单个新提交

   ```bash
   git rev-list --reverse master -- README | git cherry-pick -n --stdin
   ```

Last Modified 2021-09-06

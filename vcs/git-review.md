# 预览 Git 仓库

## log

查看提交历史

```
git log [<options>] [<revision range>] [[--] <path>…​]
```

- 向下滚动
  - `j` 或 `Arrow Down`，滚动一行
  - `d` 滚动半页
  - `f` 或 `Page Down` 滚动一页
- 向上滚动
  - `k` 或 `Arrow Up`，滚动一行
  - `u` 滚动半页
  - `b` 或 `Page Up` 滚动一页
- 按 `q` 退出预览

### 选项

<style>
table th:first-of-type {
    width: 24%;
}
</style>

| 选项                                               | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| :------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| --source                                           | 每个提交展示具体的分支                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| --log-size                                         | 展示日志大小                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| -L\<start\>,\<end\>:\<file\>                       | 展示指定文件的指定行变更历史，其中\<start\>和\<end\>支持如下表达式<br>`number` - 如果 \<start\> 或 \<end\> 是一个数字，它指定一个绝对行号（行数从 1 开始）<br>`/regex/` - 此表单将使用与给定 POSIX 正则表达式匹配的第一行。如果 \<start\> 是正则表达式，它将从前一个 -L 范围的末尾开始搜索（如果有），否则从文件的开头开始。如果 \<start\> 是 ^/regex/，它将从文件的开头搜索。如果 \<end\> 是正则表达式，它将从 \<start\> 给出的行开始搜索<br> `+offset` or `-offset` - 这仅对 \<end\> 有效，并将指定在 \<start\> 给出的行之前或之后的行数。 |
| -\<number\>, -n \<number\>, --max-count=\<number\> | 指定预览展示最大提交数量                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| --skip=\<number\>                                  | 跳过开始\<number\>次提交                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| --since=\<date\>, --after=\<date\>                 | 展示指定时间之后的提交，\<date\>支持 ISO 格式的日期时间表示，如 `1990-01-01T14:00:00+05:00`、`1 month 3 days 5 hours 7 minutes 6 seconds`                                                                                                                                                                                                                                                                                                                                                                                                    |
| --until=\<date\>, --before=\<date\>                | 展示指定时间之前的提交，\<date\>支持 ISO 格式的日期时间表示，如 `1990-01-01T14:00:00+05:00`、`1 month 3 days 5 hours 7 minutes 6 seconds`                                                                                                                                                                                                                                                                                                                                                                                                    |
| --author=\<pattern\>, --committer=\<pattern\>      | 过滤提交人（支持使用简单的正则表达式），可以指定多次此选项，表示多个不同的提交人                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| --grep=\<pattern\>                                 | 过滤包含有指定内容的提交（支持使用简单的正则表达式），可以指定多次此选项                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| --all-match                                        | 过滤满足所有`--grep`过滤选项的提交                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| --invert-grep                                      | 过滤不满足`--grep`过滤选项的提交                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| -i, --regexp-ignore-case                           | 正则表达式忽略大小写                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| --basic-regexp                                     | 使用最基本的正则表达式解析引擎                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| -E, --extended-regexp                              | 使用拓展的正则表达式解析引擎，支持正则表达式的大部分元字符                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| -F, --fixed-strings                                | 不解析正则表达式，按一般的字符串来处理                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| --merges                                           | 过滤合并的提交                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| --no-merges                                        | 过滤非合并的提交                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| --oneline                                          | 每个提交记录仅展示一行                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| --relative-date                                    | 展示的提交的相对时间，类似 `--date=relative`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| --date=\<format\>                                  | 提交时间格式化，\<format\>支持的值：`relative`、`local`、`iso`、`iso-strict`、`rfc`、`short`、`raw`、`human`、`unix`、`default`                                                                                                                                                                                                                                                                                                                                                                                                              |
| --graph                                            | 绘制分支基本图表                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| --pretty=\<format\>, --format=\<format\>           | 格式化输出，\<format\>支持的值：`oneline`、`short`、`medium`、`full`、`fuller`、`reference`、`email`、`mboxrd`、`raw`                                                                                                                                                                                                                                                                                                                                                                                                                        |
| -p, -u, --patch                                    | 展示变更文件内容的具体差异                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| --raw                                              | 展示原始的提交数据                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| --patch-with-raw                                   | 类似 `-p --raw`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| --stat=[=\<width\>[,\<name-width\>[,\<count\>]]]   | 生成文件内容差异，\<width\>为输出内容宽度，\<name-width\>为输出文件名宽度，\<count\>为数量                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| --patch-with-stat                                  | 类似 `-p --stat`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |

### 输出格式化

- oneline

  ```
  <hash> <title line>
  ```

- short

  ```
  commit <hash>
  Author: <author>
  <title line>
  ```

- medium

  ```
  commit <hash>
  Author: <author>
  Date:   <author date>
  <title line>
  <full commit message>
  ```

- full

  ```
  commit <hash>
  Author: <author>
  Commit: <committer>
  <title line>
  <full commit message>
  ```

- fuller

  ```
  commit <hash>
  Author:     <author>
  AuthorDate: <author date>
  Commit:     <committer>
  CommitDate: <committer date>
  <title line>
  <full commit message>
  ```

- reference

  ```
  <abbrev hash> (<title line>, <short author date>)
  ```

- email

  ```
  From <hash> <date>
  From: <author>
  Date: <author date>
  Subject: [PATCH] <title line>
  <full commit message>
  ```

- mboxrd

  与 `email` 类似但在 Form 中有 `>`表示的引用

- raw

  原始的数据

### 例子

1. 不展示合并的提交

   ```bash
   git log --no-merges
   ```

2. 查看自 v2.6.12 版本以来指定路径的所有变化

   ```
   git log v2.6.12.. include/scsi drivers/scsi
   ```

3. 查看两周内的提交

   ```
   git log --since="2 weeks ago"
   ```

4. 查看指定文件的变化历史

   ```
   git log --follow builtin/rev-list.c
   ```

5. 显示任何本地分支中的所有提交，但不在任何远程跟踪分支中的源（远程仓库中不存在的）

   ```
   git log --branches --not --remotes=origin
   ```

6. 显示在本地 main 分支存在，但在任何远程 main 分支中不存在的提交

   ```
   git log main --not --remotes=*/main
   ```

7. 显示文件 main.c 中的函数 main() 如何随时间演变。

   ```
   git log -L '/int main/',/^}/:main.c
   ```

8. 展示最近 3 次的提交

   ```
   git log -3
   ```

9. 查看 `2020-01-01 13:00:00` 到 `2020-02-23 08:00:00` 之间的提交

   ```
   git log --since="2020-01-01 13:00:00" --until="2020-02-23 08:00:00"
   ```

## show

查看具体的提交明细信息

```
git show [<options>] [<object>…​]
```

### 例子

1. 查看某次提交的信息

   ```
   git show 7de3107
   ```

## diff

对比文件内容差异

```
git diff [<options>] [<commit>] [--] [<path>…​]
git diff [<options>] --cached [--merge-base] [<commit>] [--] [<path>…​]
git diff [<options>] [--merge-base] <commit> [<commit>…​] <commit> [--] [<path>…​]
git diff [<options>] <commit>…​<commit> [--] [<path>…​]
git diff [<options>] <blob> <blob>
git diff [<options>] --no-index [--] <path> <path>
```

### 例子

1. 对比同一个文件在两个分支中的差异

   ```
   git diff remotes/origin/a..remotes/origin/b index.html
   ```

2. 对比当前修改的文件和某次提交的差异

   ```
   git diff 04baf0f index.html
   ```

Last Modified 2021-08-27

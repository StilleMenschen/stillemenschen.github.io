# Git 仓库分支

## tag

为提交添加标记

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

## checkout

## merge

Last Modified 2021-08-28

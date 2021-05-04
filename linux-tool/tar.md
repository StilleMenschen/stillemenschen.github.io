# tar

## 简介

tar 创建和处理档案，这些档案实际上是许多其他文件的集合。该程序为用户提供了一种用于控制大量数据的有组织的系统方法。名
称`tar`最初来自短语`"Tape ARchive"`，但是存档不必（而且如今通常不需要）存储在磁带上

```
tar [OPTION...] [FILE]...
```

## 选项

<style>
table th:first-of-type {
    width: 18%;
}
</style>

| 选项                         | 说明                                                                                                                                   |
| :--------------------------- | :------------------------------------------------------------------------------------------------------------------------------------- |
| -c, --create                 | 创建一个新的 tar 归档文件                                                                                                              |
| -r, --append                 | 追加文件到归档文件中，仅支持单独以`-c`选项归档的文件（可能会令归档文件中存在同名文件）                                                 |
| -u, --update                 | 追加文件到归档文件中，前提是比归档中的文件新或者不存在，仅支持单独以`-c`选项归档的文件                                                 |
| -t, --list                   | 列出档案的内容                                                                                                                         |
| -x, --extract                | 从存档中提取一个或多个文件，如果不指定文件名则默认提取所有                                                                             |
| -d, --compare                | 将存档内的文件与文件系统中的文件进行比较，并报告文件大小，模式，所有者，修改日期和内容的差异，如果比较的文件在归档中不存在，则返回错误 |
| --delete                     | 从存档中删除指定文件，文件路径必须是完整的路径，否则不会执行删除操作                                                                   |
| -f name, --file=name         | 指定归档文件名称`name`                                                                                                                 |
| -T file, --files-from=file   | 参考文件`file`中指定的目录或文件进行归档（`file`中每一行只能写一个归档项，如果是相对路径要注意执行命令的目录）                         |
| -v, --verbose                | 在 tar 运行时显示正在处理的文件                                                                                                        |
| -h, --dereference            | 在读取或写入要归档的文件时，tar 访问符号链接指向的文件，而不是符号链接本身                                                             |
| -C dir, --directory=dir      | 指定此选项后，tar 将在执行任何操作之前将其当前目录更改为 dir，在归档创建期间使用此选项时，它对选项顺序敏感                             |
| --exclude=pattern            | 按`pattern`排除目录或文件（`pattern`支持 shell 中的通配符，如`*`、`?`、`[abc]`、`[a-z]`、`[!a-z]`、`{s1,s2,...}`）                     |
| -X file, --exclude-from=file | 与`--exclude`相似，但 tar 将使用文件`file`中的模式列表（`file`中每一行只能写一个排除项）                                               |
| --exclude-vcs-ignores        | 排除版本控制相关的文件，如`cvsignore`，`.gitignore`，`.bzrignore`，`.hgignore`                                                         |
| --exclude-vcs                | 排除版本控制相关的目录和文件，如`.git/`，`.gitignore`，`.svn/`等                                                                       |
| -z, --gzip, --gunzip         | 此选项告诉 tar 通过 gzip 读取或写入档案，从而允许 tar 透明地直接对多种压缩档案进行操作                                                 |
| --ignore-case                | 忽略`pattern`中的大小写字母                                                                                                            |
| --no-ignore-case             | 取消忽略`pattern`中的大小写字母                                                                                                        |
| --index-file=file            | 将 tar 的详细操作信息写入文件`file`                                                                                                    |
| -k, --keep-old-files         | 从归档中提取文件时，请勿覆盖现有文件。如果存在此类文件，则返回错误                                                                     |
| --overwrite                  | 从归档中提取文件时，覆盖现有的目录和文件                                                                                               |
| --overwrite-dir              | 从归档中提取文件时，覆盖现有的目录                                                                                                     |
| --remove-files               | 归档文件后删除源文件                                                                                                                   |
| --skip-old-files             | 从归档中提取文件时，忽略已经存在的文件（与`--warning=existing-file`一起使用以查看忽略了哪些文件）                                      |
| -O, --to-stdout              | 提取文件到`stdout`中                                                                                                                   |
| -?, --help                   | 显示帮助信息并退出                                                                                                                     |

## 命令示例

1. 打包`test/`目录中的所有文件，但排除与`git`相关的文件和`jpg`、`png`图片

   ```bash
   tar -cvf test.tar --exclude=*.jpg --exclude=*.png --exclude-vcs test/*
   ```

2. 解压文件但排除所有`txt`文件

   ```bash
   tar -xvf test.tar --exclude=*.txt
   ```

3. 追加目录`test/`中的所有文件到归档中，但只追加有变化或不存在的文件

   ```bash
   tar -uvf test.tar test/*
   ```

4. 打包文件夹`dist/`但不包含顶级文件夹（即少一层`dist/`文件夹）
   ```bash
   tar -cvf web.tar -C dist/ $(ls --file-type dist/ | sed 's/[@|=>]$//g')
   ```

Last Modified 2021-04-11

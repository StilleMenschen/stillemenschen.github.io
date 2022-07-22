# diff

逐行比较文件内容

```
diff [OPTION]... FILES
```

>与 diff 类似的一个命令是 cmp，cmp 可以逐个字节比较文件内容

## 选项

<style>
table th:first-of-type {
    width: 30%;
}
</style>

| 选项                           | 说明                                               |
| :----------------------------- | :------------------------------------------------- |
| --normal                       | 正常输出文件差异（默认）                           |
| -c, -C NUM, --context[=NUM]    | 输出 NUM（默认 3）行复制的上下文                   |
| -u, -U NUM, --unified[=NUM]    | 输出 NUM（默认 3）行统一上下文                     |
| -e, --ed                       | 输出一个 ed 脚本                                   |
| -n, --rcs                      | 输出 RCS 格式差异                                  |
| -y, --side-by-side             | 两列输出                                           |
| -W, --width=NUM                | 最多输出 NUM（默认 130）个打印列                   |
| --left-column                  | 只输出公共行的左列                                 |
| --suppress-common-lines        | 不输出公共线                                       |
| -t, --expand-tabs              | 将制表符扩展到输出中的空格                         |
| --tabsize=NUM                  | 制表符停止每 NUM（默认 8）列打印                   |
| --suppress-blank-empty         | 在空输出行之前抑制空格或制表符                     |
| -l, --paginate                 | 通过'pr'传递输出以对其进行分页                     |
| -r, --recursive                | 递归比较找到的任何子目录                           |
| --no-dereference               | 不跟踪符号链接                                     |
| -N, --new-file                 | 将不存在的文件视为空                               |
| --unidirectional-new-file      | 将不存在的第一个文件视为空                         |
| --ignore-file-name-case        | 比较文件名时忽略大小写                             |
| --no-ignore-file-name-case     | 比较文件名时不忽略大小写                           |
| -x, --exclude=PAT              | 排除匹配 PAT 的文件                                |
| -X, --exclude-from=FILE        | 排除与 FILE 中任何模式匹配的文件                   |
| -S, --starting-file=FILE       | 比较目录时以 FILE 开头                             |
| --from-file=FILE1              | 将 FILE1 与所有操作数进行比较 （FILE1 可以是目录） |
| --to-file=FILE2                | 将所有操作数与 FILE2 进行比较（FILE2 可以是目录）  |
| -i, --ignore-case              | 忽略文件内容的大小写差异                           |
| -Z, --ignore-trailing-space    | 忽略行尾的空白                                     |
| -b, --ignore-space-change      | 忽略空白的变化                                     |
| -w, --ignore-all-space         | 忽略所有空白                                       |
| -B, --ignore-blank-lines       | 忽略所有行都是空白的更改                           |
| -I, --ignore-matching-lines=RE | 忽略所有行匹配正则表达式（RE）的更改               |
| -a, --text                     | 将所有文件视为文本                                 |
| --strip-trailing-cr            | 在输入时去除尾随回车                               |
| -D, --ifdef=NAME               | 输出带有“#ifdef NAME”差异的合并文件                |
| -d, --minimal                  | 努力找到较小的一组更改                             |
| --horizon-lines=NUM            | 保留 NUM 行的公共前缀和后缀                        |
| --speed-large-files            | 假设大文件和许多分散的小变化                       |
| --help                         | 查看帮助                                           |
| -v, --version                  | 查看版本                                           |


## 命令示例

1. 比较文件并输出简单的差异

   ```bash
   diff -u file1.txt file2.txt
   ```

2. 以两列展示两个文件的差异

   ```bash
   diff -y file1.txt file2.txt
   ```

3. 以两列展示文件差异，仅左侧展示相同的行，同时忽略空白字符的差异

   ```bash
   diff -y -w --left-column --suppress-common-lines file1.txt file2.txt
   ```

4. 比较两个文件夹内的同名文件，同时将不存在的文件视为空白

   ```bash
   diff -N dir1/ dir2/
   ```

Last Modified 2021-09-15

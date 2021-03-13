# grep

## 简介

搜索指定的输入文件（如果未指定文件或给定单个`'-'`连字符，则搜索标准输入），以查找包含与给定表达式匹配的行。 默认情况下，`grep`打印匹配的行。另外还有另外两种与grep类似的命令，分别是`egrep`（grep -E）和`fgrep`（grep -F），`egrep`支持基本和扩展的正则表达式，`fgrep`不支持正则表达式，按照字符串表面意思进行匹配。

```
grep [OPTIONS] PATTERN [FILE...]
grep [OPTIONS] [-e PATTERN | -f FILE] [FILE...]
```

## 参数

参数 | 说明
:--- | :---
-i | 搜索时忽略大小写
-c | 只输出匹配行的数量
-v | 显示不包含匹配文本的所有行
-w | 匹配整词
-x | 匹配整行
-L | 仅输出匹配到的文件名，匹配到第一个文件停止搜索
-h | 查询多文件时不显示文件名
-n | 显示行号
-r | 只有使用命令行执行时，才跟随符号链接递归搜索每个目录下的文件
-R | 跟随符号链接递归搜索每个目录下的文件
--color[=WHEN], --colour[=WHEN] | 为匹配到的字符着色，`WHEN`的值为`never`，`always`或者`auto`

## 正则表达式

下面仅说明比较基本的用法，更加高级的用法自行搜索

> 在基本正则表达式中，元字符`?`，`+`，`{`，`|`，`(`和`)`失去其特殊含义；而是使用反斜杠版本`\?`，`\+`，`\{`，`\|`，`\(`和`\)`

### 字符列表

在`[`和`]`包裹的字符表示可能出现的字符列表，支持连词表示，如`[a-e]`表示`[abcde]`，`\<`和`\>`表示单词开头和结尾，`\b`表示以空格分隔的单词开头或结尾，`\B`表示非空格分隔的单词开头或结尾，`\w`表示`[0-9A-Za-z]`，`\W`表示`[^0-9A-Za-z]`

> 如果在`[`前面使用了`^`，则表示不包含，如`[^a-e]`表示不能包含字母`abcde`

### 开头结尾

`^`符号匹配开头，`$`符号匹配结尾

### 重复运算符

```
?      指定前面的字符出现一次或零次
*      指定前面的字符出现零次或多次
+      指定签名档字符出现至少一次以上
{n}    指定前面的字符出现n次
{n,}   指定前面的字符出现n次以上
{,m}   指定前面的字符出现零次且最大出现m次
{n,m}  指定前面的字符出现至少n次且最大出现m次
```

## 命令示例

> 当终端的字符编码为`UTF-8`的情况下，可以使用中文搜索

1. 输出当前目录下包含`abc`字符串的以`test`开头的所有文件和`/etc/hosts`文件

```bash
grep abc test* /etc/hosts
```

2. 输出当前目录下`test`开头的所有文件中匹配`abc`字符串行的数量

```bash
grep abc -c test*
```

3. 输出当前目录下`test`开头的所有文件中匹配`abc`字符串的行，并输出匹配行号

```bash
grep abc -n test*
```

4. 输出当前目录下所有以`test`开头的文件中不包含匹配`abc`字符串的所有行，且不显示文件名

```bash
grep abc -vh test*
```

5. 递归搜索当前目录和子目录中以`test`开头的所有文件中包含匹配`abc`字符串的行

```bash
grep abc -r test*
```

6. 计算一个文件中的空行或非空的行的数量

  空行
  ```bash
  grep ^$ -c test.txt
  ```

  非空行
  ```bash
  grep -v ^$ -c test.txt
  ```

7. 查找log文件中2021年1月到3月且1号到19号的`Error`级别的信息，同时忽略大小写

```bash
grep -i '2021-0[1-3]-[01][0-9].* Error ' main.log
```

Last Modified 2021-03-13
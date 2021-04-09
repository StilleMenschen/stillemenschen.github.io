# sed

## 简介

sed是一个流编辑器。流编辑器用于对输入流（文件或来自管道的输入）执行基本的文本转换。尽管在某种程度上类似于允许脚本编辑（例如ed）的编辑器，但sed通过仅对输入进行一次传递来工作，因此效率更高。

```
sed [OPTION]... {script-only-if-no-other-script} [input-file]...
```

## 参数

参数 | 说明
:- | :-
-n, --quiet, --silent              | 仅显示处理后的结果
-e script, --expression=script     | 将选项指定的脚本添加到要执行的命令中
-f script-file, --file=script-file | 将指定脚本文件的内容添加到要执行的命令中
-i[SUFFIX], --in-place[=SUFFIX]    | 将处理结果直接写入文件，如果指定了`SUFFIX`，则按`SUFFIX`备份源文件
-r, --regexp-extended              | 在脚本中使用扩展的表达式（特殊字符不需要转义）
-s, --separate                     | 将多个文件视为同一个文件处理
-u, --unbuffered                   | 从输入文件中加载最少量的数据，并更频繁地刷新输出缓冲区
--help                             | 打印帮助信息
--version                          | 打印版本信息

> 如果未提供`-e`，`-expression`，`-f`或`--file`选项，则将第一个非选项参数用作要解释的sed脚本，其余所有参数均为输入文件的名称；如果未指定输入文件，那么将读取标准输入。

> 若不指定`-r`选项，正则表达式中的元字符`?`，`+`，`{`，`|`，`(`和`)`失去其特殊含义；需要添加反斜杠表示`\?`，`\+`，`\{`，`\|`，`\(`和`\)`

## 脚本命令

> 默认情况下，sed会将每一行放入一个模式空间里处理完之后再输出

命令 | 说明
:- | :-
{                     | 语句块开始符号
}                     | 语句块结束符号
a text                | 在找到的行之后添加一行`text`
i text                | 在找到的行之前插入一行`text`
c text                | 在找到的行使用`text`替换整行
q [exit-code]         | 在找到的行执行退出脚本
r filename            | 在找到的行后追加`filename`文件内容
R filename            | 在找到的行后追加`filename`文件第一行内容
w filename            | 将模式空间中的内容写入到`filename`文件中
W filename            | 将模式空间中的第一行内容写入到`filename`文件中
d                     | 删除找到的行
h H                   | 复制/追加模式空间的数据到临时空间
g G                   | 复制/追击临时空间的数据到模式空间
n N                   | 读取/追加下一行到模式空间
s/regexp/replacement/ | 使用正则表达式与模式空间中的内容匹配并替换为指定的内容。使用`&`代表被匹配到的项，使用`\1`到`\9`代表元组匹配到的第N个项
x                     | 交换模式空间和临时空间的内容

> `n`指令读取到模式空间的行会被后面的指令处理，而`N`指令追加到模式空间的行不会被后续的指令处理

## 地址范围

切入与该地址匹配的行；若使用两个地址，将对所有输入行执行该命令，这些输入行为第一个地址开始一直延续到第二个地址之间的所有行。有关地址范围的三点注意事项：语法为`addr1,addr2`（即地址之间用逗号分隔）；即使`addr2`选择了较早的行，也将始终接受与`addr1`匹配的行；如果`addr2`是正则表达式，则不会针对`addr1`匹配的行进行测试。

在地址（或地址范围）之后，在命令之前，使用`!`可以指定仅当地址（或地址范围）不匹配时才执行命令。

地址 | 说明
:- | :-
number     | 匹配指定行
first~step | 从`first`行开始，每间隔`step`步长匹配行
$          | 匹配最后一行
/regexp/   | 使用正则表达式匹配行
\cregexpc  | 使用正则表达式匹配行，`\c`可以是任意的字符
addr1,+N   | 从地址`addr1`开始向后匹配N行
addr1,~N   | 从地址`addr1`开始向后匹配到N的倍数行的下一行

## 命令示例

1. 在maven的配置文件中添加阿里云镜像

    首先准备一个`aliyun-maven.xml`文件，内容如下，保存文件到`/opt`目录中

    ```xml
        <mirror>
          <id>aliyunmaven</id>
          <mirrorOf>*</mirrorOf>
          <name>阿里云公共仓库</name>
          <url>https://maven.aliyun.com/repository/public</url>
        </mirror>
    ```

    假设`maven`的路径在`/usr/local/maven3`下，执行如下命令，添加镜像到配置文件中并备份配置文件重命名为`settings.xml.bak`

    ```bash
    sed -ci.bak '/<mirrors>/r /opt/aliyun-maven.xml' /usr/local/maven3/conf/settings.xml
    ```

2. 修改文件中所有的链接

    将所有`http://www.example.com`改为`https://linux.org`

    ```bash
    sed -r 's@http://www.example.com@https://linux.org@g' url.conf
    ```

    > 注意这里使用`@`符号替代默认的`/`来表示界定符

Last Modified 2021-03-17
# less

## 简介

less是类似于more的程序，但是它允许文件在中向后以及向前移动。此外，启动前不必读取整个输入文件，因此，对于大型输入文件，其启动速度比诸如vi之类的文本编辑器更快。

命令基于more和vi。命令前面可以有一个十进制数，在下面的描述中称为N。

```
less -?
less --help
less -V
less --version
less [-[+]aABcCdeEfFgGiIJKLmMnNqQrRsSuUVwWX~]
     [-b space] [-h lines] [-j line] [-k keyfile]
     [-{oO} logfile] [-p pattern] [-P prompt] [-t tag]
     [-T tagsfile] [-x tab,...] [-y lines] [-[z] lines]
     [-# shift] [+[+]cmd] [--] [filename]...
```

## 命令

使用less打开文件后，默认为查看文件，此时可以直接输入命令

命令 | 说明
:--- | :---
空格键 | 向后滚动1页
回车键 | 向后滚动1行
d | 输入数字可指定向后滚动N行，如`7d`，默认半屏
u | 输入数字可指定向前滚动N行，如`7u`，默认半屏
y | 向前滚动1行
b | 向前滚动1页
g | 输入数字指定跳转的行，如`7g`，默认跳转到第1行
G | 输入数字指定跳转的行，如`7G`，默认跳转到最后1行
p, % | 输入数字指定跳转到文档百分比位置，如`7p`或`7%`
F | 追踪文件新增内容，按Ctrl+C终止；查看log文件很有用
/pattern | 输入表达式向后搜索
?pattern | 输入表达式向前搜索
n | 下一个搜索匹配项
N | 上一个搜索匹配项
&pattern | 搜索完全与表达式匹配的行
q, Q | 退出
v | 查找一个编辑器打开当前文件编辑
! shell-command | 执行shell命令

> 使用键盘上的翻页按键和方向按键也可以移动

## 参数

参数 | 说明
:--- | :---
-I, --ignore-case | 指定搜索忽略大小写
-g, --hilite-search | 指定每次只高亮下一个查找匹配项
-G, --HILITE-SEARCH | 禁用高亮所有查找匹配项
-m | 显示与`more`类似的百分比
-M | 显示与`more`类似的百分比，并且显示行范围
-N | 展示行号
-ppattern | 指定从表达式`pattern`搜索到的位置开始查看
-s, --squeeze-blank-lines | 将连续的空行转换为一个空行

Last Modified 2021-03-13
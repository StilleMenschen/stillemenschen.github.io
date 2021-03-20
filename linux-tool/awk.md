# awk


## 简介

awk的基本功能是在文件中搜索包含某些模式的行（或其他文本单位）。当一行与其中一种模式匹配时，awk在该行上执行指定的操作。awk继续以这种方式处理输入行，直到到达输入文件的末尾。

```
awk [options] -f progfile [--] file ...
awk [options] [--] 'program' file ...
```

awk中的程序与大多数其他语言中的程序不同，因为awk程序是数据驱动的（即，您描述要使用的数据，然后在找到数据时要执行的操作）。其他大多数语言都是程序性的。您必须详细描述程序应采取的每个步骤。使用过程语言时，通常很难清楚地描述程序将要处理的数据。因此，awk程序通常很容易读取和编写。

当您运行awk时，可以指定一个awk程序来告诉awk该怎么做。该程序由一系列规则组成（它可能还包含函数定义，这是我们现在将忽略的高级功能；请参阅用户定义函数一节）。每个规则指定一个要搜索的模式，并在找到该模式时执行一个动作。

从句法上讲，规则由一个模式和一个动作组成。该动作用大括号括起来，以将其与模式分开。换行符通常是分开的规则。因此，awk程序如下所示：

```awk
# This program prints a nice, friendly message.  It helps
# keep novice users from being afraid of the computer.
pattern { action }
pattern { action }
...
```

引号问题

```bash
awk 'BEGIN { print "Don\47t Panic!" }' # \47表示单引号
awk "BEGIN { print \"Don't Panic!\" }" # 转义引号
```

## 参数

命令 | 说明
:--- | :---
-f, --file                  | 指定awk脚本文件
-F fs, --field-separator fs | 自定义分隔符`fs`（默认是以空格作为分隔符）
-v var=val, --assign var=val | 自定义变量`key=value`形式
-d[file], --dump-variables[=file] | 将全局变量，变量的类型和最终值的排序列表打印到文件中。如果没有提供文件，则将此列表打印到当前目录中名为awkvars.out的文件中。如果提供了文件，则-d和文件之间不允许有空格。

> 列出所有全局变量是在程序中查找印刷错误的好方法。如果您的大型程序具有很多功能，并且还希望确保您的函数不会无意中使用本应是局部变量的全局变量，那么您也可以使用此选项。（使用i，j等简单变量名称时，这是一个特别容易犯的错误）
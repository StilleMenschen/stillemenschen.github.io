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

参数 | 说明
:--- | :---
-f source-file, --file source-file     | 指定awk脚本文件
-F fs, --field-separator fs            | 自定义分隔符`fs`（默认以空格作为分隔符）
-v var=val, --assign var=val           | 自定义变量`key=value`形式
-d[file], --dump-variables[=file]      | 将全局变量，变量的类型和最终值的排序列表打印到文件中。如果没有提供文件，则将此列表打印到当前目录中名为`awkvars.out`的文件中。如果提供了文件，则`-d`和文件之间不允许有空格。
-e program-text, --source program-text | 在程序文本中提供程序源代码
-E file, --exec file                   | 与`-f`相似，从文件中读取awk程序文本。 与`-f`有两个区别：<br> 此选项终止选项处理。命令行上的其他任何内容都直接传递给awk程序<br> 不允许使用`var=value`形式的命令行变量分配
-h, --help                             | 输出使用帮助信息并退出
-p[file], --profile[=file]             | 启用对awk程序的性能分析。表示`--no-optimize`。默认情况下，配置文件是在名为`awkprof.out`的文件中创建的。可选的file参数使您可以为配置文件指定其他文件名。如果提供了文件，则`-p`和文件之间不允许有空格。该配置文件在左边距中包含程序中每个语句的执行计数，以及每个函数的函数调用计数。
-S, --sandbox                          | 启用沙盒，禁用`system()`函数，使用`getline`输入重定向，使用`print`和`printf`输出重定向，以及动态扩展。另外，禁止在gawk开始运行时将不存在的文件名添加到`ARGV`。当您要从可疑来源运行awk脚本并且需要确保脚本无法访问系统（指定的输入数据文件除外）时，此功能特别有用
-V, --version                          | 打印版本信息并退出
--                                     | 标记所有选项的结尾。跟随`-`的所有命令行参数都放在ARGV中，即使它们以减号开头

> 使用`-d`选项列出所有全局变量是在程序中查找错误的好方法。如果您的大型程序具有很多功能，并且还希望确保您的函数不会无意中使用本应是局部变量的全局变量，那么您也可以使用此选项。（使用i，j等简单变量名称时，这是一个特别容易犯的错误）

## 匹配表达式

> `@include`可以引用其它外部脚本文件，如`@include "/usr/awklib/network"`表示引用`/usr/awklib/network`文件中的awk脚本

语法 | 说明
:--- | :---
/regexp/        | 正则表达式搜索
exp ~ /regexp/  | 正则表达式搜索指定列，如`$2 ~ /a/`表示搜索以指定分隔符分隔的第二列中包含有小写字母a的文本
exp !~ /regexp/ | 正则表达式搜索不匹配的列，如`$2 !~ /a/`表示搜索以指定分隔符分隔的第二列中不包含有小写字母a的文本
$1 == "aa"      | 每行的第一个按分隔符分隔的字段等于`aa`，比较符可以为`==`、`!=`，支持预定义变量
$2 == 3         | 每行的第二个按分隔符分隔的字段等于`3`，比较符可以为`==`、`<`、`>`、`<=`、`>=`、`!=`，支持预定义变量

通过匹配表达式，可以对匹配的行做操作，多个操作可以使用花括号包裹（语句块），表示不同的匹配项执行不同的操作，不写表达式的语句块表示每一行都指向操作，只写表达式不写语句块表示输出匹配到的行，操作顺序从上往下，从左往右。示例：

```awk
/Jack/ {print "current line matches Jack."}
$2 ~ /Jack/ {print "is Jack"}
$2 !~ /Jack/ {print "No Jack!"}
$1 == "Jack" {print "is Jack"}
FNR >= 3 {print "current line number greater than 3"}
$1 == 3 {print "The first field is equal to 3"}
```

> 在花括号语句块前可以使用两个特殊的标记`BEGIN`和`END`，`BEGIN`表示开始前执行的语句块，`END`表示结束后执行的语句块，如`BEGIN{print "start"}{print $0}END{print "end"}`表示开始时输出一次`start`，中间持续输出每一行，结束后输出一次`end`

## 转义字符

转义 | 说明
:--- | :---
`\\`   | 反斜杠
`\a`   | 警报字符，Ctrl-g，ASCII码`7`（BEL），（这通常会发出某种可听见的声音）
`\b`   | 退格键, Ctrl-h, ASCII码`8` (BS).
`\f`   | 换页, Ctrl-l, ASCII码`12` (FF).
`\n`   | 新行, Ctrl-j, ASCII码`10` (LF).
`\r`   | 回车, Ctrl-m, ASCII码`13` (CR).
`\t`   | 水平`TAB`, Ctrl-i, ASCII码`9` (HT).
`\v`   | 垂直`TAB`, Ctrl-k, ASCII码`11` (VT).
`\nnn` | 八进制字符，由1到3位的0到7数字组成，例如ASCII中的`ESC`（escape）字符表示为`\033`
`\xhh` | 十六进制字符，由1到2位的0到9和a到f（或A到F）组成，例如数字100表示为`\x64`（POSIX中的awk不允许使用`\x`转义）
`\/`   | 斜杠
`\"`   | 双引号

## 预定义变量

变量 | 说明
:--- | :---
`FS`       | 输入行的字段分隔符
`RS`       | 输入行分隔符
`RT`       | 与输入行分隔符`RS`表示的文本匹配的输入文本，每次读取记录时设置
`OFS`      | 输出行的字段分隔符
`ORS`      | 输出行分隔符
`ARGV`     | 参数数组，第一个为awk本身，索引为0到`ARGC`-1
`ARGC`     | 参数数量
`ARGIND`   | 正在处理的参数索引位置，从1开始
`ENVIRON`  | 环境变量数组，读取当前操作系统的环境变量，如`ENVIRON["JAVA_HOME"]`
`FILENAME` | 当前正在处理的文件名
`FNR`      | 当前处理文件的行号
`NF`       | 当前处理行以指定分隔符分隔的字段数量，可以使用`$NF`表示最后一个字段，`$(NF-1)`表示倒数第二个字段
`NR`       | 行号，从awk开始执行时自动累加
`PROCINFO` | 该数组的元素提供对有关正在运行的awk程序的信息的访问。<br>部分变量`PROCINFO["egid"]`、`PROCINFO["euid"]`、`PROCINFO["gid"]`、`PROCINFO["pgrpid"]`、`PROCINFO["pid"]`、`PROCINFO["ppid"]`、`PROCINFO["uid"]`、`PROCINFO["version"];`
`RLENGTH`  | 由`match()`函数匹配的子字符串的长度，RLENGTH是通过调用`match()`函数来设置的。它的值是匹配字符串的长度，如果找不到匹配项，则为-1
`RSTART`   | 与`match()`函数匹配的子字符串的字符的起始索引， 通过调用`match()`函数来设置`RSTART`，它的值是匹配的子字符串开始的字符串位置，如果找不到匹配项，则为0
`SYMTAB `  | 一个数组，其索引是程序中所有已定义的全局变量和数组的名称。SYMTAB使awk程序员可以看到gawk的符号表。它是在gawk解析程序时构建的，并在程序开始运行之前完成

## 变量定义

### 字符串

```awk
thing = "food"
predicate = "good"
message = "this " thing " is " predicate
```

### 数字

```awk
var1=1
var2=2.1
var3=var1 + var2
```

支持的操作符：`+`、`-`、`*`、`/`、`%`、`^`、`+=`、`-=`、`*=`、`/=`、`%=`、`^=`、`++`、`--`

> `^`表示幂运算，如`a=2^3;print a`输出`a`的值为8。`^=`表示幂运算并赋值，如`a=2;b=3;a^=b;print a`输出`a`的值为8。由于`/=`是运算操作符，如果想在正则表达式中表示`/==/`匹配，应该写成`/[=]=/`

### 数组

数组可以直接定义，只需要指明索引，数组索引支持字符串或纯数字，下面的操作就是把三行的数字乘2后记录到数组中，并在最后输出数组的每个值

```bash
printf "1\n2\n3" | awk 'BIGINE{i=0}{s[i++]=$1*2}END{print "array is:";for (i in s) print "array["i"]="s[i]}'
```

一个有趣的例子，先看看待处理的文本

```
5  I am the Five man
2  Who are you?  The new number two!
4  . . . And four on the floor
1  Who is number one?
3  I three you.
```

通过以下程序可以对数据的行按第一个数字进行排序

```awk
{
    if ($1 > max)
        max = $1
    arr[$1] = $0
}
END {
    for (x = 1; x <= max; x++)
        print arr[x]
}
```

字符串作为索引的数组

```awk
BEGIN {
    a["here"] = "here"
    a["is"] = "is"
    a["a"] = "a"
    a["loop"] = "loop"
    for (i in a) {
        print i
    }
}
```

## 条件表达式

### 判断表达式

支持类似C语言的`if`、`if-else`、`if-else if-else`、`switch-case`

> `"jack" in arr1`表示判断字符串`jack`是否存在于数组`arr1`的索引中

### 循环表达式

支持类似C语言的`while`、`do-while`、`for`、`continue`、`break`，遍历数组的`for-in`

### 操作表达式

忽略后续操作跳到下一行`next`，忽略当前文件跳到下一个文件`nextfile`，停止执行并退出`exit`。

## 内置函数

### 数字

函数 | 说明
:-- | :--
atan2(y, x) | 求`y / x`的反正切值，例如使用`pi = atan2(0, -1)`求圆周率（Pi）的值
cos(x)      | 求`x`的余弦值，`x`表示弧度
sin(x)      | 求`x`的正弦值，`x`表示弧度
exp(x)      | 返回`x`的指数
int(x)      | 返回最接近`x`的整数，返回值为0到x之间，例如`int(3.9)`等于`3`，`int(-3.9)`等于`-3`，`int(-3)`等于`-3`
log(x)      | 如果`x`为正数，返回`x`的自然对数。否则，在IEEE 754系统上返回NaN（非数字），当`x`为负数时awk将发出警告信息
rand()      | 返回0到1之间（不包含1）的随机数
sqrt(x)     | 求`x`的平方根

### 字符串

函数 | 说明
:-- | :--
asort(source [, dest [, how ] ])             | 对字符串数组的值进行排序，如果指定了`dest`则将结果输出到`dest`中并保留源数组`source`
asorti(source [, dest [, how ] ])            | 对数组的字符串索引进行排序，如果指定了`dest`则将结果输出到`dest`中并保留源数组`source`
gensub(regexp, replacement, how [, target])  | 在目标字符串目标中搜索正则表达式`regexp`的匹配项。如果字符串以`'g'`或`'G'`开头（`global`的缩写），则将所有`regexp`匹配项替换为`replace`。否则，将`how`视为一个数字，该数字指示要替换的`regexp`匹配项。将小于1的数值视为1。如果未提供`target`，默认使用`$0`。返回修改后的字符串作为函数的结果。原始目标字符串不会被更改
gsub(regexp, replacement [, target])         | 搜索`target`以找到它可以找到的所有匹配子字符串，并用`replacement`替换它们，在`replacement`中
index(in, find)                              | 在字符串`in`中搜索与`find`相等的第一个匹配项，然后返回该字符在字符串`in`中开始位置（以字符为单位）
length([string])                             | 返回`string`中的字符数。如果`string`是数字，则返回代表该数字的数字字符串的长度。例如，`length("abcde")`为5。相比之下，`length(15*35)`等于3。在此示例中，`15*35=525`，然后将`525`转换为具有三个字符的字符串`"525"`
match(string, regexp [, array])              | 在`string`中搜索与正则表达式`regexp`匹配子字符串，并返回该子字符串开始的字符位置（索引）（如果从字符串的开头开始，则返回一个字符位置）。如果找不到匹配项，则返回零。如果指定了`array`则将匹配到的数据写入到`array`中，元组匹配项从`array`的第2个索引开始
split(string, array [, fieldsep [, seps ] ]) | 将字符串划分为由`fieldsep`分隔的片段，并将片段存储在数组中，将分隔符字符串存储在`seps`数组中。第一块存储在`array[1]`中，第二块存储在`array[2]`中，依此类推。第三个参数的字符串值`fieldsep`是一个正则表达式，描述了在哪里拆分字符串（就像`FS`可以是一个正则表达式，描述了在哪里拆分输入行）。如果省略`fieldsep`，则使用`FS`的值。 `split()`返回创建的元素数。`seps`是一个gawk扩展，其中`seps[i]`是`array[i]`和`array[i + 1]`之间的分隔符字符串。如果`fieldsep`是单个空格，则任何前导空格都将进入`seps [0]`，任何尾随空格都将进入`seps[n]`，其中n是`split()`的返回值（即数组中元素的数量）
strtonum(str)                                | 检查`str`并返回其数值。如果`str`以`0`开头，则`strtonum()`判断`str`是一个八进制数。如果`str`以`0x`或`0X`开头，则`strtonum()`判断`str`是一个十六进制数
sub(regexp, replacement [, target])          | 搜索`target`，查找与正则表达式`regexp`匹配的子字符串。通过用`replacement`替换匹配的文本来修改整个字符串。修改后的字符串将成为`target`的新值。 返回进行替换的次数（零或一）
substr(string, start [, length ])            | 返回`string`中从字符编号`start`开始的指定`length`的子字符串。如果不指定`length`则返回从`start`位置开始一直到末尾的子字符串
tolower(string)                              | 将`string`中的大写字母全部转换为小写字母
toupper(string)                              | 将`string`中的小写字母全部转换为大写字母

## 命令示例

1. 输出1到10的随机数10次

    ```bash
    awk 'BEGIN{for (i=0;i<10;i++) print 1+int(rand()*10)}'
    ```

2. 找出占用指定端口的java程序的PID

    ```bash
    netstat -ntlp|awk '/:8088/{sub(/[^0-9]+/,"",$NF);print $NF;exit}'
    ```

3. 提取文本数据

    先看看示例文本mail-list
    ```txt
    Amelia       555-5553     amelia.zodiacusque@gmail.com    F
    Anthony      555-3412     anthony.asserturo@hotmail.com   A
    Becky        555-7685     becky.algebrarum@gmail.com      A
    Bill         555-1675     bill.drowning@hotmail.com       A
    Broderick    555-0542     broderick.aliquotiens@yahoo.com R
    Camilla      555-2912     camilla.infusarum@skynet.be     R
    Fabius       555-1234     fabius.undevicesimus@ucb.edu    F
    Julie        555-6699     julie.perscrutabor@skeeve.com   F
    Martin       555-6480     martin.codicibus@hotmail.com    A
    Samuel       555-3430     samuel.lanceolis@shu.edu        A
    Jean-Paul    555-2127     jeanpaul.campanorum@nyu.edu     R
    ```

    找到第三列所有`edu`邮箱的人员并输出第一列名称

    ```bash
    awk '$3 ~ /.edu/{print $1}' mail-list
    ```

    找到第四列字符为`A`的人员，修改第二列号码中`555`为`963`，并输出

    ```bash
    awk '$4 == "A"{sub(/555/,"963",$2);print}' mail-list
    ```

更多说明可参考GNU的[awk](https://www.gnu.org/software/gawk/manual/gawk.html)文档

Last Modified 2021-03-28
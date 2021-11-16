# shell

## 保留关键字

下列单词在不加引号的情况下被视为保留字，并且是命令的第一个单词

```bash
if   then  elif    else   fi  time
for  in	   until   while  do  done
case esac  coproc  select  function
{    }    [[    ]]    !
```

## 变量

### 常规变量

```bash
name=[value]
```

或者定义一个变量引用

```bash
declare -n ref=$1
```

引用变量可以间接操作被引用变量的值

### 特殊变量

- `*` 扩展位置参数（从 1 开始），变量间以`IFS`分隔
- `@` 扩展位置参数（从 1 开始），变量间以空格分隔
- `#` 获取当前得到的参数个数
- `?` 获取最近执行的前台命名管道退出状态
- `-` 调用时由`set builtin`命令或由 shell 本身设置的那些当前指定的选项标志
- `$` shell 的进程 ID，在`()`子外壳中为调用子外壳的进程 ID
- `!` 最近放置在后台作业的进程 ID，无论是作为异步命令执行还是使用`bg`内置命令执行
- `0` shell 或 shell 脚本名称

## 数组

### 索引数组

直接为变量指定一个数字下标（`subscript`表示一个算术表达式），即定义了索引数组

```bash
name[subscript]=value
```

或者显示的声明一个索引数组

```bash
declare -a name
```

### 命名数组

```bash
declare -A name
```

示例

```bash
arr1=(9 8 7 6 5 4 3 2 1 0)
declare -A arr2
arr2=( [key1]=value1 [key2]=value2 )
name=(jim tom lucy)
declare -A phone
phone=([jim]=135 [tom]=136 [lucy]=158)

echo "输出数组下标";
for e in ${!arr1[@]}; do
  printf " %d" $e;
done
printf "\n输出数组的值\n";
for e in ${arr1[@]}; do
  printf " %d" $e;
done
printf "\n输出关联数组的值\n";
for e in ${arr2[@]}; do
  printf " %s" $e;
done
printf "\n输出关联数组的下标\n";
for e in ${!arr2[@]}; do
  printf " %s" $e;
done
printf "\n";

echo 数组元素数量 ${#arr1[@]};
echo 数组元素数量 ${#arr2[@]};
echo 索引数组最后一个元素 ${arr1[-1]}

for i in `eval echo {0..$((${#name[@]}-1))}`
do
    echo ${name[i]} phone number is ${phone["${name[i]}"]}
done
```

> shell 数组索引越界会导致脚本执行出错

## 管道

管道是由一个或多个命令组成的序列，这些命令由控制操作符`|`或`|&`分隔

```
[time [-p]] [!] command1 [ | or |& command2 ] ...
```

如果使用了`|&`则表示将标准错误输出转到标准输出上，即`2>&1 |`的简易表示方式

管道可以对命令的输出信息逐级传递，如下表示使用`last`命令将系统内登录过的信息列出，通过`awk`按 IP 聚合，最后用`sort`按登
录次数从大到小排序

```bash
last | awk '$3~/[0-9]{1,3}./{sum[$3]+=1}END{for(i in sum) print "IP: "i" Count: "sum[i]}' | sort -rnk 4
```

## 命令列表

命令列表可通过`;`，`&`,`&&`或`||`分隔开，并且可以`;`，`&`或新行（`\n`）终止

在这些列表运算符中，`&&`和`||`具有相同的优先级，其后是具有相同优先级的`;`和`＆`

当`command1`执行成功（返回值为 0）时才继续执行`command2`

```
command1 && command2
```

只有`command1`执行失败（返回值非 0），才会继续执行`command2`

```
command1 || command2
```

## 复合命令

### 循环结构

1. 当条件不满足时继续执行

   ```
   until test-commands; do consequent-commands; done
   ```

2. 当条件满足时继续执行

   ```
   while test-commands; do consequent-commands; done
   ```

3. 同时定义初始值，条件和增量
   ```
   for name [ [in [words ...] ] ; ] do commands; done
   ```
   ```
   for (( expr1 ; expr2 ; expr3 )) ; do commands; done
   ```

三种方式计算 1 到 100 的和

```bash
i=1; sum=0; until (( i>100 )); do (( sum += i )); (( i++ )); done; echo "sum=$sum";
i=1; sum=0; while (( i<=100 )); do (( sum += i )); (( i++ )); done; echo "sum=$sum";
sum=0; for (( i=1; i<=100; i++)); do (( sum += i )); done; echo "sum=$sum";
```

使用`break`关键字可以结束循环，使用`continue`可以跳出当前循环

### 选择结构

1. `if`判断

   ```
   if test-commands; then
     consequent-commands;
   [elif more-test-commands; then
     more-consequents;]
   [else alternate-consequents;]
   fi
   ```

   根据数字大小判断输出

   ```bash
   sum=120;
   if ((sum > 120)); then
     echo "sum > 120";
   elif ((sum > 100)); then
     echo "sum > 100";
   else
     echo "sum < 100";
   fi
   ```

2. `case`判断

   ```
   case word in
       [ [(] pattern [| pattern]...) command-list ;;]...
   esac
   ```

   判断动物有多少条腿

   ```bash
   echo -n "Enter the name of an animal: "
   read ANIMAL
   echo -n "The $ANIMAL has "
   case $ANIMAL in
     horse | dog | cat) echo -n "four";;
     man | kangaroo ) echo -n "two";;
     *) echo -n "an unknown number of";;
   esac
   echo " legs."
   ```

3. `select`选择

   ```
   select name [in words ...]; do commands; done
   ```

   列出当前目录下的文件和子目录并供选择

   ```bash
   select fname in *;
   do
       echo you picked $fname \($REPLY\)
       break;
   done
   ```

4. `((...))`算术运算

   - `id++`、`id--` 后自增和后自减
   - `++id`、`--id` 先自增和先自减
   - `- +` 一元减号和加号
   - `! ~` 逻辑非和按位取反
   - `**` 求幂
   - `* / %` 乘法，除法，取余数
   - `+ -` 加法，减法
   - `<< >>` 按位左移或按位右移
   - `<= >= < >` 大小比较
   - `== !=` 相等和不相等
   - `&` 按位与
   - `^` 按位异或
   - `|` 按位或
   - `&&` 并且
   - `||` 或者
   - `expr ? expr : expr` 三元运算
   - `= *= /= %= += -= <<= >>= &= ^= |=` 运算并赋值
   - `expr1 , expr2` 逗号分隔

   一般情况下数值都是十进制的，如果以`0`开头则表示八进制数，如果以`0x`或`0X`开头则表示十六进制数

5. `[[...]]`条件运算
   - `-a file` 文件存在则返回`True`
   - `-b file` 文件为块特殊文件且存在则返回`True`
   - `-c file` 文件为字符特殊文件且存在则返回`True`
   - `-d file` 文件为目录且存在则返回`True`
   - `-e file` 文件存在则返回`True`
   - `-f file` 文件为常规文件且存在则返回`True`
   - `-g file` 如果文件存在并且设置了它的`set-group-id`位，则为`True`
   - `-h file` 文件为符号链接且存在则返回`True`
   - `-k file` 如果文件存在并且设置了它的`sticky`位，则为`True`
   - `-p file` 文件为命名管道（FIFO）且存在则返回`True`
   - `-r file` 文件可读且存在则返回`True`
   - `-s file` 文件大小大于 0 且存在则返回`True`
   - `-t fd` 如果文件描述符`fd`已打开并指向终端，则为`True`
   - `-u file` 如果文件存在并且设置了它的`set-user-id`位，则为`True`
   - `-w file` 文件可写且存在则返回`True`
   - `-x file` 文件可执行且存在则返回`True`
   - `-G file` 文件设置了有效（存在）的用户组且存在则返回`True`
   - `-L file` 文件为符号链接且存在则返回`True`
   - `-N file` 如果文件存在并且自上次读取以来已被修改，则为`True`
   - `-O file` 文件设置了有效（存在）的用户且存在则返回`True`
   - `-S file` 文件为套接字且存在则返回`True`
   - `file1 -ef file2` 如果 file1 和 file2 引用相同的设备和 inode 编号，则为`True`
   - `file1 -nt file2` 如果 file1 比 file2 更新（根据修改日期），或者如果 file1 存在而 file2 不存在，则为`True`
   - `file1 -ot file2` 如果 file1 早于 file2，或者 file2 存在且 file1 不存在，则为`True`
   - `-o optname` 如果启用了 shell 选项 optname，则为`True`
   - `-v varname` 如果变量 varname 已设置了有效值则为`True`
   - `-R varname` 如果变量 varname 已设置并且是名称引用，则为`True`
   - `-z string` 如果字符串的长度为零，则为`True`
   - `-n string`或`string` 如果字符串的长度不为零，则为`True`
   - `string1 == string2`或`string1 = string2` 如果字符串相等，则为`True`当与`[[`命令一起使用时，它将执行如上所述的模式
     匹配。为了与 POSIX 一致，应在测试命令中使用`"="`
   - `string1 != string2` 字符串不相等则为`True`
   - `string1 < string2` 如果 string1 在字典顺序上排在 string2 之前，则为`True`
   - `string1 > string2` 如果 string1 在字典顺序上排在 string2 之后，则为`True`
   - `arg1 OP arg2` OP 表示`-eq`，`-ne`，`-lt`，`-le`，`-gt`，`-ge` 如果 arg1 分别等于，不等于，小于，小于等于，大于，大
     于等于 arg2，则这些算术二进制运算符返回`True`。arg1 和 arg2 可以是正整数或负整数。当与`[[`命令一起使用时，arg1 和
     arg2 将作为算术表达式求值

> 使用`[`和`]`包裹的表达式，变量名一般都会加上引号，如`[ -r "$var1" ]`。这里需要注意`[`和`[[`是两个不同的指令

> `[`其实是一个命令，使用`type [`可以看出来；`! expr`表示取反，`( expr )`表示提高括号内的运算优先级，`expr1 -a expr2`表
> 示并且，`expr1 -o expr2`表示或者。`expr`代表有效的表达式

### 命令组

1. `( list )` 将命令列表放在括号之间会导致创建一个子外壳环境，并且列表中的每个命令都将在该子外壳中执行。由于列表是在子
   shell 中执行的，因此在子 shell 完成后变量分配不会保持有效

2. `{ list; }` 在大括号之间放置命令列表会导致该列表在当前 shell 上下文中执行。命令必须是以分号（或换行符）结束

> 除了创建子外壳之外，由于历史原因，这两种构造之间还存在细微的差异。花括号是保留字，因此必须用空格或其他外壳元字符将它们
> 与列表分隔开。括号是运算符，即使它们没有用空格与列表分开，它们也被外壳识别为单独的标记。这两个构造的退出状态均为 list
> 的退出状态

## 函数

Shell 函数是一种使用单个名称对命令进行分组以便以后执行的方法。它们像常规命令一样执行。当将外壳函数的名称用作简单命令名称
时，将执行与该函数名称关联的命令列表。Shell 函数是在当前 Shell 上下文中执行的；没有创建新的过程来解释它们

```
fname () compound-command [ redirections ]
```

```
function fname [()] compound-command [ redirections ]
```

以下两种方式都定义了一个函数，函数运算并输出九九乘法表

```bash
nnmt1 () {
  sum=0;
  for i in {1..9}; do
    for (( j=i; j<10; j++ )); do
      (( sum=i*j ));
      printf "%d * %d = %-4d" $i $j $sum;
    done
    printf "\n";
  done
}

function nnmt2 {
  sum=0;
  for i in {1..9}; do
    for (( j=i; j<10; j++ )); do
      (( sum=i*j ));
      printf "%d * %d = %-4d" $i $j $sum;
    done
    printf "\n";
  done
}

nnmt1;
echo ------------------------------;
nnmt2;
```

> 注意函数名称不能和 shell 保留的关键字相同，也尽量不要与系统中的命令名称相同

## 模式匹配

- `*` 匹配零个到多个
- `?` 匹配零个或一个
- `[...]` 匹配括号中指定的任意字符
  - `[abc]` 表示匹配字符 a，b，c
  - `[0-9]` 表示匹配数字字符 0 到 9
  - `[!a-z]` 表示匹配不是小写字母的其它字符
  - `[^a-z]` 表示匹配不是小写字母的其它字符
- `{string1,string2,...}` 表示匹配字符串 string1 string2 等等

> 如果要在`[`到`]`直接指定匹配`-`字符，可以将`-`放在`[`后面或`]`前面，如`[a-zA-Z0-9-]`

## 拓展

- `echo A{aa,bb,cc}S` 花括号拓展
- `${parameter:-word}` 如果参数未定义或为 null 则取`word`作为值返回
- `${parameter:?word}` 如果参数未定义或为 null 则取`word`作为值输出到标准错误输出
- `${parameter:+word}` 如果参数已定义且不为 null 则取`word`作为值返回
- `${parameter:offset}`或`${parameter:offset:length}`截取字符串或数组，注意`offset`使用负数需要在负号前有空格，与`:`隔开

  ```
  $ string=01234567890abcdefgh
  $ echo ${string:7}
  7890abcdefgh
  $ echo ${string:7:0}

  $ echo ${string:7:2}
  78
  $ echo ${string:7:-2}
  7890abcdef
  $ echo ${string: -7}
  bcdefgh
  $ echo ${string: -7:0}

  $ echo ${string: -7:2}
  bc
  $ echo ${string: -7:-2}
  bcdef
  $ array[0]=01234567890abcdefgh
  $ echo ${array[0]:7}
  7890abcdefgh
  $ echo ${array[0]:7:0}

  $ echo ${array[0]:7:2}
  78
  $ echo ${array[0]:7:-2}
  7890abcdef
  $ echo ${array[0]: -7}
  bcdefgh
  $ echo ${array[0]: -7:0}

  $ echo ${array[0]: -7:2}
  bc
  $ echo ${array[0]: -7:-2}
  bcdef
  ```

  ```
  $ array=(0 1 2 3 4 5 6 7 8 9 0 a b c d e f g h)
  $ echo ${array[@]:7}
  7 8 9 0 a b c d e f g h
  $ echo ${array[@]:7:2}
  7 8
  $ echo ${array[@]: -7:2}
  b c
  $ echo ${array[@]: -7:-2}
  bash: -2: substring expression < 0
  $ echo ${array[@]:0}
  0 1 2 3 4 5 6 7 8 9 0 a b c d e f g h
  $ echo ${array[@]:0:2}
  0 1
  $ echo ${array[@]: -7:0}

  ```

- `${!prefix*}`或`${!prefix@}`列出以`prefix`开头的变量名，使用`*`表示以`IFS`作为分隔，使用`@`则以空格作为分隔
- `${!name[@]}`或`${!name[*]}`列出数组的索引
- `${#parameter}`列出字符串长度或数组元素数量
- `${parameter#word}`或`${parameter##word}`匹配开头为`word`的模式（`word`支持使用通配符）并删除参数值中与`word`匹配的字
  符，如果参数为数组且使用了`*`或`@`则会对每个数组元素进行处理并输出，`##`表示最大范围匹配
- `${parameter%word}`或`${parameter%%word}`匹配结尾为`word`的模式（`word`支持使用通配符）并删除参数值中与`word`匹配的字
  符，如果参数为数组且使用了`*`或`@`则会对每个数组元素进行处理并输出，`%%`表示最大范围匹配
- `${parameter/pattern/string}` 查找模式并替换，如果参数为数组且使用了`*`或`@`则会对每个数组元素进行处理并输出，如果模式
  以`#`开头，则要求参数的值开头与模式完全匹配；若模式以`%`开头，则要求参数的值结尾与模式完全匹配

  ```bash
    a="aabcdaabcdd"
    echo ${a/aa/TT};
    echo ${a/#aa/TT};
    echo ${a/%dd/TT};
  ```

- `${parameter^pattern}`、`${parameter^^pattern}`将模式匹配到的字符转换为大写字母，`^`表示仅转换第一个字符，`^^`表示转换
  所有符合匹配的字符，模式中只能使用一个字符
- `${parameter,pattern}`、`${parameter,,pattern}`将模式匹配到的字符转换为小写字母，`,`表示仅转换第一个字符，`,,`表示转换
  所有符合匹配的字符，模式中只能使用一个字符

## 命令替换

使用`$(command)`或者`` `command` ``可暂存命令输出作为其它命令的参数

```bash
echo 今天的日期时间是 `date`
echo 今天的日期时间是 $(date)
curpath=`echo current path is \`pwd\``
echo $curpath
```

## 算术运算

使用`$(( expression ))`可以对表达式内的数学运算求值并暂存作为其它命令的参数

```bash
echo $(( 1 + 2 + 3 ))
```

更多内容可参考[官方文档](https://www.gnu.org/software/bash/manual/html_node/index.html)，在 Linux 系统内，可以使
用`man command`的方式查看一个命令的参考手册，如`man bash`，很多命令都是有简单的帮助文档的，键入`--help`就可以查看，
如`ls --help`

## 重定向

在 Linux 中输入输出分为三种

- 标准输入（文件描述符为 0）
- 标准输出（文件描述符为 1）
- 标准错误输出（文件描述符为 2）

### 标准输入重定向

最简单的输入重定向使用`<`表示，比如要将`package.list`文件中的包使用`yum`安装

```bash
sudo yum install $(<package.list)
sudo yum install `<package.list`
```

### 追加输入重定向

追加输入重定向使用`<<`或`<<<`表示，最常用的应该是**Here-document**和**Here-string**，**Here-document**使用`EOF`表示开始
和结束符，如下命令表示输入多行文本后输出。标记符号不一定固定就是`EOF`，也可以使用别的英文字母（仅）表示，单字符或字符串
都可

```bash
cat <<EOF
This
is
multi-line
text
EOF
```

> 大部分支持读取标准输入的程序都可以使用到追加输入重定向，如`cat`、`sed`、`grep`等

而**Here-string**则是用到了`<<<`，命令如下

```bash
cat <<<"This is a single line of text"
```

> 如果**Here-string**中包含有空格，需要使用双引号包裹

### 标准输出重定向

标准输出（文件描述符`1`）重定向使用`>`表示，最简单的方式就是将命令的输出转到文件中（若文件已存在且有内容，则原有内容将被
清空），如

```bash
ps -ef > process.list
```

> 如果使用了`set -o noclobber`开启了**noclobber**，则在使用输出重定向覆盖一个已经存在的文件时，将会发出警告，而使
> 用`>|`来重定向输出则会忽略**noclobber**选项，即使文件存在也会直接覆盖其内容

### 标准输出和错误输出重定向

程序运行输出结果时使用除了标准输出，发生错误反显在终端时还使用到了标准错误输出（文件描述符为`2`），如果想希望输出全部指
向同一流可以使用如下命令

```bash
find /opt -name file 2>&1
find /opt -name file > find.out 2>&1
```

> `>word 2>&1`还有一个比较简便的表示方法，即`&>word`

如果不想看到错误输出，可以将错误输出到`/dev/null`中，

```bash
ls 2>/dev/null
```

### 追加输出重定向

追加输出重定向使用`>>`表示，如果想把程序的输出内容追加到某个文件，可以使用这种方式

```bash
ps -aux >> exists-file.txt
```

### 追加标准输出和错误输出重定向

在追加输出的基础上再重定向标准错误输出，如

```bash
find /opt -name file >> find.out 2>&1
find /opt -name file &>> find.out
```

其实，结合输出和输出的重定向，还可以利用`cat`命令修改文件内容或追加多行内容到文件中，如

```bash
# 修改文件内容
cat > file <<EOF
aaa
bbb
ccc
EOF
# 追加多行内容到文件
cat >> file <<EOF
aaa
bbb
ccc
EOF
```

Last Modified 2021-04-26

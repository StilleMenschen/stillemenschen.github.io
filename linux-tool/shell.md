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

```
name=[value]
```
或者定义一个变量引用
```
declare -n ref=$1
```
引用变量可以间接操作被引用变量的值

### 特殊变量

 - `*` 扩展位置参数（从1开始），变量间以`IFS`分隔
 - `@` 扩展位置参数（从1开始），变量间以空格分隔
 - `#` 获取当前得到的参数个数
 - `?` 获取最近执行的前台命名管道退出状态
 - `-` 调用时由`set builtin`命令或由shell本身设置的那些当前指定的选项标志
 - `$` shell的进程ID，在`()`子外壳中为调用子外壳的进程ID
 - `!` 最近放置在后台作业的进程ID，无论是作为异步命令执行还是使用`bg`内置命令执行
 - `0` shell或shell脚本名称

## 数组

### 索引数组

直接为变量指定一个数字下标（`subscript`表示一个算术表达式），即定义了索引数组
```
name[subscript]=value
```
或者显示的声明一个索引数组
```
declare -a name
```

### 命名数组

```
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

for i in `eval echo {0..$((${#name[@]}-1))}`
do
    echo ${name[i]} phone number is ${phone["${name[i]}"]}
done
```
## 管道

管道是由一个或多个命令组成的序列，这些命令由控制操作符`|`或`|&`分隔
```
[time [-p]] [!] command1 [ | or |& command2 ] ...
```
如果使用了`|&`则表示将标准错误输出转到标准输出上，即`2>&1 |`的简易表示方式

## 命令列表

命令列表可通过`;`，`&`,`&&`或`||`分隔开，并且可以`;`，`&`或新行（`\n`）终止

在这些列表运算符中，`&&`和`||`具有相同的优先级，其后是具有相同优先级的`;`和`＆`

当`command1`执行成功（返回值为0）时才继续执行`command2`
```
command1 && command2
```
只有`command1`执行失败（返回值非0），才会继续执行`command2`
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
    for name [ [in [words …] ] ; ] do commands; done
    ```
    ```
    for (( expr1 ; expr2 ; expr3 )) ; do commands; done
    ```

三种方式计算1到100的和
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
    select name [in words …]; do commands; done
    ```
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
    - `-s file` 文件大小大于0且存在则返回`True`
    - `-t fd` 如果文件描述符`fd`已打开并指向终端，则为`True`
    - `-u file` 如果文件存在并且设置了它的`set-user-id`位，则为`True`
    - `-w file` 文件可写且存在则返回`True`
    - `-x file` 文件可执行且存在则返回`True`
    - `-G file` 文件设置了有效（存在）的用户组且存在则返回`True`
    - `-L file` 文件为符号链接且存在则返回`True`
    - `-N file` 如果文件存在并且自上次读取以来已被修改，则为`True`
    - `-O file` 文件设置了有效（存在）的用户且存在则返回`True`
    - `-S file` 文件为套接字且存在则返回`True`
    - `file1 -ef file2` 如果file1和file2引用相同的设备和inode编号，则为`True`
    - `file1 -nt file2` 如果file1比file2更新（根据修改日期），或者如果file1存在而file2不存在，则为`True`
    - `file1 -ot file2` 如果file1早于file2，或者file2存在且file1不存在，则为`True`
    - `-o optname` 如果启用了shell选项optname，则为`True`
    - `-v varname` 如果变量varname已设置了有效值则为`True`
    - `-R varname` 如果变量varname已设置并且是名称引用，则为`True`
    - `-z string` 如果字符串的长度为零，则为`True`
    - `-n string`或`string` 如果字符串的长度不为零，则为`True`
    - `string1 == string2`或`string1 = string2` 如果字符串相等，则为`True`当与`[[`命令一起使用时，它将执行如上所述的模式匹配。为了与POSIX一致，应在测试命令中使用`"="`
    - `string1 != string2` 字符串不相等则为`True`
    - `string1 < string2` 如果string1在字典顺序上排在string2之前，则为`True`
    - `string1 > string2` 如果string1在字典顺序上排在string2之后，则为`True`
    - `arg1 OP arg2` OP表示`-eq`，`-ne`，`-lt`，`-le`，`-gt`，`-ge` 如果arg1分别等于，不等于，小于，小于等于，大于，大于等于arg2，则这些算术二进制运算符返回`True`。arg1和arg2可以是正整数或负整数。当与`[[`命令一起使用时，arg1和arg2将作为算术表达式求值

> 使用`[`和`]`包裹的表达式，变量名一般都会加上引号，如`[ -r "$var1" ]`。这里需要注意`[`和`[[`是两个不同的指令

### 命令组

1. `( list )` 将命令列表放在括号之间会导致创建一个子外壳环境，并且列表中的每个命令都将在该子外壳中执行。由于列表是在子shell中执行的，因此在子shell完成后变量分配不会保持有效

2. `{ list; }` 在大括号之间放置命令列表会导致该列表在当前shell上下文中执行。命令必须是以分号（或换行符）结束

> 除了创建子外壳之外，由于历史原因，这两种构造之间还存在细微的差异。花括号是保留字，因此必须用空格或其他外壳元字符将它们与列表分隔开。括号是运算符，即使它们没有用空格与列表分开，它们也被外壳识别为单独的标记。这两个构造的退出状态均为list的退出状态

## 函数

Shell函数是一种使用单个名称对命令进行分组以便以后执行的方法。它们像常规命令一样执行。当将外壳函数的名称用作简单命令名称时，将执行与该函数名称关联的命令列表。Shell函数是在当前Shell上下文中执行的；没有创建新的过程来解释它们
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

> 注意函数名称不能和shell保留的关键字相同，也尽量不要与系统中的命令名称相同
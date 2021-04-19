# shell

## 保留关键字

下列单词在不加引号的情况下被视为保留字，并且是命令的第一个单词
```bash
if   then  elif    else   fi  time
for  in	   until   while  do  done
case esac  coproc  select  function
{    }    [[    ]]    !
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
sum=0; for (( i=1; i<100; i++)); do (( sum += i )); done; echo "sum=$sum";
```

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
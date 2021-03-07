# ls

## 简介

ls命令就是list的缩写，通过ls命令不仅可以查看linux文件夹包含的文件，而且可以查看文件权限(包括目录、文件夹、文件权限)，查看目录信息等。

```
ls [选项]... [文件]...
```

## 参数

参数 | 说明
:--- | :---
-l                    | 使用较长格式列出信息
-A, --almost-all      | 列出除. 及.. 以外的任何项目
--author              | 配合 -l 同时使用时列出每个文件的作者
-b, --escape          | 以八进制溢出序列表示不可打印的字符
--block-size=SIZE     | 缩放大小，按SIZE进行打印；例如 '--block-size=M' 以以下单位打印尺寸1,048,576字节；请参阅下面的SIZE格式
-B, -ignore-backups   | 不列出以~结尾的隐含条目
-c                    | 配合 -lt 参数使用：按时间排序并显示ctime（最后修改文件状态信息的时间）；配合 -l 参数使用：显示ctime并按名称排序；否则：按ctime排序，最新的优先
--color[=WHEN]        | 为输出内容着色；允许的值为"never"，"auto" 或"always"（默认）
-F, --classify        | 附加指示符（* / => @ \| 之一）分类到条目
--file-type           | 配合 ---classify类似，但不附加'*'
--format=WORD         | 格式化，WORD可以为：across -x, commas -m, horizontal -x, long -l, single-column -1, verbose -l, vertical -C
--full-time           | 类似于 -l --time-style=full-iso
-h, --human-readable  | 配合 -l 一起，以易于阅读的格式输出文件大小(例如 1K 1M 1G)
--si                  | 配合 -h 类似，但是使用1000为基准计算大小而非1024
-i, --inode           | 打印每个文件的索引号
-L, --dereference     | 当显示符号链接的文件信息时，显示符号链接所指示的对象而并非符号链接本身的信息
-n, --numeric-uid-gid | 类似 -l，但列出UID及GID号
-r, --reverse         | 逆序排列
-R, --recursive       | 递归显示子目录
-S                    | 按文件大小排序
--sort=WORD           | 按 WORD来排序而不是根据文件名: none (-U), size (-S,) time (-t), version (-v), extension (-X)
--time=WORD           | 配合 -l 参数，将时间显示为WORD而不是默认的修改时间，有效的参数为："atime", "access", "use", "ctime", "status"，如果使用--sort=time排序
--time-style=STYLE    | 配合 -l 参数, 使用指定的时间格式 STYLE，可用参数为：full-iso, long-iso, iso, locale, 或者+FORMAT +FORMAT与date命令的+FORMAT格式化选项类似
-t                    | 按修改时间排序，最新的优先
-u                    | 配合 -lt：按访问时间排序并显示；配合 -l：显示访问时间并按名称排序；否则：按访问时间排序
-X                    | 按条目扩展名的字母顺序排序
-1                    | 每行列出一个文件

## 其他说明

SIZE是整数和可选单位（例如：10M是10 * 1024 * 1024）。 单位为K，M，G，T，P，E，Z，Y（1024的幂）或KB，MB，...（1000的幂）。

使用 --color=auto 选项，ls 只在标准输出被连至终端时才生成颜色代码。

退出状态：
  * 0  正常
  * 1  一般问题 (例如：无法访问子文件夹)
  * 2  严重问题 (例如：无法使用命令行参数)

## 命令示例

1. 列出当前目录以及子目录的详细信息

```bash
ls -lR
```

2. 列出/opt目录中所有p开头的文件和目录详细信息

```bash
ls -l /opt/p*
```

3. 列出当前目录下的所有子目录的详细信息

```bash
ls -l | grep ^d
```

其实也可以反过来，列出当前目录下的所有文件但不包含目录

```bash
ls -l | grep -v ^d
```

4. 列出当前目录下所有文件详细信息，按文件修改时间升序

```bash
ls -lhtr
```

5. 列出当前目录下的所有文件，包括隐藏文件但不包含.和..，不显示文件的所有者信息，并且格式化输出文件的修改时间

```bash
ls -Alhgo --sort=time --time-style=long-iso
```

Last Modified 2021-03-07
# lsof

## 简介

列出进程打开的文件、网络连接、管道等信息
```
lsof [ -?abChlnNOPRtUvVX ] [ -A A ] [ -c c ] [ +c c ] [ +|-d d ]
    [ +|-D D ] [ +|-e s ] [ +|-E ] [ +|-f [cfgGn] ] [ -F [f] ] [ -g
    [s] ] [ -i [i] ] [ -k k ] [ -K k ] [ +|-L [l] ] [ +|-m m ] [ +|-M
    ] [ -o [o] ] [ -p s ] [ +|-r [t[m<fmt>]] ] [ -s [p:s] ] [ -S [t]
    ] [ -T [t] ] [ -u s ] [ +|-w ] [ -x [fl] ] [ -z [z] ] [ -Z [Z] ]
    [ -- ] [names]
```

## 选项

<style>
table th:first-of-type {
    width: 14%;
}
</style>

选项 | 说明
:- | :-
-?, -h    | 显示帮助信息并退出
-a        | 与运算符，对选项进行`AND`操作
-c c      | 为执行以下操作的进程选择文件列表以`c`字符开头的命令，如果以`^`开头则表示排除；如果字符是以`/`开头和结尾，则表示使用正则表达式，结尾的`/`后可以跟一个`i`表示忽略大小写匹配，如`lsof -c /JA/i`
+d s      | 从指定目录中搜索使用了该目录的所有相关进程文件，此选项不会递归查询子目录，也不会跟踪符号链接。如果指定`.`则表示查询当前目录下的子目录和文件关联进程
-d s      | 筛选文件描述符，指定多个使用`,`号分隔，如果以`^`开头则表示排除，如`cwd,1,3`、`^6,^2`
+D D      | 从指定目录中搜索使用了该目录的所有相关进程文件，此选项会递归搜索指定目录的所有子目录，耗时较长
-g [s]    | 过滤进程组`PGID`，指定多个使用`,`号分隔，如果以`^`开头则表示排除，如`123`、`123,^456`
-i [i]    | 选择其Internet地址中的任何一个的文件列表与`i`中指定的地址匹配，完整参数`[46][protocol][@hostname\|hostaddr][:service\|port]`<br>`46`表示IPv4或IPv6<br>`protocol`表示连接协议TCP或UDP<br>`hostname`主机名<br>`hostaddr`主机IP地址<br>`service`服务名，如`smtp`，可参考`/etc/services`<br>`port`数字表示的端口
-l        | 禁用将用户ID转为用户名
+\|-L [l] | `+`启用文件链接计数显示（`l`表示筛选小于等于该数值的链接计数）；`-`禁用文件链接计数显示
-n        | 禁止将网络号转为主机名
-N        | 过滤`NFS`文件列表
-p [s]    | 过滤指定进程`PID`，指定多个使用`,`号分隔，如果以`^`开头则表示排除，如`123`、`123,^456`
-P        | 禁止将端口号（数值）转为端口名（字符串描述）
+\|-r [t] | 指定lsof间隔`t`秒重复一次，前缀`-`需要手动停止，而前缀`+`在查询不到文件时自动退出
-R        | 列出父进程`PPID`
-t        | 简短输出进程的`PID`，可以利用此选项传递参数给`kill`命令
-u s      | 过滤进程用户，支持用户名或用户ID，指定多个使用`,`号分隔，如果以`^`开头则表示排除，如`nginx`、`548,^root`
-U        | 过滤`Unix`套接字
-v        | 显示版本信息并退出


## 输出信息说明
- **COMMAND** 命令
- **PID** 进程ID
- **TID** 线程ID
- **PPID** 父进程ID
- **PGID** 进程组ID
- **USER** 用户
- **FD** 文件描述符

    - `cwd` 当前工作目录
    - `Lnn` 库参考（AIX）
    - `err` 文件描述符错误信息（请参阅`NAME`列）
    - `jld` 看守目录（FreeBSD）
    - `ltx` 共享库文本（代码和数据）
    - `Mxx` 十六进制内存映射类型编号xx
    - `m86` DOS合并映射文件
    - `mem` 内存映射文件
    - `mmap` 内存映射设备
    - `pd` 父目录
    - `rtd` 根目录
    - `tr` 内核跟踪文件（OpenBSD）
    - `txt` 程序文本（代码和数据）
    - `v86` `VP/ix`映射文件

- **TYPE** 类型

    - `IPv4` IPv4套接字
    - `IPv6` IPv6网络文件
    - `ax25` Linux`AX.25`套接字
    - `inet` 以太网域套接字
    - `lla` `HP-UX`链接级访问文件
    - `rte` `AF_ROUTE`套接字
    - `sock` 未知域的套接字
    - `unix` `UNIX`域套接字
    - `x.25` `HP-UX`的`x.25`套接字
    - `BLK` 块特殊文件
    - `CHR` 字符特殊文件
    - `DEL` 已删除的`Linux`映射文件
    - `DIR` 目录
    - `DOOR` `VDOOR`文件
    - `FIFO` `FIFO`文件
    - `KQUEUE` `BSD`样式内核事件队列文件
    - `LINK` 符号链接文件
    - `MPB` 多路复用块文件
    - `MPC` 多路复用字符文件
    - `NOFD` 对于无法打开的Linux`/proc/<PID>/fd`目录 - 目录路径显示在NAME列中，后跟一条错误消息
    - `PAS` `/proc/as`文件
    - `PAXV` `/proc/auxv`文件
    - `PCRE` `/proc/cred`文件
    - `PCTL` `/proc`控制文件
    - `PCUR` 当前`/proc`进程
    - `PCWD` `/proc`当前工作目录
    - `PDIR` `/proc`目录
    - `PETY` `/proc`执行类型（etype）
    - `PFD` `/proc`文件描述符
    - `PFDR` `/proc`文件描述符目录
    - `PFIL` 可执行`/proc`文件
    - `PFPR` `/proc` `FP`寄存器集
    - `PGD` `/proc/pagedata`文件
    - `PGID` `/proc`组通知程序文件
    - `PIPE` 管道流
    - `PLC` `/proc/lwpctl`文件
    - `PLDR` `/proc/lpw`目录
    - `PLDT` `/proc/ldt`文件
    - `PLPI` `/proc/lpsinfo`文件
    - `PLST` `/proc/lstatus`文件
    - `PLU` `/proc/lusage`文件
    - `PLWG` `/proc/gwindows`文件
    - `PLWI` `/proc/lwpsinfo`文件
    - `PLWS` `/proc/lwpstatus`文件
    - `PLWU` `/proc/lwpusage`文件
    - `PLWX` `/proc/xregs`文件
    - `PMAP` `/proc`地图文件（map）
    - `PMEM` `/proc`内存镜像文件
    - `PNTF` `/proc`进程通知程序文件
    - `POBJ` `/proc/object`文件
    - `PODR` `/proc/object`目录
    - `POLP` 旧格式`/proc`轻量级处理文件
    - `POPF` 旧格式`/proc` `PID`文件
    - `POPG` 旧格式`/proc`页面数据文件
    - `PORT` `SYSV`命名管道
    - `PREG` `/proc`注册文件
    - `PRMP` `/proc/rmap`文件
    - `PRTD` `/proc`根目录
    - `PSGA` `/proc/sigact`文件
    - `PSIN` `/proc/psinfo`文件
    - `PSTA` `/proc`状态文件
    - `PSXSEM` POSIX信号量文件
    - `PSXSHM` POSIX共享内存文件
    - `PTS` `/dev/pts`文件
    - `PUSG` `/proc/usage`文件
    - `PW` `/proc/watch`文件
    - `PXMP` `/proc/xmap`文件
    - `REG` 常规文件
    - `SMT` 共享内存传输文件
    - `STSO` 流套接字
    - `UNNM` 未命名的类型文件
    - `XNAM` 未知类型的`OpenServer Xenix`专用文件；
    - `XSEM` `OpenServer Xenix`信号灯文件
    - `XSD` `OpenServer Xenix`共享数据文件
    - 如果不知道对应的名称，则返回四个类型数字八位字节

- **DEVICE** 表示设备号或数据地址，以逗号分隔
- **SIZE, SIZE/OFF, OFFSET** 文件大小或文件偏移量（以字节为单位）
- **NLINK** 文件链接数量（配合`+L`选项）
- **NODE** 本地文件节点号、NFS文件的索引节点号等
- **NAME** 文件所在的安装点和文件系统的名称、符号链接指向的文件、网络地址或端口、unix套接字等

## 命令示例

1. 找出java进程的工作目录
    ```bash
    lsof -c java -a -d cwd
    ```

2. 列出GID号进程详情：
    ```bash
    lsof -g
    ```

3. 列出目录下被打开的文件：
    ```bash
    lsof +d /root
    ```

4. 递归列出目录下被打开的文件：
    ```bash
    lsof +D /home/test
    ```

5. 列出使用NFS的文件：
    ```bash
    lsof -n /root
    ```

Last Modified 2021-04-11

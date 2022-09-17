# ps

## 简介

ps 显示有关活动进程选择的信息。如果要重复更新所选内容和显示的信息，请使用 top

```
ps [options]
```

## 选项

<style>
table th:first-of-type {
    width: 20%;
}
</style>

| 选项                         | 说明                                                                                                                     |
| :--------------------------- | :----------------------------------------------------------------------------------------------------------------------- |
| -A                           | 选择所有进程，与`-e`相同                                                                                                 |
| -a                           | 选择当前会话关联的进程                                                                                                   |
| -e                           | 选择与当前会话不关联的所有进程                                                                                           |
| --deselect                   | 选择除满足指定条件的所有进程外的所有进程（取消选择）与`-N`相同                                                           |
| -e                           | 选择所有进程，与`-A`相同                                                                                                 |
| -N                           | 选择除满足指定条件的所有进程外的所有进程（取消选择）与`--deselect`相同                                                   |
| -C cmdlist                   | 通过命令名称选择。这将选择其可执行文件名称在 cmdlist 中给出的进程                                                        |
| -g grplist, --group grplist  | 按真实组 ID（RGID）或名称选择。这将选择真实组名或 ID 在 grplist 列表中的进程。真实的组 ID 标识创建进程的用户的组         |
| -p pidlist, --pid pidlist    | 通过 PID 选择。这将选择其进程 ID 号显示在 pidlist 中的进程                                                               |
| --ppid pidlist               | 按父进程 ID 选择。这将选择在 pidlist 中具有父进程 ID 的进程。也就是说，它选择的进程是 pidlist 中列出的那些进程的子进程   |
| -s sesslist, --sid sesslist  | 通过会话 ID 选择。这将选择具有在 sesslist 中指定的会话 ID 的进程                                                         |
| -t ttylist, --tty ttylist    | 列表按 tty 选择。这将选择与 ttylist 中给出的终端相关联的进程                                                             |
| -u userlist, --user userlist | 通过有效的用户 ID（EUID）或名称进行选择。这将选择有效用户名或 ID 在用户列表中的进程                                      |
| -f                           | 展示全格式列表                                                                                                           |
| -F                           | 额外的完整格式                                                                                                           |
| -o format, --format format   | 用户自定义输出信息                                                                                                       |
| -j                           | 作业格式                                                                                                                 |
| -l                           | 长格式，可以配合`-y`选项使用                                                                                             |
| -y                           | 不显示标志；显示 rss 代替 addr。这个选项只能与`-l`一起使用                                                               |
| --forest                     | 显示进程层次结构（ASCII）                                                                                                |
| -H                           | 显示进程层次结构                                                                                                         |
| --headers                    | 每一页输出一次表头                                                                                                       |
| --no-headers                 | 不输出表头                                                                                                               |
| k, --sort                    | 指定排序顺序，`+`表示升序（默认），`-`表示降序，语法`[+ \| -]key[,[+ \| -]key[,...]]`，如`ps -jax --sort=uid,-ppid,+pid` |
| -w                           | 使用两次以启用无限制屏幕宽度（输出信息不会被截断）                                                                       |
| -L                           | 显示线程，可能带有 LWP 和 NLWP 列                                                                                        |
| -m                           | 在进程之后显示线程                                                                                                       |
| -T                           | 显示线程，可能带有 SPID 列                                                                                               |

## 状态码

表头中的`STAT`或`S`表示当前进程的状态

| 状态 | 说明                                               |
| :--- | :------------------------------------------------- |
| `D`  | 不间断的睡眠（通常是 IO）                          |
| `I`  | 空闲的内核线程                                     |
| `R`  | 正在运行或可运行（在运行队列中）                   |
| `S`  | 睡眠中断（等待事件完成）                           |
| `T`  | 被作业控制信号停止                                 |
| `t`  | 在跟踪过程中被调试器停止                           |
| `W`  | 分页（自 2.6.xx 内核以来无效）                     |
| `X`  | 已终止（永远都看不到）                             |
| `Z`  | 已终止（僵尸）进程，已终止但其父进程未能获取到信息 |
| `<`  | 高优先级（对其他用户不利）                         |
| `N`  | 低优先级（对其他用户友好）                         |
| `L`  | 将页面锁定在内存中（用于实时和自定义 IO）          |
| `s`  | 表示会话创建者                                     |
| `l`  | 具有多线程的进程                                   |
| `+`  | 在前台进程组中                                     |

## 排序参考

| 键  | 完整键     | 说明                       |
| :-- | :--------- | :------------------------- |
| c   | cmd        | 可执行文件的简单名称       |
| C   | pcpu       | CPU 利用率                 |
| f   | flags      | 标记为长格式 F 字段        |
| g   | pgrp       | 进程组 ID                  |
| G   | tpgid      | 控制 tty 进程组 ID         |
| j   | cutime     | 累计用户时间               |
| J   | cstime     | 累计系统时间               |
| k   | utime      | 用户时间                   |
| m   | min_flt    | 次要页面错误数             |
| M   | maj_flt    | 主要页面错误数             |
| n   | cmin_flt   | 累积的次要页面错误         |
| N   | cmaj_flt   | 累积主要页面错误           |
| o   | session    | 会话 ID                    |
| p   | pid        | 进程 ID                    |
| P   | ppid       | 父进程 ID                  |
| r   | rss        | 居民集大小                 |
| R   | resident   | 常驻页面                   |
| s   | size       | 内存大小（以千字节为单位） |
| S   | share      | 共享页面数                 |
| t   | tty        | 控制 tty 的设备号          |
| T   | start_time | 进程开始运行时间           |
| U   | uid        | 用户 ID 号                 |
| u   | user       | 用户名                     |
| v   | vsize      | 使用 KiB 表示的 VM 总大小  |
| y   | priority   | 内核调度优先级             |

## 输出信息说明

因个人理解原因，小部分信息未做说明

| 代码       | 表头     | 说明                                                                                                                           |
| :--------- | :------- | :----------------------------------------------------------------------------------------------------------------------------- |
| %cpu       | %CPU     | CPU 使用率                                                                                                                     |
| %mem       | %MEM     | 内存使用率                                                                                                                     |
| args       | COMMAND  | 命令与命令参数                                                                                                                 |
| bsdstart   | START    | 命令启动的时间。如果该过程在 24 小时之前启动，则输出格式为`HH:MM`，否则为`Mmm:SS`（其中`Mmm`是一个月的三个字母）               |
| bsdtime    | TIME     | 累积的 cpu 时间，用户+系统。显示格式通常为·MMM:SS`，但是如果该进程使用了超过 999 分钟的 cpu 时间，则向右移动                   |
| cgname     | CGNAME   | 显示该进程所属的控制组的名称                                                                                                   |
| cgroup     | CGROUP   | 显示该进程所属的控制组                                                                                                         |
| cmd        | CMD      | 参考 `args`，`command`                                                                                                         |
| comm       | COMMAND  | 命令名称（仅可执行文件名称），如果输出超出屏幕宽度，可以通过`--cols`来修改屏幕宽度或使用两个`-w`选项指定无限制的宽度           |
| command    | COMMAND  | 参考 `args`                                                                                                                    |
| cputime    | TIME     | 累积 CPU 时间，`[DD-]hh:mm:ss`格式                                                                                             |
| cputimes   | TIME     | 累计 CPU 时间（以秒为单位）                                                                                                    |
| drs        | DRS      | 数据常驻集大小，即除可执行代码之外的专用物理内存量                                                                             |
| egid       | EGID     | 进程的有效组 ID 号，以十进制整数表示                                                                                           |
| egroup     | EGROUP   | 进程的有效组 ID。如果可以获取并且字段宽度允许，则为文本组 ID，否则为十进制表示                                                 |
| eip        | EIP      | 指令指针                                                                                                                       |
| esp        | ESP      | 堆栈指针                                                                                                                       |
| etime      | ELAPSED  | 自进程启动以来经过的时间，格式为`[[DD-]hh:]mm:ss`                                                                              |
| etimes     | ELAPSED  | 自启动过程以来经过的时间（以秒为单位）                                                                                         |
| euid       | EUID     | 有效的用户 ID                                                                                                                  |
| euser      | EUSER    | 有效的用户名。如果可以获取并且字段宽度允许，则为文本用户 ID，否则为十进制表示                                                  |
| exe        | EXE      | 可执行文件的路径。如果无法通过`cmd`，`comm`或`args`格式选项打印路径，则很有用                                                  |
| f          | F        | 进程相关标志，`1`已经`fork`但未执行，`4`使用超级用户特权                                                                       |
| fgid       | FGID     | 文件系统访问组 ID                                                                                                              |
| fgroup     | FGROUP   | 文件系统访问组 ID。如果可以获取并且字段宽度允许，则为文本组 ID，否则为十进制表示                                               |
| flag       | F        | 参考`f`                                                                                                                        |
| flags      | F        | 参考`f`                                                                                                                        |
| fname      | COMMAND  | 进程的可执行文件的基本名称的前 8 个字节。此列中的输出可能包含空格                                                              |
| fuid       | FUID     | 文件系统访问用户标识                                                                                                           |
| fuser      | FUSER    | 文件系统访问用户标识。如果可以获取并且字段宽度允许，则为文本用户 ID，否则为十进制表示                                          |
| gid        | GID      | 参考`egid`                                                                                                                     |
| group      | GROUP    | 参考`egroup`                                                                                                                   |
| ipcns      | IPCNS    | 描述进程所属名称空间的唯一索引节点号                                                                                           |
| lstart     | STARTED  | 命令启动的时间                                                                                                                 |
| lsession   | SESSION  | 如果已包含 systemd 支持，则显示进程的登录会话标识符。                                                                          |
| luid       | LUID     | 显示与进程关联的登录 ID                                                                                                        |
| lwp        | LWP      | 可调度实体的轻量进程（线程）ID                                                                                                 |
| lxc        | LXC      | 在其中运行任务的 lxc 容器的名称。如果某个进程未在容器内运行，则会显示破折号（'-'）                                             |
| machine    | MACHINE  | 如果已包含 systemd 支持，则显示分配给 VM 或容器的进程的计算机名称                                                              |
| maj_flt    | MAJFLT   | 此进程中发生的主要页面错误数                                                                                                   |
| min_flt    | MINFLT   | 此过程中发生的次要页面错误数                                                                                                   |
| mntns      | MNTNS    | 描述进程所属名称空间的唯一索引节点号                                                                                           |
| netns      | NETNS    | 描述进程所属名称空间的唯一索引节点号                                                                                           |
| nlwp       | NLWP     | 进程中的 lwps（线程）数                                                                                                        |
| numa       | NUMA     | 与最近使用的处理器关联的节点。`-1`表示 NUMA 信息不可用                                                                         |
| nwchan     | WCHAN    | 进程正在休眠的内核函数的地址（如果需要内核函数名，请使用 wchan）。正在运行的任务将在此列中显示一个破折号（'-'）                |
| ouid       | OWNER    | 如果已包含 systemd 支持，则显示进程会话所有者的 Unix 用户标识符                                                                |
| pcpu       | %CPU     | 参考`%cpu`                                                                                                                     |
| pgid       | PGID     | 进程组 ID 或等效的进程组创建者的进程 ID                                                                                        |
| pgrp       | PGRP     | 参考`pgid`                                                                                                                     |
| pid        | PID      | 数字表示的进程 ID                                                                                                              |
| pidns      | PIDNS    | 描述进程所属名称空间的唯一索引节点号                                                                                           |
| pmem       | %MEM     | 参考`%mem`                                                                                                                     |
| ppid       | PPID     | 父进程 ID                                                                                                                      |
| pri        | PRI      | 进程的优先级。较高的数字表示较低的优先级                                                                                       |
| psr        | PSR      | 当前分配给该进程的处理器                                                                                                       |
| rgid       | RGID     | 真实进程组 ID                                                                                                                  |
| rgroup     | RGROUP   | 真实的组名。如果可以获取并且字段宽度允许，则为文本组 ID，否则为十进制表示                                                      |
| rss        | RSS      | 常驻集大小，任务已使用的未交换物理内存（以千字节为单位）                                                                       |
| rssize     | RSS      | 参考`rss`                                                                                                                      |
| rsz        | RSZ      | 参考`rss`                                                                                                                      |
| rtprio     | RTPRIO   | 实时优先级                                                                                                                     |
| ruid       | RUID     | 真实用户 ID                                                                                                                    |
| ruser      | RUSER    | 真实的用户名。如果可以获取并且字段宽度允许，则为文本用户 ID，否则为十进制表示                                                  |
| s          | S        | 最小状态显示（一个字符）                                                                                                       |
| seat       | SEAT     | 如果已包含 systemd 支持，则显示与分配给特定工作场所的所有硬件设备关联的标识符                                                  |
| sess       | SESS     | 会话 ID，或等效的会话创建者的进程 ID                                                                                           |
| sgi_p      | P        | 当前正在执行该进程的处理器。如果进程当前未运行或不可运行，则显示`*`                                                            |
| sgid       | SGID     | 保存的组 ID                                                                                                                    |
| sgroup     | SGROUP   | 保存的组名。如果可以获取并且字段宽度允许，则为文本组 ID，否则为十进制表示                                                      |
| sid        | SID      | 参考`sess`                                                                                                                     |
| slice      | SLICE    | 如果已包含 systemd 支持，则显示进程所属的切片单元                                                                              |
| spid       | SPID     | 参考`lwp`                                                                                                                      |
| stackp     | STACKP   | 该进程的堆栈底部（起始）的地址                                                                                                 |
| start      | STARTED  | 命令启动的时间。如果该过程在 24 小时之前启动，则输出格式为`HH:MM:SS`，否则为`Mmm dd`（其中`Mmm`是三个字母的月份名称）          |
| start_time | START    | 进程的开始时间或日期。如果在调用 ps 的同一年中未启动该过程，则仅显示年份；如果未在同一天启动，则仅显示`MmmDD`，否则显示`HH:MM` |
| stat       | STAT     | 多字符处理状态                                                                                                                 |
| state      | S        | 参考`s`                                                                                                                        |
| stime      | STIME    | 参考`start_time`                                                                                                               |
| suid       | SUID     | 保存的用户                                                                                                                     |
| supgid     | SUPGIDID | 补充组的组 ID（如果有）                                                                                                        |
| supgrp     | SUPGRP   | 补充组的组名称（如果有）                                                                                                       |
| suser      | SUSER    | 保存的用户名。如果可以获取并且在字段宽度允许的情况下，它将是文本用户 ID，否则将是十进制表示形式                                |
| svgid      | SVGID    | 参考`sgid`                                                                                                                     |
| svuid      | SVUID    | 参考`suid`                                                                                                                     |
| sz         | SZ       | 进程核心映像的物理页面大小。这包括文本，数据和堆栈空间。设备映射目前不包括在内； 这可能会发生变化                              |
| tgid       | TGID     | 一个数字，表示任务所属的线程组（别名 pid）。它是线程组创建者的进程 ID                                                          |
| thcount    | THCNT    | 参考`nlwp`                                                                                                                     |
| time       | TIME     | 累积 CPU 时间，`[DD-]HH:MM:SS`格式                                                                                             |
| times      | TIME     | 累计 CPU 时间（以秒为单位）                                                                                                    |
| tname      | TTY      | 控制 tty（终端）                                                                                                               |
| tpgid      | TPGID    | 进程连接到的 tty（终端）上的前台进程组的 ID；如果进程未连接到 tty，则为-1                                                      |
| trs        | TRS      | 文本常驻集大小，专用于可执行代码的物理内存量                                                                                   |
| tt         | TT       | 控制 tty（终端）                                                                                                               |
| tty        | TT       | 控制 tty（终端）                                                                                                               |
| ucmd       | CMD      | 参考`comm`                                                                                                                     |
| ucomm      | COMMAND  | 参考`comm`                                                                                                                     |
| uid        | UID      | 参考`euid`                                                                                                                     |
| uname      | USER     | 参考`euser`                                                                                                                    |
| unit       | UNIT     | 如果已包含 systemd 支持，则显示进程所属的单元                                                                                  |
| user       | USER     | 参考`euser`                                                                                                                    |
| userns     | USERNS   | 描述进程所属名称空间的唯一索引节点号                                                                                           |
| utsns      | UTSNS    | 描述进程所属名称空间的唯一索引节点号                                                                                           |
| uunit      | UUNIT    | 如果已包含 systemd 支持，则显示进程所属的用户单元                                                                              |
| vsize      | VSZ      | 参考`vsz`                                                                                                                      |
| vsz        | VSZ      | 进程的虚拟内存大小，以 KiB（1024 字节为单位）。设备映射目前不包括在内；这可能会发生变化                                        |
| wchan      | WCHAN    | 进程正在休眠的内核函数的名称；如果进程正在运行，则为“-”；如果进程为多线程且 ps 未显示线程，则为`*`                             |

## 示例

1. 使用标准语法查看系统上的每个进程

   ```bash
   ps -e
   ps -ef
   ps -eF
   ps -ely
   ps aux
   ps lax
   ```

2. 输出进程树

   ```bash
   ps -ejH
   ps -axjf
   ```

3. 获取有关线程的信息

   ```bash
   ps -eLf
   ps -axms
   ```

4. 获取安全信息

   ```bash
   ps -eo euser,ruser,suser,fuser,f,comm,label
   ps -eM
   ```

5. 要查看以 root 身份运行的每个进程（真实和有效 ID），用户格式

   ```bash
   ps -U root -u root u
   ```

6. 查看用户定义格式的每个进程

   ```bash
   ps -eo pid,tid,class,rtprio,ni,pri,psr,pcpu,stat,wchan:14,comm
   ps -Ao pid,tt,user,fname,tmout,f,wchan
   ```

7. 仅打印`syslogd`的进程 ID

   ```bash
   ps -C syslogd -o pid=
   ```

8. 仅打印 PID`42`的进程名称

   ```bash
   ps -q 42 -o comm=
   ```

## 参考文档

- man7 ps https://man7.org/linux/man-pages/man1/ps.1.html

Last Modified 2022-09-17

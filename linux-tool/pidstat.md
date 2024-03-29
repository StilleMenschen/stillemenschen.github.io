# pidstat

## 简介

报告 Linux 内核管理的任务统计信息

```
pidstat [ -d ] [ -h ] [ -I ] [ -l ] [ -r ] [ -s ] [ -t ] [ -U [ username ] ] [ -u ] [ -V ] [ -w ] [ -C comm ]
        [ -p { pid [,...] | SELF | ALL } ] [ -T { TASK | CHILD | ALL } ] [ interval [ count ] ]
```

## 选项

<style>
table th:first-of-type {
    width: 24%;
}
</style>

| 选项                        | 说明                                                                                                                    |
| :-------------------------- | :---------------------------------------------------------------------------------------------------------------------- |
| -C comm                     | 指定一个进程监听                                                                                                        |
| -d                          | 只展示 I/O 统计信息                                                                                                     |
| -h                          | 在一行中显示每个任务的统计                                                                                              |
| -I                          | 在 SMP 环境中显示 CPU 的占用率                                                                                          |
| -l                          | 显示完整的命令和参数                                                                                                    |
| -p pid                      | 显示指定进程 ID 的统计信息                                                                                              |
| -r                          | 只展示页面错误率和内存利用率                                                                                            |
| -s                          | 只显示堆栈利用率                                                                                                        |
| -T { TASK \| CHILD \| ALL } | 指定统计进程的方式<br> TASK - 仅显示进程<br> CHILD - 显示指定进程和其子进程<br>ALL - 显示单个进程或者所选进程和其子进程 |
| -t                          | 显示与进程关联的线程统计信息                                                                                            |
| -U [ username ]             | 显示用户名而不是显示用户的 UID， 如果指定了用户名则只显示与用户关联的进程                                               |
| -u                          | 只显示 CPU 的利用率                                                                                                     |
| -w                          | 显示进程切换活动                                                                                                        |
| -V                          | 显示版本号                                                                                                              |

## 输出说明

- 通用
  - `UID` 用户 ID
  - `USER` 用户名
  - `PID` 进程 ID
  - `Command` 进程名称
- `-d` 选项，显示进程 I/O 统计信息
  - `kB_rd/s` 进程每秒从磁盘读取的千字节数
  - `kB_wr/s` 进程每秒从写入磁盘的千字节数
  - `kB_ccwr/s` 进程取消写入硬盘的千字节数（进程清空脏页缓存时可能会出现）
- `-r` 选项，显示进程的内存利用率

  监听单个进程时

  - `minflt/s` 进程每秒发生的小故障总数，那些不需要从磁盘加载内存页面的故障
  - `majflt/s` 进程每秒发生的主要故障总数，需要从磁盘加载内存页面的故障总数
  - `VSZ` 虚拟内存占用大小：整个进程的虚拟内存使用量，以千字节为单位
  - `RSS` 驻留集大小：进程使用的非交换物理内存，以千字节为单位
  - `%MEM` 进程当前使用的可用物理内存占比

  监听所有进程时

  - `minflt-nr` 进程及其所有子进程产生的小故障总数，并在时间间隔内收集
  - `majflt-nr` 进程及其所有子进程发生的重大故障总数，并在时间间隔内收集

- `-s` 选项，显示堆栈利用率
  - `StkSize` 为进程保留的内存量（以千字节为单位）作为堆栈，但不一定使用
  - `StkRef` 进程引用的用作堆栈的内存量（以千字节为单位）
- `-t` 选项，显示与选定进程关联的线程的统计信息
  - `TGID` 线程组对应主进程的 ID
  - `TID` 被监听的线程 ID
- `-u` 选项，显示 CPU 的利用率

  监听单个进程时

  - `%usr` 在用户级别（应用程序）执行时的 CPU 占用率
  - `%system` 在系统级别（内核）执行时的 CPU 占用率
  - `%guest` 在虚拟机（运行虚拟处理器）中的 CPU 占用率
  - `%CPU` 总计的 CPU 占用率
  - `CPU` 进程附加的处理器编号

  监听所有进程时

  - `usr-ms` 进程及其子进程在用户级别（应用程序）执行时花费的毫秒数
  - `system-ms` 进程及其子进程在系统级别（内核）执行时花费的毫秒数
  - `guest-ms` 进程及其子进程在虚拟机（运行虚拟处理器）中花费的总毫秒数

- `-w` 选项，显示进程切换活动
  - `cswch/s` 每秒进程执行的自愿上下文切换总数。当进程因需要不可用的资源而阻塞时，会发生自愿上下文切换
  - `nvcswch/s` 每秒执行的进程的非自愿上下文切换总数。当进程在其时间片的持续时间内执行并被迫放弃处理时，就会发生非自愿的上下文切换

Last Modified 2022-03-12

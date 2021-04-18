# netstat

## 简介

显示网络连接，路由表，接口统计信息，伪装连接和多播成员身份
```
netstat [address_family_options] [--tcp|-t] [--udp|-u]
       [--udplite|-U] [--sctp|-S] [--raw|-w] [--l2cap|-2] [--rfcomm|-f]
       [--listening|-l] [--all|-a] [--numeric|-n] [--numeric-hosts]
       [--numeric-ports] [--numeric-users] [--symbolic|-N]
       [--extend|-e[--extend|-e]] [--timers|-o] [--program|-p]
       [--verbose|-v] [--continuous|-c] [--wide|-W]
```
```
netstat {--route|-r} [address_family_options]
      [--extend|-e[--extend|-e]] [--verbose|-v] [--numeric|-n]
      [--numeric-hosts] [--numeric-ports] [--numeric-users]
      [--continuous|-c]
```
```
netstat {--interfaces|-i} [--all|-a] [--extend|-e[--extend|-e]]
       [--verbose|-v] [--program|-p] [--numeric|-n] [--numeric-hosts]
       [--numeric-ports] [--numeric-users] [--continuous|-c]
```
```
netstat {--groups|-g} [--numeric|-n] [--numeric-hosts]
       [--numeric-ports] [--numeric-users] [--continuous|-c]
```
```
netstat {--masquerade|-M} [--extend|-e] [--numeric|-n]
       [--numeric-hosts] [--numeric-ports] [--numeric-users]
       [--continuous|-c]
```
```
netstat {--statistics|-s} [--tcp|-t] [--udp|-u] [--udplite|-U] [--raw|-w] [delay]
```
```
netstat {--version|-V}
```
```
netstat {--help|-h}
```
address_family_options:
```
[-4|--inet] [-6|--inet6]
 [--protocol={inet,inet6,unix,ipx,ax25,netrom,ddp,bluetooth, ... }]
 [--unix|-x] [--inet|--ip|--tcpip] [--ax25] [--x25] [--rose]
 [--ash] [--bluetooth] [--ipx] [--netrom] [--ddp|--appletalk]
 [--econet|--ec]
```

Netstat显示有关Linux网络子系统的信息。显示的信息类型由第一个参数控制，如下所示：

- `(none)` 默认情况下，netstat显示已打开的`socket`列表
- `-r, --route` 显示路由表
- `-g, --groups` 显示IPv4和IPv6的多路广播组信息
- `-i, --interfaces` 显示所有网络接口，以及网络接收和传输错误计数器
- `-M, --masquerade` 显示无效连接
- `-s, --statistics` 显示每个协议的统计信息

## 选项

<style>
table th:first-of-type {
    width: 12%;
}
</style>

选项 | 说明
:- | :-
-v, --verbose                | 显示详细操作信息
-W, --wide                   | 不要通过使用所需宽度的输出来截断IP地址
-n, --numeric                | 显示数字地址而不是尝试确定符号，主机，端口或用户名
--numeric-hosts              | 显示数字主机地址，不影响端口和用户名解析
--numeric-ports              | 显示数字端口号，不影响主机地址和用户名解析
--numeric-users              | 显示数字用户ID，不影响主机地址和端口解析
-A family, --protocol=family | 过滤网络协议，`family`包括`inet`、 `inet6`、 `unix`、 `ipx`、 `ax25`、 `netrom`、 `econet`、 `ddp`、`bluetooth`
-c, --continuous             | 每间隔一秒输出所选信息
-e, --extend                 | 显示更多信息，使用两次该选项以展示最多的信息
-o, --timers                 | 包括与联网计时器有关的信息
-p, --program                | 显示套接字对应的程序PID和名称，如果套接字属于内核，则显示一个连字符
-l, --listening              | 仅显示监听中的套接字
-a, --all                    | 同时显示监听和非监听套接字
-F                           | 通过`FIB`输出信息
-C                           | 输出路由缓存信息

## 输出

### 活跃的互联网连接

- **Proto** 套接字协议（tcp，udp，udpl，raw）
- **Recv-Q** 已建立：连接到此套接字的用户程序未复制的字节数
- **Send-Q** 已建立：远程主机未确认的字节数
- **Local Address** 套接字本地端的地址和端口号
- **Foreign Address** 套接字远程端的地址和端口号
- **State** 套接字状态

    - **ESTABLISHED** 套接字已建立连接
    - **SYN_SENT** 套接字正在积极尝试建立一个联系
    - **SYN_RECV** 已从网络接收到连接请求
    - **FIN_WAIT1** 套接字已关闭，并且连接已关闭
    - **FIN_WAIT2** 连接已关闭，套接字正在等待从远端关闭
    - **TIME_WAIT** 套接字在关闭后仍在等待以处理数据包在网络中
    - **CLOSE** 未使用该套接字
    - **CLOSE_WAIT** 远端已关闭，正在等待套接字连接关闭
    - **LAST_ACK** 远端已经关闭，并且套接字已关闭等待确认
    - **LISTEN** 套接字正在侦听传入的连接
    - **CLOSING** 两个套接字均已关闭，但我们仍未发送所有数据
    - **UNKNOWN** 套接字的状态未知

- **User** 持有该套接字的用户名或用户ID
- **PID/Program name** 持有该套接字的进程ID和进程名称
- **Timer** 与此套接字关联的TCP计时器。格式为`timer(a/b/c)`。`timer`是以下值之一：

    - **off** 没有为此套接字设置计时器
    - **on** 套接字的重传计时器处于活动状态
    - **keepalive** 套接字的keepalive计时器处于活动状态
    - **timewait** 连接正在关闭，并且套接字的timewait计时器处于活动状态

    三个值表示的意思：

    - **a** 计时器值
    - **b** 发送的重传次数
    - **c** 发送的Keepalive数量

### 活跃的UNIX域套接字

- **Proto** 协议（通常为unix）
- **RefCnt** 参考计数（即通过此套接字连接的进程）
- **Flags** 显示的标志是SO_ACCEPTON（显示为ACC），SO_WAITDATA（W）或SO_NOSPACE（N）。如果未连接的套接字的相应进程正在等待连接请求，则在未连接的套接字上使用SO_ACCECPTON
- **Type** 套接字访问有几种类型：

    - **SOCK_DGRAM** 该套接字在数据报（无连接）模式下使用
    - **SOCK_STREAM** 这是一个流（连接）套接字
    - **SOCK_RAW** 该套接字用作原始套接字
    - **SOCK_RDM** 这个服务于可靠传递的消息
    - **SOCK_SEQPACKET** 这是一个顺序的数据包套接字
    - **SOCK_PACKET** 原始接口访问套接字
    - **UNKNOWN** 套接字的状态未知

- **State** 该字段将包含以下关键字之一：

    - **FREE** 未分配套接字
    - **LISTENING** 套接字正在监听连接请求。仅当您指定`--listening`（-l）或`--all`（-a）选项时，此类套接字才会包含在输出中
    - **CONNECTING** 套接字即将建立连接
    - **CONNECTED** 套接字已连接
    - **DISCONNECTING** 套接字已断开连接
    - **(empty)** 套接字未建立任意连接
    - **UNKNOWN** 套接字的状态未知

- **PID/Program name** 套接字已打开的进程的进程ID（PID）和进程名称
- **Path** 这是相应进程附加到套接字的路径名

## 相关文件

- /etc/services 服务翻译文件
- /proc proc文件系统的挂载点，可通过以下文件访问内核状态信息
- /proc/net/dev 设备信息
- /proc/net/raw 原始套接字信息
- /proc/net/tcp **TCP**套接字信息
- /proc/net/udp **UDP**套接字信息
- /proc/net/udplite **UDPLite**套接字信息
- /proc/net/igmp **IGMP**组播信息
- /proc/net/unix **Unix**域套接字信息
- /proc/net/ipx **IPX**套接字信息
- /proc/net/ax25 **AX25**套接字信息
- /proc/net/appletalk **DDP(appletalk)**套接字信息
- /proc/net/nr **NET/ROM**套接字信息
- /proc/net/route **IP**路由信息
- /proc/net/ax25_route **AX25**路由信息
- /proc/net/ipx_route **IPX**路由信息
- /proc/net/nr_nodes **NET/ROM**节点列表
- /proc/net/nr_neigh **NET/ROM**邻居
- /proc/net/ip_masquerade
- /sys/kernel/debug/bluetooth/l2cap 蓝牙**L2CAP**信息
- /sys/kernel/debug/bluetooth/rfcomm 蓝牙串行连接
- /proc/net/snmp 统计数据

## 命令示例

1. 显示UDP端口号的使用情况

    ```
    netstat -apu
    ```

2. 显示当前监听的连接和相关进程

    ```
    netstat -nlp
    ```

3. 显示网卡列表

    ```
    netstat -i
    ```

4. 显示组播组的关系

    ```
    netstat -g
    ```

Last Modified 2021-04-18

# netstat

## 简介

显示处于活动状态的`TCP`连接、计算机正在侦听的端口、以太网统计信息、`IP`路由表、用于`IP`、`ICMP`、`TCP`和`UDP`协议的`IPv4`统计信息和`IPv6`统计信息 (`IPv6`、`ICMPv6`、`TCP over IPv6`和`UDP over IPv6`协议) 。使用没有选项的情况下，此命令显示活动`TCP`连接

```
netstat [-a] [-b] [-e] [-n] [-o] [-p <Protocol>] [-r] [-s] [<interval>]
```

## 选项

<style>
table th:first-of-type {
    width: 14%;
}
</style>

选项 | 说明
:- | :-
-a              | 显示所有活动`TCP`连接以及计算机正在侦听的`TCP`和`UDP`端口
-b              | 显示创建每个连接或侦听端口所涉及的可执行文件。在某些情况下，众所周知的可执行文件托管多个独立组件，并且在这种情况下，将显示创建连接或侦听端口所涉及的组件序列。在这种情况下，可执行文件名称位于底部的 []，顶部是它调用的组件，依此类推，直到达到 `TCP/IP`。请注意，此选项可能非常耗时且将失败，除非你有足够的权限
-E              | 显示以太网统计信息，如发送和接收的字节数和数据包数。此选项可以与`-s`组合
-n              | 显示活动`TCP`连接，但地址和端口号用数字表示，而不会尝试确定名称
-o              | 显示活动`TCP`连接，并包含每个连接 (PID) 的`进程ID`。您可以根据Windows任务管理器中"进程"选项卡上的`PID`查找该应用程序。此选项可以与`-a`、`-n`和`-p`结合使用
-p `<Protocol>` | 显示协议指定的协议的连接。在这种情况下，该协议可以是`tcp`、`udp`、`tcpv6`或`udpv6`。如果将此选项与`-s`一起使用以按协议显示统计信息，则协议可以是`tcp`、`udp`、`icmp`、`ip`、`tcpv6`、`udpv6`、`icmpv6`或`ipv6`
-S              | 按协议显示统计信息。默认情况下，会显示`TCP`、`UDP`、`ICMP`和`IP`协议的统计信息。如果安装了`IPv6`协议，则会显示`TCP over IPv6`、基于`IPv6`的`UDP`、`ICMPv6`和`IPv6`协议的统计信息。`-P`选项可用于指定一组协议
-r              | 显示`IP`路由表的内容。这等效于路由打印命令
`<interval>`    | 每隔间隔秒重新计算选定的信息。按`CTRL + C`停止重新显示。如果省略此选项，则此命令仅打印选定的信息一次
/?              | 在命令提示符下显示帮助

## 输出信息

字段 | 说明
:- | :-
Proto           | 协议(`TCP`或`UDP`)的名称
Local address   | 本地计算机的IP地址和所使用的端口号。除非指定了`-n`选项，否则显示与`IP`地址和端口名称对应的本地计算机的名称。如果尚未建立端口，则端口号显示为星号(`*`)
Foreign address | 套接字连接到的远程计算机的`IP`地址和端口号。除非指定了`-n`选项，否则将显示与`IP`地址和端口对应的名称。如果尚未建立端口，则端口号显示为星号(`*`)
Status          | 指示`TCP`连接的状态，包括：<br>CLOSE_WAIT<br>CLOSED<br>ESTABLISHED<br>FIN_WAIT_1<br>FIN_WAIT_2<br>LAST_ACK<br>LISTEN<br>SYN_RECEIVED<br>SYN_SEND<br>TIMED_WAIT

## 示例

- 若要显示以太网统计信息和所有协议的统计信息，请键入
    ```batch
    netstat -e -s
    ```
- 若要仅显示`TCP`和`UDP`协议的统计信息，请键入
    ```batch
    netstat -s -p tcp udp
    ```
- 若要`每隔5秒`显示一次活动`TCP`连接和`进程ID`，请键入
    ```batch
    netstat -o 5
    ```
- 若要使用数字形式显示活动`TCP`连接和`进程ID`，请键入
    ```batch
    netstat -n -o
    ```

Last Modified 2021-04-07

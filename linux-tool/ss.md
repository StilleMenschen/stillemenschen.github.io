# ss

## 简介

`ss`用于转储套接字统计信息。它允许显示类似于`netstat`的信息。它可以显示比其他工具更多的`TCP`和状态信息。

```
ss [options] [ FILTER ]
```

## 选项

当没有使用选项时，`ss`显示已建立连接的打开的非侦听套接字列表（例如`TCP/UNIX/UDP`）。

<style>
table th:first-of-type {
    width: 20%;
}
</style>

| 选项                                            | 说明                                                                                                                                                                                                                                                                                             |
| :---------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -h, --help                                      | 显示帮助                                                                                                                                                                                                                                                                                         |
| -H, --no-header                                 | 不显示表头                                                                                                                                                                                                                                                                                       |
| -O, --oneline                                   | 在一行上打印每个套接字的数据                                                                                                                                                                                                                                                                     |
| -n, --numeric                                   | 不尝试解析服务名称如`ssh`表示`22`端口                                                                                                                                                                                                                                                            |
| -r, --resolve                                   | 尝试解析数字地址/端口                                                                                                                                                                                                                                                                            |
| -a, --all                                       | 显示侦听和非侦听（对于`TCP`这意味着已建立的连接）套接字                                                                                                                                                                                                                                          |
| -l, --listening                                 | 仅显示侦听套接字（默认情况下不展示）                                                                                                                                                                                                                                                             |
| -o, --options                                   | 显示定时器信息。 对于 TCP 协议，输出格式为：<br> `timer:(<timer_name>,<expire_time>,<retrans>`                                                                                                                                                                                                   |
| -e, --extended                                  | 显示详细的套接字信息。 输出格式为：<br> `uid:<uid_number> ino:<inode_number> sk:<cookie>`                                                                                                                                                                                                        |
| -m, --memory                                    | 显示套接字内存使用情况。输出格式为： `skmem:(r<rmem_alloc>,rb<rcv_buf>,t<wmem_alloc>,tb<snd_buf>,<fwd_alloc>,w<wmem_queued>,o<opt_mem>,bl<back_log>,d<sock_drop>)`                                                                                                                               |
| -p, --processes                                 | 使用套接字显示进程                                                                                                                                                                                                                                                                               |
| -i, --info                                      | 显示内部 TCP 信息                                                                                                                                                                                                                                                                                |
| -K, --kill                                      | 尝试强行关闭套接字。该选项显示成功关闭的套接字，并静默跳过内核不支持关闭的套接字。它仅支持`IPv4`和`IPv6`套接字                                                                                                                                                                                   |
| -s, --summary                                   | 打印汇总统计信息。此选项不解析从各种来源获取摘要的套接字列表。当套接字数量如此之大以至于解析`/proc/net/tcp`很痛苦时，它很有用                                                                                                                                                                    |
| -E, --events                                    | 在`socket`被销毁时继续显示`socket`                                                                                                                                                                                                                                                               |
| -Z, --context                                   | 作为`-p`选项，还显示进程安全上下文（需要启用`SElinux`）                                                                                                                                                                                                                                          |
| -z, --contexts                                  | 作为`-Z`选项还显示套接字上下文。套接字上下文取自关联的`inode`，而不是内核持有的实际套接字上下文。套接字通常标有创建过程的上下文，但是显示的上下文将反映应用的任何策略角色、类型和/或范围转换规则，因此是一个有用的参考。                                                                         |
| -N NSNAME, --net=NSNAME                         | 切换到指定的网络命名空间名称                                                                                                                                                                                                                                                                     |
| -b, --bpf                                       | 显示套接字 BPF 过滤器（只允许管理员获取这些信息）                                                                                                                                                                                                                                                |
| -4, --ipv4                                      | 仅显示`IPv4`套接字（`-f inet`的别名）                                                                                                                                                                                                                                                            |
| -6, --ipv6                                      | 仅显示`IPv6`套接字（`-f inet6`的别名）                                                                                                                                                                                                                                                           |
| -0, --packet                                    | 显示`PACKET`套接字（`-f link`的别名）                                                                                                                                                                                                                                                            |
| -t, --tcp                                       | 显示`TCP`套接字                                                                                                                                                                                                                                                                                  |
| -u, --udp                                       | 显示`UDP`套接字                                                                                                                                                                                                                                                                                  |
| -d, --dccp                                      | 显示`DCCP`套接字                                                                                                                                                                                                                                                                                 |
| -w, --raw                                       | 显示`RAW`套接字                                                                                                                                                                                                                                                                                  |
| -x, --unix                                      | 显示`Unix`域套接字（`-f unix`的别名）                                                                                                                                                                                                                                                            |
| -S, --sctp                                      | 显示`STCP`套接字                                                                                                                                                                                                                                                                                 |
| --vsock                                         | 显示`vsock`套接字（`-f vsock`的别名）                                                                                                                                                                                                                                                            |
| --xdp                                           | 显示`xdp`套接字（`-f xdp`的别名）                                                                                                                                                                                                                                                                |
| -f FAMILY, --family=FAMILY                      | 显示 FAMILY 类型的套接字。目前支持以下系列：unix、inet、inet6、link、netlink、vsock、xdp                                                                                                                                                                                                         |
| -A QUERY, --query=QUERY, --socket=QUERY         | 要转储的套接字表列表，以逗号分隔。可以理解以下标识符：<br>all、inet、tcp、udp、raw、unix、packet、netlink、unix_dgram、unix_stream、unix_seqpacket、packet_raw、packet_dgram、dccp、sctp、vsock_stream、vsock_dgram、xdp 列表中的任何项目都可以选择以感叹号 (!) 为前缀，以排除该套接字表被转储。 |
| -D FILE, --diag=FILE                            | 不显示任何内容，只是在应用过滤器后将有关 TCP 套接字的原始信息转储到 FILE。 如果 FILE 是 - 使用标准输出                                                                                                                                                                                           |
| -F FILE, --filter=FILE                          | 从 FILE 中读取过滤器信息。 FILE 的每一行都被解释为单个命令行选项。如果 FILE 是 - 使用标准输入                                                                                                                                                                                                    |
| FILTER := [ state STATE-FILTER ] [ EXPRESSION ] | 有关过滤器的详细信息，请查看官方文档。                                                                                                                                                                                                                                                           |

## 状态过滤器

STATE-FILTER 允许构造任意一组状态来匹配。它的语法是关键字 state 和 exclude 的序列，后跟状态标识符。

可用的标识符是：

- 所有标准 TCP 状态：established、syn-sent、syn-recv、fin-wait-1、fin-wait-2、time-wait、closed、close-wait、last-ack、listening 和 closing。
- all - 显示所有的
- connected - 除了 listening 和 closed 之外的所有状态
- synchronized - 除了 syn-sent 之外的所有连接状态
- bucket - 状态，作为 minisockets 维护，即 time-wait 和 syn-recv
- big - 与 bucket 相反

## 表达式

- src 表示来源网络地址以及端口
- dst 表示目标网络地址以及端口

地址端口的表示方法如下示例

```
src :8080
src 192.168.1.1
src 192.168.0.0/16
src 192.168.1.1:22
src 192.168.1.1/24:80

dst :8080
dst 192.168.1.1
dst 192.168.0.0/16
dst 192.168.1.1:22
dst 192.168.1.1/24:80
```

## 命令示例

1. 显示所有`TCP`连接

   ```bash
   ss -t -a
   ```

2. 显示指定网卡指定网络地址的流量并以 40 秒内的平均网络流量排序

   ```bash
   ss -t -a -Z
   ```

3. 显示所有`UDP`连接

   ```bash
   ss -u -a
   ```

4. 显示所有已建立的`SSH`连接

   ```bash
   ss -o state established '( dport = :ssh or sport = :ssh )'
   ```

5. 查找连接到 X 服务器的所有本地进程

   ```bash
   ss -x src /tmp/.X11-unix/*
   ```

6. 查找`HTTP`端口并列出所有处于 FIN-WAIT-1 状态的 tcp 套接字，目标网络为`193.233.7/24`并查看它们的计时器

   ```bash
   ss -o state fin-wait-1 '( sport = :http or sport = :https )' dst 193.233.7/24
   ```

7. 从所有套接字表中列出所有状态的套接字，但排除`TCP`连接（较旧的版本可能不支持`!`排除）

   ```bash
   ss -a -A 'all,!tcp,!udp'
   ```

8. 如果是虚拟化隔离的程序，需要先进入其命名空间才能查看它的网络连接，如通过 Docker 运行的程序

   ```bash
   docker inspect -f {{.State.Pid}} nginx
   docker top nginx
   nsenter -n -t [进程PID]
   ss -tn
   ```

9. 查询特定地址和端口的 TCP 连接

   ```bash
   ss -tna '( sport = :5672 or sport = :3306 or sport = :8510 ) dst 192.168.3.0/24 or dst 192.168.9.0/24 or dst 192.168.80.0/23'
   ss -tna 'dst 192.168.5.0/24 or dst 192.168.79.0/24 or dst 192.168.80.0/23' | sort -k 4
   ```

Last Modified 2023-12-11

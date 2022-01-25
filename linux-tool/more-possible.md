# 更多的命令

## dig

域名检测

```bash
dig www.bing.com
```

## host

域名解析

```bash
host bing.com
```

## uptime

系统运行时间和负载

```
~ $ uptime
13:58:01 up 15 days,  5:20,  1 user,  load average: 0.16, 0.23, 0.26
```

## uname

系统信息

```bash
uname -a
```

## hostnamectl

系统主机名配置

```
~ $ hostnamectl
   Static hostname: localhost.localdomain
Transient hostname: local
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 16059b01f4d64f3a99923d5e0addbf3e
           Boot ID: 7098257cd6b140479015a154f4ff93cd
    Virtualization: vmware
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 3.10.0-1062.el7.x86_64
      Architecture: x86-64
```

## head/tail

查看文本前面或后面的几行，默认 10 行

## cat

同时查看一个或多个文本文件的内容

## wc

统计文本文件字符数、行数或字节数，也能统计单词数，但前提是单词是以空格隔开的

## man

使用手册（manuals），如`man lsof`、`man ls`、`man tail`，打开的手册页面可以按箭头上下翻页，按 q 键退出

## bc

一个简单的计算器，支持加减乘除和括号，输入 quit 退出

## sort

以指定方式按行排序输入文本

## seq

生成一个数字序列

## uniq

删除输入文本中重复的行

> 可以通过阅读手册或使用`--help`选项获取更多使用说明

Last Modified 2022-01-25

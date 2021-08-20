# bashrc

Unix 系统`/etc/bashrc`（Redhat 内核），`/etc/bash.bashrc`（Debian 内核）

```
# 终端的字符编码
export LANG=zh_CN.UTF-8
# 花里胡哨的终端, Git Bash自带有
# export PS1='\e[32m\u\e[37m@\e[33m\h \e[35m\w\e[0m\n# '
# 部分命令的别名，自定义了一些默认的参数
alias ll='ls -AFlh --color=auto --time-style=long-iso'
alias grep='grep --color=auto'
alias less='less -gM'
alias lsn='less -N'
export HISTCONTROL=ignoreboth
```

Last Modified 2021-04-12

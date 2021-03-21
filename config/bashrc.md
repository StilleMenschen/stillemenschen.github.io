# bashrc

On the /etc/bashrc (Redhat) or /etc/bash.bashrc (Debian)
```
export LANG=zh_CN.UTF-8
export PS1='\e[32m\u\e[37m@\e[33m\h \e[35m\w\e[0m\n# '
alias ll='ls -AlhtF --color=auto --time-style=long-iso'
alias grep='grep --color=auto'
alias less='less -gM'
```

Last Modified 2021-03-21
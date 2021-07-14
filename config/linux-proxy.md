# Linux Proxy

仅在终端里生效一次

```bash
export HTTP_PROXY="http://127.0.0.1:4096"
export HTTPS_PROXY="http://127.0.0.1:4096"
export HTTP_PROXY="socks://127.0.0.1:1024"
export HTTPS_PROXY="socks://127.0.0.1:1024"
export ALL_PROXY="socks5://127.0.0.1:1024"
```

设置快捷方式

```bash
alias setproxy="export ALL_PROXY=socks5://127.0.0.1:1024"
alias unsetproxy="unset ALL_PROXY"
```

Last Modified 2021-07-15

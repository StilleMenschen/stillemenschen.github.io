# pip

现在国内镜像挺多的，可以多问问搜索引擎。文件放在在用户目录下，Linux在`$HOME/.config/pip/pip.conf`或`$HOME/.pip/pip.conf`，Windows在`%USERPROFILE%\.npmrc`，全局路径在哪里可以问问搜索引擎

```
[global]
# 下载超时
timeout=60
# 国内镜像
index-url=https://mirrors.aliyun.com/pypi/simple/
# 下载缓存
cache-dir=/usr/local/cache
[install]
#find-links=
#    https://pypi.douban.com/simple/
#    https://pypi.tuna.tsinghua.edu.cn/simple/
# 信任国内镜像SSL证书
trusted-host=mirrors.aliyun.com
#    pypi.douban.com
#    pypi.tuna.tsinghua.edu.cn
[freeze]
# pip freeze 输出全局已安装包的超时
timeout=10
```

Last Modified 2021-04-12

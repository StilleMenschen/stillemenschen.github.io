# pip

现在国内镜像挺多的，可以多问问搜索引擎。文件放在在用户目录下，Linux
在`$HOME/.config/pip/pip.conf`或`$HOME/.pip/pip.conf`，Windows 在`%USERPROFILE%\.npmrc`，全局路径在哪里可以问问搜索引擎

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

如果想要修改`site-packages`的位置，需要进入`Python`的安装目录找到文件`$PYTHON_HOME/Lib/site.py`，修
改`ENABLE_USER_SITE`、`USER_SITE`和`USER_BASE`变量

```
# ...
ENABLE_USER_SITE = True
# ...
USER_SITE = r'D:\Program\Python37\site-packages'
USER_BASE = r'D:\Program'
# ...
```

> 文件夹中的`Python37`中的`37`数字一般是取当前安装的 Python 版本号前两位

Last Modified 2021-04-28

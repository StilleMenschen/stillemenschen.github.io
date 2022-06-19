# npmrc

文件放在在用户目录下，Linux 在`$HOME/.npmrc`，Windows 在`%USERPROFILE%\.npmrc`，全局路径在哪里可以问问搜索引擎

```
# 全局包路径
prefix=/opt/nodejs/npm
# 下载缓存
cache=/opt/nodejs/cache
# 国内Taobao镜像
registry=https://registry.npm.taobao.org/
```

yarn 的配置命令

```
yarn config set prefix "D:\software\Yarn\Data"
yarn config set global-folder "D:\software\Yarn\Data\global"
yarn config set cache-folder "D:\software\Yarn\Cache"
```

Last Modified 2022-06-20

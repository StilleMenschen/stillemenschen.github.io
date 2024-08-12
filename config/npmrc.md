# npmrc

文件放在在用户目录下，Linux 在`$HOME/.npmrc`，Windows 在`%USERPROFILE%\.npmrc`，全局路径在哪里可以问问搜索引擎

```
# 全局包路径
prefix=/opt/nodejs/npm
# 下载缓存
cache=/opt/nodejs/cache
# 国内Taobao镜像
registry=https://registry.npmmirror.com
```

npm 的配置命令

```
npm config set prefix=/opt/nodejs/npm
npm config set cache=/opt/nodejs/cache
npm config set registry https://registry.npmmirror.com
```

yarn 的配置命令

```
yarn config set prefix "/opt/nodejs/yarn"
yarn config set global-folder "/opt/nodejs/yarn/global"
yarn config set cache-folder "/opt/nodejs/cache"
yarn config set registry https://registry.npmmirror.com
```

Last Modified 2024-08-12

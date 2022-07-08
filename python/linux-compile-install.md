# Linux 编译安装

## 安装依赖的库

以`CentOS 7 x64`为例

```bash
sudo yum groupinstall -y "Development Tools"
sudo yum install -y openssl-devel libffi-devel bzip2-devel
sudo yum install -y readline-devel
sudo yum install -y sqlite*
```

> 安装`readline-devel`是为了方便在`Python`的命令行里面使用方向键和退格键，安装`sqlite`则是`ipython`用到了这个依赖

## 安装延长支持版 OpenSSL

先卸载旧的`openssl`

```
yum remove -y openssl
```

安装`openssl`，官方下载地址`https://www.openssl.org/source/`，下载稳定版的（本文撰写时延长支持版是`1.1.1p`，最新版是`3.0.4`）

```bash
tar -zxf openssl-1.1.1p.tar.gz
cd cd openssl-1.1.1p
./config --prefix=/usr/local/lib/openssl
make && make install
```

链接`openssl`的库（如果已经有了可以忽略）

```bash
ln -s /usr/local/lib/openssl/lib/libssl.so.1.1 /usr/lib64/libssl.so.1.1
ln -s /usr/local/lib/openssl/lib/libcrypto.so.1.1 /usr/lib64/libcrypto.so.1.1
```

## 下载 Python 3.10

进入对应的版本下载页面，如`https://www.python.org/downloads/release/python-3105/`，下载`Python`的源码包，如`Python-3.10.5.tgz`

## 配置 Python 包含 OpenSSL

配置`Python`，先修改安装文件，启用`openssl`

```bash
tar -zxf Python-3.10.5.tgz
cd Python-3.10.5
vim Modules/Setup
```

大概在两百多行的位置，找到类似下面的配置去掉注释，然后修改`openssl`的安装路径，
注意路径要写正确，如`OPENSSL=/usr/local/lib/openssl`为上面步骤安装`openssl`的路径

```
OPENSSL=/usr/local/lib/openssl
  _ssl _ssl.c \
      -I$(OPENSSL)/include -L$(OPENSSL)/lib \
      -lssl -lcrypto
 _hashlib _hashopenssl.c \
      -I$(OPENSSL)/include -L$(OPENSSL)/lib \
      -lcrypto
```

## 安装 Python

然后生成`Makefile`并安装

```bash
./configure --prefix=/usr/local/lib/python3.10 --with-openssl=/usr/local/lib/openssl
make
make altinstall
```

> 上面的配置没有使用到优化，官方文档里面是建议用`--enable-optimizations --with-lto`（PGO + LTO）配置 Python，以便实现最佳性能。<br>
> 默认安装的`Python`是在`/usr/local/bin`目录下的，为了和服务器本身存在的`Python 2`区分开，执行程序一般是程序名加版本号，如`python3.10`。<br>
> 如果安装过程中有报错可以先删掉`Makefile`，执行`make clean`或者`make distclean`，然后重新配置和安装。

## 尝试安装框架

```bash
python3.10 -m pip install -U -i https://mirrors.aliyun.com/pypi/simple/ requests numpy ipython
```

## 进入交互式命令行

```bash
python3.10
```

> 或者用`ipython`也可以，有语法提示

```python
import requests
import numpy
requests.get('https://www.bing.com').content
np_array = numpy.arange(12)
np_array.shape
np_array.shape = 3, 4
np_array
np_array[:, 1]
np_array.transpose()
```

## 关于卸载

如果没有在配置时指定`--prefix`，可以重新使用`configure`配置一次，然后重新配置指定路径并安装，再到路径中查看类似创建和移动文件或文件夹的命令访问的操作，
参考删除文件和文件夹

或者可以执行一次安装的预览，如使用`make -n altinstall`预览安装过程会执行哪些操作，但不会实际进行安装，具体可参考`make --help`

Last Modified 2021-06-27

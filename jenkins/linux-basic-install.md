# Linux 系统基本安装

## 开始之前

- 准备好一台 Linux 服务器
- 熟悉部分 Linux 工具使用
- ssh 远程工具和文件传输工具
- 有条件的的话最好准备一个梯子

> 本文档基于 CentOS 7 系统上的操作编写

## 下载

搜索引擎查 Jenkins 一般都可以找到[官方网站](https://www.jenkins.io/)，另外官方中文社区也有翻译的[官网](https://www.jenkins.io/zh/)，域名相同。

然后点击上方导航最右侧的[下载](https://www.jenkins.io/download/)，进入下载页面。打开下载页面后可以看到有两个不同类型的安装包，
一种是 LTS（长期支持）版，每隔 4 周发布一个新版本；另一种是定期发布的版本，每周都会发布一个新版本，根据自己的需要选择。

由于本文介绍基本的安装方式，所以选择下载 war 包，点击`Generic Java package (.war)`下载。如果下载速度有点慢可以选择国内的镜像下载，
比如清华大学的开源站，访问[tuna](https://mirrors.tuna.tsinghua.edu.cn/)，在搜索框中搜索 jenkins，然后点击链接进入 Jenkins 的镜像仓库。

可以看到有比较多的目录，但其实只需要看两个目录，`war/`目录下是最新版，`war-stable/`目录下是 LTS 版，进入目录后可以看到很多的版本，
没有特殊要求的话找到最新的版本下载即可。

另外，可以复制下载链接直接在 Linux 上下载，使用`wget`或者`curl`工具下载到当前目录，若服务器未安装这些工具可执行`sudo yum install -y wget`安装，
安装速度慢可能是没切换`yum`的安装源到国内，搜索一下操作方式，切换到国内镜像源后速度应该快许多了。

```bash
wget https://get.jenkins.io/war-stable/2.263.4/jenkins.war
# or
curl -O https://get.jenkins.io/war-stable/2.263.4/jenkins.war
```

> 其它的镜像网站因为没有使用过，就不做说明了

## 安装依赖软件

启动 Jenkins 的 war 包只需要有 Java 就可以了，`Open JDK`或者`Oracle JDK`都行，这里选择 openjdk，并安装后续步骤要使用到的工具。

如果不确定服务器有没有 java，可以试试`type java`和`java -version`这两个命令，以 openjdk 为例，version 命令可以输出 java 版本

```text
# java -version
openjdk version "1.8.0_252"
OpenJDK Runtime Environment (build 1.8.0_252-b09)
OpenJDK 64-Bit Server VM (build 25.252-b09, mixed mode)
```

使用`yum`安装需要的工具，这里需要编辑器`vim`。如果使用`XShell`和`Xftp`可以再安装一个高速传输的工具`lrzsz`，好像是用到了 ZMODEM 啥的？

```bash
sudo yum install lrzsz vim*
```

## 创建用户和主目录

创建 jenkins 用户并添加同名组、创建用户目录，默认 shell 为 bash；同时重置账户的密码

```bash
sudo useradd -mU jenkins -s /bin/bash
sudo passwd jenkins
```

切换到 Jenkins 用户，并进入用户主目录`/home/jenkins`，创建一个 jenkins 文件存放的目录`data`

```bash
su jenkins
cd ~
mkdir data
```

上传刚刚下载好的 war 包到用户目录`/home/jenkins`中，或者直接用命令下载到目录中，war 包不需要解压

## 首次启动

在`/home/jenkins`目录下创建一个`startup.sh`文件，主要是用来后台启动 Jenkins。启动参数中的 JENKINS_HOME 指定 Jenkins 主目录，
而 httpPort 为 Jenkins 的启动参数，指定监听端口，更多参数可以参考[官方说明](https://www.jenkins.io/doc/book/installing/initial-settings/)，
启动后可以类似`http://192.168.0.1:8888`的地址来访问，端口按需修改。

`startup.sh`文件内容如下，后台启动程序的语法似乎是固定的，有兴趣可以去研究研究。

```bash
#!/usr/bin/sh
#!/usr/bin/bash

# 所在目录
jenkins_dir=/home/jenkins
# 后台启动并将输出写入log文件
nohup java -DJENKINS_HOME=${jenkins_dir}/data -jar ${jenkins_dir}/jenkins.war --httpPort=8888 > ${jenkins_dir}/jenkins.log 2>&1 &
```

保存文件，确认当前命令行在`/home/jenkins`目录下，执行`sh ./startup.sh`

> sh 文件就不给执行权限了，实际也不会经常去重启 Jenkins 的，除非服务器太垃圾了(笑)

假设服务器分配的 IP 地址是`192.168.0.1`，访问`http://192.168.0.1:8888`就可以看到解锁 Jenkins 的界面了，这里先别去找管理员密码登录，
先做下面的操作，修改插件的服务器地址到国内镜像

## 修改插件镜像

修改之前，先写一个停止 Jenkins 的`shutdown.sh`文件，主要是通过 ps 找到 Jenkins 的`pid`将 java 程序直接 kill 掉，以停止Jenkins，内容如下

```bash
#!/usr/bin/sh
#!/usr/bin/bash

kill -9 $(ps -aux|grep '[j]enkins'|awk '{print $2}')
```

执行`sh ./shutdown.sh`停止 Jenkins。当然也有其它的方法停止 Jenkins，比如通过启动的 8888 端口来找到`pid`并 kill 掉，随意发挥

接下来修改插件的更新地址，进入之前指定的 Jenkins 主目录`/home/jenkins/data`，修改文件`hudson.model.UpdateCenter.xml`的内容，
改为清华大学的镜像地址

```xml
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <!-- 注释原本的地址 -->
    <!-- <url>https://updates.jenkins.io/update-center.json</url> -->
    <url>https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json</url>
  </site>
</sites>
```

此外还需要再修改一个地方，进入主目录的 updates 目录，如`/home/jenkins/data/updates`，修改`default.json`文件，
替换下载地址为清华大学镜像地址，替换网络检测地址为百度，命令如下

```bash
sed -i 's/https:\/\/updates.jenkins.io\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' default.json
sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json
```

## 初始化

再次执行`. ./startup.sh`启动 Jenkins，然后访问`http://192.168.0.1:8888`，根据提示找到管理员密码，填写到文本框点击继续

可以直接复制给出的路径，执行`cat`命令查看，如

```bash
cat /home/jenkins/data/secrets/initialAdminPassword
```

输入完密码后点击继续，进入插件选择界面，如果对 Jenkins 还不是很了解直接选择安装推荐的插件就可以了，或者自己选择需要的插件

插件安装可能会失败，可以点击重试或者直接继续，等后面在去重装失败的插件

插件安装完后就是创建管理员账号了，建议创建一个，当然也可以不创建继续使用 admin 账号

接着是指定 Jenkins 的访问地址，使用现在访问的地址就可以了，如果还有代理转发的需求，可以选择现在不设置，后面再定义

最后就是开始使用 Jenkins，使用刚刚创建的账户登录或者 admin 账户登录

> 将上面写过的`startup.sh`和`shutdown.sh`文件结合起来实际上就是重启的操作了

Last Modified 2022-02-19

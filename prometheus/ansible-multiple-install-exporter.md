# Ansible 多机器 Dcoker 部署 Exporter

## 安装 Ansible

由于使用的是 CentOS 7 所以只简单说一下这个系统下的安装，首先是包管理器的方式，执行

```bash
sudo yum install ansible
```

因为 CentOS 7 带有 Python 2.7，所以也可以使用 pip 来安装，如果没有 pip，先安装 pip

```bash
curl https://bootstrap.pypa.io/get-pip.py -o /opt/get-pip.py
python /opt/get-pip.py --user
# pip版本在20.3之前才支持python2.7，可以在安装完成后重新更换pip版本
pip install --force-reinstall pip==20.3.4
```

然后使用 pip 安装 ansible

```bash
pip install ansible==2.10.9
```

>这里有个很神奇的地方，2.10.9 版本在 pypi 上面找不到，但实际上又可以安装；如果安装速度太慢，可以考虑切换 pip 的镜像源为
> taobao 的安装源https://mirrors.aliyun.com/pypi/simple/

## 被控端配置

在系统中添加一个文件`/etc/ansible/hosts`，指定被控服务器的地址、ssh 用户和 ssh 密码，这里使用了`testServer`来命名一个服
务器分组分组中包含 3 台被控服务器

```
[testServer]
centos76.server.test ansible_host=192.168.1.76 ansible_port=22 ansible_user=root ansible_password="abc@76."
centos77.server.test ansible_host=192.168.1.77 ansible_port=22 ansible_user=root ansible_password="abc@77."
centos78.server.test ansible_host=192.168.1.78 ansible_port=22 ansible_user=root ansible_password="abc@78."
```

>官方建议还是不要将服务器密码写在配置文件中，尽量在所有被控服务器和控制服务器之间做好免密登录，尽量不要使用 root 用户

保存文件后，可以试试服务器是否访问正常

```bash
ansible testServer -m ping
```

>如果执行 ping 的时候有提示远程登录的错误，可以先使用 ssh 分别登录一次被控端服务器，保存鉴权信息到控制端服务器
>的`known_hosts`中，执行`cat ~/.ssh/known_hosts`查看是否已经有对应机器的鉴权文件

## 采集器配置

在`/usr/local/src/ansible`目录下创建一个 docker-compose 文件，文件命名`docker-compose-node-exporter.yml`，用来在被控端服
务器上部署一个采集器容器；这里使用的是`v1.1.2` 版本的 exporter，并映射容器内被控端的采集端口`9100`到宿主机的`59100`上

```yml
version: "3.3"
services:
  prom-node-exporter:
    image: prom/node-exporter:v1.1.2
    container_name: prom-node-exporter
    # 开放特殊目录访问权限
    privileged: true
    # 客户端采集端口
    ports:
      - "59100:9100"
    volumes:
      - "/:/host:ro,rslave"
      - "/etc/localtime:/etc/localtime:ro"
    command:
      - "--path.rootfs=/host"
```

## PlayBooks 配置

PlayBooks 有剧本的意思，可以告诉 Ansible 在被控服务器上按顺序执行多个操作，在`/usr/local/src/ansible`目录下创建一个
`deploy-prometheus-node-exporter-playbooks.yml`文件，`hosts`中指定的 testServer 是刚刚定义的主机组名

```yml
---
- name: deploy prometheus node exporter
  hosts: testServer
  remote_user: root

  tasks:
    # 1. 创建一个目录来放docekr-compose配置文件
    - name: create prometheus source folder
      file:
        path: /usr/local/src/prometheus
        state: directory
        mode: 0755
    # 2. 复制配置文件到被控端
    - name: copy docker-compose file
      copy:
        src: /usr/local/src/ansible/docker-compose-node-exporter.yml
        dest: /usr/local/src/prometheus/docker-compose.yml
        mode: 0644
    # 3. 拉取 node exporter 镜像（此步骤其实可以忽略，启动容器的时候docker会自己拉取本地不存在的镜像）
    - name: pull prometheus node exporter docker images
      shell: docker pull prom/node-exporter:v1.1.2
    # 4. 进入到第1步骤创建好的目录中，使用docker-compose启动容器且后台运行
    - name: run node exporter with docker container
      shell: docker-compose up -d
      args:
        chdir: /usr/local/src/prometheus
```

## 检查和执行部署

先执行命令检查 PlayBooks 是否正确，这个操作仅仅是检查，并不会真正执行命令，所以第四个步骤中进入目
录`/usr/local/src/prometheus`可能会失败

```bash
ansible-playbook deploy-prometheus-node-exporter-playbooks.yml --check
```

检查完成后就可以执行部署了

```bash
ansible-playbook deploy-prometheus-node-exporter-playbooks.yml --verbose
```

## 检查采集器状态

由于有 Ansible，可以直接三台服务器一起看运行状态，查看容器信息和端口占用，由于 Node Exporter 使用了相同的容器命名，可以
统一看 log 输出

```bash
ansible testServer -a "docker ps -as"
ansible testServer -a "netstat -ntulp"
ansible testServer -a "docker logs prom-node-exporter"
```

确认采集器服务可以在被控端服务器上能访问到

```bash
for i in {6..8}; do curl -sL http://192.168.1.7${i}:59100/metrics | tail; done
```

最后，在部署 Prometheus 的监控服务器注册这三台机器的采集器，就可以实现监控了

## 参考文档

- Ansible Installation Guide https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html
- Ansible Getting Started https://docs.ansible.com/ansible/latest/user_guide/intro_getting_started.html
- Ansible PlayBooks https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html
- Ansible Module shell https://docs.ansible.com/ansible/2.3/shell_module.html
- Ansible Module file https://docs.ansible.com/ansible/2.3/file_module.html
- Ansible Module copy https://docs.ansible.com/ansible/2.3/copy_module.html
- Ansible 中文权威指南 http://www.ansible.com.cn/docs/intro.html

Last Modified 2021-05-15

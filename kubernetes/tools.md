# Kubernetes 工具

避免在生产环境中使用`kubectl`来操作集群，应当使用受版本控制系统管理的部署清单来控制集群行为

## 命令别名

```bash
alias k="kubectl"
alias kg="kubectl get"
alias kgdep="kubectl get deployment"
alias ksys="kubectl --namespace=kube-system"
alias kd="kubectl describe"
```

## 标志缩写

```bash
# --namespace 用 -n 代替
kubectl get pods -n kube-system
# --selector 用 -l 代替
kubectl get pods -l environment=staging
```

## 命令缩写

- po (pods)
- deploy (deployments)
- svc (services)
- ns (namespace)
- no (nodes)
- cm (configmaps)
- sa (serviceaccounts)
- ds (daemonsets)
- pv (persistentvolumes)

```bash
kubectl get po
kubectl get deploy
kubectl get svc
kubectl get ns
kubectl get no
kubectl get cm
kubectl get sa
kubectl get ds
kubectl get pv
```

## 自动补齐

```bash
sudo apt install -y bash-completion
kubectl completion bash >> ~/.bash_profile
source ~/.bash_profile
```

```bash
kubectl -h
kubectl <TAB><TAB>
kubectl get -h
kubectl get <TAB><TAB>
kubectl get pods --<TAB><TAB>
```

## 资源帮助

```bash
kubectl explain --recursive deploy.spec.template.spec
```

## 更详细地输出

```bash
kubectl get nodes -o wide
```

## 输出格式

```bash
kubectl get namespace -o json
```

使用`jq`命令行工具过滤输出的 json

> 如果没有则通过包管理器安装，如 Ubuntu 下执行 sudo apt install -y jq

```bash
kubectl get namespace -o json | jq '.items[].metadata.name'
# 无网络情况下替代的方式
kubectl get pods -o=jsonpath={.items[0].metadata.name}
```

## 监视对象

指定 watch 参数会间隔一段时间刷新列表信息

```bash
kubectl get pods -w
kubectl get pods --watch
```

> 也可以试试使用`kubespy`工具来实时监视资源

## 查看对象描述

查看描述中的 Events 部分对问题排查有一定帮助

```bash
kubectl describe pods busybox
```

## 编辑部署

用默认的编辑器打开部署配置文件

```bash
kubectl edit deployments busybox
```

> 避免在生产环境中使用`kubectl`来操作集群，应当使用受版本控制系统管理的部署清单来控制集群行为

## 通过命令生成清单

标志`--dry-run=client` 告诉 kubectl 不必实际创建资源，只需输出需要创建的资源。标志`-o yaml`可以输出 YAML 格式的资源清单。

```bash
kubectl create deployment demo --image=nginx --dry-run=client -o yaml
kubectl create deployment demo --image=nginx --dry-run=client -o yaml > demo-deployment.yaml
```

## 导出资源

可以获取当前已存在的资源清单

```bash
kubectl get deployments demo -o yaml
```

## 对比差异

```bash
kubectl diff -f ./web-deployment.yaml
```

## 容器日志

查看指定命名空间中的 pod 指定最后多少行的日志，且追踪日志输出

```bash
kubectl logs --namespace area1 --tail=30 --follow web-8667899c97-5kdkd
kubectl logs -n kube-system --tail=30 --follow etcd-docker-desktop
```

也可以直接附着到容器上查看其输出`kubectl attach busybox -c busybox -i -t`，结束执行可以按下`CTRL+C`，但这样容器也会退出；如果只是想结束附着但不退出容器，可以按下`CTRL-P,CTRL-Q`，但前提是容器运行参数有`-i`和`-t`选项

## 转发端口

将容器的端口转发到当前计算机上就能通过转发的端口直接访问了

```bash
kubectl port-forward demo-28ea8f88d-vm38az 9999:8888
```

## 连接容器终端

```bash
kubectl exec -it busybox -- /bin/ash
```

如果 Pod 中有多个容器，可以使用`-c`指定容器的名称

```bash
kubectl exec -it -c containerName POD_NAME /bin/sh
```

## 排除故障

可以通过网络访问容器来找出问题，如运行一个临时的容器来请求出问题的 Web 容器

```bash
kubectl run demo --image cloudnatived/demo:hello --expose --port 8888
```

```bash
kubectl run nslookup --image=busybox --rm -it --restart=Never --command -- nslookup demo
kubectl run wget --image=busybox --rm -it --restart=Never --command -- wget -qO- http://demo:8888
```

- `--rm` 表示退出后即删除容器资源，不会影响本地的 pod
- `-it` 表示以交互式的方式运行一个伪终端
- `--restart=Never` 表示容器退出后不会重新启动
- `--command --` 表示传递命令到临时启动的容器中，`--`后面的参数会传入到容器内执行

> `kubesquash` 工具可以将调试器附着到容器中进行调试

## 上下文切换

如果拥有多个集群，例如，你的机器上有一个用于本地测试的 Kubernetes 集群，云中有一个生产集群，或许还有另一个预发布以及开发的远程集群。kubectl 怎么知道你指的是哪个集群？

为了解决这个问题，kubectl 引入了上下文的概念。集群、用户以及命名空间组合在一起就构成了上下文。

kubectl 的命令总是在当前的上下文中执行

```bash
kubectl config get-contexts
```

每个上下文都有一个名称，并指向特定的集群，以及该集群的用户名（用于身份验证）和集群内的命名空间。

当前上下文会在第一列中显示`*`如果现在运行一个 kubectl 命令，则它将在 Docker Desktop 集群的默认命名空间中运行（因为 NAMESPACE 列为空，表明上下文指向默认命名空间）

```bash
kubectl cluster-info
```

### 切换上下文

```bash
kubectl config use-context gke
```

可以把上下文视为书签：它们可以帮助你轻松地松切换到特定的集群和特定的命名空间。

创建一个新的上下文

```bash
kubectl config set-context myapp --cluster=gke --namespace=myapp
```

当前上下文

```bash
kubectl config current-context
```

`kubectx`工具可以实现快速的上下文切换，`kubens`工具可以快速地切换命名空间

`kube-ps1`工具可以显示当前的命名空间，配置到终端的`PROMPT`中

```
source /path/to/kube-ps1.sh
PS1='[\u@\h \W $(kube_ps1)]\$ '
```

## 其它工具

`kube-shell`可以在终端中提供补全候选列表

`Kubed-sh`则是直接在集群中运行`shell`，可以创建本地脚并交由它在实际的集群中执行

`Stern`可以更方便地查看 Kubernetes 的资源日志，它支持以正则表达式的方式指定资源并跟踪其日志输出

如果还需要更深度的定制自己的工具，可以通过 Go 语言来编写程序，Kubernetes 也是使用 Go 开发的

Last Modified 2023-06-22

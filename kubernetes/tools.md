# Kubernetes 工具

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

Last Modified 2023-06-21

# 应用资源

## 简单资源配置

k8s/deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo
  labels:
    app: demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
        - name: demo
          image: cloudnatived/demo:hello
          ports:
            - containerPort: 8888
```

> 可以尝试将副本数量增加，如`replicas: 3`，这样就会启动多个应用

## 运行

```bash
kubectl apply -f k8s/deployment.yaml
deployment.apps "demo" created
```

```bash
kubectl get pods --selector app=demo
NAME                    READY     STATUS    RESTARTS   AGE
demo-6d99bf474d-z9zv6   1/1       Running   0          2m
```

但此时还是无法访问到应用的，还需要创建网络转发，请往下看

## 网络服务

假设你想与 Pod 建立网络连接（例如我们的示例应用程序）。那么应该:怎么做？

你可以找出 Pod 的 IP 地址，然后直接连接到该地址和应用程序的端！上。但是，当 Pod 重启时，IP 地址就可能会改变，因此你必须不断查找并确保总是连接到正确的地址。
更糟糕的是，Pod 可能有多个副本，每个副本都有不同的地址。任何需要连系统一样，接这些 Pod 的应用程序都必须维护这些地址的列表，这个办法可不理想。

幸运的是，我们有更好的方法：服务资源可以提供一个不变的 IP 地址或 DNS 名称，并自动路由到与之匹配的 Pod 上。

可以将服务当成 Web 代理或负载均衡器，负责将请求转发到一组后端的 Pod 上 。但是，服务并非只能用于 Web 端口，它可以将流量从任意端口转发到其他端口，如规范的 ports 部分所示。

k8s/service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: demo
  labels:
    app: demo
spec:
  ports:
    - port: 8888
      protocol: TCP
      targetPort: 8888
  selector:
    app: demo
  type: ClusterIP
```

注意 kind 指定的是 Service，selector 部分告诉服务如何将请求路由到特定的 Pod 上。请求会被转发到所有与指定标签集匹配的 Pod 上；这里的标签是 app: demo。在示例中，只有一个 Pod 可以匹配，但是如果有多个 Pod，则服务会将每个请求发送到一个随机选择的 Pod 上（默认的负载均衡算法）。

```bash
kubectl apply -f k8s/service.yaml
service "demo" created

kubectl port-forward service/demo 9999:8888
Forwarding from 127.0.0.1:9999 -> 8888
Forwarding from [::1]:9999 -> 8888
```

查询集群状态

```bash
kubectl get nodes
```

## 删除

可以删除指定目录下通过 yaml 文件指定的所有资源

```bash
kubectl delete -f k8s/
```

如果想要参数化资源文件配置，可以考虑使用包管理器 Helm，这个是云原生计算基金会的项目之一

## 模板化的资源

需要先安装 Helm，地址：https://helm.sh/docs/intro/install/

下面是模板化的配置文件，里面的标记变量由 Helm 自动填写

templates/deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Values.container.name }}
spec:
  replicas: {{ .Values.replicas }}
  selector:
    matchLabels:
      app: {{ .Values.container.name }}
  template:
    metadata:
      labels:
        app: {{ .Values.container.name }}
        environment: {{ .Values.environment }}
    spec:
      containers:
        - name: {{ .Values.container.name }}
          image: {{ .Values.container.image }}:{{ .Values.container.tag }}
          ports:
            - containerPort: {{ .Values.container.port }}
          env:
            - name: environment
              value: {{ .Values.environment }}
```

templates/service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ .Values.container.name }}-service
  labels:
    app: {{ .Values.container.name }}
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: {{ .Values.container.port }}
  selector:
    app: {{ .Values.container.name }}
  type: LoadBalancer
```

Helm 配置文件

Chart.yaml

```yaml
apiVersion: v1
description: A Helm chart for myapp
version: 1.0.1
appVersion: 1.0.0
name: demo
sources:
  - https://github.com/cloudnativedevops/cloudnatived
```

### 不同上下文环境的配置

production-values.yaml

```yaml
environment: production
```

staging-values.yaml

```yaml
environment: staging
```

values.yaml

```yaml
environment: development
container:
  name: demo
  port: 8888
  image: cloudnatived/demo
  tag: hello
replicas: 1
```

## 通过 Helm 安装

```bash
ls k8s/demo
Chart.yaml             prod-values.yaml staging-values.yaml    templates
values.yaml
```

```bash
kubectl delete all --selector app=demo
```

```bash
helm install --name demo ./k8s/demo
```

查看正在运行的 Release

```bash
helm list
```

## Helm 简介

三大概念

Chart 代表着 Helm 包。它包含在 Kubernetes 集群内部运行应用程序，工具或服务所需的所有资源定义。你可以把它看作是 Homebrew formula，Apt dpkg，或 Yum RPM 在 Kubernetes 中的等价物。

Repository（仓库） 是用来存放和共享 charts 的地方。它就像 Perl 的 CPAN 档案库网络 或是 Fedora 的 软件包仓库，只不过它是供 Kubernetes 包所使用的。

Release 是运行在 Kubernetes 集群中的 chart 的实例。一个 chart 通常可以在同一个集群中安装多次。每一次安装都会创建一个新的 release。以 MySQL chart 为例，如果你想在你的集群中运行两个数据库，你可以安装该 chart 两次。每一个数据库都会拥有它自己的 release 和 release name。

在了解了上述这些概念以后，我们就可以这样来解释 Helm：

Helm 安装 charts 到 Kubernetes 集群中，每次安装都会创建一个新的 release。你可以在 Helm 的 chart repositories 中寻找新的 chart。

> 详见 https://helm.sh/docs/intro/using_helm/

Last Modified 2023-06-01

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

Last Modified 2023-05-31

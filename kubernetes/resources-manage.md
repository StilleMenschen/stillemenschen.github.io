# 资源管理

Kubernetes 支持使用资源的请求、约束和默认值，支持 Pod 垂直自动扩展器优化这些值；还可以使用就绪探针、存活探针和 Pod 中断预算来管理容器。另外，如果使用云服务，还要懂得如何优化云存储；了解如何以及何时使用可抢占或预留的实例来控制成本。

## 资源请求

Kubernetes 支持配置 Pod 所需的 CPU 和内存资源，这样可以告诉调度器应该如何调度 Pod（判断节点是否具备符合要求的空闲资源来运行 Pod）。另外还有其它重要的资源类型，如网络带宽、磁盘 I/O 操作（IOPS）和磁盘空间，这些资源可能引发集群内的竞争，不过 Kubernetes 暂不支持描述这些资源的要求。如果没有任何节点有足够的空闲资源，则 Pod 将一直处于 pending 状态，直到有空闲资源出现。

针对每个容器，你都可以指定其资源限制和请求，包括如下选项：

- spec.containers[].resources.limits.cpu
- spec.containers[].resources.limits.memory
- spec.containers[].resources.limits.hugepages-<size>
- spec.containers[].resources.requests.cpu
- spec.containers[].resources.requests.memory
- spec.containers[].resources.requests.hugepages-<size>

尽管你只能逐个容器地指定请求和限制值，考虑 Pod 的总体资源请求和限制也是有用的。对特定资源而言，Pod 的资源请求/限制是 Pod 中各容器对该类型资源的请求/限制的总和。

## 资源单位

### CPU 资源单位

CPU 资源的限制和请求以 “cpu” 为单位。在 Kubernetes 中，一个 CPU 等于 1 个物理 CPU 核 或者 1 个虚拟核，取决于节点是一台物理主机还是运行在某物理主机上的虚拟机。

你也可以表达带小数 CPU 的请求。当你定义一个容器，将其 spec.containers[].resources.requests.cpu 设置为 0.5 时，你所请求的 CPU 是你请求 1.0 CPU 时的一半。对于 CPU 资源单位，数量表达式 0.1 等价于表达式 100m，可以看作 “100 millicpu”。有些人说成是“一百毫核”，其实说的是同样的事情。

CPU 资源总是设置为资源的绝对数量而非相对数量值。例如，无论容器运行在单核、双核或者 48-核的机器上，500m CPU 表示的是大约相同的计算能力。

> Kubernetes 不允许设置精度小于 1m 的 CPU 资源。因此，当 CPU 单位小于 1 或 1000m 时，使用毫核的形式是有用的；例如 5m 而不是 0.005。

### 内存资源单位

memory 的限制和请求以字节为单位。你可以使用普通的整数，或者带有以下数量后缀的定点数字来表示内存：E、P、T、G、M、k。你也可以使用对应的 2 的幂数：Ei、Pi、Ti、Gi、Mi、Ki。例如，以下表达式所代表的是大致相同的值：

```
128974848, 129e6, 129M, 128974848000m, 123Mi
```

## 资源配置例子

以下 Pod 有两个容器。每个容器的请求为 0.25 CPU 和 64MiB（2^26 字节）内存，每个容器的资源限制为 0.5 CPU 和 128MiB 内存。你可以认为该 Pod 的资源请求为 0.5 CPU 和 128 MiB 内存，资源限制为 1 CPU 和 256MiB 内存。

```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
    - name: app
      image: images.my-company.example/app:v4
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m"
    - name: log-aggregator
      image: images.my-company.example/log-aggregator:v6
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m"
```

Kubernetes 允许资源被过度使用，也就是说，一个节点上所有容器的资源约束值总和可以超过该节点实际的总资源量。这是一种赌博，调度器赌的是在大多数情况下，大多数容器需要的资源量不会达到各自的资源约束。

如果赌博失败，而且资源使用总量接近节点的最大容量，则 Kubernetes 会更加积极地终止容器。在面临资源压力的时候，即使那些超出了请求值但未超过约束值的容器也可能会被终止。

在所有其他条件都相同的情况下，如果 Kubernetes 需要终止 Pod，则它会从超出请求值最多的 Pod 开始下手。除非在极少数情况下（例如 Kubernetes 无法运行 kubelet 等系统组件），否则在请求值内的 Pod 不会被终止。

## 生命周期

我们已经看到，如果 Kubernetes 知道 Pod 所需的 CPU 和内存，那么就能更好地管理 Pod。但是，它还需要知道容器何时在工作，也就是说，容器何时正常运行，处于可以处理请求的状态。
容器化的应用程序陷入阻塞状态是很常见的，虽然进程仍在运行，但不会处理任何请求。Kubernetes 需要一种方法来检测这种情况，以便通过重新启动容器来解决问题。

### 基于文件存活探针

在这个配置文件中，可以看到 Pod 中只有一个 Container。periodSeconds 字段指定了 kubelet 应该每 5 秒执行一次存活探测。initialDelaySeconds 字段告诉 kubelet 在执行第一次探测前应该等待 5 秒。kubelet 在容器内执行命令 cat /tmp/healthy 来进行探测。如果命令执行成功并且返回值为 0，kubelet 就会认为这个容器是健康存活的。如果这个命令返回非 0 值，kubelet 会杀死这个容器并重新启动它。

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
    - name: liveness
      image: registry.k8s.io/busybox
      args:
        - /bin/sh
        - -c
        - touch /tmp/healthy; sleep 30; rm -f /tmp/healthy; sleep 600
      livenessProbe:
        exec:
          command:
            - cat
            - /tmp/healthy
        initialDelaySeconds: 5
        periodSeconds: 5
```

```bash
# 部署
kubectl apply -f exec-liveness.yaml
# 检测
kubectl describe pod liveness-exec
```

### 基于 HTTP 存活探针

在这个配置文件中，你可以看到 Pod 也只有一个容器。periodSeconds 字段指定了 kubelet 每隔 3 秒执行一次存活探测。initialDelaySeconds 字段告诉 kubelet 在执行第一次探测前应该等待 3 秒。kubelet 会向容器内运行的服务（服务在监听 8080 端口）发送一个 HTTP GET 请求来执行探测。如果服务器上 /healthz 路径下的处理程序返回成功代码（2xx 或 3xx 状态码，有时候云负载均衡器不一定这样认为，如 301 可能就会被认为是不健康的状态），则 kubelet 认为容器是健康存活的。如果处理程序返回失败代码，则 kubelet 会杀死这个容器并将其重启。

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-http
spec:
  containers:
    - name: liveness
      image: registry.k8s.io/liveness
      args:
        - /server
      livenessProbe:
        httpGet:
          path: /healthz # 避免请求地址冲突
          port: 8080
          httpHeaders:
            - name: Custom-Header
              value: Awesome
        initialDelaySeconds: 3
        periodSeconds: 3
```

```bash
# 部署
kubectl apply -f http-liveness.yaml
# 检测
kubectl describe pod liveness-http
```

### 基于 TCP 存活探针

TCP 检测的配置和 HTTP 检测非常相似。下面这个例子同时使用就绪和存活探针。kubelet 会在容器启动 5 秒后发送第一个就绪探针。探针会尝试连接 goproxy 容器的 8080 端口。如果探测成功，这个 Pod 会被标记为就绪状态，kubelet 将继续每隔 10 秒运行一次探测。

除了就绪探针，这个配置包括了一个存活探针。kubelet 会在容器启动 15 秒后进行第一次存活探测。与就绪探针类似，存活探针会尝试连接 goproxy 容器的 8080 端口。如果存活探测失败，容器会被重新启动。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: goproxy
  labels:
    app: goproxy
spec:
  containers:
    - name: goproxy
      image: registry.k8s.io/goproxy:0.1
      ports:
        - containerPort: 8080
      readinessProbe:
        tcpSocket:
          port: 8080
        initialDelaySeconds: 5
        periodSeconds: 10
      livenessProbe:
        tcpSocket:
          port: 8080
        initialDelaySeconds: 15
        periodSeconds: 20
```

```bash
# 部署
kubectl apply -f tcp-liveness-readiness.yaml
# 检测
kubectl describe pod goproxy
```

### 基于 gRPC 存活探针

要使用 gRPC 探针，必须配置 port 属性。如果要区分不同类型的探针和不同功能的探针，可以使用 service 字段。你可以将 service 设置为 liveness，并使你的 gRPC 健康检查端点对该请求的响应与将 service 设置为 readiness 时不同。这使你可以使用相同的端点进行不同类型的容器健康检查（而不需要在两个不同的端口上侦听）。如果你想指定自己的自定义服务名称并指定探测类型，Kubernetes 项目建议你使用使用一个可以关联服务和探测类型的名称来命名。例如：myservice-liveness（使用 - 作为分隔符）。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: etcd-with-grpc
spec:
  containers:
    - name: etcd
      image: registry.k8s.io/etcd:3.5.1-0
      command: ["/usr/local/bin/etcd", "--data-dir", "/var/lib/etcd", "--listen-client-urls", "http://0.0.0.0:2379", "--advertise-client-urls", "http://127.0.0.1:2379", "--log-level", "debug"]
      ports:
        - containerPort: 2379
      livenessProbe:
        grpc:
          port: 2379
        initialDelaySeconds: 10
```

```bash
# 部署
kubectl apply -f grpc-liveness.yaml
# 检测
kubectl describe pod goproxy
```

### 就绪探针

就绪探针的配置和存活探针的配置相似。唯一区别就是要使用 readinessProbe 字段，而不是 livenessProbe 字段。

```yaml
readinessProbe:
  exec:
    command:
      - cat
      - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

最短就绪时间

.spec.minReadySeconds 是一个可选字段，用于指定新创建的 Pod 在没有任意容器崩溃情况下的最小就绪时间， 只有超出这个时间 Pod 才被视为可用。默认值为 0（Pod 在准备就绪后立即将被视为可用）。

Last Modified 2023-06-02

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

### 配置示例

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

.spec.minReadySeconds 是一个可选字段，用于指定新创建的 Pod 在没有任意容器崩溃情况下的最小就绪时间，只有超出这个时间 Pod 才被视为可用。默认值为 0（Pod 在准备就绪后立即将被视为可用）。

## 中断预算

有时，Kubernetes 需要停止你的 Pod（这个过程叫作驱逐），即使 Pod 处于活动状态，且已准备就绪。例如，也许运行 Pod 的节点在升级之前已处于排空的状态，需要将 Pod 移动到另一个节点上。
然而，只要有足够的副本运行，这种行为就不会导致应用程序停机。你可以使用 Pod 中断预算（PodDisruptionBudget）资源指定应用程序在任何给定时间内可以承受损失多少 Pod。
例如，你可以指定一次中断的数量不能超过应用程序 Pod 的 10％。或者指定 Kubernetes 可以驱逐任意数量的 Pod，但需要保证至少有三个副本在运行。

一个 PodDisruptionBudget 有 3 个字段：

- 标签选择算符 .spec.selector 用于指定其所作用的 Pod 集合，该字段为必需字段。
- .spec.minAvailable 表示驱逐后仍须保证可用的 Pod 数量。即使因此影响到 Pod 驱逐 （即该条件在和 Pod 驱逐发生冲突时优先保证）。minAvailable 值可以是绝对值，也可以是百分比。
- .spec.maxUnavailable （Kubernetes 1.7 及更高的版本中可用）表示驱逐后允许不可用的 Pod 的最大数量。其值可以是绝对值或是百分比。

用户在同一个 PodDisruptionBudget 中只能够指定 maxUnavailable 和 minAvailable 中的一个。maxUnavailable 只能够用于控制存在相应控制器的 Pod 的驱逐（即不受控制器控制的 Pod 不在 maxUnavailable 控制范围内）。

- 示例 1：设置 minAvailable 值为 5 的情况下，驱逐时需保证 PodDisruptionBudget 的 selector 选中的 Pod 中 5 个或 5 个以上处于健康状态。
- 示例 2：设置 minAvailable 值为 30% 的情况下，驱逐时需保证 Pod 所需副本的至少 30% 处于健康状态。
- 示例 3：设置 maxUnavailable 值为 5 的情况下，驱逐时需保证所需副本中最多 5 个处于不可用状态。
- 示例 4：设置 maxUnavailable 值为 30% 的情况下，只要不健康的副本数量不超过所需副本总数的 30% （取整到最接近的整数），就允许驱逐。如果所需副本的总数仅为一个，则仍允许该单个副本中断，从而导致不可用性实际达到 100%。

> 干扰预算并不能真正保证指定数量/百分比的 Pod 一直处于运行状态。例如：当 Pod 集合的规模处于预算指定的最小值时，承载集合中某个 Pod 的节点发生了故障，这样就导致集合中可用 Pod 的数量低于预算指定值。预算只能够针对调度器自发执行驱逐提供保护，而不能针对所有 Pod 不可用的诱因。

## 命名空间

在 Kubernetes 中，**名字空间（Namespace）**提供一种机制，将同一集群中的资源划分为相互隔离的组。同一名字空间内的资源名称要唯一，但跨名字空间时没有这个要求。名字空间作用域仅针对带有名字空间的对象，（例如 Deployment、Service 等），这种作用域对集群范围的对象 （例如 StorageClass、Node、PersistentVolume 等）不适用。

Kubernetes 名字空间为集群中的 Pod、Service 和 Deployment 提供了作用域。与一个名字空间交互的用户不会看到另一个名字空间中的内容。

Kubernetes 启动时会创建四个初始名字空间：

    default
    Kubernetes 包含这个名字空间，以便于你无需创建新的名字空间即可开始使用新集群。

    kube-node-lease
    该名字空间包含用于与各个节点关联的 Lease（租约）对象。节点租约允许 kubelet 发送心跳，由此控制面能够检测到节点故障。

    kube-public
    所有的客户端（包括未经身份验证的客户端）都可以读取该名字空间。该名字空间主要预留为集群使用，以便某些资源需要在整个集群中可见可读。该名字空间的公共属性只是一种约定而非要求。

    kube-system
    该名字空间用于 Kubernetes 系统创建的对象。

> 避免使用前缀 kube- 创建名字空间，因为它是为 Kubernetes 系统名字空间保留的。

### 查看命名空间

```bash
kubectl get namespace
```

### 使用命名空间

```
kubectl create namespace <名字空间名称>
kubectl run nginx --image=nginx --namespace=<名字空间名称>
kubectl get pods --namespace=<名字空间名称>
kubectl --namespace=<名字空间名称> delete pod nginx
```

> 注意：命名空间删除后，会关联删除命名空间下的**所有内容**！

配置文件的方式

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: <insert-namespace-name-here>
```

### 命名空间和 DNS

当你创建服务时，Kubernetes 会创建相应的 DNS 条目。此条目的格式为 <服务名称>.<名字空间名称>.svc.cluster.local。这意味着如果容器使用 <服务名称>，它将解析为名字空间本地的服务。这对于在多个名字空间（如开发、暂存和生产）中使用相同的配置非常有用。如果要跨名字空间访问，则需要使用完全限定的域名（FQDN）。

> 更多信息参考 https://kubernetes.io/zh-cn/docs/concepts/services-networking/

## 资源配额

当多个用户或团队共享具有固定节点数目的集群时，人们会担心有人使用超过其基于公平原则所分配到的资源量。

资源配额是帮助管理员解决这一问题的工具。

资源配额，通过 ResourceQuota 对象来定义，对每个命名空间的资源消耗总量提供限制。它可以限制命名空间中某种类型的对象的总数目上限，也可以限制命名空间中的 Pod 可以使用的计算资源的总上限。

```yaml
apiVersion: v1
  kind: ResourceQuota
  metadata:
    name: pods-low
  spec:
    hard:
      cpu: "5"
      memory: 10Gi
      pods: "10"
```

针对命名空间应用上面的配置

```bash
kubectl apply --namespace demo -f resource-quota.yaml
```

> 更多信息参考 https://kubernetes.io/zh-cn/docs/concepts/policy/resource-quotas/

### 限制范围

默认情况下，Kubernetes 集群上的容器运行使用的计算资源没有限制。使用 Kubernetes 资源配额，管理员（也称为 集群操作者）可以在一个指定的命名空间内限制集群资源的使用与创建。在命名空间中，一个 Pod 最多能够使用命名空间的资源配额所定义的 CPU 和内存用量。作为集群操作者或命名空间级的管理员，你可能也会担心如何确保一个 Pod 不会垄断命名空间内所有可用的资源。

LimitRange 是限制命名空间内可为每个适用的对象类别 （例如 Pod 或 PersistentVolumeClaim） 指定的资源分配量（限制和请求）的策略对象。

一个 LimitRange（限制范围） 对象提供的限制能够做到：

- 在一个命名空间中实施对每个 Pod 或 Container 最小和最大的资源使用量的限制。
- 在一个命名空间中实施对每个 PersistentVolumeClaim 能申请的最小和最大的存储空间大小的限制。
- 在一个命名空间中实施对一种资源的申请值和限制值的比值的控制。
- 设置一个命名空间中对计算资源的默认申请/限制值，并且自动的在运行时注入到多个 Container 中。

当某命名空间中有一个 LimitRange 对象时，将在该命名空间中实施 LimitRange 限制。LimitRange 的名称必须是合法的 DNS 子域名。

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-resource-constraint
spec:
  limits:
    - default: # 此处定义默认限制值
        cpu: 500m
      defaultRequest: # 此处定义默认请求值
        cpu: 500m
      max: # max 和 min 定义限制范围
        cpu: "1"
      min:
        cpu: 100m
      type: Container
```

LimitRange 不 检查所应用的默认值的一致性。这意味着 LimitRange 设置的 limit 的默认值可能小于客户端提交给 API 服务器的规约中为容器指定的 request 值。如果发生这种情况，最终 Pod 将无法调度。

当容器不满足运行要求时，会出现以下类似的错误

```
Pod "example-conflict-with-limitrange-cpu" is invalid: spec.containers[0].resources.requests: Invalid value: "700m": must be less than or equal to cpu limit
```

从理论上讲，你只需在 LimitRange 中设置默认值，而不必为每个容器指定请求或约束。但这不是一个好习惯。要想知道容器的请求和约束，应该只需要查看容器的规范即可，而不必知道是否有 LimitRange 在起作用。我们应该将 LimitRange 作为最后一道防线，防止容器的所有者忘记指定请求和约束而引发问题。

Last Modified 2023-06-03

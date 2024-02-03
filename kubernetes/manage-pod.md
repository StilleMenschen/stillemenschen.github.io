# 管理 Pod

## 标签

标签是附加到 Kubernetes 对象上的键值对，标签的主要作用是指定对用户有意义且相关的对象的标识属性，但对核心系统没有直接性的语义含义。

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: demo
```

## 选择器

选择器是一个表达式，能够匹配一个标签（或一组标签）。选择器是一种根据标签指定一组资源的方式。例如，服务资源拥有一个选择器，标识它将请求发送到哪些 Pod

```yaml
apiVersion: v1
kind: Service
spec:
  selector:
    app: demo
```

这是一个非常简单的选择器，能够匹配任何带有 app 标签且值为 demo 的资源。如果资源根本没有 app 标签，则不会与该选择器匹配。如果带有 app 标签，但值不是 demo，也不匹配。只有标签为 app:demo 的资源才会匹配，而且所有这类的资源都会被该服务选中。

标签不仅用于连接服务和 Pod，还可以通过 --selector 标志，在 kubectl get 查询集群时指定标签：

```bash
kubectl get pods --all-namespaces --selector app=demo
```

> --selector 可以缩写为 -l

如果想查看 Pod 上定义了哪些标签，可以在 kubectl get 中使用 --show-labels 标志：

```bash
kubectl get pods --show-labels
```

## 高级选择器

大多数时候，你只需要一个简单的选择器，比如 app:demo（又叫作相等选择器）。你可以结合使用不同的标签来建立更具体的选择器：

```bash
kubectl get pods -l app=demo,environment=production
```

上述命令将返回同时拥有 app:demo 和 environment:production 标签的 Pod。与此等效的 YAML（例如在某个服务中）如下：

```yaml
apiVersion: v1
kind: Service
spec:
  selector:
    app: demo
    environment: production
```

服务资源只能使用这类相等选择器，但是在 kubectl 的交互式查询中，或对于更复杂的资源（例如部署），还有其他选择。

一种选择是不相等的标签：

```bash
kubectl get pods -l app!=demo
```

该查询将返回所有带有 app 标签且值不等于 demo 的 Pod，或根本没有 app 标签的 Pod。

你还可以通过一组值来过滤标签值：

```bash
kubectl get pods -l environment in (staging, production)
```

等效的 YAML 为：

```yaml
selector:
  matchExpressions
    - { key: environment, operator: In, values: [staging, production] }
```

你还可以要求标签值不在指定的集合中：

```bash
kubectl get pods -l environment notin (production)
```

等效的 YAML 为：

```yaml
selector:
  matchExpressions:
    - { key: environment, operator: NotIn, values: [production] }
```

可以根据实际的情况为其增加多个标签，方便从多个维度查询和管理集群资源

## 硬亲和性

亲和性的定义方式是描述希望 Pod 在任何节点上运行，可以就 Kubernetes 如何为 Pod 选择节点添加一些规则。每个规则都使用 nodeSelectorTerms 字段表示

```yaml
apiVersion: v1
kind: Pod
# ...
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: "failure-domain.beta.kubernetes.io/zone"
                operator: In
                values: ["us-central1-a"]
```

上述，只有位于 us-central1-a 区域的节点才匹配该规则，因此总体效果是确保将 Pod 调度到该区域中。

## 软亲和性

软亲和性的定义方式几乎与上述相同，不同之处在于，每个规则都要分配 1~100 的数字权重，表明它对结果的影响度。下面是一个例子：

```yaml
apiVersion: v1
kind: Pod
# ...
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
        - weight: 10
            preference:
            matchExpressions:
                - key: "failure-domain.beta.kubernetes.io/zone"
                  operator: In
                  values: ["us-central1-a"]
        - weight: 100
            preference:
            matchExpressions:
                - key: "failure-domain.beta.kubernetes.io/zone"
                  operator: In
                  values: ["us-central1-b"]
```

preferred 一词表明这是软亲和性： Kubernetes 可以将 Pod 调度到任何节点上，但是它会优先考虑与这些规则匹配的节点。

你可以看到这两个规则拥有不同的 weight 值。第一个规则的权重为 10，但第二个规则的权重为 100。如果存在多个同时满足这两个规则的节点，则 Kubernetes 给予与第二个规则相匹配的节点（即位于 us-central1-b 区域的节点）的优先度是第一个规则的 10 倍。权重是表达偏好相对重要性的有效方法。

## 将 Pod 调度到一起

假设有一个应用程序，它的标签为 app:server，它运行还需要一个缓存服务，缓存服务的标签为 app:cache，可以通过下面的配置实现将应用调度到具有缓存服务的节点上

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: server
  labels:
    app: server
# ...
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        labelSelector:
          - matchExpressions:
              - key: app
                operator: In
                values: ["cache"]
        topologyKey: kubernetes.io/hostname
```

这个亲和性的整体效果是，如果有可能的话，将 server Pod 调度到一个正在运行带有 cache 标签 Pod 的节点上。如果没有这样的节点，或者匹配的节点没有足够的空闲资源来运行 Pod，则该 Pod 将无法运行。

但实际上我们并不会这样做。如果两个 Pod 必须在一起，则请将两者的容器放入同一个 Pod 中。如果你只是希望它们位于同一个位置上，则请使用 Pod 软亲和性 preferredDuringSchedulingIgnoredDuringExecution

## 分开 Pod

下面，我们来谈谈反亲和性的示例：将某些 Pod 分开。我们可以将 podAffinity 换成 podAntiAffinity:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: server
  labels:
    app: server
# ...
spec:
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        labelSelector:
          - matchExpressions:
              - key: app
                operator: In
                values: ["server"]
        topologyKey: kubernetes.io/hostname
```

这个例子与上一个非常相似，只不过这里指定的是 podAntiAffinity，因此表达的意思也是相反的，而且 match 表达式也不同。该表达式的意思是：app 标签的值必须是 server。

这个亲和性的整体效果是，确保 Pod 不会被调度到任何与该规则匹配的节点上。换句话说，如果某个节点上已有标记了 app:server 的 Pod 正在运行，则被标记了 app:server 的 Pod 皆不可调度到该节点上。该亲和性可以强制将 server Pod 均匀地分布到整个集群中，而代价是可能无法达到所需的副本数。

## 软反亲和性

然而，通常我们更加关心的是否拥有足够数量的副本，而不是尽可能均匀地分布。因此，硬性规定并不是我们真正想要的。下面，我们将上述亲和性修改成软反亲和性：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: server
  labels:
    app: server
# ...
spec:
  affinity:
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
        - weight: 1
          podAntiAffinityTerm:
            labelSelector:
              - matchExpressions:
                  - key: app
                    operator: In
                    values: ["server"]
            topologyKey: kubernetes.io/hostname
```

请注意，现在这个规则是 preferred，而不是 required，因此这是一个软反亲和性。最好能够满足规则。如果不能满足，Kubernetes 也会调度 Pod。

因为它是一种偏好，所以我们指定了 weight 值，就像软节点亲和性一样。如果使用了多个亲和性规则，Kubernetes 会根据你为每个规则分配的权重对它们进行优先级排序。

就像节点亲和性一样，你应该将 Pod 亲和性作为处理特殊情况的微调强化功能。调度器能够妥当地安置 Pod，并确保集群的最佳性能和可用性。Pod 亲和性限制了调度器的自由度，以牺牲一个应用程序为代价成全了另一个应用程序。只有当你已经发现了生产环境中的某个问题，而且 Pod 亲和性是唯一的修复办法时，才应当予以考虑。

## 污点与容忍

亲和性可以让节点亲近（或远离），污点允许节点根据节点的某些属性排斥一组 Pod。还可以使用污点创建专用节点，仅为特定种类的 Pod 保留节点

我们可以使用 kubectl taint 命令，将污点添加到特定的节点上：

```bash
kubectl taint nodes docker-for-desktop dedicated=true:NoSchedule
```

该命令将在 docker-for-desktop 节点上添加一个名为 dedicated=true 的污点，其效果为 NoSchedule，意思是除非 Pod 拥有匹配的容忍，否则就不能调度到该节点上。

如果想查看特定节点上设置的污点，请使用 `kubectl describe node ...` 如果想删除节点上的污点，也请使用 kubectl taint 命令，但污点名称后面必须加一个减号 `-`：

```bash
kubectl taint nodes docker-for-desktop dedicated:NoSchedule-
```

容忍是 Pod 的属性，描述了它们能够忍受的污点。如果 Pod 容忍污点 dedicated=true，则可以将其添加到 Pod 的规格中：

```yaml
apiVersion: v1
kind: Pod
# ...
spec:
  tolerations:
    - key: "dedicated"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
```

这相当于说：“允许该 Pod 在拥有 dedicated=true 污点且效果为 NoSchedule 节点上运行。”由于该容忍与污点匹配，所以该 Pod 可以调度到这个节点上。

凡是没有这类容忍的 Pod 都不能在这个受污染的节点上运行。如果某个 Pod 由于受污染的节点而导致完全无法运行，则它将保持 Pending 状态，而且你将在 Pod 描述中看到以下消息：

```
Warning FailedScheduling 4s (x10 over 2m) default-scheduler 0/1 nodes are available: 1 node(s) had taints that the pod didn't tolerate.
```

除此之外，污点和容忍还可以用于标记带有专用硬件（比如 GPU）的节点，以及允许某些 Pod 容忍某些类型的节点问题等。

比如某个节点掉线，Kubernetes 会自动添加污点 node.kubernetes.io/unreachable。通常，这会导致 kubelet 驱逐节点上的所有 Pod。但是，网络有可能在合理的期限内恢复正常，因此某些 Pod 应该仍然保持运行状态。为此，你可以在这些 Pod 中添加一个与 unreachable 污点相匹配的容忍。

## 守护进程集

假设你想将所有应用程序的日志发送到中心化的日志服务器，例如 Elasticsearch-Logstash-Kibana（ELK）栈，或 SaaS 监视产品（比如 Datadog），那么实现方法有好几种。

一种方式是在每个应用程序中加入一段代码，连接到日志记录服务、进行身份验证、写入日志等，但这会导致很多重复的代码，效率很低。

还有一种方法，你可以在每个 Pod 中运行一个额外的容器，充当日志记录代理（这种方式又称为 Sidecar 模式）。这意味着每个应用程序不必知道如何与日志记录服务通信，但是意味着每个节点可能都需要运行日志记录代理的多个副本。

由于日志记录代理所做的工作只是管理与日志记录服务的连接，并传递日志消息，因此实际上每个节点只需要一个日志记录代理的副本。这是一个很常见的要求，为此 Kubernetes 提供了一个特殊的控制器对象：守护进程集 （DaemonSet）。

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
#...
spec:
  template:
    spec:
      containers:
        - name: fluentd-elasticsearch
```

当需要在集群中的每个节点上运行 Pod 的一个副本时，请使用守护进程集。如果对于正在运行的应用程序来说，维护一定数量的副本比控制 Pod 在哪个节点上更为重要，则请使用部署。

## 状态集

与部署或守护进程集类似，状态集（StatefulSet）也是一种 Pod 控制器。状态集增加的功能是可以按特定的顺序启动和停止 Pod。
例如，部署可以按照随机的顺序启动和停止所有的 Pod。对于无状态服务，这种方式没问题，因为在无状态服务中，每个副本都是相同的，并且执行相同的工作。

但有时候，你需要按照特定的编号顺序启动 Pod，而且还需要通过编号识别它们。例如，Redis、 MongoDB 或 Cassandra 之类的分布式应用程序会创建自己的集群，并且需要能够通过可预测的名称来标识集群领导者。

在这种情况下，状态集是理想之选。例如，如果创建一个名为 redis 的状态集，则第一个启动的 Pod 将被命名为 redis-0，而且 Kubernetes 会等到该 Pod 准备好之后再启动下一个 Pod，即 redis-1。

有些应用程序可以使用这个属性以可靠的方式将 Pod 组合成集群。例如，每个 Pod 运行一个启动脚本，检查自己是否在 redis-0 上运行。如果是，那么它就是集群领导者；如果不是，则需要通过联系 redis-0 加入集群。

Kubernetes 会等到状态集中的每个副本都已运行且准备就绪，再启动下一个副本。类似地，当状态集终止时，副本将以相反的顺序关闭，等到每个 Pod 关闭后再继续关闭下一个副本。
除了这些特殊的属性之外，状态集看起来与普通的部署非常相似：

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: redis
spec:
  selector:
    matchLabels:
      app: redis
  serviceName: "redis"
  replicas: 3
  template:
  #...
```

为了通过可预测的 DNS 名称（例如 Iredis-1）访问各个 Pod，你需要创建一个服务，并将 clusterIP 类型设置为 None（称为无头服务，Headless Service）。

非无头服务会获得一个 DNS 条目（例如 redis），它可以在所有后端 Pod 上实现负载均衡。无头服务也会获得一个服务的 DNS 名称，但是每个 Pod 还会单独获得一个带有编号的 DNS 条目，例如 redis-0、redis-1、redis-2 等。

需要加入 Redis 集群的 Pod 可以专门联系 redis-0，但是只需要负载均衡的 Redis 服务的应用程序则可以通过 DNS 名称 Redis 与随机选择的 Redis Pod 对话。

另外，状态集还可以利用卷声明模板对象（它会自动创建持久卷声明）来管理 Pod 的磁盘存储。

## 作业

Kubernetes 还有另外一个非常实用的 Pod 控制器：作业（Job）。部署会运行指定数量的 Pod，并不断重启它们，而作业只需运行一定的次数。之后，作业将会被视为已完成。

例如，批处理任务或队列的工作 Pod 通常会启动、完成工作、然后再退出。因此非常适合由作业管理。

控制作业执行的字段有两个：completions 和 parallelism。前者 completions 决定作业在被视为完成之前，需要成功地运行多少次指定 Pod。默认值为 1，表示 Pod 只需运行一次。
parallelism 字段指定一次运行多少个 Pod。同样，默认值为 1，表示一次只能运行一个 Pod。

例如，假设你需要运行队列的工作作业，目的是消耗队列中的工作项。你可以将 parallelism 设置为 10，不要设置 completions，那么就会启动 10 个 Pod，每个 Pod 都会消耗队列中的工作项，直到队列中的工作项消耗完，然后退出，此时该作业就完成了：

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: queue-worker
spec:
  completions: 10
  template:
    metadata:
      name: queue-worker
    spec:
      containers:
    # ...
```

或者，如果你想运行类似于批处理作业的操作，则可以让 completions 和 parallelism 都保持默认值 1，这样就会启动 Pod 的一个副本，并等待其成功完成。如果 Pod 崩溃、失败或以任何非成功的方式退出，则作业会重启 Pod，就像部署一样。只有成功退出才会计入 completions 指定的次数。

你可以手动启动作业，方法是使用 kubectl 或 Helm 来应用作业清单。另外，作业也可以自动触发，例如通过持续部署流水线。

但是，最常见的运行作业的方法是，在指定时间点或按照指定的时间间隔定期启动作业。为此，Kubernetes 提供了一种特殊的作业类型：定时作业 （Cronjob）.

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: demo-cron
spec:
  schedule: "＊/1＊＊＊＊"
  jobTemplate:
    spec:
    # ...
```

定时作业的清单中有两个重要的字段：spec.schedule 和 spec.jobTemplate。

schedule 字段指定何时运行作业，与 Unix cron 程序的格式相同。jobTemplate 指定要运行的作业模板，与普通作业的清单完全相同。

## Pod 水平自动伸缩

部署控制器可以维护指定数量的 Pod 副本。如果一个副本失败，则启动另一个副本替换；如果出于某种原因 Pod 副本数过多，则部署会停止多余的 Pod，以保证目标数量的副本。

所需副本数在部署清单中设置，如果请求量很大，则可以通过调整来增加 Pod 的数量；如果出现空闲的 Pod，则可以通过减少 Pod 数量来缩小部署的规模。

Kubernetes 能够根据需求自动调整副本数，Pod 水平伸缩器。（水平伸缩指的是调整服务的副本数量，而垂直伸缩则指的是调整单个副本的大小。）

Pod 水平伸缩器（Horizontal Pod Autoscaler，HPA）会监视指定的部署，并通过持续监控给定的指标来判断是否需要增加或减少副本的数量。

最常见的自动伸缩指标之一是 CPU 利用率。Pod 可以请求一定数量的 CPU 资源，例如 500 毫核。在 Pod 运行期间，它使用的 CPU 会发生波动，这意味着无论在任何时刻 Pod 实际使用的 CPU 只是原本请求的一部分。

你可以根据这个值自动扩展部署。例如，你可以创建一个 HPA，目标是 Pod 的 CPU 使用率为 80%。如果部署中所有 Pod 的 CPU 平均使用率仅为请求的 70%，则 HPA 会通过减少目标副本数来缩小规模。保证 Pod 计算资源可以得到充分利用，就不需要那么多的副本数量。

另一方面，如果平均 CPU 利用率为 90%，超出了 80% 的目标，则我们需要添加更多副本，直到 CPU 平均使用率下降为止。HPA 将修改部署来增加目标副本数。

每当 HPA 确定需要进行伸缩操作时，它都会根据实际指标值与目标值的比率，调整副本的数量。如果部署非常接近目标 CPU 利用率，则 HPA 只会添加或删除少量副本；但是如果差距过大，则 HPA 会进行大幅的调整。

```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: demo-hpa
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: demo
  minReplicas: 1
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 80
```

需要注意的字段包括：

- `spec.scaleTargetRef` 指定要扩展的部署。
- `spec.minReplicas` 和 `spec.maxReplicas` 指定伸缩的限制。
- `spec.metrics` 伸的判断指标。

尽管 CPU 使用率是最常见的伸缩指标，但你可以使用任何 Kubernetes 指标，包括系统内置的指标（比如 CPU 和内存使用率）以及应用程序特有的服务指标（你可以在应用程序中定义和导出这些指标）。

比如可以根据应用程序错误率进行伸缩。更多有关自动伸缩器以及自定义指标的信息，请参阅 Kubernetes 文档，地址：`https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/`。

Last Modified 2024-02-03

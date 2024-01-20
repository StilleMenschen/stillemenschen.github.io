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

Last Modified 2024-01-20

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

Last Modified 2024-01-20

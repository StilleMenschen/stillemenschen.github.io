# 操作器和自定义资源

标准的 Kubernetes 对象（例如部署和服务等）适合简单无状态的应用程序，但它们有局限性。有些应用程序需要多个互相协作的 Pod，而且这些 Pod 必须按照特定的顺序初始化（例如拥有多个副本的数据库或集群形式的服务）。

如果应用程序需要比状态集更复杂的管理，则可以自行创建新类型的对象，即自定义资源定义（Custom Resource Definition，即 CRD）。例如，备份工具 Velero 创建了自定义的 Configs 和 Backups 等 Kubernetes 对象。

Kubernetes 在设计时就考虑了可扩展性，你可以使用 CRD 机制自由定义和创建任何类型的对象。有些 CRD 只是为了存储数据，比如 Velero 的 BackupStorageLocation 对象。但是，你还可以更进一步，创建 Pod 控制器对象，就像部署或状态集一样。

例如，如果需要创建一个控制器对象，在 Kubernetes 中设置拥有多个副本的、高可用性 MySQL 数据库集群，该怎么做呢？第一步，创建自定义控制器对象的 CRD。为了让这个对象执行操作，你需要编写一个程序，与 Kubernetes API 通信。这类的程序称为操作器（因为它可以像人类操作员那样执行各种操作）。

编写操作器不需要任何自定义对象。开发运维工程师 Michael Treacher 编写 了一个很好的操作器示例（地址：https:/medium.com/@mtreacher/writing-a-kubernetes-operator-a9b86f19bfb9），这个操作器可以监视命名空间的创建，并自动将 RoleBinding 添加到新的命名空间。

## Ingress 资源

Ingress 接收来自客户端的请求，并将其发送到服务。然后，服务根据标签选择器将它们发送到正确的 Pod。

下面是一个非常简单的 Ingress 资源示例:

```yaml
apiVersion: apps/v1
kind: Ingress
metadata:
  name: demo-ingress
spec:
  backend:
    serviceName: demo-service
    servicePort: 80
```

这个 Ingress 会将流量转发到名为 demo-service 的 80 端口上（实际上，请求直接从 Ingress 转到合适的 Pod，但从概念上可以认为请求经过了服务）。

## Ingress 规则

服务主要负责路由集群中的内部流量（例如，从一个微服务路由到另一个），而 Ingress 则负责将外部的流量路由到集群和适当的微服务上。Ingress 可以根据指定的某些规则将流量转发到不同的服务。常见的一种是根据请求 URL 将请求路由到不同的地方，又称作分列（fanout）：

```yaml
apiVersion: apps/v1
kind: Ingress
metadata:
  name: fanout-ingress
spec:
  rules:
    - http:
      paths:
        - path: /news
          backend:
            serviceName: news
            servicePort: 80
        - path: /about
          backend:
            serviceName: about
            servicePort: 80
```

这种 Ingress 有很多用途。高可用的负载均衡器可能十分昂贵，因此你可以通过一个负载均衡器以及与之关联的 Ingress，将流量路由到大量的其他服务上。你不仅可以根据 URL 路由请求，而且还可以使用 HTTP 的 Host 头部（相当于基于名称的虚拟主机）。带有不同域名的请求（例如 example.com）会根据域名被路由到合适的后端服务。

## Ingress TLS

Ingress 还可以使用 TLS（以前叫作 SSL 协议）处理安全连接。如果同一域上有很多不同的服务和应用程序，则它们可以共享一个 TLS 证书，而且可以通过一个 Ingress 资源管理这些连接

```yaml
apiVersion: apps/v1
kind: Ingress
metadata:
  name: demo-ingress
spec:
  tls:
    secretName: demo-tls-secret
      backend:
        serviceName: demo-service
        servicePort: 80
```

在这个示例中，添加了一个新的 t1s，用于指示 Ingress 使用 TLS 证书来保护与客户端的流量。证书本身存储为 Kubernetes Secret 资源。

如果已有 TLS 证书，或者打算从证书颁发机构购买 TLS 证书，则可以在 Ingress 中使用该证书。首先创建一个 Secret:

```yaml
apiVersion: v1
kind: Secret
type: kubernetes.io/tls
metadata:
  name: demo-tls-secret
data:
  tls.crt: ASHUHuIHAuIAHiuH...LSotasg==
  tls.key: A35UHuI3AuIAHiuH...LSotasg==
```

证书的内容放在 tls.crt 字段中，而密钥放在 tls.key 中。与 KubernetesSecret 一样，在将证书和密钥数据添加到清单之前，应该对其进行 base64 编码。

如果你想使用流行的 LetsEncrypt 授权（或其他 ACME 证书提供商）自动请求和更新 TLS 证书，则可以使用 cert-manager。
在集群中运行 cert-manager，它会自动检测没有证书的 TLS Ingress，并发送请求到指定的提供商（比如 LetsEncrypt）。与流行的 kube-lego 相比，cert-manager 是一款更现代、更强大的工具。

TLS 连接的具体处理方式则取决于 Ingress 控制器。

## Ingress 控制器

Ingress 控制器负责管理集群中的 Ingress 资源。选用的控制器因集群的运行位置而异。

如果想自定义 Ingress 的行为的话，可以添加 Ingress 控制器能够识别的特定注释。

在 Google GKE 上运行的集群可以选择使用 Google 面向 Ingress 的计算负载均衡器（Compute Load Balancer for Ingress）。AWS 有一个类似的产品名叫应用程序负载均衡器（Application Load Balancer）。这些托管服务提供了一个公共 IP 地址，Ingress 可以通过这个地址监听请求。

如果你需要使用 Ingress 在 Google 云或 AWS 上运行 Kubernetes，那么这些都是不错的选择。每款产品的文档请参照各自的代码库：

- Google Ingress https://github.com/kubernetes/ingress-gce
- AWS Ingress https://github.com/kubernetes-sigs/aws-load-balancer-controller

你还可以选择在集群中安装和运行自己的 Ingress 控制器，甚至可以根据需要运行多个控制器。流行的控制器包括：

- nginx-Ingress

  早在 Kubernetes 出现之前，NGINX 一直是流行的负载均衡器工具。该控制器给 Kubernetes 带来了很多 NGINX 的功能。基于 NGINX 的 Ingress 控制器还有几个，但这个是官方版本。

- Contour

  Contour 的底层实际上使用另一个名为 Envoy 的工具来代理客户端和 Pod 之间的请求。

- Traefik

  这是一款轻量级的代理工具，可以自动管理 Ingress 的 TLS 证书。

这些控制器各有特色，并提供自己的设置和安装说明，以及处理路由和证书等的方式。了解不同的工具，并在自己的集群中通过应用程序进行尝试，才能掌握它们的工作方式。

## Istio

Istio 是一种服务网格，适用于多个应用程序和服务之间的相互通信。它可以处理服务之间的路由，并加密网络流量，此外还有其他重要的功能，例如指标、日志和负载均衡等。

Istio 是许多托管 Kubernetes 集群（包括 Google Kubernetes Engine）的可选附件（关于如何启用 Istio，请参见提供商的文档）。

如果你想在自托管集群中安装 Istio，请使用官方的 Istio Helm Chart。如果你的应用程序非常依赖彼此之间的通信，可以考虑 Istio。参考文档：https://istio.io/latest/zh/docs/concepts/traffic-management/

## Envoy

大多数托管的 Kubernetes 服务（例如 Google Kubernetes Engine）都提供了某种云负载均衡器的集成。例如，当你在 GKE 或 Ingress 上创建类型为 LoadBalancer 的服务时，系统会自动创建 Google 云负载均衡器，并将其连接到服务。

这些标准的云负载均衡器具有良好的扩展性，但是它们非常简单，并且配置不多。例如，默认的负载均衡算法通常是 random。该算法会将每个连接随机发送到不同的后端。

然而，有的时候 random 并不能满足你的需求。例如，如果发到服务的请求可能会长时间运行且占用大量 CPU，那么有些后端节点可能会过载，而有些则处于空闲状态。

更智能的算法会将请求路由到最不繁忙的后端。有时这种算法称作 leastconn 或 LEAST_REQUEST。对于这类更为复杂的负载均衡，你可以使用一种名叫 Envoy 的产品。这款产品本身不属于 Kubernetes，但常常与 Kubernetes 应用程序一起使用。

Envoy 是用 C++ 编写、为单个服务和应用程序而设计的高性能分布式代理，但也可以用作服务网格体系结构的一部分。开发人员 Mark Vincze 写了一篇很棒的博客文章，详细介绍了如何在 Kubernetes 中设置和配置 Envoy，地址：https://blog.markvincze.com/how-to-use-envoy-as-a-load-balancer-in-kubernetes/

Last Modified 2024-02-03

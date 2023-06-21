# Kubernetes 安装程序

## kops

kops 是自动配置 Kubernetes 集群的命令行工具。它是 Kubernetes 项目的一部分，而且作为 AWS 的专用工具也有很长一段时间了，如今又增加了对 Google 云的 beta 支持，而且还计划支持其他提供商。

kops 支持构建高可用集群，因此很适合生产 Kubernetes 的部署。它使用声明式的配置，就像 Kubernetes 资源本身一样，而且它不仅能够分配必要的云资源并建立集群，还可以扩大或缩小集群、调整节点大小、完成升级以及其他管理任务。

与 Kubernetes 世界中的所有事物一样， kops 也在快速发展中，但它是一个相对成熟且复杂的工具，使用很广泛。如果你打算在 AWS 中运行自托管的 Kubernetes，那么 kops 是一个不错的选择。

## Kubespray

Kubespray （原名 Kargo）是 Kubernetes 旗下的一个项目，该工具可以很容易地部署可供生产环境使用的集群。它提供了许多功能，包括高可用性以及对多平台的支持等。

Kubespray 侧重于在现有机器上安装 Kubernetes，尤其是在企业内部及裸金属服务器上。但是，它也适用于所有云环境，包括私有云（在自己的服务器上运行的虚拟机）。

## TK8

TK8 是一种用于配置 Kubernetes 集群的命令行工具，可同时利用 Terraform （创建云服务器）和 Kubespray （在其上安装 Kubernetes） 。TK8 是用 Go 语言编写的（很显然） ，它支持在 AWS， OpenStack 和裸金属服务器上的安装，并支持 Azure 和 Google 云的流水线。

TK8 不仅可以构建 Kubernetes 集群，还可以安装可选的附加组件，包括 Jmeter Cluster （负载测试） 、Prometheus （监控） 、Jaeger、 Linkerd 或 Zipkin （跟踪） 、Ambassador API Gateway （通过 Envoy 提供 ingress 及负载均衡）、Istio （服务网格支持）、Jenkins-X （CI/CD）以及 Helm 或 Kedge （Kubernetes 打包）。

> **困难模式的 Kubernetes**
>
> 如果想了解在云服务上部署 Kubernetes，可以看看 Kelsey Hightower 的教程[《Kubernetes Hard Way》](https://github.com/kelseyhightower/kubernetes-the-hard-way)，这是一篇有倾向性的教程，它介绍了构建 Kubernetes 集群的过程，展示了各个环节的复杂性。尽管如此，这篇教程很有启发性，那些考虑运行 Kubernetes（即使作为托管服务）的人都可以尝试一下这种练习，以便了解幕后的工作方式。（这个教程使用的是 Google 云服务来部署 Kubernetes 的）

## kubeadm

kubeadm 是 Kubernetes 发行版的一部分，旨在根据最佳实践帮助你安装和维护 Kubernetes 集群。kubeadm 并不会为集群本身提供基础设施，因此很适合在裸金属服务器或任何类型的云实例上安装 Kubernetes。

## Tarmak

Tarmak 是 Kubernetes 集群生命周期管理工具，注重修改和升级集群节点的简化和可靠性。许多工具只是通过替换节点来解决这个问题，但这样做花费的时间很长，而且往往需要在重建过程中在节点之间移动大量数据。而 Tarmak 可以修复或升级该节点。

Tarmak 的后台使用了 Terraform 来配置集群节点，并使用 Puppet 来管理节点本身的配置。因此可以更快、更安全地推出节点配置的变更。

## Rancher Kubernetes Engine （RKE）

RKE 的目标是成为一个简单快速的 Kubernetes 安装程序。它不会为你提供节点，而且你必须先在节点上安装 Docker，然后才能使用 RKE 安装集群。RKE 支持 Kubernetes 控制平面的高可用性。

## Puppet Kubernetes 模块

Puppet 是一款强大、成熟且复杂的常规配置管理工具，已得到广泛使用，而且还有大型开源模块生态系统。官方支持的 Kubernetes 模块可在现有节点上安装和配置 Kubernetes，包括对控制平面和 eted 的高可用性支持。

## Kubeformation

Kubeformation 是一款在线 Kubernetes 配置器，你可以使用 Web 界面为集群选择选项，然后为你选择的特定云提供商的自动化 API （例如 Google 云的 Deployment Manager，或 Azure 的 Azure Resource Manager）生成相应的配置模板。

Kubeformation 的使用可能不像其他工具那么简单，但由于它是现有自动化工具（如 Deployment Manager 等）的包装，因此非常灵活。如果你使用 Deployment Manager 来管理 Google 云基础设施，那么 Kubeformation 会非常适合现有的工作流程。

## 尽可能地使用托管服务

如果不能选择托管服务，可以考虑使用一站式服务；如果没有合理的业务原因，不要自托管集群服务。如果决定了要自托管，请不要低估初始设置和持续维护所需要付出的资金和时间。

Last Modified 2023-05-29

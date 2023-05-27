# 基本概念

应用部署后会持续运行，除非因为程序错误、系统资源不足等原因而停止运行了，这个时候就需要重新部署或重新启动应用。在传统的服务器中，你可以使用 systemd、runit 或 supervisord 等工具来执行此操作。Docker 有类似的功能，而且可想而知，Kubernetes 也有管理程序的功能，那就是部署。

大多数 Kubernetes 应用程序都以长期、可靠地运行为目标，因此上述行为是合理的:容器可以因为种种原因退出，并且在大多数情况下，运维人员能做的也仅仅是重启它们，因此 Kubernetes 默认也会这样做。

查询部署和获取详细信息

```bash
kubectl get deployments
kubectl describe deployments/demo
```

## Pod

Pod 是 Kubernetes 对象，代表一组（一个或多个）容器（Pod 也有一群鲸鱼的意思，非常符合 Kubernetes 隐含的依稀航海风）。

## ReplicaSet

我们说过，部署会启动 Pod，但它要做的还不止这些。实际上，部署不会直接管理 Pod，那是副本集（ReplicaSet）对象的工作。
副本集负责一组相同的 Pod （即副本， replica）。如果 Pod 数量少于（或多于）规范指定的数量，则副本集控制器会启动（或停止）一些 Pod，以纠正这种情况。

## 维持所需状态

Kubernetes 控制器会不断根据集群的实际状态检查每个资源指定的所需状态，并进行必要的调整以保证二者的同步。这个过程叫作协调循环，因此这个协调过程会无休止地循环下去，设法让实际状态与所需状态保持一致。

Last Modified 2023-05-27

# 运行容器

## 什么是容器

容器是一个标准化的软件包，其中包含软件本身以及各个依赖项、配置、数据等运行软件所需的一切。

在 Linux 和大多数其他操作系统中，计算机上运行的一切都是通过进程来完成的。进程表示正在运行的应用程序（例如 Chrome、iTunes 或 Visual Studio Code）的二进制代码和内存状态。所有进程都在同一个全局命名空间中，它们可以相互查看和交互，它们共享同一个资源池，例如 CPU、内存和文件系统等（Linux 的命名空间有点像 Kubernetes 的命名空间，尽管从技术上讲二者不是同一个东西）。

从操作系统的角度来看，容器代表一个（或一组）位于各自命名空间中的隔离进程，容器内部的进程看不到外部的进程，反之亦然。容器不能访问属于其他容器或容器外部进程的资源。容器的边界就像栅栏，可以阻止进程运行异常并耗尽彼此的资源。

对于容器内部的进程而言，它就像在自己的计算机上运行，可以访问所有的资源，而且没有其他正在运行的进程。想验证一下的话，可以试试看在容器运行一些命令：

```
kubectl run alpine --image alpine:3.18 --rm -it --restart=Never /bin/ash
/ # ps ax
PID   USER     TIME  COMMAND
   1 root      0:00   ash
  30 root      0:00   ps ax
/ # hostname
alpine
```

通常， ps ax 命令会列出计算机上运行的所有进程，而且一般会有很多（一般的 Linux 服务器有几百个）。然而，这里仅显示了两个进程: /bin/ash 和 ps ax。因此，容器内部唯一可见的进程就是实际正在容器中运行的进程。hostname 命令通常会返回主机名称，但在这里却显示 alpine，其实这是容器的名称。

容器中其实可以运行一个完整的 Linux 发行版，其中包含多个正在运行的应用程序、网络服务等，这就是为什么容器也会被称为轻量级虚拟机的原因，但这并不是使用容器最佳的方法，因为这样就无法享受资源隔离的优势了。

如果进程不需要彼此了解，那么就不必在同一容器中运行。关于容器，有一个很好的经验法则，即一个容器只做一件事。

容器有一个入口点，即在容器启动时运行的命令。通常运行该命令只需要创建一个进程，尽管某些应用程序通常会启动一些子进程来充当辅助进程或工作进程。如果想在容器中启动多个单独的进程，你需要编写一个包装脚本作为入口，并由脚本来启动你想要的进程。

## Pod 组织容器

以上我们介绍了容器是什么，现在你明白通过 Pod 将容器组织到一起非常有利。Pod 代表一组需要相互通信和共享数据的容器；它们需要一起调度，它们需要一起启动和停止，而且它们还需要在同一台物理计算机上运行。

举一个例子，在本地缓存中保存数据的应用程序，比如 Memcached。你需要运行两个进程：应用程序进程，以及处理存储和检索数据的 memcached 服务器进程。尽管你可以在一个容器中运行这两个进程，但没有必要，因为它们仅需要通过网络 socket 进行通信。最好将它们分到两个单独的容器，每个容器只需要关心构建和运行自己的进程即可。

事实上，你可以使用 Docker Hub 提供的公共 Memcached 容器镜像，它可以直接作为 Pod 的一部分与其他容器一起运行。

因此，你创建的 Pod 拥有两个容器: Memcached，以及你的应用程序。该应用程序可以通过网络连接与 Memcached 通信，并且由于两个容器位于同一个 Pod 中，因此二者之间的连接始终在本地发生，因为这两个容器将始终在同一个节点上运行。

## 容器清单

在一个部署中，`template.spec`用来指定容器

这是一个单容器的部署

```yaml
spec:
  containers:
    - name: nginx
      image: nginx:1.14.2
      ports:
        - containerPort: 80
```

这是一个多容器的部署

```yaml
spec:
  containers:
    - name: container1
      image: example/container1
    - name: container2
      image: example/container2
```

每个容器的规范中必须指定的字段只有 name 和 image，容器必须有名称，以方便其他资源引用，而且你必须告诉 Kubernetes 在容器中运行哪个镜像。

## 镜像标识符

每个镜像标识符都有四个不同的部分：镜像仓库的主机名、镜像仓库的命名空间、镜像仓库以及标签。除了镜像名称以外，其他都是可选项。镜像标识符会用到所有的字段，如下所示:

docker.io/cloudnatived/demo:hello

- 在这个示例中，镜像仓库的主机名是 docker.io；实际上，这是 Docker 镜像的默认值，因此我们无需指定。但是，如果你的镜像存储在其他仓库，则需要提供主机名。例如， Google Container Registry 镜像的前缀是 gcr.io。

- 镜像仓库的命名空间为 cloudnatived：如果不指定镜像仓库的命名空间，则会使用默认的命名空间（即 library）。下面是一组官方的镜像（地址：https:/docs.docker.com/docker-hub/official_images/），由 Docker Inc 批准与维护。流行的官方镜像包括各个 OS 基础镜像 （alpine、 ubuntu、 debian centos），语言环境（golang.python. ruby， php， java），以及广泛使用的软件（mongo. mysal. nginx.redis）。
- 镜像仓库是 demo，它指定了仓库和命名空间内某个特定的容器镜像。
- 标签是 hello。标签可以标识同一个镜像的不同版本。

容器中放人哪些标签由你决定，常见的标签包括：

- 语义版本标记，比如 v1.3.0。通常指应用程序的版本。
- Git SHA 标签，例如 5ba6bfd 标识构建容器时使用的源代码库中某个特定的提交。
- 它所代表的环境，例如 staging 或 production。

如果在拉取镜像时未指定标签，则默认标签为 latest。举个例子，如果在运行 alpine 镜像时未指定标签，则默认值为 alpine:latest。如果在构建或推送镜像时未指定标签，则 latest 标签就会作为默认标签添加到镜像上。latest 标签指向的镜像未必就是最新的镜像，只不过是最新的没有明确标记的镜像。因此， latest 并不适合用作标识符。

在 Dockerfile 中引用基础镜像时，如果不指定标签，也会默认使用 latest，和部署容器一样。由于 latest 的语义难以捉摸，因此建议指定某个特定的基础镜像标识，如 alpine:3.18

## 容器摘要

如上所述，latest 标签并不一定代表最新的版本，甚至连语义版本或 Git SHA 标签也不能唯一且永久地标识特定的容器镜像。如果维护人员用相同的标签推送不同的镜像，则下次部署时，你将获得更新后的镜像。用技术术语来说，标签是不确定的。

有时，我们需要确定性的部署：换句话说，保证部署引用的就是你指定的容器镜像。你可以通过容器的摘要（Digest）来保证这一点。摘要是一个镜像内容的加密哈希值，可以永久不变地标识该镜像。
镜像可以拥有多个标签，但只能有一个摘要。这意味着，如果容器清单指定的是镜像摘要，则可以保证部署的确定性。以下示例是一个带有摘要的镜像

cloudnatived/demo＠sha256:aeae1e551a6cbd60bcfd56c3b4ffec732c45b8012b7cb758c6c4a34...

## 镜像拉取策略

如你所知，容器在节点上运行之前，必须从相应的容器仓库中拉取或下载镜像容器上的 imagePullPolicy 字段可以控制 Kubernetes 多久执行一次该操作。它可以从以下三个值中选择一个：Always、IfNotPresent 或 Never

- Always：每次启动容器时都会拉取镜像。假设你指定了一个标签，那么就没必要指定 Always 了，因为这只会浪费时间和带宽。
- IfNotPresent：默认值，适用于大多数情况。如果节点上没有镜像，则下载镜像。在这之后，除非你更改镜像规格，否则每次容器启动时都会使用已下载的镜像，而不会尝试重新下载镜像。
- Never：永远不会更新镜像。在这个策略下，Kubernetes 永远也不会从仓库获取镜像，如果节点上已有镜像则使用；如果没有，则容器启动失败。不推荐使用。

## 环境变量

环境变量是一种在运行时将信息传递到容器的方法，很常见但作用有限。之所以常见，是因为所有的 Linux 可执行文件都可以访问环境变量，甚至在容器出现很久之前编写的程序都可以使用环境变量来配置环境。之所以有限是因为环境变量只能是字符串值，一般不能使用数组、键值或结构化的数据。进程环境的总大小上限为 32KiB，因此你不能在环境中传递大型数据文件。

你可以按照如下方式在容器的 env 字段中设置环境变量

```yaml
containers:
  - name: demo
    image: cloudnatived/demo:hello
    env:
      - name: GREETING
        value: "Hello from the environment"
```

如果容器镜像本身指定了环境变量（比如在 Dockerfile 中） ，则会被 Kubernetes env 的设置覆盖。在更改容器的默认设置时可以采用这种方法。

## 容器安全

在前面的介绍中，当我们使用 ps ax 命令查看容器中的进程列表时，所有进程都是以 root 用户身份运行的。在 Linux 以及其他 UNIX 派生的操作系统中，root 是超级用户，拥有读取任何数据、修改任何文件以及在系统上执行任何操作的特权。

在完整的 Linux 系统上，有些进程需要以 root 的身份运行（例如负责管理所有其他进程的 init） ，但通常容器不需要。

不建议在非必要的时候以 root 用户身份运行进程。因为这违反了最小权限原则（Principle of least privilege） 。该原则要求程序只能访问完成工作必需的信息和资源。

凡是程序都有错误，因为程序都是人写的，而人都会犯错。有些错误会让恶意用户有机可乘，劫持程序读取机密数据或执行任意代码。为了缓解这种情况使用最小权限来运行容器很重要。

首先，不要以 root 身份运行容器，应该为它们分配一个普通的用户，即一个没有特殊特权（例如读取其他用户的文件）的用户。

攻击者还有可能利用容器运行时中的错误“逃离”容器，并在主机上获得与容器中相同的权限。

## 以非 root 运行容器

下面是一个容器规范的示例，该规范告诉 Kubernetes 以特定用户身份运行客器：

```yaml
containers:
  - name: demo
    image: cloudnatived/demo:hello
    securityContext:
    runAsUser: 1000
```

runAsUser 的值是 UID （用户数字标识符）。在许多 Linux 系统上，UID 1000 会被分配给系统上创建的第一个非 root 用户，因此通常容器中的 UID 造择 1000 或更高的值比较安全。容器中是否存在具有该 UID 的 UNIX 用户或者甚至容器中是否存在操作系统都没有关系，即便是空白的容器也可以过样指定。

Docker 还允许在 Dockerfile 中指定一个用户来运行容器的进程，但是你不需要这样做。在 Kubernetes 规范中设置 runAsUser 字段更加容易和灵活。

如果指定了 runAsUser UID，则它将覆盖容器镜像中配置的用户。如果没有 runAsUser，但容器指定了一个用户，则 Kubernetes 将以该用户身份来运行容器，如果清单和镜像中均未指定任何用户，则该容器将以 root 身份运行（不推荐这种做法）。

为了获得最高安全性，应该为每个容器选择一个不同的 UID。如此一来，即某个容器遭到破坏，或意外覆盖数据，它也只能访问自己的数据，而无权访问其他容器。

相反，如果希望两个或多个容器能够访问相同的数据（例如通过挂载卷），则应为它们分配相同的 UID。

## 阻止 root 容器

Kubernetes 可以禁止以 root 用户身份运行容器。

只需设置 runAsNonRoot: true 即可：

```yaml
containers:
  - name: demo
    image: cloudnatived/demo:hello
    securityContext:
      runAsNonRoot: true
```

Kubernetes 在运行该容器时会检查该容器是否以 root 用户身份运行。如果以 root 用户身份运行，则拒绝启动。这种方式可以避免忘记在容器中设置非 root 用户，或运行以 root 用户身份运行的第三方容器。

如果发生这种情况，Pod 状态会显示 CreateContainerConfigError，而通过 kubectl describe 查看该 Pod，则会看到如下错误：

`Error: container has runAsNonRoot and image will run as root`

## 配置只读的文件系统

还有一个重要的安全上下文设置是 readOnlyRootFilesystem，这个设置可以防止容器写入自己的文件系统。你可以设想，如果容器利用了 Docker 或 Kubernetes 的一个错误，那么写入文件系统可能会影响宿主节点上的文件。如果容器的文件系统是只读的，则不会发生这种情况，容器会收到一个 IO 错误

```yaml
containers:
  - name: demo
    image: cloudnatived/demo:hello
    securityContext:
      readOnlyRootFilesystem: true
```

许多容器不需要向自己的文件系统写入任何内容，因此这个设置不会干扰它们。除非容器确实需要写入文件，否则最好设置 readOnlyRootFilesystem.

## 禁用权限提升

通常 Linux 可执行文件在执行时获得的权限就是执行它们的用户的权限。但有一个例外，拥有 `setuid` 机制的可执行文件可以临时获得该可执行文的拥有者（通常是 root）权限。
这对于容器来说是一个潜在问题，因为即使容器以常规用户身份运行（例如，UID 1000），如果它包含 `setuid` 的可执行文件，则默认情况下该执行文件仍然可以获得 root 权限。

为了避免这种情况，要将容器的安全策略字段 allowPrivilegeEscalation 设置为 false

```yaml
containers:
  - name: demo
    image: cloudnatived/demo:hello
    securityContext:
      allowPrivilegeEscalation: false
```

现代 Linux 程序不需要 `setuid`，它们可以使用更灵活、更小粒度的特权机制来达到这个目的，即能力（capability）。

## 能力

一般 UNIX 程序拥有两个级别的权限：普通用户和超级用户。普通程序的权限不会超过执行它们的用户权限，而超级用户程序可以做任何事，可以绕过所有的内核安全检查。

Linux 的能力（capability）机制改进了权限控制，它定义了多种特定操作，比如加载内核模块、执行直接的网络 I/O 操作、访问系统设备等。凡是有需要的程序都可以获得这些特定的权限，但无法获得其他权限。

例如，监听端口 80 的 Web 服务器通常需要以 root 身份运行才能执行此操作。1024 以下的端口号都被视为特权系统端口。但是，我们可以将 NET_BIND_SERVICE 能力赋予程序，这样就可以将其绑定到任何端口，同时不会赋予其他特殊权限。

Docker 容器默认提供了一套非常通用的能力。这是为了权衡实用性与安全性而做出的决定，如果默认不为容器提供如何能力，运维人员需要为大量的容器设置能力，它们才能运行。

另一方面，最小权限原则表明，容器不应拥有不必要的能力。Kubernetes 的安全上下文允许删除默认设置中的能力，而且还可以根据需要添加能力

```yaml
containers:
  - name: demo
    image: cloudnatived/demo:hello
    securityContext:
      capabilities:
        drop: ["CHOWN", "NET_RAW", "SETPCAP"]
        add: ["NET_ADMIN"]
```

这个容器删除了 `CHOWN`、 `NET RAW` 和 `SETPCAP` 能力，并添加了 `NET_ADMIN` 能力。Docker 文档列出了默认情况下容器上设置的所有能力，以及可以根据需要添加的能力。

> 地址：https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities

如果需要最大安全性，则应该删除每个容器的所有能力，并仅添加所需的特定能力：

```yaml
containers:
  - name: demo
    image: cloudnatived/demo: hello
    securityContext:
      capabilities:
      drop: ["all"]
      add: ["NET_BIND_SERVICE"]
```

能力机制对容器内部的进程进行了硬性限制，即使它们以 root 身份运行也是如此。一旦容器删除了某一项能力，就无法重新再获得，即使是拥有最大特权的恶意进程也没办法。

Last Modified 2024-01-09

# Config Element

## CSV Data Set Config

CSV 数据读取，默认按行顺序读取（所有线程共享），以指定的分隔符切分赋值给相同数量的变量，如果启用了双引号包裹数据，可以在
双引号中使用分隔符，如果要使用双引号，可以定义两个双引号表示

```
1,"2,3","4""5" =>
1
2,3
4"5
```

## FTP Request Defaults

FTP 请求默认值

## DNS Cache Manager

当用户从不同 IP 接收内容时，DNS 缓存管理器元素允许测试应用程序，该应用程序在负载均衡器（CDN 等）后面有多个服务器。默认情
况下，JMeter 使用 JVM DNS 缓存。这就是为什么群集中只有一台服务器接收负载的原因。DNS 缓存管理器在每次迭代时分别解析每个线
程的名称，并将解析结果保存到其内部 DNS 缓存中，该内部 DNS 缓存与 JVM 和 OS 的 DNS 缓存无关

## HTTP Authorization Manager

授权管理器使您可以为使用服务器身份验证限制的网页指定一个或多个用户登录名。当您使用浏览器访问受限页面时，您会看到这种身份
验证，并且浏览器将显示一个登录对话框。当遇到这种类型的页面时，JMeter 会传输登录信息

授权标头可能不会显示在树视图侦听器的“请求”选项卡中。Java 实现执行抢占式身份验证，但是当 JMeter 获取标头时，它不返回
Authorization 标头。自 3.2 起，HttpComponents（HC 4.5.X）实现默认为抢占式，并且将显示标头。要禁用此功能，请按如下所示设
置值，在这种情况下，仅在响应质询时才执行身份验证

在文件 jmeter.properties 中，设置 httpclient4.auth.preemptive = false

## HTTP Cache Manager

HTTP 缓存管理器用于在其范围内向 HTTP 请求添加缓存功能，以模拟浏览器缓存功能。每个虚拟用户线程都有自己的缓存。默认情况下
，缓存管理器将使用 LRU 算法在每个虚拟用户线程中在缓存中最多存储 5000 个项目。使用属性“ maxSize”来修改该值。请注意，此值
增加得越多，HTTP 缓存管理器将消耗更多的内存，因此请确保相应地调整-Xmx JVM 选项

## HTTP Cookie Manager

Cookie Manager 元素具有两个功能：首先，它像网络浏览器一样存储和发送 cookie。如果您有 HTTP 请求，并且响应包含 cookie，则
cookie 管理器会自动存储该 cookie，并将其用于将来对该特定网站的所有请求。每个 JMeter 线程都有其自己的“cookie 存储区”。因
此，如果您正在测试使用 cookie 来存储会话信息的网站，则每个 JMeter 线程都会拥有自己的会话。请注意，此类 cookie 不会出现在
Cookie Manager 的显示中，但是可以使用“查看结果树侦听器”看到它们

## HTTP Request Defaults

该元素使您可以设置 HTTP 请求控制器使用的默认值。例如，如果您要创建一个具有 25 个 HTTP 请求控制器的测试计划，并且所有请求
都被发送到同一服务器，则可以添加单个 HTTP 请求默认值元素，并在其中填写“服务器名称或 IP”字段。添加 25 个 HTTP 请求控制器
时，请将“服务器名称或 IP”字段保留为空。控制器将从 HTTP Request Defaults 元素继承此字段值

## HTTP Header Manager

标头管理器使您可以添加或覆盖 HTTP 请求标头

JMeter 现在支持多个 Header 管理器。Header 条目被合并以形成采样器的列表。如果要合并的条目与现有的标头名称匹配，它将替换前
一个条目。这样一来，便可以设置一组默认的 Header，并将调整应用于特定的采样器。请注意，标头的空值不会删除现有的标头，而只
是替换其值

## Java Request Defaults

Java Request Defaults 组件使您可以设置 Java 测试的默认值

## JDBC Connection Configuration

根据提供的 JDBC 连接设置创建数据库连接（由 JDBC Request Sampler 使用）。可以选择在线程之间合并连接。否则，每个线程将获得
自己的连接。JDBC Sampler 使用连接配置名称来选择适当的连接。使用的池是 DBCP，请参见 BasicDataSource 配置参数

## Keystore Configuration

密钥库配置元素使您可以配置如何加载密钥库以及将使用哪些密钥。该组件通常用于不希望在响应时间中考虑密钥库初始化的 HTTPS 方
案中

要使用此元素，您需要首先使用您要测试的客户端证书来设置 Java 密钥库：

使用 Java keytool 实用程序或通过 PKI 创建证书如果是由 PKI 创建的，则通过将其转换为 JKS 可接受的格式，将密钥导入 Java 密
钥存储区中。然后通过两个 JVM 属性引用密钥库文件（或将它们添加到 system.properties 中）：

```
-Djavax.net.ssl.keyStore = path_to_keystore
-Djavax.net.ssl.keyStorePassword = password_of_keystore
```

要将 PKCS11 用作商店的源，需要将 `javax.net.ssl.keyStoreType` 设置为 PKCS11，并将 `javax.net.ssl.keyStore` 设置为 NONE

## Login Config Element

登录配置元素使您可以在使用用户名和密码作为设置一部分的采样器中添加或覆盖用户名和密码设置

## LDAP Request Defaults

LDAP Request Defaults 组件使您可以设置 LDAP 测试的默认值。请参阅 LDAP Request

## LDAP Extended Request Defaults

LDAP 扩展请求默认值组件使您可以设置扩展 LDAP 测试的默认值。请参阅 LDAP Extended Request

## TCP Sampler Config

TCP 采样器配置为 TCP Sampler 提供默认数据

## User Defined Variables

用户定义的变量元素使您可以定义一组初始变量

因此，您不能引用定义为测试运行的一部分的变量，例如 在后处理器中

有关在测试运行期间定义变量的信息，请参见用户参数。UDV 按照在计划中出现的顺序从上到下进行处理

为简单起见，建议将 UDV 仅放置在线程组的开始处（或可能位于测试计划本身的下面）

测试计划和所有 UDV 处理完毕后，将结果集变量复制到每个线程以提供初始变量集

如果运行时元素（例如用户参数预处理器或正则表达式提取器）定义的变量与 UDV 变量之一具有相同的名称，则它将替换初始值，并且
线程中的所有其他测试元素将看到更新的值

## Random Variable

随机变量配置元素用于生成随机数字字符串，并将其存储在变量中，以备后用。比将用户定义的变量与`__Random()`函数一起使用要简单
，使用`USER_000`会将数字以`java.text.DecimalFormat`来格式化输出，如随机数为`13`则会返回`USER_013`

## Counter

允许用户创建一个可以在线程组中任何地方引用的计数器。计数器配置使用户可以配置起点，最大值和增量。计数器将从开始到最大循环
，然后从头开始，继续这样直到测试结束

## Simple Config Element

通过“简单配置元素”，您可以在采样器中添加或覆盖任意值。您可以选择值的名称和值本身。尽管一些冒险的用户可能会发现此元素的用
法，但在这里主要是供开发人员使用的基本 GUI，供他们在开发新的 JMeter 组件时使用

## Bolt Connection Configuration

从提供的连接设置中创建一个 Bolt 连接池（由 Bolt Request Sampler 使用）

Last Modified 2021-05-10

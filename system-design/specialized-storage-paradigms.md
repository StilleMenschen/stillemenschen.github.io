# 专用存储范式

## Blob 存储

在小型和大型系统中广泛使用的存储类型。它们本身并不真正算作数据库，部分原因是它们只允许用户根据 blob 的名称存储和检索数据。这有点像键值存储，但通常 blob 存储有不同的保证。
它们可能比键值存储慢，但值可能是兆字节（MB）大小（有时甚至是千兆字节（GB））。通常人们使用它来存储大型二进制文件、数据库快照或图像以及网站可能拥有的其他静态资源。

内部部署 Blob 存储相当复杂，只有像谷歌和亚马逊这样的大公司才有支持它的基础设施。因此，通常在系统设计面试的背景下，您可以假设您将能够使用 GCS 或 S3。
这些是分别由 Google 和 Amazon 托管的 blob 存储服务，费用取决于您使用的存储量以及从该存储中存储和检索 blob 的频率。

## 时间序列数据库

TSDB（Time Series Database）是一种特殊类型的数据库，针对存储和分析时间索引数据进行了优化：特定时间出现在特定时刻的数据点。TSDB 的示例有 InfluxDB、Prometheus 和 Graphite。

## 图数据库

一种按照图（Graph）数据模型存储数据的数据库。图数据库中的数据条目可以具有明确定义的关系，就像图中的节点可以有边一样。
图数据库利用其底层图结构非常快速地对深度连接的数据执行复杂查询。因此，在处理数据点自然形成图并具有多层次关系的系统（例如社交网络）时，使用图数据库通常优于关系数据库。

## Cypher

一种图查询语言，最初是为 Neo4j 图数据库开发的，但此后已被标准化以与其他图数据库一起使用，以使其成为“图的 SQL”。Cypher 查询通常比 SQL 查询简单得多。在流行的图形数据库 Neo4j 中查找数据的 Cypher 查询示例：

```cypher
MATCH (some_node:SomeLabel)-[:SOME_RELATIONSHIP]->(some_other_node:SomeLabel {some_property:'value'})
```

```cypher
// Populate data.
CREATE (facebook: Company {name: 'Facebook'})

CREATE (clement: Candidate {name: 'Clement'})
CREATE (antoine: Candidate {name: 'Antoine'})
CREATE (simon: Candidate {name: 'Simon'})
CREATE (alex: Interviewer {name: 'Alex'})
CREATE (meghan: Interviewer {name: 'Meghan'})
CREATE (marli: Interviewer {name: 'Marli'})
CREATE (sandeep: Interviewer {name: 'Sandeep'})
CREATE (molly:Interviewer {name: 'Molly' })
CREATE (akshay: Interviewer {name: 'Akshay'})
CREATE (aditya: Interviewer {name: 'Aditya'})
CREATE (brandon: Interviewer {name: 'Brandon'})
CREATE (pedro: Interviewer {name: 'Pedro'})
CREATE (ryan: Interviewer {name: 'Ryan'})
CREATE (xi: Interviewer {name: 'Xi'})
CREATE (simran: Interviewer {name: 'Simran'})
CREATE (amanda: Interviewer {name: 'Amanda'})

CREATE (alex)-[:INTERVIEWED {score: 'passed'}]->(clement)
CREATE (meghan)-[:INTERVIEWED {score: 'passed'}]->(clement)
CREATE (simran)-[:INTERVIEWED {score: 'passed'}]->(clement)
CREATE (molly)-[:INTERVIEWED {score: 'failed'}]->(clement)
CREATE (marli)-[:INTERVIEWED {score: 'failed'}]->(antoine)
CREATE (akshay)-[:INTERVIEWED {score: 'passed'}]->(antoine)
CREATE (aditya)-[:INTERVIEWED {score: 'passed'}]-> (antoine)
CREATE (meghan)-[:INTERVIEWED {score: 'passed'}]->(antoine)
CREATE (marli)-[:INTERVIEWED {score: 'failed'}]->(simon)
CREATE (meghan)-[:INTERVIEWED {score: 'failed'}]->(simon)
CREATE (brandon)-[:INTERVIEWED {score: 'passed'}]->(simon)
CREATE (xi)-[:INTERVIEWED {score: 'failed'}]->(simon)

CREATE (ryan)-[:APPLIED {status: 'rejected'}]->(facebook)
CREATE (simran)-[:APPLIED {status: 'accepted'}]->(facebook)
CREATE (xi)-[:APPLIED {status: 'rejected'}]->(facebook)
CREATE (molly)-[:APPLIED {status: 'rejected'}]-> (facebook)
CREATE (alex)-[:APPLIED {status: 'rejected'}]-> (facebook);


// Find the interviewers who interviewed and failed CLement
// and who also applied to and got rejected by Facebook.
MATCH (interviewer: Interviewer)-[:INTERVIEWED {score: 'failed'}]->(:Candidate {name:'Clement'})
WHERE (interviewer)-[:APPLIED {status: 'rejected '}]->(:Company {name: 'Facebook'})
RETURN interviewer.name;
```

## 空间数据库

一种为存储和查询空间（spatial）数据（如地图上的位置）而优化的数据库。空间数据库依靠四叉树等空间索引来快速执行空间查询，例如查找区域附近的所有位置。

## 四叉树

最常用于索引二维空间数据的树数据结构。四叉树中的每个节点要么有零个子节点（因此是叶节点），要么有四个子节点。

通常，四叉树节点包含某种形式的空间数据——例如，地图上的位置——具有某个指定数量 n 的最大容量。只要节点没有满负荷，它们就一直是叶节点；
一旦数据达到容量上限数，它们就会被赋予四个子节点，并且它们的数据条目被拆分到四个子节点中。

四叉树非常适合存储空间数据，因为它可以表示为填充有矩形的网格，这些矩形递归地细分为四个子矩形，其中每个四叉树节点由一个矩形表示，每个矩形代表一个空间区域。
假设我们要存储世界上的位置，我们可以想象一个具有最大节点容量 n 的四叉树，如下所示：

- 代表整个世界的根节点是最外层的矩形。

- 如果整个世界有 n 个以上的位置，则最外面的矩形被分成四个象限，每个象限代表世界的一个区域。

- 只要一个区域有 n 个以上的位置，它对应的矩形就被细分为四个象限（四叉树中的对应节点被赋予四个子节点）。

- 具有少于 n 个位置的区域是未分割的矩形（叶节点）。

- 网格中具有许多细分矩形的部分代表人口稠密的区域（如城市），而网格中具有较少细分矩形的部分代表人口稀少的区域（如农村地区）。

在完全四叉树中查找给定位置是一个非常快的操作，运行时间为 log4(x)（其中 x 是位置的总数），因为四叉树节点有四个子节点。

## 谷歌云存储

GCS 是 Google 提供的 Blob 存储服务。了解更多：https://cloud.google.com/storage

## S3

S3 是 Amazon 通过 Amazon Web Services (AWS) 提供的 blob 存储服务。了解更多：https://aws.amazon.com/s3/

## InfluxDB

一个流行的开源时间序列数据库。了解更多：https://www.influxdata.com/

## Prometheus

一个流行的开源时间序列数据库，通常用于监控目的。了解更多：https://prometheus.io/

## Neo4j

一个流行的图形数据库，由节点、关系、属性和标签组成。了解更多：https://neo4j.com/

Last Modified 2022-01-27

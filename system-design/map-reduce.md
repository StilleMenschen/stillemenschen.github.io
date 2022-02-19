# MapReduce

## 文件系统

一种存储介质的抽象，定义如何管理数据。虽然存在许多不同类型的文件系统，但大多数都遵循由目录和文件组成的层次结构，比如 Unix 文件系统的结构。

## MapReduce

一种流行的框架，用于在分布式环境中以高效、快速和容错的方式处理非常庞大的数据集。MapReduce 作业由 3 个主要步骤组成：

- Map 步骤，它在数据集的各个块上运行 map 函数，并将这些块转换为中间键值对。
- Shuffle 步骤，它重新组织中间键值对，以便在最后一步将相同键对路由到同一台机器。
- Reduce 步骤，对 Shuffle 的键值对运行 reduce 函数，并将它们转换为更有意义的数据。

MapReduce 用例的典型示例是计算大型文本文件中单词的出现次数。

在处理 MapReduce 库时，工程师或系统管理员只需要关注 map 和 reduce 函数，以及它们的输入和输出。所有其他问题，包括任务的并行化和 MapReduce 作业的容错，都被抽象出来并由 MapReduce 实现处理。

## 分布式文件系统

分布式文件系统是对（通常是大型的）机器集群的抽象，允许它们像一个大型文件系统一样工作。DFS 的两个最流行的实现是 Google 文件系统 (GFS) 和 Hadoop 分布式文件系统 (HDFS)。

通常，DFSS 会处理可用性和复制保证，这在分布式系统设置中可能很棘手。总体思路是将文件分成一定大小的块（例如 4MB 或 64MB），并且这些块在大型机器集群中分片。中央控制平台负责决定每个块所在的位置，将读取路由到正确的节点，并处理机器之间的通信。

不同的 DFS 实现的 API 和语义略有不同，但它们实现了相同的目标：超大规模的持久存储。

## Hadoop

一个流行的开源框架，支持 MapReduce 作业和许多其他类型的数据处理管道。它的核心组件是 HDFS (Hadoop Distributed File System)，在此基础上还开发了其他技术。了解更多信息：https://hadoop.apache.org/

## Demo

```bash
mkdir -p host1/map_results/
mkdir -p host2/map_results/
mkdir map_results/
mkdir reduce_results/
```

generator.js

```js
function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1) + min);
}

const TOTAL = 100;

function run() {
  for (let i = 0; i < TOTAL; i++) {
    console.log(getRandomInt(100, 40000));
  }
}

run();
```

```bash
node generator.js > host1/latencies.txt
node generator.js > host2/latencies.txt
```

map.js

```js
const mapReduce = require("./map-reduce");

function map(text) {
  const lines = text.split("\n");
  for (const line of lines) {
    const latency = parseInt(line);
    if (latency < 10000) {
      mapReduce.emitMapResult("under_10_seconds", 1);
    } else {
      mapReduce.emitMapResult("over_10_seconds", 1);
    }
  }
}

const mapInput = mapReduce.getMapInput("latencies.txt");
map(mapInput);
```

shuffle.js

```js
const fs = require("fs");

const HOSTS = process.env.HOSTS.split(",");

for (const host of HOSTS) {
  const fileNames = fs.readdirSync(`${host}/map_results`, "utf-8");

  for (const fileName of fileNames) {
    const key = fileName.split(".")[0];
    const contents = fs.readFileSync(`${host}/map_results/${fileName}`, "utf-8");
    fs.appendFileSync(`map_results/${key}.txt`, contents);
  }
}
```

reduce.js

```js
const mapReduce = require("./map-reduce");

function reduce(key, values) {
  const valuesCount = values.length;
  mapReduce.emitReduceResult(key, valuesCount);
}

const reduceInputs = mapReduce.getReduceInputs();
for (const input of reduceInputs) {
  reduce(input[0], input[1]);
}
```

map-reduce.js

```js
const fs = require("fs");

const HOST = process.env.HOST;

function getMapInput(fileName) {
  const path = `${HOST}/${fileName}`;
  return fs.readFileSync(path, "utf-8");
}

function emitMapResult(key, value) {
  const fileName = `${HOST}/map_results/${key}.txt`;
  fs.appendFileSync(fileName, value + "\n");
}

function getReduceInputs() {
  const fileNames = fs.readdirSync("map_results", "utf-8");
  const inputs = [];

  for (const fileName of fileNames) {
    const key = fileName.split(".")[0];
    const contents = fs.readFileSync(`map_results/${fileName}`, "utf-8");
    inputs.push([key, contents.split("\n").filter((value) => value !== "")]);
  }

  return inputs;
}

function emitReduceResult(key, value) {
  const fileName = `reduce_results/results.txt`;
  fs.appendFileSync(fileName, key + " " + value + "\n");
}

module.exports.getMapInput = getMapInput;
module.exports.emitMapResult = emitMapResult;
module.exports.getReduceInputs = getReduceInputs;
module.exports.emitReduceResult = emitReduceResult;
```

run.sh

```bash
#!/usr/bin/sh
#!/usr/bin/bash

# Clean up stray files from the previous run.
rm -f host1/map_results/*.txt
rm -f host2/map_results/*.txt
rm -f map_results/*.txt
rm -f reduce_results/results.txt

# Run the map step on both hosts in parallel.
HOST=host1 node map.js &
HOST=host2 node map.js &

# Wait for them to both be done.
wait

# Run the shuffle step.
HOSTS=host1,host2 node shuffle.js

# Run the reduce step。
node reduce.js

# View the final result of the MapReduce job.
cat reduce_results/results.txt
```

Last Modified 2022-02-19

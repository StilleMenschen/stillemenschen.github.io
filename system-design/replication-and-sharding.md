# 复制和分片

## 复制

将数据从一台数据库服务器复制到其他服务器的行为。例如，这有时用于增加系统的冗余并容忍区域故障。
其他情况下，可以利用复制让数据更接近客户端，从而减少访问特定数据的延迟。

## 分片

有时称为数据分区，分片是将数据库分成两个或多个称为分片的部分的行为，通常用于增加数据库的吞吐量。流行的分片策略包括：

- 基于客户区域的分片
- 基于存储数据类型的分片（例如，用户数据存储在一个分片中，支付数据存储在另一个分片中）
- 基于列 hash 的分片（仅针对结构化数据）

## 热点

在一组服务器上分配工作负载时，该工作负载可能分布不均。如果分片键或散列函数不是最佳的，或者如果工作负载自然偏斜，则可能会发生这种情况：
某些服务器将比其他服务器接收更多的流量，从而产生“热点（Hot Spot）”。

## Demo

database-server.js

```js
const express = require("express");
const fs = require("fs");

const PORT = process.env.PORT;
const DATA_DIR = process.env.DATA_DIR;

const app = express();
app.use(express.json());

app.post("/:key", (req, res) => {
  const { key } = req.params;
  console.log(`Storing data at key ${key}`);
  const destinationFile = `${DATA_DIR}/${key}`;
  fs.writeFileSync(destinationFile, req.body.data);
  res.send();
});

app.get("/:key", (req, res) => {
  const { key } = req.params;
  console.log(`Retrieving data from key ${key}`);
  const destinationFile = `${DATA_DIR}/${key}`;
  try {
    const data = fs.readFileSync(destinationFile);
    res.send(data);
  } catch (e) {
    res.send("null");
  }
});

app.listen(PORT, () => {
  console.log(`Database listening on port ${PORT}!`);
});
```

database-proxy.js

```js
const axios = require("axios");
const express = require("express");

const SHARD_ADDRESSES = ["http://localhost:3001", "http://localhost:3002"];
const SHARD_COUNT = SHARD_ADDRESSES.length;

const app = express();
app.use(express.json());

function getShardEndpoint(key) {
  // 这里仅仅是使用了最简单的取模方式
  // 在实际生产环境中应用时, 应该选择更合适的处理策略
  const shardNumber = key.charCodeAt(0) % SHARD_COUNT;
  const shardAddress = SHARD_ADDRESSES[shardNumber];
  return `${shardAddress}/${key}`;
}

app.post("/:key", (req, res) => {
  const shardEndpoint = getShardEndpoint(req.params.key);
  console.log(`Forwarding to : ${req.method} ${shardEndpoint}`);
  axios.post(shardEndpoint, req.body).then((innerRes) => {
    res.send();
  });
});

app.get("/:key", (req, res) => {
  const shardEndpoint = getShardEndpoint(req.params.key);
  console.log(`Forwarding to : ${req.method} ${shardEndpoint}`);
  axios.get(shardEndpoint, req.body).then((innerRes) => {
    if (innerRes.data === null) {
      res.send("null");
      return;
    }
    res.send(innerRes.data);
  });
});

app.listen(5000, () => {
  console.log("Proxy listening on port 5000!");
});
```

```bash
PORT=3001 DATA_DIR=db_data_0 node database-server.js &

PORT=3002 DATA_DIR=db_data_1 node database-server.js &

node database-proxy.js
```

```bash
curl --header 'Content-Type: application/json' \
     --data '{"data": "This is a some data!"}' \
     http://localhost:5000/a

curl -w "\n" http://localhost:5000/a

curl --header 'Content-Type: application/json' \
     --data '{"data": "This is a some data!"}' \
     http://localhost:5000/b

curl -w "\n" http://localhost:5000/b
```

Last Modified 2022-01-28

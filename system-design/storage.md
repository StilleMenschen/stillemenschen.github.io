# 存储

## 数据库

数据库是使用磁盘或内存来做两个核心事情的程序：记录数据和查询数据。一般来说，他们自己长期存在并通过网络调用与应用程序的其余部分交互的服务器，
传输协议基于 TCP 或者 HTTP。一些数据库只将记录保存在内存中，这些数据库的用户都知道如果机器或进程死掉，这些记录可能会永远丢失。
但在大多数情况下，数据库需要这些记录的持久性，因此不能使用内存。这意味着您必须将数据写入磁盘。
任何写入磁盘的内容都将通过断开电源或使用网络分区保留下来，因此这就是用来保存永久记录的东西。
由于机器经常在大型系统中发生故障，数据库进程使用特殊的磁盘分区或卷，即使机器永久停机，这些卷也可以被恢复。

## 磁盘

通常是指 HDD（硬盘驱动器）或 SSD（固态驱动器）。写入磁盘的数据将在电源故障和一般机器出现停机后仍然存在，所以磁盘也称为非易失性存储。
SSD 比 HDD 的读写速度快得多（请参阅从 SSD 和 HDD 访问数据的延迟），但从经济角度来看也昂贵得多。
因此，HDD 通常用于很少访问或更新但存储很长时间的数据，而 SSD 用于经常访问和更新的数据。

## 内存

随机存取存储器 (**Random Access Memory**, RAM) 的缩写。 当写入该数据的进程终止时，存储在内存中的数据将丢失。

## 持久化存储

通常指的是磁盘，但一般来说，它是任何形式的存储，如果负责管理它的进程终止，它仍然会持续存在。

## Demo

```js
const express = require("express");
const fs = require("fs");

const DATA_DIR = "db_data";
const app = express();

app.use(express.json());

const hashTable = {};

app.post("/memory/:key", (req, res) => {
  hashTable[req.params.key] = req.body.data;
  res.send("OK");
});

app.get("/memory/:key", (req, res) => {
  const key = req.params.key;
  if (key in hashTable) {
    res.send(hashTable[key]);
    return;
  }
  res.send("null");
});

app.post("/disk/:key", (req, res) => {
  const destinationFile = `${DATA_DIR}/${req.params.key}`;
  fs.writeFileSync(destinationFile, req.body.data);
  res.send("OK");
});

app.get("/disk/:key", (req, res) => {
  const destinationFile = `${DATA_DIR}/${req.params.key}`;
  try {
    const data = fs.readFileSync(destinationFile);
    res.send(data);
  } catch (e) {
    console.error(e);
    res.send("null");
  }
});

app.listen(3000, () => {
  console.log("Listening on port 3000!");
});
```

```bash
curl --header 'content-type: application/json' \
     --data '{"data": "This is some data in memory."}' \
     http://localhost:3000/memory/bar

curl http://localhost:3000/memory/bar

curl --header 'content-type: application/json' \
     --data '{"data": "This is some data in disk."}' \
     http://localhost:3000/disk/bar

curl http://localhost:3000/disk/bar
```

Last Modified 2022-01-28

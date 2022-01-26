# 键值对存储

## 键值存储

键值存储是一种灵活的 NoSQL 数据库，通常用于缓存和动态配置。流行的选项包括 DynamoDB、Etcd、Redis 和 ZooKeeper。

## Etcd

Etcd 是一个强一致性和高可用的键值对存储，通常用于在系统中实现领导者选举。了解更多：https://etcd.io/

## Redis

内存中的键值存储。确实提供了一些持久性存储选项，但通常用作真正快速、尽力而为的缓存解决方案。
Redis 也经常用于实现速率限制。了解更多：https://redis.io/

## Zookeeper

ZooKeeper 是一个强一致性、高可用的键值对存储。它通常用于存储重要配置或执行领导者选举。了解更多：https://zookeeper.apache.org

## Demo

```js
const database = {
  ["index.html"]: "<html>Hello World!</html>",
};
module.exports.get = (key, callback) => {
  setTimeout(() => {
    callback(database[key]);
  }, 2000);
};
```

```js
const database = require("./database");
const express = require("express");
// If redis version is v3.x, then you need using redis client v3.2.1
const redisClient = require("redis").createClient();

const app = express();

app.get("/nocache/index.html", (req, res) => {
  database.get("index.html", (page) => {
    res.send(page);
  });
});

app.get("/withcache/index.html", (req, res) => {
  redisClient.get("index.html", (err, redisRes) => {
    if (redisRes) {
      res.send(redisRes);
      return;
    }

    database.get("index.html", (page) => {
      redisClient.set("index.html", page, "EX", 10);
      res.send(page);
    });
  });
});

app.listen(3006, () => {
  console.log("Listening on port 3006!");
});
```

Last Modified 2022-01-26

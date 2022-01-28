# 缓存

## 缓存

一种存储数据的硬件或软件，通常意味着比其他方式更快地检索数据。缓存通常用于存储对网络请求的响应以及计算长操作的结果。
请注意，如果数据的主要真实来源（即缓存背后的主数据库）得到更新而缓存没有更新，则缓存中的数据可能会变得陈旧。

## 缓存命中

在缓存中找到请求的数据时。

## 缓存未命中

当请求的数据可以在缓存中找到但为无效的。这通常用于指代系统故障或糟糕的设计选择的负面后果。例如：
如果服务器出现故障，我们的负载均衡服务将不得不把请求转发到新服务器，这就导致了缓存未命中的情况。

## 缓存清理策略

从缓存中清理或删除值的策略。流行的缓存驱逐策略包括 LRU（最近最少使用，least-recently used）、
FIFO（先进先出，first in first out）和 LFU（最不常用，least-frequently used）。

## 内容交付网络

CDN （Content Delivery Network）是一种第三方服务，其作用类似于服务器的缓存。如果服务器仅位于另一个区域，
则不在此区域的用户的 Web 应用程序访问速度可能会较慢。CDN 在世界各地都有服务器，这意味着 CDN 服务器的延迟几乎总是比服务器的延迟低得多。
CDN 的服务器通常称为 PoPs（Points of Presence）。两个最受欢迎的 CDN 是 Cloudflare 和 Google Cloud CDN。

## Demo

```js
const database = {
  ["index.html"]: "<html>Hello World !</html>",
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

const app = express();
const cache = {};

app.get("/nocache/index.html", (req, res) => {
  database.get("index.html", (page) => {
    res.send(page);
  });
});

app.get("/withcache/index.html", (req, res) => {
  if ("index.html" in cache) {
    res.send(cache["index.html"]);
    return;
  }

  database.get("index.html", (page) => {
    cache["index.html"] = page;
    res.send(page);
  });
});

app.listen(3000, () => {
  console.log("Listening on port 3000!");
});
```

```bash
curl http://localhost:3000/nocache/index.html

curl http://localhost:3000/withcache/index.html
```

Last Modified 2022-01-28

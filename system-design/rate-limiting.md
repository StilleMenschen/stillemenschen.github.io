# 速率限制

## 速率限制

限制发送到系统或从系统发送的请求数量的行为。速率限制最常用于限制传入请求的数量以防止 DoS 攻击，
并且可以在 IP 地址级别、用户帐户级别或区域级别实施。当然，速率限制也可以分层实现，
比如一种类型的网络请求可以限制为每秒 1 个、每 10 秒 5 个和每分钟 10 个。

## Dos 攻击

DoS 攻击是“拒绝服务攻击（denial-of-service attack）”的缩写，是一种恶意用户试图关闭或破坏系统以使其对用户不可用的攻击。
一些 DoS 攻击很容易通过速率限制来预防，而另一些则很难防御。

## DDoS 攻击

分布式拒绝服务攻击（distributed denial-of-service attack）的缩写，DDoS 攻击是一种 DoS 攻击，
其中向目标系统发送请求的流量来自许多不同的来源（如数千台机器），这使得防御变得更加困难。

## Redis

内存中的键值存储。确实提供了一些持久性存储选项，但通常用作真正快速、尽力而为的缓存解决方案。
Redis 也经常用于实现速率限制。了解更多：https://redis.io/

## Demo

```js
const database = {
  ["index.html"]: "<html>Hello World!</html>",
};
module.exports.get = (key, callback) => {
  setTimeout(() => {
    callback(database[key]);
  }, 1000);
};
```

```js
const database = require("./database");
const express = require("express");
const app = express();

app.listen(3000, () => console.log("Listening on port 3000."));

// Keep a hash table of the previous access time for each user.
const accesses = {};
app.get("/index.html", function (req, res) {
  const { user } = req.headers;
  if (user in accesses) {
    const previousAccessTime = accesses[user];

    // Limit to 1 request every 5 seconds.
    if (Date.now() - previousAccessTime < 5000) {
      res.status(429).send("Too many requests.\n");
      return;
    }
  }

  // Serve the page and store this access time.
  database.get("index.html", (page) => {
    accesses[user] = Date.now();
    res.send(page + "\n");
  });
});
```

```bash
for i in {1..3}; do curl --header 'user: Johnny' http://localhost:3000/index.html; done
for i in {1..3}; do curl --header 'user: Grace' http://localhost:3000/index.html; done
```

Last Modified 2022-02-13

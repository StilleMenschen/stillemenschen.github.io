# 负载均衡

## 负载均衡

一种在服务器之间分配流量的反向代理。负载均衡器可以在系统的许多组成部分中找到，从 DNS 层一直到数据库层。

## 服务器选择策略

在多台服务器之间分配流量时，负载均衡器如何选择服务器。常用的策略包括循环、随机选择、基于性能的选择（选择具有最佳性能指标的服务器，例如最快的响应时间或最少的流量）和基于 IP 的路由。

## 危险区

在一组服务器上分配工作负载时，该工作负载可能分布不均。如果您的分片键或散列函数不是最理想的，或者如果您的工作负载自然偏斜，则可能会发生这种情况：某些服务器将比其他服务器接收更多的流量，从而产生“危险区（Hot Spot）”。

## Nginx

发音为“engine X”而不是“N jinx”，Nginx 是一种非常流行的 Web 服务器，通常用作反向代理和负载均衡器。了解更多：https://www.nginx.com/

## Demo

```nginx
events {}

http {
    upstream node-backend {
        server 127.0.0.1:3004 weight=2;
        server 127.0.0.1:3005;
    }

    server {
        listen 8082;

        location / {
           proxy_set_header systemexpert-tutorial ture;
           proxy_pass http://node-backend;
        }
    }
}
```

```js
const express = require("express");

const app = express();

const port = process.env.PORT;

app.get("/hello", (req, res) => {
  console.log("Headers:", req.headers);
  res.send(`Hello from port ${port}.\n`);
});

app.listen(port, () => console.log(`Listening on port ${port}.`));
```

```bash
PORT=3004 node server.js
```

```bash
PORT=3005 node server.js
```

```bash
for i in {1..30}; do curl http://localhost:8082/hello; done
```

Last Modified 2022-01-22

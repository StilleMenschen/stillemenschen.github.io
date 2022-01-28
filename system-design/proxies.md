# 代理

## 正向代理

位于客户端和服务器之间并代表客户端运行的服务器，通常用于屏蔽客户端的身份（IP 地址）。请注意，正向代理也通常被称为代理。

## 反向代理

位于客户端和服务器之间并代表服务器运行的服务器，通常用于日志记录、负载均衡或缓存。

## Nginx

发音为“engine X”而不是“N jinx”，Nginx 是一种非常流行的 Web 服务器，通常用作反向代理和负载均衡器。了解更多：https://www.nginx.com/

## Demo

```nginx
events {}

http {
    upstream node-backend {
        server 127.0.0.1:3000;
    }

    server {
        listen 5000;

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

app.get("/hello", (req, res) => {
  console.log(req.headers);
  res.send("Hello.\n");
});

app.listen(3000, () => {
    console.log("Listening on port 3000!");
});
```

```bash
curl http://localhost:5000/hello
```

Last Modified 2022-01-28

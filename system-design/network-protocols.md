# 网络协议

## IP

代表互联网协议，该网络协议概述了世界上几乎所有机器对机器通信的发生方式，其它协议如 TCP、UDP 和 HTTP 建立在 IP 之上。

## TCP

建立在 Internet 协议 (IP) 之上的网络协议。通过创建连接，允许通过公共互联网在机器之间进行有序、可靠的数据传输。

TCP 通常在内核中实现，它向应用程序公开套接字，应用程序可以使用这些套接字通过开放连接传输数据。

## HTTP

HTTP (HyperText Transfer Protocol) 是在 TCP 的基础上实现的一种非常常见的网络协议，客户端发出 HTTP 请求，服务器作出响应。

请求通常具有以下结构

```
host: string (example: algoexpert.io)
port: integer (example: 80 or 443)
method: string (example: GET，PUT, POST, DELETE， OPTIONS or PATCH)
headers: pair list (example: "Content-Type" => " application/json")
body: opaque sequence of bytes
```

响应通常具有以下结构

```
status code: integer (example: 200，401)
headers: pair list (example: "Content-Length" => 1238)
body: opaque sequence of bytes
```

## IP Packet

有时更广泛地称为（网络）数据包，IP 数据包实际上是用于描述通过 IP 发送的数据（字节除外）的最小单位。一个 IP 数据包包括：

- IP 报头，其中包含来源和目的 IP 地址以及与网络相关的其它信息
- 一个有效负载，它只是通过网络发送的数据

## Demo

```js
const express = require("express");
const app = express();

app.use(express.json());

app.listen(3000, () => console.log("Listening on port 3000."));

app.get("/hello", (req, res) => {
  console.log("Headers:", req.headers);
  console.log("Method:", req.method);
  res.send("Received GET request!\n");
});

app.post("/hello", (req, res) => {
  console.log("Headers:", req, headers);
  console.log("Method:", req.method);
  console.log("Body:", req.body);
  res.send("Received POST request!\n");
});
```

```bash
curl http://localhost:3000/hello
```

```bash
curl --header 'content-type: application/json' \
     --data '{"foo": "bar"}' \
     http://localhost:3000/hello
```

Last Modified 2022-01-21

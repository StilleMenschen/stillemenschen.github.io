# 发布/订阅模式

## 发布/订阅模式

发布/订阅模式通常简称为 Pub/Sub，是一种流行的消息传递模型，由发布者和订阅者组成。发布者将消息发布到特殊主题（有时称为频道），
而不关心甚至不知道谁会阅读这些消息，订阅者订阅主题并阅读来自这些主题的消息。Pub/Sub 系统通常带有非常强大的保证，
例如至少一次交付、持久存储、消息排序和消息的可重放性。

## 幂等操作

无论执行多少次，最终结果都相同的操作。如果一个操作可以多次执行而不改变其整体效果，那么它就是幂等的。
通过 Pub/Sub 消息系统执行的操作通常必须是幂等的，因为 Pub/Sub 系统倾向于允许多次使用相同的消息。
例如，增加数据库中的整数值不是幂等操作，因为重复此操作不会产生与仅执行一次相同的效果。相反，将值设置为“COMPLETE”是幂等操作，
因为重复此操作将始终产生相同的结果：值将是“COMPLETE”。

## Apache kafka

LinkedIn 创建的分布式消息系统。在使用流模式而不是轮询时非常有用。了解更多：https://kafka.apache.org/

## Google Pub/Sub

由 Google 创建的高度可扩展的 Pub/Sub 消息传递服务。保证至少一次传递消息并支持“倒带”以重新处理消息。了解更多：https://cloud.google.com/pubsub/

## Demo

messaging-api.js

```js
const axios = require("axios");
const WebSocket = require("ws");

function publish(message, topicId) {
  return axios.post(`http://localhost:3001/${topicId}`, message);
}

function subscribe(topicId) {
  return new WebSocket(`ws://localhost:3001/${topicId}`);
}

module.exports.publish = publish;
module.exports.subscribe = subscribe;
```

message-server.js

```js
const express = require("express");
const expressWs = require("express-ws");

const app = express();
expressWs(app);

const sockets = {};

app.use(express.json());

app.listen(3001, () => {
  console.log("Listening on port 3001!");
});

app.post("/:topicId", (req, res) => {
  const { topicId } = req.params;
  const message = req.body;
  const topicSockets = sockets[topicId] || [];

  for (const socket of topicSockets) {
    socket.send(JSON.stringify(message));
  }
  res.send();
});

app.ws("/:topicId", (socket, req) => {
  const { topicId } = req.params;
  if (!sockets[topicId]) sockets[topicId] = [];

  const topicSockets = sockets[topicId];
  topicSockets.push(socket);

  socket.on("close", () => {
    topicSockets.splice(topicSockets.indexOf(socket), 1);
  });
});
```

publisher.js

```js
const messagingApi = require("./messaging-api");
const readline = require("readline");

const TOPIC_ID = process.env.TOPIC_ID;

const terminal = readline.createInterface({
  input: process.stdin,
});

terminal.on("line", (text) => {
  const name = process.env.NAME;
  const message = { name, text };
  messagingApi.publish(message, TOPIC_ID);
});
```

subscriber.js

```js
const messagingApi = require("./messaging-api");

const TOPIC_ID = process.env.TOPIC_ID;

function displayMessage(message) {
  console.log(`> ${message.name}: ${message.text}`);
}

function streamMessages() {
  const messagingSocket = messagingApi.subscribe(TOPIC_ID);

  messagingSocket.on("message", (data) => {
    const message = JSON.parse(data);
    displayMessage(message);
  });
}

streamMessages();
```

```bash
node message-server.js
```

```bash
TOPIC_ID=stock_price NAME=Deepdream node publisher.js
```

```bash
TOPIC_ID=stock_price node subscriber.js
```

Last Modified 2022-02-16

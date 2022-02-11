# 轮询和流传输

## Socket

一种类似于流的文件。进程可以读取和写入 Socket 并以这种方式进行通信。大多数时候 Socket 是 TCP 连接的前身。

## 轮询

定期获取资源或数据的行为，以确保数据不会太陈旧。

## 流

在网络中，它通常是指通过在两台机器或进程之间保持开放连接来不断地从服务器获取信息的行为。

## Demo

helpers.js

```js
function getRandomInt(max) {
  return Math.floor(Math.random() * Math.floor(max));
}

module.exports.getRandomInt = getRandomInt;
```

messaging-api.js

```js
const axios = require("axios");
const WebSocket = require("ws");

function createMessagingSocket() {
  return new WebSocket("ws://localhost:3001/messages");
}

function getMessages() {
  return axios.get("http://localhost:3001/messages").then((res) => res.data);
}

function sendMessage(message) {
  return axios.post("http://localhost:3001/messages", message);
}

module.exports.createMessagingSocket = createMessagingSocket;
module.exports.getMessages = getMessages;
module.exports.sendMessage = sendMessage;
```

chat-room-server.js

```js
const express = require("express");
const expressWs = require("express-ws");
const app = express();
expressWs(app);

const messages = [{ id: 0, text: "Welcome!", username: "Chat Room" }];
const sockets = [];

app.use(express.json());

app.listen(3001, () => {
  console.log("Listening on port 3001!");
});

app.get("/messages", (req, res) => {
  res.json(messages);
});

app.post("/messages", (req, res) => {
  const message = req.body;
  messages.push(message);

  for (const socket of sockets) {
    socket.send(JSON.stringify(message));
  }
});

app.ws("/messages", (socket) => {
  sockets.push(socket);

  socket.on("close", () => {
    sockets.splice(sockets.indexOf(socket), 1);
  });
});
```

chat-room-client.js

```js
const helpers = require("./helpers");
const messagingApi = require("./messaging-api");
const readline = require("readline");
const displayedMessages = {};

const terminal = readline.createInterface({
  input: process.stdin,
});

terminal.on("line", (text) => {
  const username = process.env.NAME;
  const id = helpers.getRandomInt(100000);
  displayedMessages[id] = true;
  const message = { id, text, username };
  messagingApi.sendMessage(message);
});

function displayMessage(message) {
  console.log(`> ${message.username}: ${message.text}`);
  displayedMessages[message.id] = true;
}

async function getAndDisplayMessages() {
  const messages = await messagingApi.getMessages();
  for (const message of messages) {
    const messageAlreadyDisplayed = message.id in displayedMessages;
    if (!messageAlreadyDisplayed) displayMessage(message);
  }
}

function pollMessages() {
  setInterval(getAndDisplayMessages, 3000);
}

function streamMessages() {
  const messagingSocket = messagingApi.createMessagingSocket();
  messagingSocket.on("message", (data) => {
    const message = JSON.parse(data);
    const messageAlreadyDisplayed = message.id in displayedMessages;
    if (!messageAlreadyDisplayed) displayMessage(message);
  });
}

if (process.env.MODE == "poll") {
  getAndDisplayMessages();
  pollMessages();
} else if (process.env.MODE == "stream") {
  getAndDisplayMessages();
  streamMessages();
}
```

test-client.js

```js
const helpers = require("./helpers");
const messagingApi = require("./messaging-api");

const username = "Any";
for (let i = 1; i <= 10; i++) {
  setTimeout(() => {
    let text = `Text ${i}`;
    const id = helpers.getRandomInt(100000);
    const message = { id, text, username };
    messagingApi.sendMessage(message);
  }, i * 1000);
}

setTimeout(() => {
  process.exit(0);
}, 12000);
```

```bash
node chat-room-server.js
```

```bash
MODE=poll NAME=Dream node chat-room-client.js
MODE=stream NAME=Deepdream node chat-room-client.js
```

```bash
node test-client.js
```

Last Modified 2022-02-11

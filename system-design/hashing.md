# 哈希

## 哈希函数

接受特定数据类型（如字符串或标识符）并输出数字的函数。 不同的输入可能具有相同的输出，但一个好的哈希函数会尝试最小化那些哈希冲突（这相当于最大化均匀性）。

## Consistent Hashing

一种哈希类型，可在调整哈希表大小时最大限度地减少需要重新映射的键的数量。负载均衡器经常使用它来将流量分配到服务器；当添加新服务器或关闭现有服务器时，它可以最大限度地减少转发到不同服务器的请求数量。

## Rendezvous Hashing

一种哈希运算方式，也称为最高随机权重（highest random weight）哈希。当服务器出现故障时，允许对映射进行最少的重新分配。

## SHA

SHA 是“安全哈希算法（Secure Hash Algorithms）”的缩写，是业界使用的加密哈希函数的集合。如今，SHA-3 是在系统中使用较多的。

## Demo

```js
function hashString(string) {
  let hash = 0;
  if (string.length === 0) return hash;
  for (let i = 0; i < string.length; i++) {
    const charCode = string.charCodeAt(i);
    hash = (hash << 5) - hash + charCode;
    hash |= 0;
  }
  return hash;
}

function computeScore(username, server) {
  const usernameHash = hashString(username);
  const serverHash = hashString(server);
  return (usernameHash * 13 + serverHash * 11) % 67;
}

module.exports.hashString = hashString;
module.exports.computeScore = computeScore;
```

```js
const utils = require("./hashing-utils");

const serverSet1 = ["server0", "server1", "server2", "server3", "server4", "server5"];
const serverSet2 = ["server0", "server1", "server2", "server3", "server4"];

const usernames = [
  "username0",
  "username1",
  "username2",
  "username3",
  "username4",
  "username5",
  "username6",
  "username7",
  "username8",
  "username9",
];

function pickServerSimple(username, servers) {
  const hash = utils.hashString(username);
  return servers[hash % servers.length];
}

function pickServerRendezvous(username, servers) {
  let maxServer = null;
  let maxScore = null;
  for (const server of servers) {
    const score = utils.computeScore(username, server);
    if (maxScore === null || score > maxScore) {
      maxScore = score;
      maxServer = server;
    }
  }
  return maxServer;
}

function exec(message, hashing) {
  console.log(message);
  for (const username of usernames) {
    const server1 = hashing(username, serverSet1);
    const server2 = hashing(username, serverSet2);
    const serversAreEqual = server1 === server2;
    console.log(`${username}: ${server1} => ${server2} | equal: ${serversAreEqual}`);
  }
}

function main() {
  exec("\nSimple Hashing Strategy:", pickServerSimple);
  exec("\nRendezvous Hashing Strategy:", pickServerRendezvous);
}

main();
```

Last Modified 2022-01-22

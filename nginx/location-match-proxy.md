# Location 匹配

## NODE 服务

使用的`Node JS`版本为`v16.16.0`

```js
const http = require("node:http");

const server = http.createServer((req, res) => {
  console.log(req.url);
  res.writeHead(200, { "Content-Type": "application/json" });
  res.end(
    JSON.stringify({
      code: 200,
      message: "Are you Okay?",
      data: req.url  // 会把请求地址返回回去
    })
  );
});
console.log('server on port 3000');
server.listen(3000);
```

## 配置示例

主要关注`location`的部分，`nginx`的端口监听默认的`80`

### 直接写路径

```
location /backend {
    proxy_pass http://127.0.0.1:3000;
}
```

```
$ curl http://localhost/backend/foo/bar?baz=1
{"code":200,"message":"Are you Okay?","data":"/backend/foo/bar?baz=1"}
```

### 在路径后面加斜杠

```
location /backend/ {
    proxy_pass http://127.0.0.1:3000;
}
```

```
$ curl http://localhost/backend/foo/bar?baz=1
{"code":200,"message":"Are you Okay?","data":"/backend/foo/bar?baz=1"}
```

### 在代理的路径后面加斜杠

```
location /backend {
    proxy_pass http://127.0.0.1:3000/;
}
```

```
$ curl http://localhost/backend/foo/bar?baz=1
{"code":200,"message":"Are you Okay?","data":"//foo/bar?baz=1"}
```

### 两个路径都在后面加斜杠

```
location /backend/ {
    proxy_pass http://127.0.0.1:3000/;
}
```

```
$ curl http://localhost/backend/foo/bar?baz=1
{"code":200,"message":"Are you Okay?","data":"/foo/bar?baz=1"}
```

### 代理的路径有子路径

```
location /backend {
    proxy_pass http://127.0.0.1:3000/foo;
}
```

```
$ curl http://localhost/backend/foo/bar?baz=1
{"code":200,"message":"Are you Okay?","data":"/foo/foo/bar?baz=1"}
```

### 路径有子路径

```
location /backend/foo {
    proxy_pass http://127.0.0.1:3000;
}
```

```
$ curl http://localhost/backend/foo/bar?baz=1
{"code":200,"message":"Are you Okay?","data":"/backend/foo/bar?baz=1"}
```

### 都有子路径

```
location /backend/foo {
    proxy_pass http://127.0.0.1:3000/foo;
}
```

```
$ curl http://localhost/backend/foo/bar?baz=1
{"code":200,"message":"Are you Okay?","data":"/foo/bar?baz=1"}
```

### 路径有子路径，代理路径有子路径且带斜杠

```
location /backend/foo {
    proxy_pass http://127.0.0.1:3000/foo/;
}
```

```
$ curl http://localhost/backend/foo/bar?baz=1
{"code":200,"message":"Are you Okay?","data":"/foo//bar?baz=1"}
```

### 都有子路径，都带斜杠

```
location /backend/foo/ {
    proxy_pass http://127.0.0.1:3000/foo/;
}
```

```
$ curl http://localhost/backend/foo/bar?baz=1
{"code":200,"message":"Are you Okay?","data":"/foo/bar?baz=1"}
```

Last Modified 2023-03-16

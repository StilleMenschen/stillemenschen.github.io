# Location 匹配

## 路径示例

### NODE 服务

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
      data: req.url, // 会把请求地址返回回去
    })
  );
});
console.log("server on port 3000");
server.listen(3000);
```

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

## 匹配优先级

### NODE 服务

使用的`Node JS`版本为`v16.16.0`，启动多个端口从`3001`到`3007`，不同的端口返回的数据不一样

```js
const http = require("node:http");

if (process.argv.length < 3) {
  console.error("必须传端口号");
  process.exit(1);
}

const port = process.argv[2];

const server = http.createServer((req, res) => {
  console.log(req.url);
  res.writeHead(200, { "Content-Type": "application/json" });
  res.end(
    JSON.stringify({
      code: 200,
      message: "Are you Okay?",
      data: `This Server on ${port}`, // 返回端口号
    })
  );
});
console.log(`server on ${port}`);
server.listen(Number(port));
```

通过 powershell 启动这几个服务

```powershell
for($i=3001; $i -le 3007; $i++){ start node "server.js $i" }
```

### 配置文件

主要关注`server`部分的，配置了多个规则对应多个不同的端口

```
server {
    listen       9999;

    location / {
        proxy_pass   http://127.0.0.1:3001;
    }

    location = /admin {
        proxy_pass   http://127.0.0.1:3002;
    }

    location /admin/ {
        proxy_pass   http://127.0.0.1:3003;
    }

    location ^~ /admin {
        proxy_pass   http://127.0.0.1:3004;
    }

    location ~* .(js|css|html)$ {
        proxy_pass   http://127.0.0.1:3005;
    }

    location ~ /admin {
        proxy_pass   http://127.0.0.1:3006;
    }

    location ~* /admin {
        proxy_pass   http://127.0.0.1:3007;
    }
}
```

### 访问示例

```
~
$ curl http://localhost:9999/
{"code":200,"message":"Are you Okay?","data":"This Server on 3001"}
~
$ curl http://localhost:9999/adm
{"code":200,"message":"Are you Okay?","data":"This Server on 3001"}
~
$ curl http://localhost:9999/admin
{"code":200,"message":"Are you Okay?","data":"This Server on 3002"}
~
$ curl http://localhost:9999/admin/
{"code":200,"message":"Are you Okay?","data":"This Server on 3006"}
~
$ curl http://localhost:9999/admin/index.html
{"code":200,"message":"Are you Okay?","data":"This Server on 3005"}
~
$ curl http://localhost:9999/admin-api
{"code":200,"message":"Are you Okay?","data":"This Server on 3004"}
~
$ curl http://localhost:9999/admin-api/index
{"code":200,"message":"Are you Okay?","data":"This Server on 3004"}
~
$ curl http://localhost:9999/admin-api/index.html
{"code":200,"message":"Are you Okay?","data":"This Server on 3004"}
~
$ curl http://localhost:9999/adm-api/index.html
{"code":200,"message":"Are you Okay?","data":"This Server on 3005"}
~
$ curl http://localhost:9999/index.html
{"code":200,"message":"Are you Okay?","data":"This Server on 3005"}
```

Last Modified 2023-03-16

# 配置

对系统至关重要的一组参数或常数。配置通常用 JSON 或 YAML 编写，可以是静态的，这意味着它是硬编码并随系统的应用程序代码（例如前端代码）一起提供的，
也可以是动态的，这意味着它位于系统的应用程序代码之外。

## JSON

```json
{
  "apiKey": "abcdef_123456",
  "showSystemsExpert": false,
  "supportedLanguages": ["cpp", "csharp", "go", "java", "javascript", " python", "swift"],
  "version": {
    "number": 0,
    "releaseDate": "2020-02-20"
  }
}
```

## YAML

```yaml
apiKey: abcdef_123456
showSystemsExpert: false
supportedLanguages:
  - cpp
  - csharp
  - go
  - java
  - javascript
  - python
  - swift
version:
  number: 0
  releaseDate: "2020-02-20"
```

## Demo

```json
{
  "newFeatureLaunched": true
}
```

```js
const fs = require("fs");
const express = require("express");
const app = express();

const staticConfig = JSON.parse(fs.readFileSync("static_config.json"));

app.listen(3000, () => console.log("Listening on port 3000."));

app.get("/static/new_feature.html", function (req, res) {
  if (!staticConfig.newFeatureLaunched) {
    res.status(401).send("Unauthorized.\n");
    return;
  }
  res.send("<html>Hello World!</htmL>\n");
});
```

Last Modified 2022-02-13

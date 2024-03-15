# 转换 Mybatis SQL 日志

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Convert Mybatis SQL Log</title>
  </head>
  <style>
    .btn {
      width: 32%;
      height: 10%;
      margin: 0.48% 0;
    }
    .text {
      display: block;
      width: 100%;
    }
  </style>
  <body>
    <textarea id="txt" class="text" rows="20"></textarea>
    <button class="btn" type="button" onclick="copyText()">复制已解析SQL</button>
    <button class="btn" type="button" onclick="convert()">解析上方Mybatis日志</button>
    <button class="btn" type="button" onclick="clearText()">清空文本框内容</button>
    <textarea id="res" class="text" rows="20"></textarea>
    <script>
      const txt = document.getElementById("txt");
      const res = document.getElementById("res");
      function evalSQL(logText) {
        // 根据日志输出的日期年份来删掉多余的换行，其中 2024 可能在年份变化后需要修改
        logText = logText.replace(/([ a-zA-Z0-9_,=-?)(><:'"*])\n(?!2024)/g, "$1");

        console.log(logText);
        // 通过换行符拆分日志
        const lines = logText.split("\n");

        // 提取出包含 "Preparing" 和 "Parameters" 的日志行
        const relevantLines = lines.filter((line) => line.includes("Preparing") || line.includes("Parameters"));
        if (!relevantLines.length || relevantLines.length < 2) {
          alert("请输入有效的 Mybatis 日志输出");
          return false;
        }
        let result = "";

        // 提取并打印出所有解析出来的 SQL
        for (let i = 0; i < relevantLines.length; i += 2) {
          const preparingLine = relevantLines[i];
          const parametersLine = relevantLines[i + 1];

          // 获取 SQL 和参数列表
          const sql = preparingLine.match(/Preparing: (.*)/)[1];
          const parameters = parametersLine.match(/Parameters: (.*)/)[1].split(", ");

          // 将参数填入 SQL 中
          const finalSql = sql.replace(/\?/g, (match) => {
            // 获取下一个参数并移除类型信息（如 (Long)、(String) 等）
            const parameter = parameters.shift();
            const matchResult = parameter.match(
              /(.*)\((String|Integer|Long|LocalDateTime|LocalDate|LocalTime|Date|BigDecimal|Short|Byte|Timestamp)\)/
            );
            if (!matchResult) {
              return parameter;
            }
            let value = matchResult[1];
            // 部分数据类型加上引号
            if (/\((String|LocalDateTime|LocalDate|LocalTime|Date|Timestamp)\)/.test(parameter)) {
              value = `'${value}'`;
            }

            // 返回填入参数后的 SQL
            return value;
          });

          console.log(finalSql);
          result += finalSql.trim() + "\n";
        }
        return result;
      }
      function convert() {
        if (!txt.value || !txt.value.trim() || !txt.value.match(/Parameters|Preparing/g)) {
          alert("请输入有效的 Mybatis 日志输出");
          return false;
        }
        res.value = evalSQL(txt.value);
      }
      function copyText() {
        if (!res.value) {
          return false;
        }
        // 文本域获得焦点
        res.focus();
        res.select();
        // 不同的浏览器可能实现的复制文本 API 不同
        if (navigator && navigator.clipboard && navigator.clipboard.writeText) {
          navigator.clipboard.writeText(res.value).then(() => {
            console.log("copy to navigator.clipboard");
          });
        } else {
          console.log("copy");
          document.execCommand("copy");
        }
      }
      function clearText() {
        txt.value = "";
        res.value = "";
      }
    </script>
  </body>
</html>
```

Last Modified 2024-03-15

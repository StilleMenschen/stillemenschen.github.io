# 高性能

## 加载脚本

- 合并多个`javascript`脚本
- 异步下载脚本，如动态创建`<script>`标签或使用`XHR`对象加载`javascript`代码注入页面

## 数据访问

- 访问直接量和局部变量速度最快，而访问数组元素和对象成员相对较慢
- 变量在作用域链中的位置越深，访问所需的时间越长。由于全局变量总处在作用域链的最末端，因此访问速度也是最慢的
- 尽量少使用多级嵌套的对象成员
- 属性和方法在原型链中的位置越深，访问它的速度也越慢
- 创建局部变量引用对象成员、数组元素、跨域变量，加快访问速度

## DOM

- 减少`DOM`的访问次数，先完成计算再渲染
- 频繁访问的`DOM`节点应该使用局部变量存储引用
- 尽量使用标准的`API`来查询`DOM`，如`document.querySelectorAll`和`firstElementChild`
- 减少页面的重绘和重排，可以先缓存元素再操作，或者使用`document.createDocumentFragment`（推荐），再或者先克隆元素修改完成后再替换
- 通过事件委托（由父级元素来处理其子节点的事件）来减少事件处理器的数量

>事实上`HTML`集合一直与文档保持着连接，每次获取其最新信息时，都会重复执行查询操作，即使只是获取元素数量也是如此，这正是低效之源

## 算法和流程控制

- 避免使用`for-in`循环，除非需要遍历一个属性未知的对象
- 减少每次迭代的计算量和减少循环的迭代次数
- 经常使用但又较少产生变化的数据考虑使用缓存

## 连接字符串

```javascript
function timeTo(f, t) {
  console.time(t);
  f();
  console.timeEnd(t);
}

function each(task, timer) {
  for (let i = 0; i < timer; i++) {
    task();
  }
}

function main() {
  const baseCase = 5e6;

  timeTo(() => {
    let a = "a";
    each(() => {
      a += "a";
    }, baseCase);
  }, "task-plus-equal");

  timeTo(() => {
    let b = [];
    each(() => {
      b.push("b");
    }, baseCase);
    const r = b.join("");
  }, "task-array-join");

  timeTo(() => {
    let c = "c";
    each(() => {
      c = c.concat("c");
    }, baseCase);
  }, "task-concat");
}
setTimeout(main, 1000);
```

在 chromium 内核的浏览器中

```
task-plus-equal: 526.25390625 ms
task-array-join: 269.375244140625 ms
task-concat: 612.717041015625 ms
```

## 正则表达式

- 使用向前查看和反向引用模拟原子组，模板类似`(?=(pattern))\1`，原子组`(?>...)`是一种具有特殊反转性的非捕获组，一但原子组中存在一个正则表达式，该组的任何回溯位置都会被丢弃，使正则引擎回溯结束得更快一点。
- 关注如何让正则表达式更快地失败
- 正则表达式以简单、必需的子元开始
- 使用量词模式，使它们后面的子元互斥
- 减少分支数量，缩小分支范围
  > 如`cat|bat`替换为`[cb]at`，`read|red`替换为`rea?d`，`red|raw`替换为`r(?:ed|aw)`，`(.|\r|\n)`替换为`[\s\S]`
- 使用非捕获组
- 只捕获感兴趣的文本以减少后续处理
- 使用合适的量词（基于预期的回溯数量）可以显著提升性能

一个取出字符串首尾空白的合适例子

```javascript
if (!String.prototype.trim) {
  String.prototype.trim = function () {
    var str = this.replace(/^\s+/, ""),
      end = str.length - 1,
      ws = /\s/;
    while (ws.test(str.charAt(end))) {
      end--;
    }
    return str.slice(0, end + 1);
  };
}
```

## AJAX

### 信标

```javascript
let url = "/status_tracker.php";
let params = ["step=2", "time=12445354897"];

let img = new Image();
img.src = `${url}?${params.join("&")}`;
```

>只能发送`GET`请求传输简单的数据

### JSONP

```php
<?php
header("Content-Type: application/json; charset=UTF-8");
$obj = json_decode($_GET["x"], false);

$conn = new mysqli("myServer", "myUser", "myPassword", "Northwind");
$result = $conn->query("SELECT name FROM ".$obj->$table." LIMIT ".$obj->$limit);
$outp = array();
$outp = $result->fetch_all(MYSQLI_ASSOC);

echo "myFunc(".json_encode($outp).")";
?>
```

```javascript
const obj = { table: "products", limit: 10 };
let s = document.createElement("script");
s.src = "jsonp_demo_db.php?x=" + JSON.stringify(obj);
document.body.appendChild(s);

function myFunc(myObj) {
  let txt = "";
  for (let x in myObj) {
    txt += myObj[x].name + "<br>";
  }
  document.getElementById("demo").innerHTML = txt;
}
```

>因为加载脚本允许跨域，可能会存在安全问题

## 不同的加载方式

### 延迟加载

```javascript
function addHandler(target, eventType, handler) {
  if (target.addEventListener) {
    // DOM2
    addHandler = function (target, eventType, handler) {
      target.addEventListener(eventType, handler, false);
    };
  } else {
    // < IE 9
    addHandler = function (target, eventType, handler) {
      target.attachEvent("on" + eventType, handler);
    };
  }

  addHandler(target, eventType, handler);
}

function removeHandler(target, eventType, handler) {
  if (target.removeEventListener) {
    // DOM2
    removeHandler = function (target, eventType, handler) {
      target.removeEventListener(eventType, handler, false);
    };
  } else {
    // < IE 9
    removeHandler = function (target, eventType, handler) {
      target.attachEvent("on" + eventType, handler);
    };
  }

  removeHandler(target, eventType, handler);
}
```

### 条件预加载

```javascript
var addHandler = document.body.addEventListener
  ? function (target, eventType, handler) {
      target.addEventListener(eventType, handler, false);
    }
  : function (target, eventType, handler) {
      target.attachEvent("on" + eventType, handler);
    };

var removeHandler = document.body.removeEventListener
  ? function (target, eventType, handler) {
      target.removeEventListener(eventType, handler, false);
    }
  : function (target, eventType, handler) {
      target.attachEvent("on" + eventType, handler);
    };
```

Last Modified 2022-09-19

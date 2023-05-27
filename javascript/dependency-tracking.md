# 依赖跟踪

需要实现一个依赖跟踪类`Dep`，类里有一个叫`depend`方法，该方法用于收集依赖项；另外还有一个`notify`方法，该方法用于触发依赖项的执行，也就是说只要在之前使用`dep`方法收集的依赖项，当调用`notify`方法时会被触发执行

`Dep`类期望可以达到的效果是，调用`dep.depend`方法收集依赖，当调用`dep.notify`方法，控制台会再次执行已经记录的操作，即输出 updated，实现如下

```javascript
class Dep {
  constructor() {
    this.subscribers = new Set();
  }

  depend() {
    if (activeUpdate) {
      // 收集依赖
      this.subscribers.add(activeUpdate);
    }
  }

  notify() {
    // 更新所有依赖
    this.subscribers.forEach((sub) => sub());
  }
}

let activeUpdate = null;
function autoRun(update) {
  function wrappedUpdate() {
    // 缓存此操作到全局变量中
    activeUpdate = wrappedUpdate;
    update();
    activeUpdate = null;
  }
  wrappedUpdate();
}

const dep = new Dep();
let id = 0;

autoRun(() => {
  dep.depend();
  console.log(`updated ${id++}`);
});
// 输出 updated 0

dep.notify();
// 输出 updated 1
```

Last Modified 2023-05-27

# 依赖跟踪

```javascript
class Dep {
  constructor() {
    this.subscribers = new Set();
  }

  depend() {
    if (activeUpdate) {
      this.subscribers.add(activeUpdate);
    }
  }

  notify() {
    this.subscribers.forEach((sub) => sub());
  }
}

let activeUpdate = null;
function autorun(update) {
  function warppedUpdate() {
    activeUpdate = warppedUpdate;
    update();
    activeUpdate = null;
  }
  warppedUpdate();
}

const dep = new Dep();
let id = 0;

autorun(() => {
  dep.depend();
  console.log(`updated ${id++}`);
});
// 打印 updated 0

dep.notify();
// 打印 updated 1
```

Last Modified 2021-04-22

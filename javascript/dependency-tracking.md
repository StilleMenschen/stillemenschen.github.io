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

let activeUpdate;
function autorun(update) {
    function warppedUpdate() {
        activeUpdate = warppedUpdate;
        update();
        activeUpdate = null;
    }
    warppedUpdate();
}

const dep = new Dep();

autorun(() => {
    dep.depend();
    console.log(`updated ${Date.now()}`);
});

dep.notify();
```
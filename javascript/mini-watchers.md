# 迷你观察者

将**依赖跟踪**和**对象属性监听**的两个整合到一起，实现一个小型的观察者，通过在`getter`和`setter`中调用`depend`方法
和`notfiy`方法，就可以实现自动更新数据的目的了，这也是 Vue 实现自动更新的核心原理

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

function observe(obj) {
  Object.keys(obj).forEach((key) => {
    let internalValue = obj[key];

    const dep = new Dep();
    Object.defineProperty(obj, key, {
      // 在getter收集依赖项，当触发notify时重新运行
      get() {
        dep.depend();
        return internalValue;
      },

      // setter用于调用notify
      set(newVal) {
        const changed = internalValue !== newVal;
        internalValue = newVal;
        if (changed) {
          dep.notify();
        }
      },
    });
  });
  return obj;
}

let activeUpdate = null;

function autorun(update) {
  const wrappedUpdate = () => {
    activeUpdate = wrappedUpdate;
    update();
    activeUpdate = null;
  };
  wrappedUpdate();
}

const state = {
  count: 0,
};

observe(state);

autorun(() => {
  console.log(`count is: ${state.count}`);
});
// 打印"count is: 0"

state.count++;
// 打印"count is: 1"
```

Last Modified 2021-04-22

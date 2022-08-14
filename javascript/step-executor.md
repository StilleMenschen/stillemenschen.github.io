# 步骤执行器

```javascript
function a1() {
  console.log("a1", Array.from(arguments));
}

function a2() {
  console.log("a2", Array.from(arguments));
}

function a3() {
  console.log("a3", Array.from(arguments));
}

function multiStep(steps, args, callback) {
  let tasks = steps.concat();

  function innerTask() {
    let start = +new Date();
    let task = tasks.shift();

    // 执行间隔小于 50 毫秒的任务连续执行, 不需要进入异步队列
    do {
      task.apply(null, args || []);
    } while (tasks.length > 0 && +new Date() - start < 50 && (task = tasks.shift()));

    if (tasks.length > 0) {
      setTimeout(innerTask, 0);
    } else {
      callback();
    }
  }

  setTimeout(innerTask);
}

multiStep([a1, a2, a3], ["Hello", "World!"], () => {
  console.log("The end.");
});
```

Last Modified 2022-08-14

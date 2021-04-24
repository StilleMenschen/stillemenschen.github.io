# 函数式编程

## index.html

```html
<!DOCTYPE html>
<html lang="zh">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <div id="app"></div>
    <script src="https://cdn.bootcdn.net/ajax/libs/vue/2.6.12/vue.js"></script>
    <script src="index.js"></script>
  </body>
</html>
```

## index.js

```javascript
function app({ el, model, view, actions }) {
  const wrappendActions = {};

  Object.keys(actions).forEach((key) => {
    const originalAction = actions[key];
    wrappendActions[key] = () => {
      const nextModel = originalAction(vm.model);
      vm.model = nextModel;
    };
  });
  const vm = new Vue({
    el,
    data() {
      return {
        model,
      };
    },
    render(h) {
      return view(h, this.model, wrappendActions);
    },
  });
}

app({
  el: "#app",
  model: {
    count: 0,
  },
  actions: {
    inc: ({ count }) => ({ count: count + 1 }),
    des: ({ count }) => ({ count: count - 1 }),
    res: ({ count }) => ({ count: count - count }),
  },
  view: (h, model, actions) =>
    h("div", { attrs: { id: "app" } }, [
      h("div", `count = ${model.count}`),
      h("button", { on: { click: actions.inc } }, "增加"),
      h("button", { on: { click: actions.des } }, "减少"),
      h("button", { on: { click: actions.res } }, "重置"),
    ]),
});
```
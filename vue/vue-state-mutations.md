# Vue简单状态管理

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
    <div id="app">
      <!-- 实现多个组件之间的数据共享 -->
      <counter></counter>
      <counter></counter>
      <counter></counter>
      <p></p>
      <button @click="inc">Add</button>
      <button @click="des">Sub</button>
    </div>
    <script src="https://cdn.bootcdn.net/ajax/libs/vue/2.6.12/vue.js"></script>
    <script src="index.js"></script>
  </body>
</html>
```

## index.js

```javascript
function createStore({ state, mutaions }) {
  // 由于Vue本身就是响应式的，所以采用Vue来实现
  return new Vue({
    data() {
      return {
        state,
      };
    },
    methods: {
      // 调用注册的函数以响应提交
      commit(mutationType) {
        // 将数据传递到具体实现方法中供方法进行操作
        mutaions[mutationType](this.state);
      },
    },
  });
}

const store = createStore({
  state: { count: 0 },
  mutaions: {
    inc(state) {
      state.count++;
    },
    des(state) {
      state.count--;
    },
  },
});

const Counter = {
  render: (h) => h("div", store.state.count),
};

const app = new Vue({
  el: "#app",
  components: {
    Counter,
  },
  methods: {
    inc() {
      store.commit("inc");
    },
    des() {
      store.commit("des");
    },
  },
});
```
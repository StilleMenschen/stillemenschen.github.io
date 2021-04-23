# 自定义Vue插件

## index.html

```html
<!DOCTYPE html>
<html lang="zh">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style> .error { color: red; } </style>
  </head>
  <body>
    <div id="app">
      <p>{{counter}}</p>
      <button @click="counter++">增加</button><button @click="counter--">减小</button>
      <p class="error" v-if="message.counter">{{ message.counter }}</p>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.12"></script>
    <script src="index.js"></script>
  </body>
</html>
```

## index.js

```javascript
const myPlugin = {
  install(Vue) {
    Vue.mixin({
      created() {
        const rules = this.$options.rules;
        if (rules) {
          Object.keys(rules).forEach((key) => {
            const { validate, message } = rules[key];
            this.$watch(key, (newValue) => {
              const result = validate(newValue);
              if (!result) {
                this.message.counter = message;
              } else {
                this.message.counter = "";
              }
            });
          });
        }
      },
    });
  },
};
Vue.use(myPlugin);
const app = new Vue({
  el: "#app",
  data() {
    return {
      counter: 5,
      message: {},
    };
  },
  rules: {
    counter: {
      validate: (value) => value > 3,
      message: "输入的值必须大于3",
    },
  },
});
```

Last Modified 2021-04-23

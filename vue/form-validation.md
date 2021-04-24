# 表单验证

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
      <form @submit="validate(event)">
        <input type="text" v-model="text" /> <br />
        <input type="text" v-model="email" /> <br />

        <ul v-if="!$v.valid" style="color: red">
          <li v-for="error in $v.errors" :key="e">{{ error }}</li>
        </ul>
        <input type="submit" value="Submit" :disabled="!$v.valid" />
      </form>
    </div>
    <script src="https://cdn.bootcdn.net/ajax/libs/vue/2.6.12/vue.js"></script>
    <script src="index.js"></script>
  </body>
</html>
```
## index.js

```javascript
const validationPlugin = {
  install(Vue) {
    Vue.mixin({
      computed: {
        $v() {
          let valid = true;
          const errors = [];
          const { validations } = this.$options;
          Object.keys(validations || {}).forEach((key) => {
            const { validate, message } = validations[key];
            const currentValue = this[key];
            const result = validate(currentValue);
            if (!result) {
              valid = false;
              errors.push(message(key, currentValue));
            }
          });
          return {
            valid,
            errors,
          };
        },
      },
    });
  },
};
Vue.use(validationPlugin);
const emailRE = /^(([^<>()[\]\.,;:\s@\"]+(\.[^<>()[\]\.,;:\s@\"]+)*)|(\".+\"))@(([^<>()[\]\.,;:\s@\"]+\.)+[^<>()[\]\.,;:\s@\"]{2,})$/i;
const app = new Vue({
  el: "#app",
  data() {
    return {
      text: "foo",
      email: "",
    };
  },
  validations: {
    text: {
      validate: (value) => value.length >= 5,
      message: (key, value) => `${key} 的长度必须大于等于5, 但目前为 ${value}`,
    },
    email: {
      validate: (value) => emailRE.test(value),
      message: (key) => `${key} 必须是有效的邮箱`,
    },
  },
  methods: {
    validate(e) {
      if (!this.$v.valid) {
        e.preventDefault();
        alert("not valid!");
      }
    },
  },
});
```

Last Modified 2021-04-24

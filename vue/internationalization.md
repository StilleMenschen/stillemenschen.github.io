# 国际化

1. 使用函数转换，如`i18n('messageKey')`
2. 自定义指令，如`v-i18n="messageKey"`
3. 预编译多语言页面，通过切换区域加载不同语言

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
      <p>{{ $t('welcom-message') }}</p>
      <button @click="changeLang('en')">English</button>
      <button @click="changeLang('zh')">中文</button>
      <button @click="changeLang('ru')">Русский</button>
    </div>
    <script src="https://cdn.bootcdn.net/ajax/libs/vue/2.6.12/vue.js"></script>
    <script src="index.js"></script>
  </body>
</html>
```

## index.js

```javascript
const i18nPlugin = {
  install(Vue, locals) {
    Vue.prototype.$t = function (id) {
      return locals[this.$root.lang][id];
    };
  },
};
Vue.use(i18nPlugin, {
  en: { "welcom-message": "Hello" },
  zh: { "welcom-message": "你好" },
  ru: { "welcom-message": "Привет" },
});
const app = new Vue({
  el: "#app",
  data() {
    return {
      lang: "en",
    };
  },
  methods: {
    changeLang(lang) {
      this.lang = lang;
    },
  },
});
```

Last Modified 2021-04-24

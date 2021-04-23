# Vue动态渲染

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
        <example :tags="['p', 'h1', 'h2', 'h3', 'span']"></example>
        <br><example2 :ok="ok"></example2>
        <button @click="ok = !ok">Toggle</button>
    </div>
    <script src="https://cdn.bootcdn.net/ajax/libs/vue/2.6.12/vue.js"></script>
    <script src="index.js"></script>
  </body>
</html>
```

## index.js

```javascript
Vue.component("example", {
  functional: true,
  render(h, { props: { tags } }) {
    return h(
      "div",
      tags.map((e, i) => h(e, `${e} index is ${i}`))
    );
  },
  props: {
    tags: {
      type: Array,
      required: true,
    },
  },
});

const Foo = {
  render(h) {
    return h(
      "div",
      {
        style: {
          color: "red",
        },
      },
      "foo"
    );
  },
};
const Bar = {
  render(h) {
    return h(
      "div",
      {
        style: {
          fontWeight: "bold",
        },
      },
      "bar"
    );
  },
};

Vue.component("example2", {
  render(h) {
    return h(this.ok ? Foo : Bar);
  },
  props: {
    ok: {
      type: Boolean,
      required: true,
    },
  },
});

const app = new Vue({
  el: "#app",
  data() {
    return {
      ok: true,
    };
  },
});
```

Last Modified 2021-04-23

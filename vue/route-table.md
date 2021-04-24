# 简单路由

## index.html

```html
<!DOCTYPE html>
<html lang="zh">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <link rel="stylesheet" href="index.css">
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
const Foo = { template: "<div>Foo</div>" };
const Bar = { template: "<div>Bar</div>" };
const NotFound = { template: "<div>Not Found</div>" };

const routerTable = {
  foo: Foo,
  bar: Bar,
};

window.addEventListener("hashchange", () => {
  console.log(window.location.hash);
  app.url = window.location.hash.slice(1);
});

const app = new Vue({
  el: "#app",
  data() {
    return {
      url: window.location.hash.slice(1),
    };
  },
  render(h) {
    return h("div", [
      h(routerTable[this.url] || NotFound, { class: "view" }),
      Object.keys(routerTable).map((key) => h("a", { attrs: { href: `#${key}` } }, key)),
    ]);
  },
});
```

## index.css

```css
.view {
  border: 1px solid #000;
  padding: 1rem;
  margin-bottom: 0.5rem;
}
a + a {
  padding-left: 1rem;
}
```

Last Modified 2021-04-24

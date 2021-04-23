# Vue高阶组件

# index.html

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
      <smart-avatar username="Jack" id="a1"></smart-avatar>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.12"></script>
    <script src="index.js"></script>
  </body>
</html>
```

# index.js

```javascript
function fetchURL(username, callback) {
  console.log(username);
  setTimeout(() => {
    callback("https://cn.vuejs.org/images/logo.png");
  }, 500);
}
const Avatar = {
  props: ["src"],
  template: '<img :src="src">',
};

function withSmartAvatar(InnerComponent, fetchAvatar) {
  return {
    props: ["username"],
    data() {
      return {
        url: "https://www.w3school.com.cn/ui2019/logo-180.png",
      };
    },
    created() {
      fetchAvatar(this.username, (url) => {
        this.url = url;
      });
    },
    render(h) {
      return h(InnerComponent, {
        attrs: {
          ...this.$attrs,
        },
        props: {
          src: this.url,
        },
      });
    },
  };
}

const SmartAvatar = withSmartAvatar(Avatar, fetchURL);

const app = new Vue({
  el: "#app",
  components: { SmartAvatar },
});
```

Last Modified 2021-04-23

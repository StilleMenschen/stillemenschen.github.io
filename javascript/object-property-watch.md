# 监听对象数据变化

`ES5`的`Object.defineProperty`提供监听属性变更的功能，下面将演示如何通过 covert 函数修改传入对象的 getter 和 setter 实现修改对象属性时打印日志的功能

## 例子

```javascript
function convert(obj) {
  // 遍历对象中的属性
  Object.keys(obj).forEach((key) => {
    let internalValue = obj[key]; // 缓存原始值
    Object.defineProperty(obj, key, {
      get() {
        console.log(`getting key "${key}": ${internalValue}`);
        // 直接返回缓存的值
        return internalValue;
      },
      set(newValue) {
        console.log(`setting key "${key}" to ${newValue}`);
        internalValue = newValue; // 记录新值并更新缓存
      },
    });
  });
}

let person = {
  name: "Jack",
  age: 18,
};

convert(person);
console.log(person.name);
// 输出 getting key "name": Jack
person.name = "Markhop";
// 输出 setting key "name" to Markhop
console.log(person.name);
console.log(person.age);
person.age = 24;
console.log(person.age);
```

## 参考

- https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty

Last Modified 2022-08-16

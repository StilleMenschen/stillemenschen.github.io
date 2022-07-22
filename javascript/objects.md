# Objects

## 配置对象属性

```javascript
// 使用 __proto__
var obj = {};
var descriptor = Object.create(null); // 没有继承的属性
// 默认没有 enumerable，没有 configurable，没有 writable
descriptor.value = "static";
Object.defineProperty(obj, "key", descriptor);

// 显式
Object.defineProperty(obj, "key", {
  enumerable: false, // enumerable 定义了对象的属性是否可以在 for...in 循环和 Object.keys() 中被枚举
  configurable: false, // configurable 特性表示对象的属性是否可以被删除，以及除 value 和 writable 特性外的其他特性是否可以被修改
  writable: false, // 当 writable 属性设置为 false 时，该属性被称为“不可写的”。它不能被重新赋值
  value: "static",
});

// 循环使用同一对象
function withValue(value) {
  var d =
    withValue.d ||
    (withValue.d = {
      enumerable: false,
      writable: false,
      configurable: false,
      value: null,
    });
  d.value = value;
  return d;
}
// ... 并且 ...
Object.defineProperty(obj, "key", withValue("static"));

// 如果 freeze 可用, 防止后续代码添加或删除对象原型的属性
// （value, get, set, enumerable, writable, configurable）
(Object.freeze || Object)(Object.prototype);

var o = {}; // 创建一个新对象

// 在对象中添加一个属性与数据描述符的示例
Object.defineProperty(o, "a", {
  value: 37,
  writable: true,
  enumerable: true,
  configurable: true,
});

// 对象 o 拥有了属性 a，值为 37

// 在对象中添加一个设置了存取描述符属性的示例
var bValue = 38;
Object.defineProperty(o, "b", {
  // 使用了方法名称缩写（ES2015 特性）
  // 下面两个缩写等价于：
  // get : function() { return bValue; },
  // set : function(newValue) { bValue = newValue; },
  get() {
    return bValue;
  },
  set(newValue) {
    bValue = newValue;
  },
  enumerable: true,
  configurable: true,
});

o.b; // 38
// 对象 o 拥有了属性 b，值为 38
// 现在，除非重新定义 o.b，o.b 的值总是与 bValue 相同

// 数据描述符和存取描述符不能混合使用
Object.defineProperty(o, "conflict", {
  value: 0x9f91102,
  get() {
    return 0xdeadbeef;
  },
});
// 抛出错误 TypeError: value appears only in data descriptors, get appears only in accessor descriptors
```

> `value`和`writable`一同出现，如果设置了`set()`、`get()`则不能同时设置`value`

## 检查对象相等

```javascript
console.log(Object.is(true, 1)); // false
console.log(Object.is({}, {})); // false
console.log(Object.is("2", 2)); // false
// 正确的 0、-0、+0 相等/不等判定
console.log(Object.is(+0, -0)); // false
console.log(Object.is(+0, 0)); // true
console.log(Object.is(-0, 0)); // false
// 正确的 NaN 相等判定
console.log(Object.is(NaN, NaN)); // true
// 要检查超过两个值，递归地利用相等性传递即可
function recursivelyCheckEqual(x, ...rest) {
  return Object.is(x, rest[0]) && (rest.length < 2 || recursivelyCheckEqual(...rest));
}
```

## 计算属性

```javascript
const nameKey = "name";
const ageKey = "age";
const jobKey = "job";
const methodKey = "sayName";

let person = {
  [nameKey]: "Matt",
  [ageKey]: 27,
  [jobKey]: "Software engineer",
  [methodKey](name) {
    console.log(`My name is ${name}`);
  },
};
console.log(person);
person.sayName("Matt");

let uniqueToken = 0;
function getUniqueKey(key) {
  return `${key}_${uniqueToken++}`;
}
person = {
  [getUniqueKey(nameKey)]: "Matt",
  [getUniqueKey(ageKey)]: 27,
  [getUniqueKey(jobKey)]: "Software engineer",
};
console.log(person);
```

## 对象解构

```javascript
let person = {
  name: "Matt",
  age: 27,
};
let { name: personName, age: personAge } = person;
console.log(personName); // Matt
console.log(personAge); // 27

let { name, age, job } = person;
console.log(name); // Matt
console.log(age); // 27
console.log(job); // undefined

let person = {
  name: "Matt",
  age: 27,
  job: {
    title: "Software engineer",
  },
};
let personCopy = {};
({ name: personCopy.name, age: personCopy.age, job: personCopy.job } = person);
// 因为一个对象的引用被赋值给 personCopy，所以修改
// person.job 对象的属性也会影响 personCopy
person.job.title = "Hacker";
console.log(person);
// { name: 'Matt', age: 27, job: { title: 'Hacker' } }
console.log(personCopy);
// { name: 'Matt', age: 27, job: { title: 'Hacker' } }

let person = {
 name: 'Matt',
 age: 27
};
function printPerson(foo, {name, age}, bar) {
 console.log(arguments);
 console.log(name, age);
}
function printPerson2(foo, {name: personName, age: personAge}, bar) {
 console.log(arguments);
 console.log(personName, personAge);
}
printPerson('1st', person, '2nd');
// ['1st', { name: 'Matt', age: 27 }, '2nd']
// 'Matt', 27
printPerson2('1st', person, '2nd');
// ['1st', { name: 'Matt', age: 27 }, '2nd']
// 'Matt', 27
```

## 浅拷贝对象

```javascript
const obj = {
  foo: 1,
  get bar() {
    return 2;
  },
};

let copy = Object.assign({}, obj);
console.log(copy); // { foo: 1, bar: 2 } copy.bar的值来自obj.bar的getter函数的返回值

// 下面这个函数会拷贝所有自有属性的属性描述符
function completeAssign(target, ...sources) {
  sources.forEach((source) => {
    let descriptors = Object.keys(source).reduce((descriptors, key) => {
      descriptors[key] = Object.getOwnPropertyDescriptor(source, key);
      return descriptors;
    }, {});

    // Object.assign 默认也会拷贝可枚举的Symbols
    Object.getOwnPropertySymbols(source).forEach((sym) => {
      let descriptor = Object.getOwnPropertyDescriptor(source, sym);
      if (descriptor.enumerable) {
        descriptors[sym] = descriptor;
      }
    });
    Object.defineProperties(target, descriptors);
  });
  return target;
}

copy = completeAssign({}, obj);
console.log(copy);
// { foo:1, get bar() { return 2 } }
```

> `Object.assign()`默认只拷贝深层对象的引用，且每次拷贝会覆盖上一次拷贝过的同名属性。如果在复制时发生了错误，那么之前拷
>贝成功的属性也同样生效

Last Modified 2021-10-23

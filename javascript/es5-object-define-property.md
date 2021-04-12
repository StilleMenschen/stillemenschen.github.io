# ES5监听对象数据变化

```javascript
function convert(obj) {
    // 遍历所有的变量
    Object.keys(obj).forEach(key => {
        // 保存当前变量值
        let internalValue = obj[key];
        Object.defineProperty(obj, key, {
            get() {
                console.log(`getting key "${key}": ${internalValue}`);
                return internalValue;
            },
            set(newValue){
                console.log(`setting key "${key}" to ${newValue}`);
                // 赋值
                internalValue = newValue;
            }
        });
    });
}
let person = {
    name: 'Jack',
    age : 18
}
convert(person);
console.log(person.name);
person.name = 'Markhop';
console.log(person.name);
console.log(person.age);
person.age = 24;
console.log(person.age);
```

Last Modified 2021-04-12
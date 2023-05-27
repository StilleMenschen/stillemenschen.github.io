# 小数位舍入问题

## 疑问

toFixed() 方法在什么情况下会出现计算精度问题呢？有以下几种可能：

- 一是当数值本身就无法被精确地表示为双精度浮点数时，例如 0.1 + 0.2 !== 0.3。这种情况下，toFixed() 方法会返回一个接近于真实值的字符串，但可能会有微小的误差。例如：

```javascript
var num = 0.1 + 0.2;
num = num.toFixed(1); // 输出结果为 "0.3"
num === 0.3; // 输出结果为 false
```

- 二是当数值的小数部分超过了指定的位数时，toFixed() 方法会进行四舍五入，但是这种四舍五入并不是严格按照十进制的规则，而是根据二进制的表示来判断。这可能会导致一些看似不合理的结果，例如：

```javascript
var num = 1.335;
num = num.toFixed(2); // 输出结果为 "1.33"，而不是 "1.34"
```

- 三是当数值的绝对值大于或等于 10^21 时，toFixed() 方法会简单调用 Number.prototype.toString() 并返回一个指数记数法格式的字符串，而不是定点表示法。例如：

```javascript
var num = 6.02 * 10 ** 23;
num = num.toFixed(2); // 输出结果为 "6.019999999999999e+23"
```

## 参考处理方式

1. 一是在调用 toFixed() 方法之前，先对数值进行一定的处理，使其不会出现四舍五入的误差。例如，你可以使用以下函数，它会先将数值乘以一个指定的倍数，然后进行四舍五入，再除以相同的倍数，最后再调用 toFixed() 方法。例如：

```javascript
function roundToFixed(num, digits) {
  var multiplier = Math.pow(10, digits);
  num = Math.round(num * multiplier) / multiplier;
  return num.toFixed(digits);
}

var num = 1.335;
num = roundToFixed(num, 2); // 输出结果为 "1.34"
```

2. 二是使用其他的库或函数来代替 toFixed() 方法，例如 lodash 的 round() 函数，它可以接受一个数值和一个精度参数，返回一个四舍五入后的数值。例如：

```javascript
var num = 1.335;
num = _.round(num, 2); // 输出结果为 1.34
```

3. 三是使用一些巧妙的技巧来实现四舍五入，例如使用 + 运算符和 e 指数记法 。例如：

```javascript
function toFixed(num, digits) {
  return +(Math.round(+(num + 'e' + digits)) + 'e' + -digits).toFixed(digits);
}

var num = 1.335;
num = toFixed(num, 2); // 输出结果为 "1.34"
```

以上是一些常用的方法，你也可以参考其他网上的教程和示例。


- (1) Javascript toFixed Not Rounding - Stack Overflow. https://stackoverflow.com/questions/10015027/javascript-tofixed-not-rounding.
- (2) js toFixed 四舍五入问题 - 掘金. https://juejin.cn/post/7050760083239600141.
- (3) 『前端BUG』—— toFixed四舍五入的不准确性 - 掘金. https://juejin.cn/post/6927215610552123406.

Last Modified 2023-05-27

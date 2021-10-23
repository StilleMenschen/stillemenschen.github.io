# Promise 简单实现

```javascript
// 判断变量否为function
var isFunction = function (variable) { return typeof variable === "function"; };
// 定义Promise的三种状态常量
var PENDING = "PENDING";
var FULFILLED = "FULFILLED";
var REJECTED = "REJECTED";
var Promises = /** @class */ (function () {
    function Promises(handle) {
        if (!isFunction(handle)) {
            throw new Error("Promises must accept a function as a parameter");
        }
        // 添加状态
        this._status = PENDING;
        // 添加状态
        this._value = undefined;
        // 添加成功回调函数队列
        this._fulfilledQueues = [];
        // 添加失败回调函数队列
        this._rejectedQueues = [];
        // 执行handle
        try {
            handle(this._resolve.bind(this), this._reject.bind(this));
        }
        catch (err) {
            this._reject(err);
        }
    }
    // 添加resovle时执行的函数
    Promises.prototype._resolve = function (val) {
        var _this = this;
        var run = function () {
            if (_this._status !== PENDING)
                return;
            // 依次执行成功队列中的函数，并清空队列
            var runFulfilled = function (value) {
                var cb;
                while ((cb = _this._fulfilledQueues.shift())) {
                    cb(value);
                }
            };
            // 依次执行失败队列中的函数，并清空队列
            var runRejected = function (error) {
                var cb;
                while ((cb = _this._rejectedQueues.shift())) {
                    cb(error);
                }
            };
            /* 如果resolve的参数为Promise对象，则必须等待该Promise对象状态改变后,
             * 当前Promsie的状态才会改变，且状态取决于参数Promsie对象的状态
             */
            if (val instanceof Promises) {
                val.then(function (value) {
                    _this._value = value;
                    _this._status = FULFILLED;
                    runFulfilled(value);
                }, function (err) {
                    _this._value = err;
                    _this._status = REJECTED;
                    runRejected(err);
                });
            }
            else {
                _this._value = val;
                _this._status = FULFILLED;
                runFulfilled(val);
            }
        };
        // 为了支持同步的Promise，这里采用异步调用
        setTimeout(run, 0);
    };
    // 添加reject时执行的函数
    Promises.prototype._reject = function (err) {
        var _this = this;
        if (this._status !== PENDING)
            return;
        // 依次执行失败队列中的函数，并清空队列
        var run = function () {
            _this._status = REJECTED;
            _this._value = err;
            var cb;
            while ((cb = _this._rejectedQueues.shift())) {
                cb(err);
            }
        };
        // 为了支持同步的Promise，这里采用异步调用
        setTimeout(run, 0);
    };
    // 添加then方法
    Promises.prototype.then = function (onFulfilled, onRejected) {
        var _this = this;
        var _a = this, _value = _a._value, _status = _a._status;
        // 返回一个新的Promise对象
        return new Promises(function (onFulfilledNext, onRejectedNext) {
            // 封装一个成功时执行的函数
            var fulfilled = function (value) {
                try {
                    if (!isFunction(onFulfilled)) {
                        onFulfilledNext(value);
                    }
                    else {
                        var res = onFulfilled(value);
                        if (res instanceof Promises) {
                            // 如果当前回调函数返回Promises对象，必须等待其状态改变后在执行下一个回调
                            res.then(onFulfilledNext, onRejectedNext);
                        }
                        else {
                            //否则会将返回结果直接作为参数，传入下一个then的回调函数，并立即执行下一个then的回调函数
                            onFulfilledNext(res);
                        }
                    }
                }
                catch (err) {
                    // 如果函数执行出错，新的Promise对象的状态为失败
                    onRejectedNext(err);
                }
            };
            // 封装一个失败时执行的函数
            var rejected = function (error) {
                try {
                    if (!isFunction(onRejected)) {
                        onRejectedNext(error);
                    }
                    else {
                        var res = onRejected(error);
                        if (res instanceof Promises) {
                            // 如果当前回调函数返回Promises对象，必须等待其状态改变后在执行下一个回调
                            res.then(onFulfilledNext, onRejectedNext);
                        }
                        else {
                            //否则会将返回结果直接作为参数，传入下一个then的回调函数，并立即执行下一个then的回调函数
                            onFulfilledNext(res);
                        }
                    }
                }
                catch (err) {
                    // 如果函数执行出错，新的Promise对象的状态为失败
                    onRejectedNext(err);
                }
            };
            switch (_status) {
                // 当状态为pending时，将then方法回调函数加入执行队列等待执行
                case PENDING:
                    _this._fulfilledQueues.push(fulfilled);
                    _this._rejectedQueues.push(rejected);
                    break;
                // 当状态已经改变时，立即执行对应的回调函数
                case FULFILLED:
                    fulfilled(_value);
                    break;
                case REJECTED:
                    rejected(_value);
                    break;
            }
        });
    };
    // 添加catch方法
    Promises.prototype["catch"] = function (onRejected) {
        return this.then(undefined, onRejected);
    };
    // 添加静态resolve方法
    Promises.resolve = function (value) {
        // 如果参数是Promises实例，直接返回这个实例
        if (value instanceof Promises)
            return value;
        return new Promises(function (resolve) { return resolve(value); });
    };
    // 添加静态reject方法
    Promises.reject = function (value) {
        return new Promises(function (resolve, reject) { return reject(value); });
    };
    // 添加静态all方法
    Promises.all = function (list) {
        var _this = this;
        return new Promises(function (resolve, reject) {
            /**
             * 返回值的集合
             */
            var values = [];
            var count = 0;
            var _loop_1 = function (k) {
                // 数组参数如果不是Promises实例，先调用Promises.resolve
                _this.resolve(list[k]).then(function (res) {
                    values[k] = res;
                    count++;
                    // 所有状态都变成fulfilled时返回的Promises状态就变成fulfilled
                    if (count === list.length)
                        resolve(values);
                }, function (err) {
                    // 有一个被rejected时返回的Promises状态就变成rejected
                    reject(err);
                });
            };
            for (var k in list) {
                _loop_1(k);
            }
        });
    };
    // 添加静态race方法
    Promises.race = function (list) {
        var _this = this;
        return new Promises(function (resolve, reject) {
            for (var _i = 0, list_1 = list; _i < list_1.length; _i++) {
                var p = list_1[_i];
                // 只要有一个实例率先改变状态，新的Promises的状态就跟着改变
                _this.resolve(p).then(function (res) {
                    resolve(res);
                }, function (err) {
                    reject(err);
                });
            }
        });
    };
    Promises.prototype["finally"] = function (cb) {
        return this.then(function (value) { return Promises.resolve(cb()).then(function () { return value; }); }, function (reason) {
            return Promises.resolve(cb()).then(function () {
                throw reason;
            });
        });
    };
    return Promises;
}());
```

Last Modified 2021-05-21

# 类继承

内置类型的原生方法使用 C 语言实现，不会调用子类中覆盖的方法

```python
import numbers


class A:
    def ping(self):
        print('A ping:', self)


class B(A):
    def pong(self):
        print('B pong:', self)


class C(A):
    def pong(self):
        print('C PONG:', self)


class D(B, C):

    def ping(self):
        super().ping()
        print('D post-ping:', self)

    def pingpong(self):
        self.ping()
        super().ping()
        self.pong()
        super().pong()
        C.pong(self)


def print_mro(cls):
    print(',  '.join(c.__name__ for c in cls.__mro__))


d = D()
d.ping()
print('---')
d.pong()
print('---')
d.pingpong()
print('---')
print_mro(D)
print_mro(numbers.Integral)
print_mro(bool)
```

- 把接口继承和实现继承区分开
- 使用抽象基类显示表示接口
- 通过混入重用代码
- 在名称中明确指明混入
- 抽象基类可以作为混入，反过来则不成立
- 不要子类化多个具体类
- 为用户提供聚合类
- 优先使用对象组合，而不是类继承

Last Modified 2022-07-25

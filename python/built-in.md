# 内置函数

<style>
table th:first-of-type {
    width: 20%;
}
</style>

## abs(x)

返回一个数的绝对值。实参可以是整数或浮点数。如果实参是一个复数，返回它的模

## all(iterable)

如果`iterable`的所有元素为真（或迭代器为空），返回`True`，等价于
```python
def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True
```

## any(iterable)

如果`iterable`的任一元素为真则返回`True`，如果迭代器为空，返回`False`，等价于
```python
def any(iterable):
    for element in iterable:
        if element:
            return True
    return False
```

## ascii(object)

就像函数`repr()`，返回一个对象可打印的字符串，但是`repr()`返回的字符串中非`ASCII`编码的字符，会使用`\x`、`\u`和`\U`来转义。生成的字符串和`Python 2`的`repr()`返回的结果相似

## bin(x)

将一个整数转变为一个前缀为`"0b"`的二进制字符串。结果是一个合法的`Python`表达式。如果`x`不是`Python`的`int`对象，那它需要定义 `__index__()`方法返回一个整数
```
>>> bin(3)
'0b11'
>>> bin(-10)
'-0b1010'
```

## class bool([x])

返回一个布尔值，`True`或者`False`。`x`使用标准的`真值测试过程`来转换。如果`x`是假的或者被省略，返回`False`；其他情况返回`True`。`bool`类是`int`的子类。其他类不能继承自它。它只有`False`和`True`两个实例

## breakpoint(*args, **kws)

此函数会在调用时将你陷入调试器中。具体来说，它调用`sys.breakpointhook()`，直接传递`args`和`kws`。默认情况下，`sys.breakpointhook()`调用 `pdb.set_trace()`且没有参数。在这种情况下，它纯粹是一个便利函数，因此您不必显式导入`pdb`且键入尽可能少的代码即可进入调试器

## class bytearray([source[, encoding[, errors]]])

返回一个新的`bytes`数组。`bytearray`类是一个可变序列，包含范围为`0 <= x < 256`的整数。它有可变序列大部分常见的方法，同时有`bytes`类型的大部分方法

## class bytes([source[, encoding[, errors]]])

返回一个新的`bytes`对象，是一个不可变序列，包含范围为`0 <= x < 256`的整数。`bytes`是`bytearray`的不可变版本 - 它有其中不改变序列的方法和相同的索引、切片操作

## callable(object)

如果参数`object`是可调用的就返回`True`，否则返回`False`。如果返回`True`，调用仍可能失败，但如果返回`False`，则调用`object`将肯定不会成功

## chr(i)

返回`Unicode`码位为整数`i`的字符的字符串格式
```
>>> chr(65)
'A'
>>>
```

## @classmethod

把一个方法封装成类方法
```python
class C:
    @classmethod
    def f(cls, arg1, arg2, ...): ...
```

## class complex([real[, imag]])

返回值为`real + imag*1j`的复数，或将字符串或数字转换为复数。如果第一个形参是字符串，则它被解释为一个复数，并且函数调用时必须没有第二个形参。第二个形参不能是字符串。每个实参都可以是任意的数值类型（包括复数）。如果省略了`imag`，则默认值为零，构造函数会像`int`和`float`一样进行数值转换。如果两个实参都省略，则返回`0j`

## delattr(object, name)

`setattr()`相关的函数。实参是一个对象和一个字符串。该字符串必须是对象的某个属性。如果对象允许，该函数将删除指定的属性。例如`delattr(x, 'foobar')`等价于`del x.foobar`

## class dict(**kwarg)
## class dict(mapping, **kwarg)
## class dict(iterable, **kwarg)

创建一个新的字典。`dict`对象是一个字典类

## dir([object])

如果没有实参，则返回当前本地作用域中的名称列表。如果有实参，它会尝试返回该对象的有效属性列表

## divmod(a, b)

它将两个（非复数）数字作为实参，并在执行整数除法时返回一对商和余数。对于混合操作数类型，适用双目算术运算符的规则。对于整数，结果和`(a // b, a % b)`一致。对于浮点数，结果是`(q, a % b)`，`q`通常是`math.floor(a / b)`但可能会比`1`小。在任何情况下，`q * b + a % b`和`a`基本相等；如果`a % b`非零，它的符号和`b`一样，并且`0 <= abs(a % b) < abs(b)`

## enumerate(iterable, start=0)

返回一个枚举对象`iterable`必须是一个序列，或`iterator`，或其他支持迭代的对象。`enumerate()`返回的迭代器的`__next__()`方法返回一个元组，里面包含一个计数值（从`start`开始，默认为`0`）和通过迭代`iterable`获得的值，等价于
```python
def enumerate(sequence, start=0):
    n = start
    for elem in sequence:
        yield n, elem
        n += 1
```

## eval(expression[, globals[, locals]])

实参是一个字符串，以及可选的`globals`和`locals`。`globals`实参必须是一个字典。`locals`可以是任何映射对象。`expression`参数会作为一个`Python`表达式（从技术上说是一个条件列表）被解析并求值，使用`globals`和`locals`字典作为全局和局部命名空间
```
>>> x = 1
>>> eval('x+1')
2
```

## exec(object[, globals[, locals]])

这个函数支持动态执行`Python`代码。`object`必须是字符串或者代码对象。如果是字符串，那么该字符串将被解析为一系列`Python`语句并执行（除非发生语法错误）。如果是代码对象，它将被直接执行。在任何情况下，被执行的代码都需要和文件输入一样是有效的（见参考手册中关于文件输入的章节）。请注意即使在传递给 exec() 函数的代码的上下文中，`return`和`yield`语句也不能在函数定义之外使用。该函数返回值是`None`。
```
>>> def show():
>>>     print(123)
>>>
>>> exec('show()')
123
```

## filter(function, iterable)

用`iterable`中函数`function`返回真的那些元素，构建一个新的迭代器。`iterable`可以是一个序列，一个支持迭代的容器，或一个迭代器。如果`function`是`None`，则会假设它是一个身份函数，即`iterable`中所有返回假的元素会被移除

## class float([x])

返回从数字或字符串`x`生成的浮点数。如果实参是字符串，则它必须是包含十进制数字的字符串，字符串前面可以有符号，之前也可以有空格
```
>>> float('+1.23')
1.23
>>> float('   -12345\n')
-12345.0
>>> float('1e-003')
0.001
>>> float('+1E6')
1000000.0
>>> float('-Infinity')
-inf
```

## class frozenset([iterable])

返回一个新的`frozenset`对象，它包含可选参数`iterable`中的元素。`frozenset`是一个内置的类

## getattr(object, name[, default])

返回对象命名属性的值。`name`必须是字符串。如果该字符串是对象的属性之一，则返回该属性的值

## globals()

返回表示当前全局符号表的字典，这总是当前模块的字典

## hasattr(object, name)

该实参是一个对象和一个字符串。如果字符串是对象的属性之一的名称，则返回`True`，否则返回`False`

## hash(object)

返回该对象的哈希值（如果它有的话）。哈希值是整数。

## help([object])

启动内置的帮助系统（此函数主要在交互式中使用）。如果没有实参，解释器控制台里会启动交互式帮助系统。如果实参是一个字符串，则在模块、函数、类、方法、关键字或文档主题中搜索该字符串，并在控制台上打印帮助信息。如果实参是其他任意对象，则会生成该对象的帮助页。

## hex(x)

将整数转换为以`0x`为前缀的小写十六进制字符串

## id(object)

返回对象的“标识值”。该值是一个整数，在此对象的生命周期中保证是唯一且恒定的。两个生命期不重叠的对象可能具有相同的`id()`值

## input([prompt])

如果存在`prompt`实参，则将其写入标准输出，末尾不带换行符。接下来，该函数从输入中读取一行，将其转换为字符串（除了末尾的换行符）并返回

## class int([x])
## class int(x, base=10)

返回一个使用数字或字符串`x`生成的整数对象，或者没有实参的时候返回 0

如果`x`不是数字，或者有`base`参数，`x`必须是字符串、bytes、表示进制为`base`的 整数字面值的`bytearray`实例

## isinstance(object, classinfo)

如果参数`object`是参数`classinfo`的实例或者是其 (直接、间接或 虚拟) 子类则返回 True。 如果`object`不是给定类型的对象，函数将总是返回 `False`。如果`classinfo`是类型对象元组（或由其他此类元组递归组成的元组），那么如果`object`是其中任何一个类型的实例就返回`True`

## issubclass(class, classinfo)

如果`class`是`classinfo`的 (直接、间接或虚拟) 子类则返回`True`。类会被视作其自身的子类。`classinfo`也以是类对象的元组，在此情况下`classinfo`中的每个条目都将被检查

## iter(object[, sentinel])

返回一个`iterator`对象。根据是否存在第二个实参，第一个实参的解释是非常不同的。如果没有第二个实参，`object`必须是支持迭代协议（有`__iter__()`方法）的集合对象，或必须支持序列协议（有`__getitem__()`方法，且数字参数从`0`开始）

## len(s)

返回对象的长度（元素个数）。实参可以是序列（如`string`、`bytes`、`tuple`、`list`或`range`等）或集合（如`dictionary`、`set`或`frozen set`等）

## class list([iterable])

虽然被称为函数，`list`实际上是一种可变序列类型

## locals()

更新并返回表示当前本地符号表的字典

## map(function, iterable, ...)

返回一个将`function`应用于`iterable`中每一项并输出其结果的迭代器
```python
a = [1, 2, 3, 4, 5, 6, 7, 8, 9]
b = [9, 8, 7, 6, 5, 4, 3, 2, 1]
print(list(map(lambda x, y: x + y + 3, a, b)))
```

## max(iterable, *[, key, default])
## max(arg1, arg2, *args[, key])

返回可迭代对象中最大的元素，或者返回两个及以上实参中最大的
```python
source = [
    {"name": "Ford", "age": 18, "models": ["Fiesta", "Focus", "Mustang"]},
    {"name": "BMW", "age": 38, "models": ["320", "X3", "X5"]},
    {"name": "Fiat", "age": 28, "models": ["500", "Panda"]}
]
print(max(source, key=lambda x: len(x['models'])))
print(max(source, key=lambda x: x['age']))
```

## min(iterable, *[, key, default])
## min(arg1, arg2, *args[, key])

返回可迭代对象中最小的元素，或者返回两个及以上实参中最小的
```python
source = [
    {"name": "Ford", "age": 18, "models": ["Fiesta", "Focus", "Mustang"]},
    {"name": "BMW", "age": 38, "models": ["320", "X3", "X5"]},
    {"name": "Fiat", "age": 28, "models": ["500", "Panda"]}
]
print(min(source, key=lambda x: len(x['models'])))
print(min(source, key=lambda x: x['age']))
```

## next(iterator[, default])

通过调用`iterator`的`__next__()`方法获取下一个元素。如果迭代器耗尽，则返回给定的`default`，如果没有默认值则触发`StopIteration`

## oct(x)

将一个整数转变为一个前缀为“0o”的八进制字符串
```
>>> oct(8)
'0o10'
>>> oct(-56)
'-0o70
```

## open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)

打开`file`并返回对应的`file object`

字符 | 含义
:- | :-
'r' | 读取（默认）
'w' | 写入，并先截断文件
'x' | 排它性创建，如果文件已存在则失败
'a' | 写入，如果文件存在则在末尾追加
'b' | 二进制模式
't' | 文本模式（默认）
'+' | 更新磁盘文件（读取并写入）

## ord(c)

对表示单个`Unicode`字符的字符串，返回代表它`Unicode`码点的整数，这是`chr()`的逆函数

## pow(x, y[, z])

返回`x`的`y`次幂；如果`z`存在，则对`z`取余（比直接`pow(x, y) % z`计算更高效）。两个参数形式的`pow(x, y)`等价于幂运算符：`x**y`

## print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)

将`objects`打印到`file`指定的文本流，以`sep`分隔并在末尾加上`end`。`sep`，`end`，`file`和`flush`如果存在，它们必须以关键字参数的形式给出

## class property(fget=None, fset=None, fdel=None, doc=None)

返回`property`属性

一个典型的用法是定义一个托管属性 x
```python
class C:
    def __init__(self):
        self._x = None

    def getx(self):
        return self._x

    def setx(self, value):
        self._x = value

    def delx(self):
        del self._x

    x = property(getx, setx, delx, "I'm the 'x' property.")
```
装饰器
```python
class Parrot:
    def __init__(self):
        self._voltage = 100000

    @property
    def voltage(self):
        """Get the current voltage."""
        return self._voltage
```
特征属性对象具有 getter, setter 以及`deleter`方法，它们可用作装饰器来创建该特征属性的副本，并将相应的访问函数设为所装饰的函数
```python
class C:
    def __init__(self):
        self._x = None

    @property
    def x(self):
        """I'm the 'x' property."""
        return self._x

    @x.setter
    def x(self, value):
        self._x = value

    @x.deleter
    def x(self):
        del self._x
```

## class range(stop)
## class range(start, stop[, step])

虽然被称为函数，但`range`实际上是一个不可变的序列类型

## repr(object)

返回包含一个对象的可打印表示形式的字符串

## reversed(seq)

返回一个反向的`iterator`。`seq`必须是一个具有`__reversed__()`方法的对象或者是支持该序列协议（具有从`0`开始的整数类型参数的`__len__()`方法和`__getitem__()`方法）

## round(number[, ndigits])

返回`number`舍入到小数点后`ndigits`位精度的值。 如果`ndigits`被省略或为`None`，则返回最接近输入值的整数

## class set([iterable])

返回一个新的`set`对象，可以选择带有从`iterable`获取的元素

## setattr(object, name, value)

此函数与`getattr()`两相对应。 其参数为一个对象、一个字符串和一个任意值。 字符串指定一个现有属性或者新增属性。函数会将值赋给该属性，只要对象允许这种操作

## class slice(stop)
## class slice(start, stop[, step])

返回一个表示由 range(start, stop, step) 所指定索引集的`slice`对象，其中`start`和`step`参数默认为 None

## sorted(iterable, *, key=None, reverse=False)

根据`iterable`中的项返回一个新的已排序列表。

具有两个可选参数，它们都必须指定为关键字参数。

`key`指定带有单个参数的函数，用于从`iterable`的每个元素中提取用于比较的键 (例如 key=str.lower)。 默认值为`None`(直接比较元素)
```python
source = [
    {"name": "Ford", "age": 18, "models": ["Fiesta", "Focus", "Mustang", "Mark"]},
    {"name": "BMW", "age": 38, "models": ["320", "X3", "X5"]},
    {"name": "Fiat", "age": 28, "models": ["500", "Panda"]}
]
source = sorted(source, key=lambda x: len(x['models']))
print(source)
```

## @staticmethod

将方法转换为静态方法

## class str(object='')
## class str(object=b'', encoding='utf-8', errors='strict')

返回一个`str`版本的`object`

## sum(iterable[, start])

从`start`开始自左向右对`iterable`中的项求和并返回总计值。`start`默认为`0`

## super([type[, object-or-type]])

返回一个代理对象，它会将方法调用委托给`type`指定的父类或兄弟类

## class tuple([iterable])

虽然被称为函数，但`tuple`实际上是一个不可变的序列类型

## class type(object)
## class type(name, bases, dict)
传入一个参数时，返回`object`的类型。 返回值是一个`type`对象，通常与`object.__class__`所返回的对象相同

## vars([object])

返回模块、类、实例或任何其它具有`__dict__`属性的对象的`__dict__`属性

## zip(*iterables)

创建一个聚合了来自每个可迭代对象中的元素的迭代器，实现方式类似
```python
def zip(*iterables):
    # zip('ABCD', 'xy') --> Ax By
    sentinel = object()
    iterators = [iter(it) for it in iterables]
    while iterators:
        result = []
        for it in iterators:
            elem = next(it, sentinel)
            if elem is sentinel:
                return
            result.append(elem)
        yield tuple(result)
```
`zip()`与`*`运算符相结合可以用来拆解一个列表
```
>>> x = [1, 2, 3]
>>> y = [4, 5, 6]
>>> zipped = zip(x, y)
>>> list(zipped)
[(1, 4), (2, 5), (3, 6)]
>>> x2, y2 = zip(*zip(x, y))
>>> x == list(x2) and y == list(y2)
True
```

Last Modified 2021-05-01

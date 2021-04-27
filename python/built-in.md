# 内置函数

## abs(x)

返回一个数的绝对值。实参可以是整数或浮点数。如果实参是一个复数，返回它的模

## all(iterable)

如果 iterable 的所有元素为真（或迭代器为空），返回`True`，等价于
```python
def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True
```

## any(iterable)

如果 iterable 的任一元素为真则返回`True`，如果迭代器为空，返回`False`，等价于
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

## @classmethod

把一个方法封装成类方法
```python
class C:
    @classmethod
    def f(cls, arg1, arg2, ...): ...
```

## compile(source, filename, mode, flags=0, dont_inherit=False, optimize=-1)

将 source 编译成代码或 AST 对象。代码对象可以被 exec() 或 eval() 执行。source 可以是常规的字符串、字节字符串，或者 AST 对象
```python
import ast

# my_code.py
# x = 10
# y = 20
# print('Multiplication = ', x * y)

# reading code from a file
f = open('my_code.py', 'r')
code_str = f.read()
f.close()
code = compile(code_str, 'my_code.py', 'exec')
exec(code)

# eval example
x = 5
code = compile('x == 5', '', 'eval')
result = eval(code)
print(result)

code = compile('x + 5', '', 'eval')
result = eval(code)
print(result)

bytes_str = bytes('x == 5', 'utf-8')
print(type(bytes_str))
code = compile(bytes_str, '', 'eval')
result = eval(code)
print(result)

ast_object = ast.parse("print('Hello world!')")
print(type(ast_object))
code = compile(ast_object, filename="", mode="exec")
print(type(code))
exec(code)
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

实参是一个字符串，以及可选的`globals`和`locals`。`globals`实参必须是一个字典。`locals`可以是任何映射对象。expression 参数会作为一个 Python 表达式（从技术上说是一个条件列表）被解析并求值，使用 globals 和 locals 字典作为全局和局部命名空间
```
>>> x = 1
>>> eval('x+1')
2
```
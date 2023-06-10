# 上下文管理

## 特殊的 else

```python
items = range(2, 11, 2)

for item in items:
    print(item)
    if item > 60:
        break
else:
    # 没有被 break 才会执行
    print('the for loop else')

idx = 0

while idx < len(items):
    print(items[idx])
    idx += 1
else:
    # 没有被 break 才会执行
    print('the while loop else')

try:
    print('first of try/except')
except Exception as e:
    print(e)
else:
    # 没有抛出异常才会执行
    print('after try/except')
```

## 基本的上下文

```python
"""
A "mirroring" ``stdout`` context.

While active, the context manager reverses text output to
``stdout``::


    >>> from mirror import LookingGlass
    >>> with LookingGlass() as what:  # <1>
    ...      print('Alice, Kitty and Snowdrop')  # <2>
    ...      print(what)
    ...
    pordwonS dna yttiK ,ecilA
    YKCOWREBBAJ
    >>> what  # <3>
    'JABBERWOCKY'
    >>> print('Back to normal.')  # <4>
    Back to normal.



This exposes the context manager operation::


    >>> from mirror import LookingGlass
    >>> manager = LookingGlass()  # <1>
    >>> manager  # doctest: +ELLIPSIS
    <mirror.LookingGlass object at 0x...>
    >>> monster = manager.__enter__()  # <2>
    >>> monster == 'JABBERWOCKY'  # <3>
    eurT
    >>> monster
    'YKCOWREBBAJ'
    >>> manager  # doctest: +ELLIPSIS
    >... ta tcejbo ssalGgnikooL.rorrim<
    >>> manager.__exit__(None, None, None)  # <4>
    >>> monster
    'JABBERWOCKY'


The context manager can handle and "swallow" exceptions.


    >>> from mirror import LookingGlass
    >>> with LookingGlass():
    ...      print('Humpty Dumpty')
    ...      x = 1/0  # <1>
    ...      print('END')  # <2>
    ...
    ytpmuD ytpmuH
    Please DO NOT divide by zero!
    >>> with LookingGlass():
    ...      print('Humpty Dumpty')
    ...      x = no_such_name  # <1>
    ...      print('END')  # <2>
    ...
    Traceback (most recent call last):
      ...
    NameError: name 'no_such_name' is not defined


"""

import sys


class LookingGlass:

    def __enter__(self):  # <1>
        self.original_write = sys.stdout.write  # <2>
        sys.stdout.write = self.reverse_write  # <3>
        return 'JABBERWOCKY'  # <4>

    def reverse_write(self, text):  # <5>
        self.original_write(text[::-1])

    def __exit__(self, exc_type, exc_value, traceback):  # <6>
        sys.stdout.write = self.original_write  # <7>
        if exc_type is ZeroDivisionError:  # <8>
            print('Please DO NOT divide by zero!')
            return True  # <9>
        # <10>
```

```python
"""
A "mirroring" ``stdout`` context manager.

While active, the context manager reverses text output to
``stdout``::


    >>> from mirror_gen import looking_glass
    >>> with looking_glass() as what:  # <1>
    ...      print('Alice, Kitty and Snowdrop')
    ...      print(what)
    ...
    pordwonS dna yttiK ,ecilA
    YKCOWREBBAJ
    >>> what
    'JABBERWOCKY'
    >>> print('back to normal')
    back to normal




This exposes the context manager operation::


    >>> from mirror_gen import looking_glass
    >>> manager = looking_glass()  # <1>
    >>> manager  # doctest: +ELLIPSIS
    <contextlib._GeneratorContextManager object at 0x...>
    >>> monster = manager.__enter__()  # <2>
    >>> monster == 'JABBERWOCKY'  # <3>
    eurT
    >>> monster
    'YKCOWREBBAJ'
    >>> manager  # doctest: +ELLIPSIS
    >...x0 ta tcejbo reganaMtxetnoCrotareneG_.biltxetnoc<
    >>> manager.__exit__(None, None, None)  # <4>
    False
    >>> monster
    'JABBERWOCKY'


The decorated generator also works as a decorator:


    >>> @looking_glass()
    ... def verse():
    ...     print('The time has come')
    ...
    >>> verse()  # <1>
    emoc sah emit ehT
    >>> print('back to normal')  # <2>
    back to normal


"""

import contextlib
import sys


@contextlib.contextmanager  # <1>
def looking_glass():
    original_write = sys.stdout.write  # <2>

    def reverse_write(text):  # <3>
        original_write(text[::-1])

    sys.stdout.write = reverse_write  # <4>
    yield 'JABBERWOCKY'  # <5>
    sys.stdout.write = original_write  # <6>
```

## 考虑错误处理

```python
"""
A "mirroring" ``stdout`` context manager.

While active, the context manager reverses text output to
``stdout``::

    >>> from mirror_gen import looking_glass
    >>> with looking_glass() as what:  # <1>
    ...      print('Alice, Kitty and Snowdrop')
    ...      print(what)
    ...
    pordwonS dna yttiK ,ecilA
    YKCOWREBBAJ
    >>> what
    'JABBERWOCKY'



This exposes the context manager operation::

    >>> from mirror_gen import looking_glass
    >>> manager = looking_glass()  # <1>
    >>> manager  # doctest: +ELLIPSIS
    <contextlib._GeneratorContextManager object at 0x...>
    >>> monster = manager.__enter__()  # <2>
    >>> monster == 'JABBERWOCKY'  # <3>
    eurT
    >>> monster
    'YKCOWREBBAJ'
    >>> manager  # doctest: +ELLIPSIS
    >...x0 ta tcejbo reganaMtxetnoCrotareneG_.biltxetnoc<
    >>> manager.__exit__(None, None, None)  # <4>
    False
    >>> monster
    'JABBERWOCKY'


The context manager can handle and "swallow" exceptions.
The following test does not pass under doctest (a
ZeroDivisionError is reported by doctest) but passes
if executed by hand in the Python 3 console (the exception
is handled by the context manager):

    >>> from mirror_gen_exc import looking_glass
    >>> with looking_glass():
    ...      print('Humpty Dumpty')
    ...      x = 1/0  # <1>
    ...      print('END')  # <2>
    ...
    ytpmuD ytpmuH
    Please DO NOT divide by zero!


    >>> with looking_glass():
    ...      print('Humpty Dumpty')
    ...      x = no_such_name  # <1>
    ...      print('END')  # <2>
    ...
    Traceback (most recent call last):
      ...
    NameError: name 'no_such_name' is not defined

"""

import contextlib
import sys


@contextlib.contextmanager
def looking_glass():
    original_write = sys.stdout.write

    def reverse_write(text):
        original_write(text[::-1])

    sys.stdout.write = reverse_write
    msg = ''  # <1>
    try:
        yield 'JABBERWOCKY'
    except ZeroDivisionError:  # <2>
        msg = 'Please DO NOT divide by zero!'
    finally:
        sys.stdout.write = original_write  # <3>
        if msg:
            print(msg)  # <4>
```

Last Modified 2023-06-10

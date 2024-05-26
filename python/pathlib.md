# pathlib

面向对象的文件系统路径，简化文件系统操作的利器

在 Python 中处理文件和目录是一项常见任务，传统的方式通常依赖于字符串操作和 `os` 模块提供的函数。然而，这种方式往往繁琐且容易出错，特别是当涉及复杂路径操作时。为了解决这一问题，Python 3.4 版本引入了 `pathlib` 库，提供了一种面向对象的、更加简洁直观的方案来操作文件系统。

`pathlib` 库的核心是 `Path` 类，它表示文件系统中的一个路径，可以是文件或目录。与字符串相比，`Path` 对象提供了丰富的属性和方法，使我们能够更轻松地进行各种文件系统操作。

## `Path` 对象：文件系统路径的抽象

`Path` 对象可以创建自字符串路径，并提供多种方法方便地访问路径信息。

```python
from pathlib import Path

# 创建一个 Path 对象，指向当前目录
current_dir = Path('.')

# 获取路径的绝对路径
absolute_path = current_dir.resolve()

# 检查路径是否存在
exists = current_dir.exists()

# 判断路径是否为文件
is_file = current_dir.is_file()

# 判断路径是否为目录
is_dir = current_dir.is_dir()

# 获取路径的父目录
parent_dir = current_dir.parent

# 获取路径的名称
name = current_dir.name

# 获取路径的扩展名
suffix = current_dir.suffix

# 获取路径的统计信息
stat_info = current_dir.stat()

# 获取文件的最后修改时间
modified_time = stat_info.st_mtime
```

## 文件系统操作：简洁高效

`Path` 对象提供了多种方法用于进行文件系统操作，包括创建、读取、写入、删除等。

**创建文件和目录:**

```python
from pathlib import Path

# 创建文件
new_file = Path('new_file.txt')
new_file.touch()  # 创建空文件

# 创建目录
new_dir = Path('new_dir')
new_dir.mkdir()

# 创建多个嵌套目录
Path('nested_dir/sub_dir').mkdir(parents=True, exist_ok=True)  # 创建不存在的父目录
```

**读取和写入文件:**

```python
from pathlib import Path

# 创建文件
new_file = Path('new_file.txt')

# 写入文件内容
with open(new_file, 'w') as f:
    f.write('Hello, world!')

# 读取文件内容
with open(new_file, 'r') as f:
    content = f.read()
```

**删除文件和目录:**

```python
from pathlib import Path

# 创建文件
new_file = Path('new_file.txt')

# 删除文件
new_file.unlink()

# 删除目录
new_dir.rmdir()

# 递归删除目录及子目录和文件
new_dir.rmtree()
```

## 文件系统遍历：简化操作

`Path` 对象提供了 `iterdir` 方法，用于遍历目录中的所有文件和子目录。

```python
from pathlib import Path

# 创建一个 Path 对象，指向当前目录
current_dir = Path('.')

# 遍历当前目录中的所有文件
for item in current_dir.iterdir():
    print(item)

# 递归遍历当前目录及其子目录
for item in current_dir.rglob('*'):
    print(item)

# 遍历目录中所有以 .txt 结尾的文件
for item in current_dir.glob('*.txt'):
    print(item)
```

## 总结

`pathlib` 库以面向对象的方式提供了一个简洁而强大的工具，简化了 Python 中的文件系统操作。与传统的字符串操作相比，`pathlib` 提供了更直观、易于理解的语法，并避免了潜在的错误。对于任何需要进行文件系统操作的 Python 开发人员而言，`pathlib` 都是不可或缺的工具。

## 附加说明

- `pathlib` 库还提供了其他一些更高级的功能，如路径的相对路径转换、符号链接操作等等。
- `pathlib` 库的详细文档可以参考官方网站：[https://docs.python.org/3/library/pathlib.html](https://docs.python.org/3/library/pathlib.html)

希望本文能帮助您更好地理解和使用 `pathlib` 库。

Last Modified 2024-05-26

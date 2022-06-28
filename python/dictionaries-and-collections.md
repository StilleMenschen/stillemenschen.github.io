# 字典与集合

```python
import sys
import re
import collections

WORD_RE = re.compile(r'\w+')

index = collections.defaultdict(list)
# 读取参数传递的文本文件中的单词并统计出现位置
with open(sys.argv[1], 'r', encoding='UTF-8') as fp:
    for line_no, line in enumerate(fp, 1):
        for match in WORD_RE.finditer(line):
            word = match.group()
            column_no = match.start() + 1
            location = line_no, column_no
            index[word].append(location)

for word in sorted(index.keys(), key=str.upper):
    print(word, index[word])
```

```python
import collections


class StrKeyDict(collections.UserDict):
    def __missing__(self, key):
        if isinstance(key, str):
            raise KeyError(key)
        return self[str(key)]

    def __contains__(self, key):
        return str(key) in self.data

    def __setitem__(self, key, value):
        self.data[str(key)] = value


d = StrKeyDict([('2', 'two',), ('4', 'four',)])
print(d['2'])
print(d[2])
print(d.get(3, 'N/A'))
print(1 in d)
print(d[True])
```

Last Modified 2022-06-28

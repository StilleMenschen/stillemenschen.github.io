# 异步 IO

asyncio 事件循环在背后调用`.send`驱动你的协程，而协程使用 await 等待其他协程，包括库提供的协程。await 的实现大量借鉴 yield from，也调用`.send`驱动协程。

await 链最终到达一个底层可异步调用对象，返回一个生成器，由事件循环驱动，对计时器或网络 I/O 等事件做出响应。位于 await 链末端的底层可异步调用对象深埋在库的实现中，不作为 API 开放，有可能是 Python/C 扩展。

使用 asyncio.gather 和 asyncio.create_task 等函数可以启动多个并发 await 通道，在单个线程内由单个事件循环驱动多个 I/O 操作并发执行。

async with 语句用于异步的上下文管理器，即一种以协程实现`__aenter__`和`__aexit__`方法的对象

## 线程与异步协程

```python
# spinner_thread.py

# credits: Adapted from Michele Simionato's
# multiprocessing example in the python-list:
# https://mail.python.org/pipermail/python-list/2009-February/675659.html

import itertools
import time
from threading import Thread, Event

def spin(msg: str, done: Event) -> None:  # <1>
    for char in itertools.cycle(r'\|/-'):  # <2>
        status = f'\r{char} {msg}'  # <3>
        print(status, end='', flush=True)
        if done.wait(.1):  # <4>
            break  # <5>
    blanks = ' ' * len(status)
    print(f'\r{blanks}\r', end='')  # <6>

def slow() -> int:
    time.sleep(3)  # <7>
    return 42

def supervisor() -> int:  # <1>
    done = Event()  # <2>
    spinner = Thread(target=spin, args=('thinking!', done))  # <3>
    print(f'spinner object: {spinner}')  # <4>
    spinner.start()  # <5>
    result = slow()  # <6>
    done.set()  # <7>
    spinner.join()  # <8>
    return result

def main() -> None:
    result = supervisor()  # <9>
    print(f'Answer: {result}')

if __name__ == '__main__':
    main()
```

```python
# spinner_async.py

# credits: Example by Luciano Ramalho inspired by
# Michele Simionato's multiprocessing example in the python-list:
# https://mail.python.org/pipermail/python-list/2009-February/675659.html

import asyncio
import itertools

async def spin(msg: str) -> None:  # <1>
    for char in itertools.cycle(r'\|/-'):
        status = f'\r{char} {msg}'
        print(status, flush=True, end='')
        try:
            await asyncio.sleep(.1)  # <2>
        except asyncio.CancelledError:  # <3>
            break
    blanks = ' ' * len(status)
    print(f'\r{blanks}\r', end='')

async def slow() -> int:
    await asyncio.sleep(3)  # <4>
    return 42

def main() -> None:  # <1>
    result = asyncio.run(supervisor())  # <2>
    print(f'Answer: {result}')

async def supervisor() -> int:  # <3>
    spinner = asyncio.create_task(spin('thinking!'))  # <4>
    print(f'spinner object: {spinner}')  # <5>
    result = await slow()  # <6>
    spinner.cancel()  # <7>
    return result

if __name__ == '__main__':
    main()
```

charfinder.py

```python
#!/usr/bin/env python3

# charfinder.py

"""
Unicode's character finder utility:
find characters based on words in their official names.

This can be used from the command line, just pass words as arguments.

Here is the ``main`` function which makes it happen::

    >>> main('rook')  # doctest: +NORMALIZE_WHITESPACE
    U+2656  ♖  WHITE CHESS ROOK
    U+265C  ♜  BLACK CHESS ROOK
    (2 matches for 'rook')
    >>> main('rook', 'black')  # doctest: +NORMALIZE_WHITESPACE
    U+265C  ♜  BLACK CHESS ROOK
    (1 match for 'rook black')
    >>> main('white bishop')  # doctest: +NORMALIZE_WHITESPACE
    U+2657  ♗   WHITE CHESS BISHOP
    (1 match for 'white bishop')
    >>> main("jabberwocky's vest")
    (No match for "jabberwocky's vest")


For exploring words that occur in the character names, there is the
``word_report`` function::

    >>> index = UnicodeNameIndex(sample_chars)
    >>> index.word_report()
        3 SIGN
        2 A
        2 EURO
        2 LATIN
        2 LETTER
        1 CAPITAL
        1 CURRENCY
        1 DOLLAR
        1 SMALL
    >>> index = UnicodeNameIndex()
    >>> index.word_report(10)
    75821 CJK
    75761 IDEOGRAPH
    74656 UNIFIED
    13196 SYLLABLE
    11735 HANGUL
     7616 LETTER
     2232 WITH
     2180 SIGN
     2122 SMALL
     1709 CAPITAL

Note: characters with names starting with 'CJK UNIFIED IDEOGRAPH'
are indexed with those three words only, excluding the hexadecimal
codepoint at the end of the name.

"""

import sys
import re
import unicodedata
import pickle
import warnings
import itertools
import functools
from collections import namedtuple

RE_WORD = re.compile(r'\w+')
RE_UNICODE_NAME = re.compile('^[A-Z0-9 -]+$')
RE_CODEPOINT = re.compile(r'U\+([0-9A-F]{4,6})')

INDEX_NAME = 'charfinder_index.pickle'
MINIMUM_SAVE_LEN = 10000
CJK_UNI_PREFIX = 'CJK UNIFIED IDEOGRAPH'
CJK_CMP_PREFIX = 'CJK COMPATIBILITY IDEOGRAPH'

sample_chars = [
    '$',  # DOLLAR SIGN
    'A',  # LATIN CAPITAL LETTER A
    'a',  # LATIN SMALL LETTER A
    '\u20a0',  # EURO-CURRENCY SIGN
    '\u20ac',  # EURO SIGN
]

CharDescription = namedtuple('CharDescription', 'code_str char name')

QueryResult = namedtuple('QueryResult', 'count items')


def tokenize(text):
    """return iterable of uppercased words"""
    for match in RE_WORD.finditer(text):
        yield match.group().upper()


def query_type(text):
    text_upper = text.upper()
    if 'U+' in text_upper:
        return 'CODEPOINT'
    elif RE_UNICODE_NAME.match(text_upper):
        return 'NAME'
    else:
        return 'CHARACTERS'


class UnicodeNameIndex:

    def __init__(self, chars=None):
        self.index = None
        self.load(chars)

    def load(self, chars=None):
        self.index = None
        if chars is None:
            try:
                with open(INDEX_NAME, 'rb') as fp:
                    self.index = pickle.load(fp)
            except OSError:
                pass
        if self.index is None:
            self.build_index(chars)
        if len(self.index) > MINIMUM_SAVE_LEN:
            try:
                self.save()
            except OSError as exc:
                warnings.warn('Could not save {!r}: {}'
                              .format(INDEX_NAME, exc))

    def save(self):
        with open(INDEX_NAME, 'wb') as fp:
            pickle.dump(self.index, fp)

    def build_index(self, chars=None):
        if chars is None:
            chars = (chr(i) for i in range(32, sys.maxunicode))
        index = {}
        for char in chars:
            try:
                name = unicodedata.name(char)
            except ValueError:
                continue
            if name.startswith(CJK_UNI_PREFIX):
                name = CJK_UNI_PREFIX
            elif name.startswith(CJK_CMP_PREFIX):
                name = CJK_CMP_PREFIX

            for word in tokenize(name):
                index.setdefault(word, set()).add(char)

        self.index = index

    def word_rank(self, top=None):
        res = [(len(self.index[key]), key) for key in self.index]
        res.sort(key=lambda item: (-item[0], item[1]))
        if top is not None:
            res = res[:top]
        return res

    def word_report(self, top=None):
        for postings, key in self.word_rank(top):
            print('{:5} {}'.format(postings, key))

    def find_chars(self, query, start=0, stop=None):
        stop = sys.maxsize if stop is None else stop
        result_sets = []
        for word in tokenize(query):
            chars = self.index.get(word)
            if chars is None:  # shorcut: no such word
                result_sets = []
                break
            result_sets.append(chars)

        if not result_sets:
            return QueryResult(0, ())

        result = functools.reduce(set.intersection, result_sets)
        result = sorted(result)  # must sort to support start, stop
        result_iter = itertools.islice(result, start, stop)
        return QueryResult(len(result),
                           (char for char in result_iter))

    def describe(self, char):
        code_str = 'U+{:04X}'.format(ord(char))
        name = unicodedata.name(char)
        return CharDescription(code_str, char, name)

    def find_descriptions(self, query, start=0, stop=None):
        for char in self.find_chars(query, start, stop).items:
            yield self.describe(char)

    def get_descriptions(self, chars):
        for char in chars:
            yield self.describe(char)

    def describe_str(self, char):
        return '{:7}\t{}\t{}'.format(*self.describe(char))

    def find_description_strs(self, query, start=0, stop=None):
        for char in self.find_chars(query, start, stop).items:
            yield self.describe_str(char)

    @staticmethod  # not an instance method due to concurrency
    def status(query, counter):
        if counter == 0:
            msg = 'No match'
        elif counter == 1:
            msg = '1 match'
        else:
            msg = '{} matches'.format(counter)
        return '{} for {!r}'.format(msg, query)


def main(*args):
    index = UnicodeNameIndex()
    query = ' '.join(args)
    n = 0
    for n, line in enumerate(index.find_description_strs(query), 1):
        print(line)
    print('({})'.format(index.status(query, n)))


if __name__ == '__main__':
    if len(sys.argv) > 1:
        main(*sys.argv[1:])
    else:
        print('Usage: {} word1 [word2]...'.format(sys.argv[0]))
```

test_charfinder.py

```python
import pytest

from charfinder import UnicodeNameIndex, tokenize, sample_chars, query_type
from unicodedata import name


@pytest.fixture
def sample_index():
    return UnicodeNameIndex(sample_chars)


@pytest.fixture(scope="module")
def full_index():
    return UnicodeNameIndex()


def test_query_type():
    assert query_type('blue') == 'NAME'


def test_tokenize():
    assert list(tokenize('')) == []
    assert list(tokenize('a b')) == ['A', 'B']
    assert list(tokenize('a-b')) == ['A', 'B']
    assert list(tokenize('abc')) == ['ABC']
    assert list(tokenize('café')) == ['CAFÉ']


def test_index():
    sample_index = UnicodeNameIndex(sample_chars)
    assert len(sample_index.index) == 9


def test_find_word_no_match(sample_index):
    res = sample_index.find_chars('qwertyuiop')
    assert len(res.items) == 0


def test_find_word_1_match(sample_index):
    res = [(ord(char), name(char))
           for char in sample_index.find_chars('currency').items]
    assert res == [(8352, 'EURO-CURRENCY SIGN')]


def test_find_word_1_match_character_result(sample_index):
    res = [name(char) for char in
           sample_index.find_chars('currency').items]
    assert res == ['EURO-CURRENCY SIGN']


def test_find_word_2_matches(sample_index):
    res = [(ord(char), name(char))
           for char in sample_index.find_chars('Euro').items]
    assert res == [(8352, 'EURO-CURRENCY SIGN'),
                   (8364, 'EURO SIGN')]


def test_find_2_words_no_matches(sample_index):
    res = sample_index.find_chars('Euro letter')
    assert res.count == 0


def test_find_2_words_no_matches_because_one_not_found(sample_index):
    res = sample_index.find_chars('letter qwertyuiop')
    assert res.count == 0


def test_find_2_words_1_match(sample_index):
    res = sample_index.find_chars('sign dollar')
    assert res.count == 1


def test_find_2_words_2_matches(sample_index):
    res = sample_index.find_chars('latin letter')
    assert res.count == 2


def test_find_chars_many_matches_full(full_index):
    res = full_index.find_chars('letter')
    assert res.count > 7000


def test_find_1_word_1_match_full(full_index):
    res = [(ord(char), name(char))
           for char in full_index.find_chars('registered').items]
    assert res == [(174, 'REGISTERED SIGN')]


def test_find_1_word_2_matches_full(full_index):
    res = full_index.find_chars('rook')
    assert res.count >= 2


def test_find_3_words_no_matches_full(full_index):
    res = full_index.find_chars('no such character')
    assert res.count == 0


def test_find_with_start(sample_index):
    res = [(ord(char), name(char))
           for char in sample_index.find_chars('sign', 1).items]
    assert res == [(8352, 'EURO-CURRENCY SIGN'), (8364, 'EURO SIGN')]


def test_find_with_stop(sample_index):
    res = [(ord(char), name(char))
           for char in sample_index.find_chars('sign', 0, 2).items]
    assert res == [(36, 'DOLLAR SIGN'), (8352, 'EURO-CURRENCY SIGN')]


def test_find_with_start_stop(sample_index):
    res = [(ord(char), name(char))
           for char in sample_index.find_chars('sign', 1, 2).items]
    assert res == [(8352, 'EURO-CURRENCY SIGN')]
```

## TCP 服务

```python
#!/usr/bin/env python

"""
Class ``InvertedIndex`` builds an inverted index mapping each word to
the set of Unicode characters which contain that word in their names.

Optional arguments to the constructor are ``first`` and ``last+1``
character codes to index, to make testing easier. In the examples
below, only the ASCII range was indexed.

The `entries` attribute is a `defaultdict` with uppercased single
words as keys::

    >>> idx = InvertedIndex(32, 128)
    >>> idx.entries['DOLLAR']
    {'$'}
    >>> sorted(idx.entries['SIGN'])
    ['#', '$', '%', '+', '<', '=', '>']
    >>> idx.entries['A'] & idx.entries['SMALL']
    {'a'}
    >>> idx.entries['BRILLIG']
    set()

The `.search()` method takes a string, uppercases it, splits it into
words, and returns the intersection of the entries for each word::

    >>> idx.search('capital a')
    {'A'}

"""

import sys
import time
from collections import defaultdict
from collections.abc import Iterator

import unicodedata

STOP_CODE: int = sys.maxunicode + 1

Char = str
Index = defaultdict[str, set[Char]]


def tokenize(text: str) -> Iterator[str]:
    """return iterator of uppercased words"""
    for word in text.upper().replace('-', ' ').split():
        yield word


class InvertedIndex:
    entries: Index

    def __init__(self, start: int = 32, stop: int = STOP_CODE):
        t0 = time.perf_counter()
        entries: Index = defaultdict(set)
        # 索引所有字符
        for char in (chr(i) for i in range(start, stop)):
            name = unicodedata.name(char, '')
            if name:
                for word in tokenize(name):
                    entries[word].add(char)
        self.entries = entries
        print('initialize time', time.perf_counter() - t0, 's')

    def search(self, query: str) -> set[Char]:
        if words := list(tokenize(query)):
            found = self.entries[words[0]]
            return found.intersection(*(self.entries[w] for w in words[1:]))
        else:
            return set()


def format_results(chars: set[Char]) -> Iterator[str]:
    for char in sorted(chars):
        name = unicodedata.name(char)
        code = ord(char)
        yield f'U+{code:04X}\t{char}\t{name}'


def main(words: list[str]) -> None:
    if not words:
        print('Please give one or more words to search.')
        sys.exit(2)  # command line usage error
    index = InvertedIndex()
    chars = index.search(' '.join(words))
    for line in format_results(chars):
        print(line)
    print('─' * 66, f'{len(chars)} found')


if __name__ == '__main__':
    main(sys.argv[1:])
```

```python
#!/usr/bin/env python3

import asyncio
import functools
import sys
from asyncio.trsock import TransportSocket
from typing import cast

from charindex import InvertedIndex, format_results  # <1>

CRLF = b'\r\n'
PROMPT = b'?> '


async def finder(index: InvertedIndex,  # <2>
                 reader: asyncio.StreamReader,
                 writer: asyncio.StreamWriter) -> None:
    # 获得客户端信息
    client = writer.get_extra_info('peername')  # <3>
    while True:  # <4>
        writer.write(PROMPT)  # can't await!  # <5>
        # 等待写入响应数据完成
        await writer.drain()  # must await!  # <6>
        data = await reader.readline()  # <7>
        if not data:  # <8>
            # 没有接收到数据则结束连接
            break
        try:
            # 获得数据（默认以 UTF-8 字符编码）
            query = data.decode().strip()  # <9>
        except UnicodeDecodeError:  # <10>
            # 如果出现解码错误的默认一个空字符
            query = '\x00'
        print(f' From {client}: {query!r}')  # <11>
        if query:
            # 如果是控制字符则结束连接
            if ord(query[:1]) < 32:  # <12>
                break
            results = await search(query, index, writer)  # <13>
            print(f'   To {client}: {results} results.')  # <14>

    writer.close()  # <15>
    # 等待关闭
    await writer.wait_closed()  # <16>
    print(f'Close {client}.')  # <17>


async def search(query: str,  # <1>
                 index: InvertedIndex,
                 writer: asyncio.StreamWriter) -> int:
    chars = index.search(query)  # <2>
    lines = (line.encode() + CRLF for line  # <3>
             in format_results(chars))
    writer.writelines(lines)  # <4>
    # 等待写入数据完成
    await writer.drain()  # <5>
    status_line = f'{"─" * 66} {len(chars)} found'  # <6>
    writer.write(status_line.encode() + CRLF)
    # 等待写入数据完成
    await writer.drain()
    return len(chars)


async def supervisor(index: InvertedIndex, host: str, port: int) -> None:
    # 当一个新的客户端连接被建立时，回调函数 client_connected_cb 会被调用。该函数会接收到一对参数 (reader, writer)，
    # reader是类 StreamReader 的实例，而writer是类 StreamWriter 的实例。
    # 这里利用 partial 冻结了第一个参数
    server = await asyncio.start_server(functools.partial(finder, index), host, port)  # <3>

    socket_list = cast(tuple[TransportSocket, ...], server.sockets)  # <4>
    addr = socket_list[0].getsockname()
    print(f'Serving on {addr}. Hit CTRL-C to stop.')  # <5>
    # 持续运行
    await server.serve_forever()  # <6>


def main(host: str = '127.0.0.1', port_arg: str = '2323'):
    port = int(port_arg)
    print('Building index.')
    # 创建倒排索引
    index = InvertedIndex()  # <7>
    try:
        # 启动事件循环
        asyncio.run(supervisor(index, host, port))  # <8>
    except KeyboardInterrupt:  # <9>
        print('\nServer shut down.')


if __name__ == '__main__':
    main(*sys.argv[1:])
```

```bash
curl -vvv telnet://127.0.0.1:2323
```

```cmd
telnet 127.0.0.1 2323
```

## HTTP 服务

```python
#!/usr/bin/env python3

import sys
import asyncio
from aiohttp import web

from charfinder import UnicodeNameIndex

TEMPLATE_NAME = 'http_charfinder.html'
CONTENT_TYPE = 'text/html'
SAMPLE_WORDS = ('bismillah chess cat circled Malayalam digit'
                ' Roman face Ethiopic black mark symbol dot'
                ' operator Braille hexagram').split()

ROW_TPL = '<tr><td>{code_str}</td><th>{char}</th><td>{name}</td></tr>'
LINK_TPL = '<a href="/?query={0}" title="find &quot;{0}&quot;">{0}</a>'
LINKS_HTML = ', '.join(LINK_TPL.format(word) for word in
                       sorted(SAMPLE_WORDS, key=str.upper))

index = UnicodeNameIndex()
with open(TEMPLATE_NAME) as tpl:
    template = tpl.read()
template = template.replace('{links}', LINKS_HTML)


async def home(request: web.Request):  # <1>
    query = request.query.get('query', '').strip()  # <2>
    print('Query: {!r}'.format(query))  # <3>
    if query:  # <4>
        descriptions = list(index.find_descriptions(query))
        res = '\n'.join(ROW_TPL.format(**descr._asdict())
                        for descr in descriptions)
        msg = index.status(query, len(descriptions))
    else:
        descriptions = []
        res = ''
        msg = 'Enter words describing characters.'

    html = template.format(query=query, result=res,  # <5>
                           message=msg)
    print('Sending {} results'.format(len(descriptions)))  # <6>
    return web.Response(content_type=CONTENT_TYPE, text=html)  # <7>


def main(address="127.0.0.1", port=8888):
    port = int(port)
    app = web.Application()  # <2>
    app.router.add_get('/', home)  # <3>
    print('Serving on {}. Hit CTRL-C to stop.'.format(address))
    web.run_app(app, host=address, port=port, loop=asyncio.new_event_loop())
    print('Server shutting down.')


if __name__ == '__main__':
    # python3 http_charfinder.py 127.0.0.1 9527
    main(*sys.argv[1:])
```

## 域名检测

```python
import asyncio
import socket
from keyword import kwlist

MAX_KEYWORD_LEN = 8  # <1>


async def probe(domain: str) -> tuple[str, bool]:  # <2>
    loop = asyncio.get_running_loop()  # <3>
    try:
        await loop.getaddrinfo(domain, None)  # <4>
    except socket.gaierror:
        return domain, False
    return domain, True


async def main() -> None:  # <5>
    names = (kw for kw in kwlist if len(kw) <= MAX_KEYWORD_LEN)  # <6>
    domains = (f'{name}.dev'.lower() for name in names)  # <7>
    coros = [probe(domain) for domain in domains]  # <8>
    for coro in asyncio.as_completed(coros):  # <9>
        domain, found = await coro  # <10>
        mark = '+' if found else ' '
        print(f'{mark} {domain}')


if __name__ == '__main__':
    asyncio.run(main())  # <11>
```

## 异步迭代

```python
"""domainlib.py
"""

import asyncio
import socket
from collections.abc import Iterable, AsyncIterator
from typing import NamedTuple, Optional


class Result(NamedTuple):  # <1>
    domain: str
    found: bool


OptionalLoop = Optional[asyncio.AbstractEventLoop]  # <2>


async def probe(domain: str, loop: OptionalLoop = None) -> Result:  # <3>
    if loop is None:
        loop = asyncio.get_running_loop()
    try:
        await loop.getaddrinfo(domain, None)
    except socket.gaierror:
        return Result(domain, False)
    return Result(domain, True)


async def multi_probe(domains: Iterable[str]) -> AsyncIterator[Result]:  # <4>
    loop = asyncio.get_running_loop()
    coros = [probe(domain, loop) for domain in domains]  # <5>
    for coro in asyncio.as_completed(coros):  # <6>
        result = await coro  # <7>
        yield result  # <8>


async def main():
    names = 'python.org go-lang.org rust-lang.org no-lang.invalid'.split()
    gen_found = (name async for name, found in multi_probe(names) if found)
    async for name in gen_found:
        print(name)
    coros = [probe(name) for name in names]
    results = await asyncio.gather(*coros)
    print(results)


if __name__ == '__main__':
    asyncio.run(main())
```

```python
#!/usr/bin/env python3
import asyncio
import sys
from keyword import kwlist

from domainlib import multi_probe


async def main(tld: str) -> None:
    tld = tld.strip('.')
    names = (kw for kw in kwlist if len(kw) <= 4)  # <1>
    domains = (f'{name}.{tld}'.lower() for name in names)  # <2>
    print('FOUND\t\tNOT FOUND')  # <3>
    print('=====\t\t=========')
    async for domain, found in multi_probe(domains):  # <4>
        indent = '' if found else '\t\t'  # <5>
        print(f'{indent}{domain}')


if __name__ == '__main__':
    if len(sys.argv) == 2:
        asyncio.run(main(sys.argv[1]))  # <6>
    else:
        print('Please provide a TLD.', f'Example: {sys.argv[0]} COM.BR')
```

```bash
python domaincheck.py .dev
```

## 参考文档

- 协程与任务 https://docs.python.org/zh-cn/3/library/asyncio-task.html
- 事件循环 https://docs.python.org/zh-cn/3/library/asyncio-eventloop.html

Last Modified 2023-06-12

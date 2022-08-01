# futures 模块

## 线程池

```python
"""
Experiment with ``ThreadPoolExecutor.map``
"""
from time import strftime
from concurrent import futures
import math

PRIMES = [
    112272535095293, 112582705942171, 112272535095293, 115280095190773,
    115797848077099, 1099726899285419, 112545606449, 4894348954034, 4236544062,
    11112324353465, 12134123432, 423578924023, 12433323433, 12334452364
]


def is_prime(n):
    def done():
        display('is_prime({}): done.'.format(n))

    msg = 'is_prime({}): doing calculating...'
    display(msg.format(n))
    if n % 2 == 0:
        done()
        return False
    sqrt_n = int(math.floor(math.sqrt(n)))
    for i in range(3, sqrt_n + 1, 2):
        if n % i == 0:
            done()
            return False
    done()
    return True


def display(message):  # <1>
    print('{} {}'.format(strftime('[%H:%M:%S]'), message))


def main():
    display('Script starting.')
    executor = futures.ThreadPoolExecutor(max_workers=3)  # <4>
    results = executor.map(is_prime, PRIMES)  # <5>
    display('results: {}'.format(results))  # <6>.
    display('Waiting for individual results:')
    for i, result in enumerate(results):  # <7>
        display('result {}: {}'.format(i, result))


if __name__ == '__main__':
    main()
```

## 国家旗帜下载

### 公共模块

```python
"""Utilities for second set of flag examples.
"""

import os
import time
import sys
import string
import argparse
from collections import namedtuple
from enum import Enum

Result = namedtuple('Result', 'status data')

HTTPStatus = Enum('Status', 'ok not_found error')

POP20_CC = ('CN IN US ID BR PK NG BD RU JP '
            'MX PH VN ET EG DE IR TR CD FR').split()

DEFAULT_CONCUR_REQ = 1
MAX_CONCUR_REQ = 1

SERVERS = {
    'REMOTE': 'http://flupy.org/data/flags',
    'LOCAL': 'http://localhost:8001/flags',
    'DELAY': 'http://localhost:8002/flags',
    'ERROR': 'http://localhost:8003/flags',
}
DEFAULT_SERVER = 'LOCAL'

DEST_DIR = 'downloads/'
COUNTRY_CODES_FILE = 'country_codes.txt'


def save_flag(img, filename):
    path = os.path.join(DEST_DIR, filename)
    with open(path, 'wb') as fp:
        fp.write(img)


def initial_report(cc_list, actual_req, server_label):
    if len(cc_list) <= 10:
        cc_msg = ', '.join(cc_list)
    else:
        cc_msg = 'from {} to {}'.format(cc_list[0], cc_list[-1])
    print('{} site: {}'.format(server_label, SERVERS[server_label]))
    msg = 'Searching for {} flag{}: {}'
    plural = 's' if len(cc_list) != 1 else ''
    print(msg.format(len(cc_list), plural, cc_msg))
    plural = 's' if actual_req != 1 else ''
    msg = '{} concurrent connection{} will be used.'
    print(msg.format(actual_req, plural))


def final_report(cc_list, counter, start_time):
    elapsed = time.time() - start_time
    print('-' * 20)
    msg = '{} flag{} downloaded.'
    plural = 's' if counter[HTTPStatus.ok] != 1 else ''
    print(msg.format(counter[HTTPStatus.ok], plural))
    if counter[HTTPStatus.not_found]:
        print(counter[HTTPStatus.not_found], 'not found.')
    if counter[HTTPStatus.error]:
        plural = 's' if counter[HTTPStatus.error] != 1 else ''
        print('{} error{}.'.format(counter[HTTPStatus.error], plural))
    print('Elapsed time: {:.2f}s'.format(elapsed))


def expand_cc_args(every_cc, all_cc, cc_args, limit):
    codes = set()
    A_Z = string.ascii_uppercase
    if every_cc:
        codes.update(a + b for a in A_Z for b in A_Z)
    elif all_cc:
        with open(COUNTRY_CODES_FILE) as fp:
            text = fp.read()
        codes.update(text.split())
    else:
        for cc in (c.upper() for c in cc_args):
            if len(cc) == 1 and cc in A_Z:
                codes.update(cc + c for c in A_Z)
            elif len(cc) == 2 and all(c in A_Z for c in cc):
                codes.add(cc)
            else:
                msg = 'each CC argument must be A to Z or AA to ZZ.'
                raise ValueError('*** Usage error: ' + msg)
    return sorted(codes)[:limit]


def process_args(default_concur_req):
    server_options = ', '.join(sorted(SERVERS))
    parser = argparse.ArgumentParser(
        description='Download flags for country codes. '
                    'Default: top 20 countries by population.')
    parser.add_argument('cc', metavar='CC', nargs='*',
                        help='country code or 1st letter (eg. B for BA...BZ)')
    parser.add_argument('-a', '--all', action='store_true',
                        help='get all available flags (AD to ZW)')
    parser.add_argument('-e', '--every', action='store_true',
                        help='get flags for every possible code (AA...ZZ)')
    parser.add_argument('-l', '--limit', metavar='N', type=int,
                        help='limit to N first codes', default=sys.maxsize)
    parser.add_argument('-m', '--max_req', metavar='CONCURRENT', type=int,
                        default=default_concur_req,
                        help='maximum concurrent requests (default={})'
                        .format(default_concur_req))
    parser.add_argument('-s', '--server', metavar='LABEL',
                        default=DEFAULT_SERVER,
                        help='Server to hit; one of {} (default={})'
                        .format(server_options, DEFAULT_SERVER))
    parser.add_argument('-v', '--verbose', action='store_true',
                        help='output detailed progress info')
    args = parser.parse_args()
    if args.max_req < 1:
        print('*** Usage error: --max_req CONCURRENT must be >= 1')
        parser.print_usage()
        sys.exit(1)
    if args.limit < 1:
        print('*** Usage error: --limit N must be >= 1')
        parser.print_usage()
        sys.exit(1)
    args.server = args.server.upper()
    if args.server not in SERVERS:
        print('*** Usage error: --server LABEL must be one of',
              server_options)
        parser.print_usage()
        sys.exit(1)
    try:
        cc_list = expand_cc_args(args.every, args.all, args.cc, args.limit)
    except ValueError as exc:
        print(exc.args[0])
        parser.print_usage()
        sys.exit(1)

    if not cc_list:
        cc_list = sorted(POP20_CC)
    return args, cc_list


def main(download_many, default_concur_req, max_concur_req):
    args, cc_list = process_args(default_concur_req)
    actual_req = min(args.max_req, max_concur_req, len(cc_list))
    initial_report(cc_list, actual_req, args.server)
    base_url = SERVERS[args.server]
    t0 = time.time()
    counter = download_many(cc_list, base_url, args.verbose, actual_req)
    assert sum(counter.values()) == len(cc_list), \
        'some downloads are unaccounted for'
    final_report(cc_list, counter, t0)
```

### 多线程

```python
"""Download flags of countries (with error handling).

ThreadPool version

Sample run::

    $ python3 flags2_threadpool.py -s ERROR -e
    ERROR site: http://localhost:8003/flags
    Searching for 676 flags: from AA to ZZ
    30 concurrent connections will be used.
    --------------------
    150 flags downloaded.
    361 not found.
    165 errors.
    Elapsed time: 7.46s

"""
import collections
from concurrent import futures

import requests
import tqdm  # <1>

from flags2_common import main, HTTPStatus, save_flag, Result

DEFAULT_CONCUR_REQ = 30  # <4>
MAX_CONCUR_REQ = 1000  # <5>


def download_one(cc, base_url, verbose=False):
    try:
        image = get_flag(base_url, cc)
    except requests.exceptions.HTTPError as exc:  # <2>
        res = exc.response
        if res.status_code == 404:
            status = HTTPStatus.not_found  # <3>
            msg = 'not found'
        else:  # <4>
            raise
    else:
        save_flag(image, cc.lower() + '.gif')
        status = HTTPStatus.ok
        msg = 'OK'

    if verbose:  # <5>
        print(cc, msg)

    return Result(status, cc)  # <6>


def download_many(cc_list, base_url, verbose, concur_req):
    counter = collections.Counter()
    with futures.ThreadPoolExecutor(max_workers=concur_req) as executor:  # <6>
        to_do_map = {}  # <7>
        for cc in sorted(cc_list):  # <8>
            future = executor.submit(download_one,
                                     cc, base_url, verbose)  # <9>
            to_do_map[future] = cc  # <10>
        done_iter = futures.as_completed(to_do_map)  # <11>
        if not verbose:
            done_iter = tqdm.tqdm(done_iter, total=len(cc_list))  # <12>
        for future in done_iter:  # <13>
            try:
                res = future.result()  # <14>
            except requests.exceptions.HTTPError as exc:  # <15>
                error_msg = 'HTTP {res.status_code} - {res.reason}'
                error_msg = error_msg.format(res=exc.response)
            except requests.exceptions.ConnectionError as exc:
                error_msg = 'Connection error'
            else:
                error_msg = ''
                status = res.status

            if error_msg:
                status = HTTPStatus.error
            counter[status] += 1
            if verbose and error_msg:
                cc = to_do_map[future]  # <16>
                print('*** Error for {}: {}'.format(cc, error_msg))

    return counter


if __name__ == '__main__':
    main(download_many, DEFAULT_CONCUR_REQ, MAX_CONCUR_REQ)
```

### 异步

```python
"""Download flags of countries (with error handling).

asyncio async/await version

"""
import asyncio
import collections

import aiohttp
from aiohttp import web
import tqdm

from flags2_common import main, HTTPStatus, Result, save_flag

# default set low to avoid errors from remote site, such as
# 503 - Service Temporarily Unavailable
DEFAULT_CONCUR_REQ = 5
MAX_CONCUR_REQ = 1000


class FetchError(Exception):  # <1>
    def __init__(self, country_code):
        self.country_code = country_code


async def get_flag(session, base_url, cc):  # <2>
    url = '{}/{cc}/{cc}.gif'.format(base_url, cc=cc.lower())
    async with session.get(url) as resp:
        if resp.status == 200:
            return await resp.read()
        elif resp.status == 404:
            raise web.HTTPNotFound()
        else:
            raise aiohttp.HttpProcessingError(
                code=resp.status, message=resp.reason,
                headers=resp.headers)


async def download_one(session, cc, base_url, semaphore, verbose):  # <3>
    try:
        async with semaphore:  # <4>
            image = await get_flag(session, base_url, cc)  # <5>
    except web.HTTPNotFound:  # <6>
        status = HTTPStatus.not_found
        msg = 'not found'
    except Exception as exc:
        raise FetchError(cc) from exc  # <7>
    else:
        save_flag(image, cc.lower() + '.gif')  # <8>
        status = HTTPStatus.ok
        msg = 'OK'

    if verbose and msg:
        print(cc, msg)

    return Result(status, cc)


async def downloader_coro(cc_list, base_url, verbose, concur_req):  # <1>
    counter = collections.Counter()
    semaphore = asyncio.Semaphore(concur_req)  # <2>
    async with aiohttp.ClientSession() as session:  # <8>
        to_do = [download_one(session, cc, base_url, semaphore, verbose)
                 for cc in sorted(cc_list)]  # <3>

        to_do_iter = asyncio.as_completed(to_do)  # <4>
        if not verbose:
            to_do_iter = tqdm.tqdm(to_do_iter, total=len(cc_list))  # <5>
        for future in to_do_iter:  # <6>
            try:
                res = await future  # <7>
            except FetchError as exc:  # <8>
                country_code = exc.country_code  # <9>
                try:
                    error_msg = exc.__cause__.args[0]  # <10>
                except IndexError:
                    error_msg = exc.__cause__.__class__.__name__  # <11>
                if verbose and error_msg:
                    msg = '*** Error for {}: {}'
                    print(msg.format(country_code, error_msg))
                status = HTTPStatus.error
            else:
                status = res.status

            counter[status] += 1  # <12>

    return counter  # <13>


def download_many(cc_list, base_url, verbose, concur_req):
    loop = asyncio.get_event_loop()
    coro = downloader_coro(cc_list, base_url, verbose, concur_req)
    counts = loop.run_until_complete(coro)  # <14>
    loop.close()  # <15>

    return counts


if __name__ == '__main__':
    main(download_many, DEFAULT_CONCUR_REQ, MAX_CONCUR_REQ)
```

## 并发模块

### multiprocessing 模块

```python
from multiprocessing import Process
from multiprocessing import cpu_count
from os import getpid
from random import randint


def compare(arr, i, j):
    """比较数组两个位置的值大小"""
    if arr[i] < arr[j]:
        return -1
    elif arr[i] > arr[j]:
        return 1
    else:
        return 0


def swap(arr, i, j):
    """交换数组两个位置的值"""
    arr[i], arr[j] = arr[j], arr[i]


def quicksort(array):
    """快速排序"""
    length = len(array)
    queue = [(0, length,)]
    while len(queue):
        start_idx, end_idx = queue.pop(0)
        if end_idx - start_idx < 5:
            for i in range(start_idx + 1, end_idx):
                j = i - 1
                while j >= start_idx:
                    if compare(array, j, j + 1) <= 0:
                        break
                    swap(array, j, j + 1)
                    j -= 1
            continue
        j, i, k = start_idx, (start_idx + end_idx) // 2, end_idx - 1
        if compare(array, k, i) < 0:
            swap(array, k, i)
        if compare(array, k, j) < 0:
            swap(array, k, j)
        if compare(array, j, i) < 0:
            swap(array, j, i)
        pivot, left, right = j, start_idx, end_idx
        while left <= right:
            right -= 1
            while right > start_idx and compare(array, right, pivot) >= 0:
                right -= 1
            left += 1
            while left < end_idx and compare(array, left, pivot) <= 0:
                left += 1
            if left > right:
                continue
            swap(array, left, right)
        swap(array, pivot, right)
        n1 = right - start_idx
        n2 = end_idx - left
        if n1 > 1:
            queue.append((start_idx, right,))
        if n2 > 1:
            queue.append((left, end_idx,))


def task(task_id):
    print(f'[Task {getpid()}] process {task_id}')
    source_list = [e + randint(-50, 50) for e in range(10)]
    print('before', source_list)
    quicksort(source_list)
    print('after', source_list)


def make_process():
    for i in range(cpu_count()):
        p = Process(target=task, args=(i + 1,))
        p.start()
        # 挂起等待子进程结束
        # p.join()


print('__name__ :  ' + __name__)

if __name__ == '__main__':
    print(f'[Main {getpid()}] process...')
    make_process()
```

### threading 模块

```python
from threading import Thread
from os import cpu_count
from random import randint


def compare(arr, i, j):
    """比较数组两个位置的值大小"""
    if arr[i] < arr[j]:
        return -1
    elif arr[i] > arr[j]:
        return 1
    else:
        return 0


def swap(arr, i, j):
    """交换数组两个位置的值"""
    arr[i], arr[j] = arr[j], arr[i]


def quicksort(array):
    """快速排序"""
    length = len(array)
    queue = [(0, length,)]
    while len(queue):
        start_idx, end_idx = queue.pop(0)
        if end_idx - start_idx < 5:
            for i in range(start_idx + 1, end_idx):
                j = i - 1
                while j >= start_idx:
                    if compare(array, j, j + 1) <= 0:
                        break
                    swap(array, j, j + 1)
                    j -= 1
            continue
        j, i, k = start_idx, (start_idx + end_idx) // 2, end_idx - 1
        if compare(array, k, i) < 0:
            swap(array, k, i)
        if compare(array, k, j) < 0:
            swap(array, k, j)
        if compare(array, j, i) < 0:
            swap(array, j, i)
        pivot, left, right = j, start_idx, end_idx
        while left <= right:
            right -= 1
            while right > start_idx and compare(array, right, pivot) >= 0:
                right -= 1
            left += 1
            while left < end_idx and compare(array, left, pivot) <= 0:
                left += 1
            if left > right:
                continue
            swap(array, left, right)
        swap(array, pivot, right)
        n1 = right - start_idx
        n2 = end_idx - left
        if n1 > 1:
            queue.append((start_idx, right,))
        if n2 > 1:
            queue.append((left, end_idx,))


def task(task_id):
    message = f'[Task {task_id}] process...\n'
    source_list = [e + randint(-50, 50) for e in range(10)]
    message += f'before {source_list}\n'
    quicksort(source_list)
    message += f'after {source_list}\n'
    print(message, end='')


def make_thread():
    for i in range(cpu_count() * 2):
        t = Thread(target=task, args=(i + 1,))
        t.start()
        # 挂起等待子线程结束
        # t.join()


if __name__ == '__main__':
    print('[Main] process...')
    make_thread()
```

Last Modified 2022-08-01

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

```python
import sys
import time
import hashlib
from concurrent import futures
from random import randrange

JOBS = 12
SIZE = 2**20
STATUS = '{} workers, elapsed time: {:.2f}s'


def sha(size):
    data = bytearray(randrange(256) for i in range(size))
    algo = hashlib.new('sha256')
    algo.update(data)
    return algo.hexdigest()


def main(workers=None):
    if workers:
        workers = int(workers)
    t0 = time.time()

    with futures.ProcessPoolExecutor(workers) as executor:
        actual_workers = executor._max_workers
        to_do = (executor.submit(sha, SIZE) for i in range(JOBS))
        for future in futures.as_completed(to_do):
            res = future.result()
            print(res)

    print(STATUS.format(actual_workers, time.time() - t0))

if __name__ == '__main__':
    if len(sys.argv) == 2:
        worker_numbers = int(sys.argv[1])
    else:
        worker_numbers = None
    main(worker_numbers)
```

## 算法示例

### RC4 算法

```python
"""RC4 compatible algorithm"""


def arcfour(key, in_bytes, loops=20):
    kbox = bytearray(256)  # create key box
    for i, car in enumerate(key):  # copy key and vector
        kbox[i] = car
    j = len(key)
    for i in range(j, 256):  # repeat until full
        kbox[i] = kbox[i - j]

    # [1] initialize sbox
    sbox = bytearray(range(256))

    # repeat sbox mixing loop, as recommened in CipherSaber-2
    # http://ciphersaber.gurus.com/faq.html#cs2
    j = 0
    for k in range(loops):
        for i in range(256):
            j = (j + sbox[i] + kbox[i]) % 256
            sbox[i], sbox[j] = sbox[j], sbox[i]

    # main loop
    i = 0
    j = 0
    out_bytes = bytearray()

    for car in in_bytes:
        i = (i + 1) % 256
        # [2] shuffle sbox
        j = (j + sbox[i]) % 256
        sbox[i], sbox[j] = sbox[j], sbox[i]
        # [3] compute t
        t = (sbox[i] + sbox[j]) % 256
        k = sbox[t]
        car = car ^ k
        out_bytes.append(car)

    return out_bytes


def test():
    from time import time
    clear = bytearray(b'1234567890' * 100000)
    t0 = time()
    cipher = arcfour(b'key', clear)
    print('elapsed time: %.2fs' % (time() - t0))
    result = arcfour(b'key', cipher)
    assert result == clear, '%r != %r' % (result, clear)
    print('elapsed time: %.2fs' % (time() - t0))
    print('OK')


if __name__ == '__main__':
    test()
```

### 算法验证

```python
#!/usr/bin/env python3

from arcfour import arcfour

'''
Source of the test vectors:
    A Stream Cipher Encryption Algorithm "Arcfour"
    http://tools.ietf.org/html/draft-kaukonen-cipher-arcfour-03
'''

TEST_VECTORS = [
    ('CRYPTLIB', {
        'Plain Text': (0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00),
        'Key': (0x01, 0x23, 0x45, 0x67, 0x89, 0xAB, 0xCD, 0xEF),
        'Cipher Text': (0x74, 0x94, 0xC2, 0xE7, 0x10, 0x4B, 0x08, 0x79),
    }
     ),
    ('COMMERCE', {
        'Plain Text': (0xdc, 0xee, 0x4c, 0xf9, 0x2c),
        'Key': (0x61, 0x8a, 0x63, 0xd2, 0xfb),
        'Cipher Text': (0xf1, 0x38, 0x29, 0xc9, 0xde),
    }
     ),
    ('SSH ARCFOUR', {
        'Plain Text': (
            0x52, 0x75, 0x69, 0x73, 0x6c, 0x69, 0x6e, 0x6e,
            0x75, 0x6e, 0x20, 0x6c, 0x61, 0x75, 0x6c, 0x75,
            0x20, 0x6b, 0x6f, 0x72, 0x76, 0x69, 0x73, 0x73,
            0x73, 0x61, 0x6e, 0x69, 0x2c, 0x20, 0x74, 0xe4,
            0x68, 0x6b, 0xe4, 0x70, 0xe4, 0x69, 0x64, 0x65,
            0x6e, 0x20, 0x70, 0xe4, 0xe4, 0x6c, 0x6c, 0xe4,
            0x20, 0x74, 0xe4, 0x79, 0x73, 0x69, 0x6b, 0x75,
            0x75, 0x2e, 0x20, 0x4b, 0x65, 0x73, 0xe4, 0x79,
            0xf6, 0x6e, 0x20, 0x6f, 0x6e, 0x20, 0x6f, 0x6e,
            0x6e, 0x69, 0x20, 0x6f, 0x6d, 0x61, 0x6e, 0x61,
            0x6e, 0x69, 0x2c, 0x20, 0x6b, 0x61, 0x73, 0x6b,
            0x69, 0x73, 0x61, 0x76, 0x75, 0x75, 0x6e, 0x20,
            0x6c, 0x61, 0x61, 0x6b, 0x73, 0x6f, 0x74, 0x20,
            0x76, 0x65, 0x72, 0x68, 0x6f, 0x75, 0x75, 0x2e,
            0x20, 0x45, 0x6e, 0x20, 0x6d, 0x61, 0x20, 0x69,
            0x6c, 0x6f, 0x69, 0x74, 0x73, 0x65, 0x2c, 0x20,
            0x73, 0x75, 0x72, 0x65, 0x20, 0x68, 0x75, 0x6f,
            0x6b, 0x61, 0x61, 0x2c, 0x20, 0x6d, 0x75, 0x74,
            0x74, 0x61, 0x20, 0x6d, 0x65, 0x74, 0x73, 0xe4,
            0x6e, 0x20, 0x74, 0x75, 0x6d, 0x6d, 0x75, 0x75,
            0x73, 0x20, 0x6d, 0x75, 0x6c, 0x6c, 0x65, 0x20,
            0x74, 0x75, 0x6f, 0x6b, 0x61, 0x61, 0x2e, 0x20,
            0x50, 0x75, 0x75, 0x6e, 0x74, 0x6f, 0x20, 0x70,
            0x69, 0x6c, 0x76, 0x65, 0x6e, 0x2c, 0x20, 0x6d,
            0x69, 0x20, 0x68, 0x75, 0x6b, 0x6b, 0x75, 0x75,
            0x2c, 0x20, 0x73, 0x69, 0x69, 0x6e, 0x74, 0x6f,
            0x20, 0x76, 0x61, 0x72, 0x61, 0x6e, 0x20, 0x74,
            0x75, 0x75, 0x6c, 0x69, 0x73, 0x65, 0x6e, 0x2c,
            0x20, 0x6d, 0x69, 0x20, 0x6e, 0x75, 0x6b, 0x6b,
            0x75, 0x75, 0x2e, 0x20, 0x54, 0x75, 0x6f, 0x6b,
            0x73, 0x75, 0x74, 0x20, 0x76, 0x61, 0x6e, 0x61,
            0x6d, 0x6f, 0x6e, 0x20, 0x6a, 0x61, 0x20, 0x76,
            0x61, 0x72, 0x6a, 0x6f, 0x74, 0x20, 0x76, 0x65,
            0x65, 0x6e, 0x2c, 0x20, 0x6e, 0x69, 0x69, 0x73,
            0x74, 0xe4, 0x20, 0x73, 0x79, 0x64, 0xe4, 0x6d,
            0x65, 0x6e, 0x69, 0x20, 0x6c, 0x61, 0x75, 0x6c,
            0x75, 0x6e, 0x20, 0x74, 0x65, 0x65, 0x6e, 0x2e,
            0x20, 0x2d, 0x20, 0x45, 0x69, 0x6e, 0x6f, 0x20,
            0x4c, 0x65, 0x69, 0x6e, 0x6f),
        'Key': (
            0x29, 0x04, 0x19, 0x72, 0xfb, 0x42, 0xba, 0x5f,
            0xc7, 0x12, 0x77, 0x12, 0xf1, 0x38, 0x29, 0xc9),
        'Cipher Text': (
            0x35, 0x81, 0x86, 0x99, 0x90, 0x01, 0xe6, 0xb5,
            0xda, 0xf0, 0x5e, 0xce, 0xeb, 0x7e, 0xee, 0x21,
            0xe0, 0x68, 0x9c, 0x1f, 0x00, 0xee, 0xa8, 0x1f,
            0x7d, 0xd2, 0xca, 0xae, 0xe1, 0xd2, 0x76, 0x3e,
            0x68, 0xaf, 0x0e, 0xad, 0x33, 0xd6, 0x6c, 0x26,
            0x8b, 0xc9, 0x46, 0xc4, 0x84, 0xfb, 0xe9, 0x4c,
            0x5f, 0x5e, 0x0b, 0x86, 0xa5, 0x92, 0x79, 0xe4,
            0xf8, 0x24, 0xe7, 0xa6, 0x40, 0xbd, 0x22, 0x32,
            0x10, 0xb0, 0xa6, 0x11, 0x60, 0xb7, 0xbc, 0xe9,
            0x86, 0xea, 0x65, 0x68, 0x80, 0x03, 0x59, 0x6b,
            0x63, 0x0a, 0x6b, 0x90, 0xf8, 0xe0, 0xca, 0xf6,
            0x91, 0x2a, 0x98, 0xeb, 0x87, 0x21, 0x76, 0xe8,
            0x3c, 0x20, 0x2c, 0xaa, 0x64, 0x16, 0x6d, 0x2c,
            0xce, 0x57, 0xff, 0x1b, 0xca, 0x57, 0xb2, 0x13,
            0xf0, 0xed, 0x1a, 0xa7, 0x2f, 0xb8, 0xea, 0x52,
            0xb0, 0xbe, 0x01, 0xcd, 0x1e, 0x41, 0x28, 0x67,
            0x72, 0x0b, 0x32, 0x6e, 0xb3, 0x89, 0xd0, 0x11,
            0xbd, 0x70, 0xd8, 0xaf, 0x03, 0x5f, 0xb0, 0xd8,
            0x58, 0x9d, 0xbc, 0xe3, 0xc6, 0x66, 0xf5, 0xea,
            0x8d, 0x4c, 0x79, 0x54, 0xc5, 0x0c, 0x3f, 0x34,
            0x0b, 0x04, 0x67, 0xf8, 0x1b, 0x42, 0x59, 0x61,
            0xc1, 0x18, 0x43, 0x07, 0x4d, 0xf6, 0x20, 0xf2,
            0x08, 0x40, 0x4b, 0x39, 0x4c, 0xf9, 0xd3, 0x7f,
            0xf5, 0x4b, 0x5f, 0x1a, 0xd8, 0xf6, 0xea, 0x7d,
            0xa3, 0xc5, 0x61, 0xdf, 0xa7, 0x28, 0x1f, 0x96,
            0x44, 0x63, 0xd2, 0xcc, 0x35, 0xa4, 0xd1, 0xb0,
            0x34, 0x90, 0xde, 0xc5, 0x1b, 0x07, 0x11, 0xfb,
            0xd6, 0xf5, 0x5f, 0x79, 0x23, 0x4d, 0x5b, 0x7c,
            0x76, 0x66, 0x22, 0xa6, 0x6d, 0xe9, 0x2b, 0xe9,
            0x96, 0x46, 0x1d, 0x5e, 0x4d, 0xc8, 0x78, 0xef,
            0x9b, 0xca, 0x03, 0x05, 0x21, 0xe8, 0x35, 0x1e,
            0x4b, 0xae, 0xd2, 0xfd, 0x04, 0xf9, 0x46, 0x73,
            0x68, 0xc4, 0xad, 0x6a, 0xc1, 0x86, 0xd0, 0x82,
            0x45, 0xb2, 0x63, 0xa2, 0x66, 0x6d, 0x1f, 0x6c,
            0x54, 0x20, 0xf1, 0x59, 0x9d, 0xfd, 0x9f, 0x43,
            0x89, 0x21, 0xc2, 0xf5, 0xa4, 0x63, 0x93, 0x8c,
            0xe0, 0x98, 0x22, 0x65, 0xee, 0xf7, 0x01, 0x79,
            0xbc, 0x55, 0x3f, 0x33, 0x9e, 0xb1, 0xa4, 0xc1,
            0xaf, 0x5f, 0x6a, 0x54, 0x7f),
    }
     ),
]

for name, vectors in TEST_VECTORS:
    print(name, end='')
    plain = bytearray(vectors['Plain Text'])
    cipher = bytearray(vectors['Cipher Text'])
    key = bytearray(vectors['Key'])
    assert cipher == arcfour(key, plain, loops=1)
    assert plain == arcfour(key, cipher, loops=1)
    print(' --> OK')
```

### 多进程运行算法

```python
import sys
import time
from concurrent import futures
from random import randrange
from arcfour import arcfour

JOBS = 12
SIZE = 2 ** 18

KEY = b"'Twas brillig, and the slithy toves\nDid gyre"
STATUS = '{} workers, elapsed time: {:.2f}s'


def arcfour_test(size, key):
    in_text = bytearray(randrange(256) for i in range(size))
    cypher_text = arcfour(key, in_text)
    out_text = arcfour(key, cypher_text)
    assert in_text == out_text, 'Failed arcfour_test'
    return size


def main(workers=None):
    if workers:
        workers = int(workers)
    t0 = time.time()

    with futures.ProcessPoolExecutor(workers) as executor:
        actual_workers = executor._max_workers
        to_do = []
        for i in range(JOBS, 0, -1):
            size = SIZE + int(SIZE / JOBS * (i - JOBS / 2))
            job = executor.submit(arcfour_test, size, KEY)
            to_do.append(job)

        for future in futures.as_completed(to_do):
            res = future.result()
            print('{:.1f} KB'.format(res / 2 ** 10))

    print(STATUS.format(actual_workers, time.time() - t0))


if __name__ == '__main__':
    if len(sys.argv) == 2:
        worker_number = int(sys.argv[1])
    else:
        worker_number = None
    main(worker_number)
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


def get_flag(base_url, cc):
    url = '{}/{cc}/{cc}.gif'.format(base_url, cc=cc.lower())
    resp = requests.get(url)
    if resp.status_code != 200:  # <1>
        resp.raise_for_status()
    return resp.content


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
    loop = asyncio.new_event_loop()
    coro = downloader_coro(cc_list, base_url, verbose, concur_req)
    counts = loop.run_until_complete(coro)  # <14>
    loop.close()  # <15>

    return counts


if __name__ == '__main__':
    main(download_many, DEFAULT_CONCUR_REQ, MAX_CONCUR_REQ)
```

## 并发模块

如果想更细致地义并发逻辑，可以考虑使用`multiprocessing`和`threading`模块

>使用多进程可以绕开`GIL`锁，利用`CPU`的多个核心执行程序，以提高效率

>`CPython`解释器本身是线程不安全的，因此有全局解释器锁（`GIL`），一次只允许使用一个线程执行 Python 字节码。因此，一个 Python 进程通常不能同时使用多个 CPU 核心。
>标准库中每个使用 C 语言编写的所有阻塞型`I/O`函数，在等待操作系统返回结果时都会释放`GIL`，允许其它线程运行。`time.sleep()`函数也会释放`GIL`

Last Modified 2022-08-11

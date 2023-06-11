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

import argparse
import string
import sys
import time
from collections import Counter
from enum import Enum
from pathlib import Path


class DownloadStatus(Enum):
    OK = 1
    NOT_FOUND = 2
    ERROR = 3


POP20_CC = ('CN IN US ID BR PK NG BD RU JP '
            'MX PH VN ET EG DE IR TR CD FR').split()

DEFAULT_CONCUR_REQ = 1
MAX_CONCUR_REQ = 1

SERVERS = {
    'REMOTE': 'https://www.fluentpython.com/data/flags',
    'LOCAL': 'http://192.168.4.105:1984/data/flags',
    'DELAY': 'http://localhost:8001/flags',
    'ERROR': 'http://localhost:8002/flags',
}
DEFAULT_SERVER = 'LOCAL'

DEST_DIR = Path('downloaded')
COUNTRY_CODES = """AD AE AF AG AL AM AO AR AT AU AZ BA BB BD BE BF BG BH BI BJ BN BO BR BS BT
BW BY BZ CA CD CF CG CH CI CL CM CN CO CR CU CV CY CZ DE DJ DK DM DZ EC EE
EG ER ES ET FI FJ FM FR GA GB GD GE GH GM GN GQ GR GT GW GY HN HR HT HU ID
IE IL IN IQ IR IS IT JM JO JP KE KG KH KI KM KN KP KR KW KZ LA LB LC LI LK
LR LS LT LU LV LY MA MC MD ME MG MH MK ML MM MN MR MT MU MV MW MX MY MZ NA
NE NG NI NL NO NP NR NZ OM PA PE PG PH PK PL PT PW PY QA RO RS RU RW SA SB
SC SD SE SG SI SK SL SM SN SO SR SS ST SV SY SZ TD TG TH TJ TL TM TN TO TR
TT TV TW TZ UA UG US UY UZ VA VC VE VN VU WS YE ZA ZM ZW"""


def save_flag(img: bytes, filename: str) -> None:
    (DEST_DIR / filename).write_bytes(img)


def initial_report(cc_list: list[str],
                   actual_req: int,
                   server_label: str) -> None:
    if len(cc_list) <= 10:
        cc_msg = ', '.join(cc_list)
    else:
        cc_msg = f'from {cc_list[0]} to {cc_list[-1]}'
    print(f'{server_label} site: {SERVERS[server_label]}')
    plural = 's' if len(cc_list) != 1 else ''
    print(f'Searching for {len(cc_list)} flag{plural}: {cc_msg}')
    if actual_req == 1:
        print('1 connection will be used.')
    else:
        print(f'{actual_req} concurrent connections will be used.')


def final_report(cc_list: list[str],
                 counter: Counter[DownloadStatus],
                 start_time: float) -> None:
    elapsed = time.perf_counter() - start_time
    print('-' * 20)
    plural = 's' if counter[DownloadStatus.OK] != 1 else ''
    print(f'{counter[DownloadStatus.OK]:3} flag{plural} downloaded.')
    if counter[DownloadStatus.NOT_FOUND]:
        print(f'{counter[DownloadStatus.NOT_FOUND]:3} not found.')
    if counter[DownloadStatus.ERROR]:
        plural = 's' if counter[DownloadStatus.ERROR] != 1 else ''
        print(f'{counter[DownloadStatus.ERROR]:3} error{plural}.')
    print(f'Elapsed time: {elapsed:.2f}s')


def expand_cc_args(every_cc: bool,
                   all_cc: bool,
                   cc_args: list[str],
                   limit: int) -> list[str]:
    codes: set[str] = set()
    A_Z = string.ascii_uppercase
    if every_cc:
        codes.update(a + b for a in A_Z for b in A_Z)
    elif all_cc:
        text = COUNTRY_CODES
        codes.update(text.split())
    else:
        for cc in (c.upper() for c in cc_args):
            if len(cc) == 1 and cc in A_Z:
                codes.update(cc + c for c in A_Z)
            elif len(cc) == 2 and all(c in A_Z for c in cc):
                codes.add(cc)
            else:
                raise ValueError('*** Usage error: each CC argument '
                                 'must be A to Z or AA to ZZ.')
    return sorted(codes)[:limit]


def process_args(default_concur_req):
    server_options = ', '.join(sorted(SERVERS))
    parser = argparse.ArgumentParser(
        description='Download flags for country codes. '
                    'Default: top 20 countries by population.')
    parser.add_argument(
        'cc', metavar='CC', nargs='*',
        help='country code or 1st letter (eg. B for BA...BZ)')
    parser.add_argument(
        '-a', '--all', action='store_true',
        help='get all available flags (AD to ZW)')
    parser.add_argument(
        '-e', '--every', action='store_true',
        help='get flags for every possible code (AA...ZZ)')
    parser.add_argument(
        '-l', '--limit', metavar='N', type=int, help='limit to N first codes',
        default=sys.maxsize)
    parser.add_argument(
        '-m', '--max_req', metavar='CONCURRENT', type=int,
        default=default_concur_req,
        help=f'maximum concurrent requests (default={default_concur_req})')
    parser.add_argument(
        '-s', '--server', metavar='LABEL', default=DEFAULT_SERVER,
        help=f'Server to hit; one of {server_options} '
             f'(default={DEFAULT_SERVER})')
    parser.add_argument(
        '-v', '--verbose', action='store_true',
        help='output detailed progress info')
    args = parser.parse_args()
    if args.max_req < 1:
        print('*** Usage error: --max_req CONCURRENT must be >= 1')
        parser.print_usage()
        # "standard" exit status codes:
        # https://stackoverflow.com/questions/1101957/are-there-any-standard-exit-status-codes-in-linux/40484670#40484670
        sys.exit(2)  # command line usage error
    if args.limit < 1:
        print('*** Usage error: --limit N must be >= 1')
        parser.print_usage()
        sys.exit(2)  # command line usage error
    args.server = args.server.upper()
    if args.server not in SERVERS:
        print(f'*** Usage error: --server LABEL '
              f'must be one of {server_options}')
        parser.print_usage()
        sys.exit(2)  # command line usage error
    try:
        cc_list = expand_cc_args(args.every, args.all, args.cc, args.limit)
    except ValueError as exc:
        print(exc.args[0])
        parser.print_usage()
        sys.exit(2)  # command line usage error

    if not cc_list:
        cc_list = sorted(POP20_CC)[:args.limit]
    return args, cc_list


def main(download_many, default_concur_req, max_concur_req):
    args, cc_list = process_args(default_concur_req)
    actual_req = min(args.max_req, max_concur_req, len(cc_list))
    initial_report(cc_list, actual_req, args.server)
    base_url = SERVERS[args.server]
    DEST_DIR.mkdir(exist_ok=True)
    t0 = time.perf_counter()
    counter = download_many(cc_list, base_url, args.verbose, actual_req)
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

from collections import Counter
from concurrent.futures import ThreadPoolExecutor, as_completed
from http import HTTPStatus

import httpx
import tqdm  # type: ignore

from flags2_common import main, save_flag, DownloadStatus

DEFAULT_CONCUR_REQ = 30  # <2>
MAX_CONCUR_REQ = 1000  # <3>


def get_flag(base_url: str, cc: str) -> bytes:
    url = f'{base_url}/{cc}/{cc}.gif'.lower()
    resp = httpx.get(url, timeout=3.1, follow_redirects=True)
    # 如果不是 2xx 的响应代码则抛出异常
    resp.raise_for_status()  # <3>
    return resp.content


def download_one(cc: str, base_url: str, verbose: bool = False) -> DownloadStatus:
    try:
        image = get_flag(base_url, cc)
    except httpx.HTTPStatusError as exc:  # <4>
        res = exc.response
        if res.status_code == HTTPStatus.NOT_FOUND:
            status = DownloadStatus.NOT_FOUND  # <5>
            msg = f'not found: {res.url}'
        else:
            # 将错误抛出由上层处理
            raise  # <6>
    else:
        save_flag(image, f'{cc}.gif')
        status = DownloadStatus.OK
        msg = 'OK'

    if verbose:  # <7>
        print(cc, msg)

    return status


def download_many(cc_list: list[str],
                  base_url: str,
                  verbose: bool,
                  concur_req: int) -> Counter[DownloadStatus]:
    counter: Counter[DownloadStatus] = Counter()
    with ThreadPoolExecutor(max_workers=concur_req) as executor:  # <4>
        to_do_map = {}  # <5>
        # 将所有请求提交到线程池中
        for cc in sorted(cc_list):  # <6>
            future = executor.submit(download_one, cc,
                                     base_url, verbose)  # <7>
            to_do_map[future] = cc  # <8>
        # 将返回的 Future 转换为一个完成迭代器，即完成处理的 Future 会返回（惰性迭代）
        done_iter = as_completed(to_do_map)  # <9>
        if not verbose:
            # 进度条
            done_iter = tqdm.tqdm(done_iter, total=len(cc_list))  # <10>
        for future in done_iter:  # <11>
            try:
                # 挂起等待完成
                status = future.result()  # <12>
            except httpx.HTTPStatusError as exc:  # <13>
                error_msg = 'HTTP error {resp.status_code} - {resp.reason_phrase}'
                error_msg = error_msg.format(resp=exc.response)
            except httpx.RequestError as exc:
                error_msg = f'{exc} {type(exc)}'.strip()
            except KeyboardInterrupt:
                break
            else:
                error_msg = ''

            if error_msg:
                status = DownloadStatus.ERROR
            # 根据不同的错误类型累计完成数量
            counter[status] += 1
            if verbose and error_msg:
                cc = to_do_map[future]  # <14>
                print(f'{cc} error: {error_msg}')

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
from collections import Counter
from http import HTTPStatus
from pathlib import Path

import httpx
import tqdm  # type: ignore

from flags2_common import main, DownloadStatus, save_flag

# low concurrency default to avoid errors from remote site,
# such as 503 - Service Temporarily Unavailable
DEFAULT_CONCUR_REQ = 5
MAX_CONCUR_REQ = 1000


async def get_flag(client: httpx.AsyncClient,  # <1>
                   base_url: str,
                   cc: str) -> bytes:
    """下载图片"""
    url = f'{base_url}/{cc}/{cc}.gif'.lower()
    resp = await client.get(url, timeout=3.1, follow_redirects=True)  # <2>
    resp.raise_for_status()
    return resp.content


async def get_country(client: httpx.AsyncClient,
                      base_url: str,
                      cc: str) -> str:  # <1>
    """获得图片完整的文件名称"""
    url = f'{base_url}/{cc}/metadata.json'.lower()
    resp = await client.get(url, timeout=3.1, follow_redirects=True)
    resp.raise_for_status()
    metadata = resp.json()  # <2>
    return metadata['country']  # <3>


async def download_one(client: httpx.AsyncClient,
                       cc: str,
                       base_url: str,
                       semaphore: asyncio.Semaphore,
                       verbose: bool) -> DownloadStatus:
    try:
        async with semaphore:  # <1>
            image = await get_flag(client, base_url, cc)
        async with semaphore:  # <2>
            country = await get_country(client, base_url, cc)
    except httpx.HTTPStatusError as exc:
        res = exc.response
        if res.status_code == HTTPStatus.NOT_FOUND:
            status = DownloadStatus.NOT_FOUND
            msg = f'not found: {res.url}'
        else:
            raise
    else:
        filename = country.replace(' ', '_')  # <3>
        # 将保存图片的操作放到线程中执行
        await asyncio.to_thread(save_flag, image, f'{filename}.gif')
        status = DownloadStatus.OK
        msg = 'OK'
    if verbose and msg:
        print(cc, msg)
    return status


async def supervisor(cc_list: list[str],
                     base_url: str,
                     verbose: bool,
                     concur_req: int) -> Counter[DownloadStatus]:  # <1>
    counter: Counter[DownloadStatus] = Counter()
    semaphore = asyncio.Semaphore(concur_req)  # <2>
    async with httpx.AsyncClient() as client:
        to_do = [download_one(client, cc, base_url, semaphore, verbose)
                 for cc in sorted(cc_list)]  # <3>
        to_do_iter = asyncio.as_completed(to_do)  # <4>
        if not verbose:
            to_do_iter = tqdm.tqdm(to_do_iter, total=len(cc_list))  # <5>
        error: httpx.HTTPError | None = None  # <6>
        for coro in to_do_iter:  # <7>
            try:
                status = await coro  # <8>
            except httpx.HTTPStatusError as exc:
                error_msg = 'HTTP error {resp.status_code} - {resp.reason_phrase}'
                error_msg = error_msg.format(resp=exc.response)
                error = exc  # <9>
            except httpx.RequestError as exc:
                error_msg = f'{exc} {type(exc)}'.strip()
                error = exc  # <10>
            except KeyboardInterrupt:
                break

            if error:
                status = DownloadStatus.ERROR  # <11>
                if verbose:
                    url = str(error.request.url)  # <12>
                    cc = Path(url).stem.upper()  # <13>
                    print(f'{cc} error: {error_msg}')
            counter[status] += 1

    return counter


def download_many(cc_list: list[str],
                  base_url: str,
                  verbose: bool,
                  concur_req: int) -> Counter[DownloadStatus]:
    coro = supervisor(cc_list, base_url, verbose, concur_req)
    counts = asyncio.run(coro)  # <14>

    return counts


if __name__ == '__main__':
    main(download_many, DEFAULT_CONCUR_REQ, MAX_CONCUR_REQ)
```

> 参考代码来自《流畅的 Python：第二版》的 [GitHub 仓库](https://github.com/fluentpython/example-code-2e/tree/master/20-executors/getflags)

## 并发模块

如果想更细致地义并发逻辑，可以考虑使用`multiprocessing`和`threading`模块，这两个模块分别提供了进程池和线程池，方便使用和管理。

Last Modified 2023-06-11

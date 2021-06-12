# 多线程

## 直接使用

```python
from threading import Thread
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
    stack = [(0, length)]
    while stack:
        first, last = stack[-1]
        del stack[-1]
        if last - first < 5:
            for i in range(first + 1, last):
                j = i - 1
                while j >= first:
                    if compare(array, j, j + 1) <= 0:
                        break
                    swap(array, j, j + 1)
                    j -= 1
            continue
        j, i, k = first, (first + last) // 2, last - 1
        if compare(array, k, i) < 0:
            swap(array, k, i)
        if compare(array, k, j) < 0:
            swap(array, k, j)
        if compare(array, j, i) < 0:
            swap(array, j, i)
        pivot, left, right = j, first, last
        while True:
            right -= 1
            while right > first and compare(array, right, pivot) >= 0:
                right -= 1
            left += 1
            while left < last and compare(array, left, pivot) <= 0:
                left += 1
            if left > right:
                break
            swap(array, left, right)
        swap(array, pivot, right)
        n1 = right - first
        n2 = last - left
        if n1 > 1:
            stack.append((first, right))
        if n2 > 1:
            stack.append((left, last))


def task(i):
    print(f'[task {i}] process...')
    source_list = [e + randint(1, 90) for e in range(10)]
    print('before', source_list)
    quicksort(source_list)
    print('after', source_list)


def make_thread():
    for i in range(12):
        t = Thread(target=task, args=(i + 1,))
        t.start()
        # 挂起等待子线程结束
        # t.join()


if __name__ == '__main__':
    print(f'[main] process...')
    make_thread()
```

## 线程池

> 需要 Python 3.7 及以上的版本

```python
from concurrent.futures import ThreadPoolExecutor
from math import sqrt
from math import floor
from os import cpu_count

PRIMES = [
    112272535095293, 112582705942171, 112272535095293, 115280095190773,
    115797848077099, 1099726899285419, 112545606449, 4894348954034, 4236544062,
    11112324353465, 12134123432, 423578924023, 12433323433, 12334452364
]


def is_prime(n):
    if n % 2 == 0:
        return False
    sqrt_n = int(floor(sqrt(n)))
    for i in range(3, sqrt_n + 1, 2):
        if n % i == 0:
            return False
    return True


def main():
    max_thread = cpu_count() * 2
    with ThreadPoolExecutor(max_workers=max_thread) as pool:
        pool.map(is_prime, PRIMES)


if __name__ == '__main__':
    main()
```

## 参考文档

- 基于线程的并行 https://docs.python.org/zh-cn/3.7/library/threading.html
- 启动并行任务 https://docs.python.org/zh-cn/3.7/library/concurrent.futures.html

Last Modified 2021-06-12

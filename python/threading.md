# 多线程

## 直接使用

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

Last Modified 2022-01-27

# 装饰器

实现计算函数的执行时间

```python
from time import time
from copy import deepcopy
from functools import wraps
from multiprocessing import Pool


def timer(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start_time = time() * 1000
        result = func(*args, **kwargs)
        end_time = time() * 1000
        duration_time = end_time - start_time
        print(f'execute time running {func.__name__}: {duration_time:.2f} ms')
        return result

    return wrapper


@timer
def f1(a):
    return 1 + a[0]


@timer
def f2(a):
    return sum(a)


@timer
def f3(a):
    basic = deepcopy(a)
    for i in a:
        for j in basic:
            pass
    return basic


def main():
    n = 1e3
    source = list()
    while n > 0:
        source.append(n)
        n -= 1
    with Pool(processes=3) as pool:
        pool.apply_async(func=f1, args=(source,))
        pool.apply_async(func=f2, args=(source,))
        pool.apply_async(func=f3, args=(source,))
        pool.close()
        pool.join()


if __name__ == '__main__':
    main()
```

Last Modified 2021-06-14

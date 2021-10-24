
# 递归

## 斐波那契数列

```python
# O(2^n) time | O(n) space
def get_nth_fib1(n):
    if n == 2:
        return 1
    if n <= 1:
        return 0
    else:
        return get_nth_fib1(n - 1) + get_nth_fib1(n - 2)


# O(n) time | O(n) space
def get_nth_fib2(n, memoize={1: 0, 2: 1}):
    if n in memoize or n < 1:
        return memoize[n]
    else:
        memoize[n] = get_nth_fib2(n - 1, memoize) + get_nth_fib2(n - 2, memoize)
        return memoize[n]


# O(n) time | O(1) space
def get_nth_fib3(n):
    first = 0
    second = 1
    counter = 3
    while counter <= n:
        next_fib = first + second
        first = second
        second = next_fib
        counter += 1
    return second if n > 1 else first


# O(n) time | O(1) space
def get_nth_fib4(n, first = 0, second = 1):
    if n <= 1:
        return first
    return get_nth_fib4(n - 1, second, first + second)


if __name__ == '__main__':
    print(get_nth_fib1(42))
    print(get_nth_fib2(42))
    print(get_nth_fib3(42))
    print(get_nth_fib4(42))
```

Last Modified 2021-10-24

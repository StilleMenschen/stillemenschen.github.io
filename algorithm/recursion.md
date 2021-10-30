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

## 多维数组求和

```python
# O(n) time | O(d) space
# n is all number element in array
# d is the array dimension
def product_sum(array, multiplier=1):
    if not len(array):
        return 0
    total = 0
    for e in array:
        if isinstance(e, list):
            total += product_sum(e, multiplier + 1)
        else:
            total += e
    return total * multiplier


if __name__ == '__main__':
    source = [5, 2, [7, -1], 3, [6, [-13, 8], 4]]
    print(product_sum(source))
```

## 排列数组

```python
# Upper Bound: O(n^2*n!) time | O(n*n!) space
# Roughly: O(n*n!) time | O(n*n!) space
def get_permutations1(array):
    permutations = []
    permutations_helper1(array, [], permutations)
    return permutations


def permutations_helper1(array, current_permutation, permutations):
    if not len(array) and len(current_permutation):
        permutations.append(current_permutation)
    else:
        for i in range(len(array)):
            new_array = array[:i] + array[i + 1:]
            new_permutation = current_permutation + [array[i]]
            permutations_helper1(new_array, new_permutation, permutations)


# O(n*n!) time | O(n*n!) space
def get_permutations2(array):
    permutations = []
    permutations_helper2(0, array, permutations)
    return permutations


def permutations_helper2(i, array, permutations):
    if i == len(array) - 1:
        permutations.append(array[:])
    else:
        for j in range(i, len(array)):
            swap(array, i, j)
            permutations_helper2(i + 1, array, permutations)
            swap(array, i, j)


def swap(array, i, j):
    array[i], array[j] = array[j], array[i]


if __name__ == '__main__':
    source = [1, 2, 3]
    print(get_permutations1(source))
    print(get_permutations2(source))
```

Last Modified 2021-10-30

# 搜索

## 数组中三个最大数

```python
def find_three_largest_numbers_helper(result, value):
    for idx in reversed(range(len(result))):
        if result[idx] < value:
            for i in range(idx):
                result[i] = result[i + 1]
            result[idx] = value
            break


# O(n) time | O(1) space
def find_three_largest_numbers(array):
    if len(array) <= 3:
        return sorted(array)
    result = [float('-inf') for _ in range(3)]
    for e in array:
        find_three_largest_numbers_helper(result, e)
    return result


if __name__ == '__main__':
    source = [-81, 103, -2, 55, 99, 151, 815]
    print(find_three_largest_numbers(source))
```

Last Modified 2021-11-02

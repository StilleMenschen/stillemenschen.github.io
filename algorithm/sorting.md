# 排序

## 冒泡排序

```python
# O(n^2) time | O(1) space
def bubble_sort(array):
    is_sorted = False
    length = len(array)
    counter = 0
    while not is_sorted:
        is_sorted = True
        for i in range(length - 1 - counter):
            if array[i] > array[i + 1]:
                swap(i, i + 1, array)
                is_sorted = False
        counter += 1
    return array


def swap(i, j, array):
    array[i], array[j] = array[j], array[i]


if __name__ == '__main__':
    a = [8, 5, 2, 9, 5, 6, 3]
    print(bubble_sort(a))
```

## 插入排序

```python
# O(n^2) time | O(1) space
def insertion_sort(array):
    for i in range(1, len(array)):
        j = i
        while j > 0 and array[j] < array[j - 1]:
            swap(j, j - 1, array)
            j -= 1
    return array


def swap(i, j, array):
    array[i], array[j] = array[j], array[i]


if __name__ == '__main__':
    a = [8, 5, 2, 9, 5, 6, 3]
    print(insertion_sort(a))
```

## 选择排序

```python
# O(n^2) time | O(1) space
def selection_sort(array):
    current_idx = 0
    length = len(array)
    while current_idx < length - 1:
        smallest_idx = current_idx
        for i in range(current_idx + 1, length):
            if array[smallest_idx] > array[i]:
                smallest_idx = i
        swap(current_idx, smallest_idx, array)
        current_idx += 1
    return array


def swap(i, j, array):
    array[i], array[j] = array[j], array[i]


if __name__ == '__main__':
    a = [8, 5, 2, 9, 5, 6, 3]
    print(selection_sort(a))
```

## 三数排序

给出两个序列 A 和 B，序列 B 包含三个不同的元素，序列 A 包含 3 个以上的元素，且序列 A 中的元素只能与序列 B 中三个元素中的
某个元素相同，现在要求按序列 B 中三个元素的顺序来排布序列 A

```python
# O(n) time | O(1) space
def three_number_sort1(array, order):
    value_counts = [0, 0, 0]
    for e in array:
        order_idx = order.index(e)
        value_counts[order_idx] += 1

    for i in range(3):
        value = order[i]
        count = value_counts[i]

        num_elements_before = sum(value_counts[:i])

        for n in range(count):
            current_idx = num_elements_before + n
            array[current_idx] = value

    return array


# O(n) time | O(1) space
def three_number_sort2(array, order):
    first_value, third_value = order[0], order[2]

    first_idx = 0
    for i in range(1, len(array)):
        if first_value == array[i]:
            swap(first_idx, i, array)
            first_idx += 1
    third_idx = len(array) - 1
    while first_idx < third_idx:
        if third_value == array[first_idx]:
            swap(third_idx, first_idx, array)
            third_idx -= 1
        first_idx += 1
    return array


# O(n) time | O(1) space
def three_number_sort3(array, order):
    first_value, second_value = order[0], order[1]

    first_idx, second_idx, third_idx = 0, 1, len(array) - 1
    while second_idx < third_idx:
        value = array[second_idx]

        if value == first_value:
            swap(first_idx, second_idx, array)
            first_idx += 1
            second_idx += 1
        elif value == second_value:
            second_idx += 1
        else:
            swap(second_idx, third_idx, array)
            third_idx -= 1

    return array


def swap(i, j, array):
    array[i], array[j] = array[j], array[i]


if __name__ == '__main__':
    a = [1, 0, 0, -1, -1, 0, 1, 1]
    o = [0, 1, -1]
    print(three_number_sort1(a, o))
    print(three_number_sort2(a, o))
    print(three_number_sort3(a, o))
```

## 快速排序

```python
# O(n * log(n)) time | O(log(n)) space
def quick_sort(array):
    quick_sort_helper(array, 0, len(array) - 1)
    return array


def quick_sort_helper(array, start_idx, end_idx):
    if start_idx >= end_idx:
        return False
    pivot_idx = start_idx
    left_idx = start_idx + 1
    right_idx = end_idx
    while right_idx >= left_idx:
        if array[left_idx] > array[pivot_idx] > array[right_idx]:
            swap(left_idx, right_idx, array)
        if array[left_idx] <= array[pivot_idx]:
            left_idx += 1
        if array[right_idx] >= array[pivot_idx]:
            right_idx -= 1
    swap(pivot_idx, right_idx, array)
    left_sub_array_is_smaller = right_idx - 1 - start_idx < end_idx - (right_idx + 1)
    if left_sub_array_is_smaller:
        quick_sort_helper(array, start_idx, right_idx - 1)
        quick_sort_helper(array, right_idx + 1, end_idx)
    else:
        quick_sort_helper(array, right_idx + 1, end_idx)
        quick_sort_helper(array, start_idx, right_idx - 1)


def swap(i, j, array):
    array[i], array[j] = array[j], array[i]


if __name__ == '__main__':
    a = [8, 5, 2, 9, 5, 6, 3]
    print(quick_sort(a))
```

Last Modified 2021-06-26

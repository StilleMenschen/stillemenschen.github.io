# 排序

> 所谓的稳定排序是指原来相等的两个元素前后相对位置在排序后依然不变

## 冒泡排序

```python
# Best: O(n) time | O(1) space
# Average: O(n^2) time | O(1) space
# Worst: O(n^2) time | O(1) space
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

> 选择排序是不稳定的排序算法，在一趟选择时，如果当前锁定元素比后面一个元素大，而后面较小的那个元素又出现在一个与当前锁定
> 元素相等的元素后面，那么交换后位置顺序显然改变了。

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

> 快速排序是不稳定的，快速算法是找出一个中枢元素来分开排序，当中枢元素发生交换时元素的相对位置发生变化了

## 堆排序

堆是一棵完全二叉树，堆的每个节点的值都大于或等于其子节点的值，为最大堆；反之为最小堆。

```python
# O(n * log(n)) time | O(1) space
def heap_sort(array):
    build_max_heap(array)
    for end_idx in reversed(range(1, len(array))):
        swap(0, end_idx, array)
        sift_down(0, end_idx - 1, array)
    return array


def build_max_heap(array):
    first_parent_idx = (len(array) - 1) // 2
    for current_idx in reversed(range(first_parent_idx + 1)):
        sift_down(current_idx, len(array) - 1, array)


def sift_down(current_idx, end_idx, heap):
    child_one_idx = current_idx * 2 + 1
    while child_one_idx <= end_idx:
        child_two_idx = current_idx * 2 + 2 if current_idx * 2 + 2 <= end_idx else -1
        if child_two_idx > -1 and heap[child_two_idx] > heap[child_one_idx]:
            idx_to_swap = child_two_idx
        else:
            idx_to_swap = child_one_idx
        if heap[idx_to_swap] > heap[current_idx]:
            swap(current_idx, idx_to_swap, heap)
            current_idx = idx_to_swap
            child_one_idx = current_idx * 2 + 1
        else:
            return False


def swap(i, j, array):
    array[i], array[j] = array[j], array[i]


if __name__ == '__main__':
    a = [8, 5, 2, 9, 5, 6, 3]
    print(heap_sort(a))
```

> 堆排序是不稳定的排序算法，堆的结构是节点 i 的孩子为 2 _ i 和 2 _ i + 1 节点，大顶堆要求父节点大于等于其 2 个子节点，小
> 顶堆要求父节点小于等于其 2 个子节点。在一个长为 n 的序列，堆排序的过程是从第 n / 2 开始和其子节点共 3 个值选择最大(大
> 顶堆)或者最小(小顶堆),这 3 个元素之间的选择当然不会破坏稳定性。但当为 n / 2 - 1, n / 2 - 2, … 1 这些个父节点选择元素时
> ，就会破坏稳定性。有可能第 n / 2 个父节点交换把后面一个元素交换过去了，而第 n / 2 - 1 个父节点把后面一个相同的元素没有
> 交换，那么这 2 个相同的元素之间的稳定性就被破坏了。

## 归并排序

先拆分为较小的子序列，然后对子序列排序，再慢慢将每个子序列按大小对比后合并到主序列中

```python
# O(n * log(n)) time | O(n * log(n)) space
def merge_sort1(array):
    if len(array) == 1:
        return array
    middle_idx = len(array) // 2
    left_half = array[:middle_idx]
    right_half = array[middle_idx:]
    return merge_sorted_arrays(merge_sort1(left_half), merge_sort1(right_half))


def merge_sorted_arrays(left_half, right_half):
    sorted_array = [None] * (len(left_half) + len(right_half))
    i = j = k = 0
    while i < len(left_half) and j < len(right_half):
        if left_half[i] <= right_half[j]:
            sorted_array[k] = left_half[i]
            i += 1
        else:
            sorted_array[k] = right_half[j]
            j += 1
        k += 1
    while i < len(left_half):
        sorted_array[k] = left_half[i]
        i += 1
        k += 1
    while j < len(right_half):
        sorted_array[k] = right_half[j]
        j += 1
        k += 1
    return sorted_array


# O(n * log(n)) time | O(n) space
def merge_sort2(array):
    if len(array) <= 1:
        return array
    auxiliary_array = array[:]
    merge_sort_helper(array, 0, len(array) - 1, auxiliary_array)
    return array


def merge_sort_helper(main_array, start_idx, end_idx, auxiliary_array):
    if start_idx == end_idx:
        return False
    middle_idx = (start_idx + end_idx) // 2
    merge_sort_helper(auxiliary_array, start_idx, middle_idx, main_array)
    merge_sort_helper(auxiliary_array, middle_idx + 1, end_idx, main_array)
    do_merge(main_array, start_idx, middle_idx, end_idx, auxiliary_array)


def do_merge(main_array, start_idx, middle_idx, end_idx, auxiliary_array):
    k = start_idx
    i = start_idx
    j = middle_idx + 1
    while i <= middle_idx and j <= end_idx:
        if auxiliary_array[i] <= auxiliary_array[j]:
            main_array[k] = auxiliary_array[i]
            i += 1
        else:
            main_array[k] = auxiliary_array[j]
            j += 1
        k += 1
    while i <= middle_idx:
        main_array[k] = auxiliary_array[i]
        i += 1
        k += 1
    while j <= end_idx:
        main_array[k] = auxiliary_array[j]
        j += 1
        k += 1


if __name__ == '__main__':
    a = [8, 5, 2, 9, 5, 6, 3]
    print(merge_sort1(a))
    print(merge_sort2(a))
```

Last Modified 2021-06-28

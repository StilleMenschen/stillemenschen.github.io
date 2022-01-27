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

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Best: O(n) time | O(1) space
// Average: O(n^2) time | O(1) space
// Worst: O(n^2) time | O(1) space
vector<int> bubbleSort(vector<int> array)
{
    if (array.empty())
    {
        return {};
    }
    bool isSorted = false;
    int counter = 0;
    while (!isSorted)
    {
        isSorted = true;
        for (size_t i = 0; i < array.size() - 1 - counter; i++)
        {
            if (array[i] > array[i + 1])
            {
                swap(array[i], array[i + 1]);
                isSorted = false;
            }
        }
        counter++;
    }
    return array;
}

int main()
{
    vector<int> source = {8, 5, 2, 9, 5, 6, 3};
    source = bubbleSort(source);
    for (const int e : source)
    {
        cout << e << " ";
    }
    return 0;
}
```

## 插入排序

```python
# Best: O(n) time | O(1) space
# Average: O(n^2) time | O(1) space
# Worst: O(n^2) time | O(1) space
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
    a = [8, -4, 5, 2, 9, 5, 6, 3]
    print(insertion_sort(a))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Best: O(n) time | O(1) space
// Average: O(n^2) time | O(1) space
// Worst: O(n^2) time | O(1) space
vector<int> insertionSort(vector<int> array)
{
    if (array.empty())
    {
        return {};
    }
    for (int i = 1; i < array.size(); i++)
    {
        int j = i;
        while (j > 0 && array[j] < array[j - 1])
        {
            swap(array[j], array[j - 1]);
            j -= 1;
        }
    }
    return array;
}

int main()
{
    vector<int> source = {89, 620, -87, 52, 6, 10, 7, 152, 354};
    source = insertionSort(source);
    for (const int e : source)
    {
        cout << e << " ";
    }
    return 0;
}
```

## 选择排序

```python
# Best: O(n^2) time | O(1) space
# Average: O(n^2) time | O(1) space
# Worst: O(n^2) time | O(1) space
def selection_sort(array):
    if not len(array):
        return array
    current_idx = 0
    length = len(array)
    while current_idx < length - 1:
        smallest_idx = current_idx
        for i in range(current_idx + 1, length):
            if array[smallest_idx] > array[i]:
                smallest_idx = i
        if smallest_idx != current_idx:
            swap(current_idx, smallest_idx, array)
        current_idx += 1
    return array


def swap(i, j, array):
    array[i], array[j] = array[j], array[i]


if __name__ == '__main__':
    a = [8, 1, 2, -6, 9, 5, 6, 3, -2]
    print(selection_sort(a))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Best: O(n^2) time | O(1) space
// Average: O(n^2) time | O(1) space
// Worst: O(n^2) time | O(1) space
vector<int> selectionSort(vector<int> array)
{
    if (array.empty())
    {
        return array;
    }
    size_t startIdx = 0;
    const size_t length = array.size();
    while (startIdx < length - 1)
    {
        size_t smallestIdx = startIdx;
        for (size_t i = startIdx + 1; i < length; i++)
        {
            if (array[smallestIdx] > array[i])
            {
                smallestIdx = i;
            }
        }
        if (smallestIdx != startIdx)
        {
            swap(array[startIdx], array[smallestIdx]);
        }
        startIdx++;
    }
    return array;
}

int main()
{
    vector<int> source = {8, -10, 5, 2, 9, -5, 6, 3};
    source = selectionSort(source);
    for (const int element : source)
    {
        cout << element << " ";
    }
    return 0;
}
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

```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <algorithm>
using namespace std;

// O(n) time | O(1) space - where n is the length of the array
vector<int> threeNumberSort1(vector<int> array, vector<int> order)
{
    vector<int> valueCounts = {0, 0, 0};

    for (int element : array)
    {
        vector<int>::iterator it = find(order.begin(), order.end(), element);
        int orderIdx = distance(order.begin(), it);
        valueCounts[orderIdx] += 1;
    }

    for (int i = 0; i < 3; i++)
    {
        int value = order[i];
        int count = valueCounts[i];

        int numElementsBefore =
            accumulate(valueCounts.begin(), valueCounts.begin() + i, 0);
        for (int n = 0; n < count; n++)
        {
            int currentIdx = numElementsBefore + n;
            array[currentIdx] = value;
        }
    }

    return array;
}

// O(n) time | O(1) space - where n is the length of the array
vector<int> threeNumberSort2(vector<int> array, vector<int> order)
{
    int firstValue = order[0];
    int thirdValue = order[2];

    int firstIdx = 0;
    for (size_t i = 0; i < array.size(); i++)
    {
        if (array[i] == firstValue)
        {
            swap(array[firstIdx], array[i]);
            firstIdx += 1;
        }
    }

    int thirdIdx = array.size() - 1;
    for (int i = array.size() - 1; i >= 0; i--)
    {
        if (array[i] == thirdValue)
        {
            swap(array[thirdIdx], array[i]);
            thirdIdx -= 1;
        }
    }

    return array;
}

// O(n) time | O(1) space - where n is the length of the array
vector<int> threeNumberSort3(vector<int> array, vector<int> order)
{
    int firstValue = order[0];
    int secondValue = order[1];

    int firstIdx = 0;
    int secondIdx = 0;
    int thirdIdx = array.size() - 1;

    while (secondIdx <= thirdIdx)
    {
        int value = array[secondIdx];

        if (value == firstValue)
        {
            swap(array[firstIdx], array[secondIdx]);
            firstIdx += 1;
            secondIdx += 1;
        }
        else if (value == secondValue)
        {
            secondIdx += 1;
        }
        else
        {
            swap(array[secondIdx], array[thirdIdx]);
            thirdIdx -= 1;
        }
    }

    return array;
}

void iterArray(vector<int> array)
{
    for (const int e : array)
    {
        cout << e << " ";
    }
    cout << endl;
}

int main()
{
    vector<int> source = {-2, -3, -3, 0, -3, -3, -2, -2, -3};
    vector<int> order = {-2, -3, 0};
    vector<int> result;
    result = threeNumberSort1(source, order);
    iterArray(result);
    result = threeNumberSort2(source, order);
    iterArray(result);
    result = threeNumberSort3(source, order);
    iterArray(result);
    return 0;
}
```

## 快速排序

```python
# Best: O(nlog(n)) time | O(log(n)) space
# Average: O(nlog(n)) time | O(log(n)) space
# Worst: O(n^2) time | O(log(n)) space
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
    left_subarray_is_smaller = right_idx - 1 - start_idx < end_idx - (right_idx + 1)
    if left_subarray_is_smaller:
        quick_sort_helper(array, start_idx, right_idx - 1)
        quick_sort_helper(array, right_idx + 1, end_idx)
    else:
        quick_sort_helper(array, right_idx + 1, end_idx)
        quick_sort_helper(array, start_idx, right_idx - 1)


def swap(i, j, array):
    array[i], array[j] = array[j], array[i]


if __name__ == '__main__':
    source = [8, 5, -3, 2, 9, 6, 3]
    print(quick_sort(source))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

void quickSortHelper(vector<int> &array, int startIdx, int endIdx);

// Best: O(nlog(n)) time | O(log(n)) space
// Average: O(nlog(n)) time | O(log(n)) space
// Worst: O(n^2) time | O(log(n)) space
vector<int> quickSort(vector<int> array)
{
    quickSortHelper(array, 0, array.size() - 1);
    return array;
}

void quickSortHelper(vector<int> &array, int startIdx, int endIdx)
{
    if (startIdx >= endIdx)
    {
        return;
    }
    int pivotIdx = startIdx;
    int leftIdx = startIdx + 1;
    int rightIdx = endIdx;
    while (rightIdx >= leftIdx)
    {
        if (array.at(leftIdx) > array.at(pivotIdx) &&
            array.at(rightIdx) < array.at(pivotIdx))
        {
            swap(array[leftIdx], array[rightIdx]);
        }
        if (array.at(leftIdx) <= array.at(pivotIdx))
        {
            leftIdx += 1;
        }
        if (array.at(rightIdx) >= array.at(pivotIdx))
        {
            rightIdx -= 1;
        }
    }
    swap(array[pivotIdx], array[rightIdx]);
    bool leftSubarrayIsSmaller =
        rightIdx - 1 - startIdx < endIdx - (rightIdx + 1);
    if (leftSubarrayIsSmaller)
    {
        quickSortHelper(array, startIdx, rightIdx - 1);
        quickSortHelper(array, rightIdx + 1, endIdx);
    }
    else
    {
        quickSortHelper(array, rightIdx + 1, endIdx);
        quickSortHelper(array, startIdx, rightIdx - 1);
    }
}

int main()
{
    vector<int> source = {8, 5, -3, 2, 9, 6, 3};
    source = quickSort(source);
    for (const int element : source)
    {
        cout << element << " ";
    }
    return 0;
}
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

> 堆排序是不稳定的排序算法，堆的结构是节点 i 的孩子为 2 * i 和 2 * i + 1 节点，大顶堆要求父节点大于等于其 2 个子节点，
> 小顶堆要求父节点小于等于其 2 个子节点。在一个长为 n 的序列，堆排序的过程是从第 n / 2 开始和其子节点共 3 个值选择最大(大顶堆)或者最小(小顶堆),
> 这 3 个元素之间的选择当然不会破坏稳定性。但当为 n / 2 - 1, n / 2 - 2, … 1 这些个父节点选择元素时，
> 就会破坏稳定性。有可能第 n / 2 个父节点交换把后面一个元素交换过去了，而第 n / 2 - 1 个父节点把后面一个相同的元素没有交换，
> 那么这 2 个相同的元素之间的稳定性就被破坏了。

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

## 基数排序

> 基数排序不支持负数，仅支持正数排序

```python
# O(d * (n + b)) time | O(n + b) space - where n is the length of the input array,
# d is the max number of digits, and b is the base of the numbering system used
def radix_sort(array):
    if len(array) == 0:
        return array

    max_number = max(array)

    digit = 0
    while max_number / 10 ** digit > 0:
        counting_sort(array, digit)
        digit += 1

    return array


def counting_sort(array, digit):
    sorted_array = [0] * len(array)
    count_array = [0] * 10

    digit_column = 10 ** digit
    for num in array:
        count_index = (num // digit_column) % 10
        count_array[count_index] += 1

    for idx in range(1, 10):
        count_array[idx] += count_array[idx - 1]

    for idx in range(len(array) - 1, -1, -1):
        count_index = (array[idx] // digit_column) % 10
        count_array[count_index] -= 1
        sorted_index = count_array[count_index]
        sorted_array[sorted_index] = array[idx]

    for idx in range(len(array)):
        array[idx] = sorted_array[idx]


if __name__ == '__main__':
    source = [518, 15, 42, 109, 5, 376, 3001]
    print(radix_sort(source))
```

```cpp
#include <vector>
#include <algorithm>
#include <iostream>
using namespace std;

void countingSort(vector<int> &array, int digit);
int myPow(int base, int exponent);

// O(d * (n + b)) time | O(n + b) space - where n is the length of the input
// array, d is the max number of digits, and b is the base of the numbering
// system used
vector<int> radixSort(vector<int> array)
{
    if (array.size() == 0)
        return array;

    int maxNumber = *max_element(array.begin(), array.end());

    int digit = 0;
    while (maxNumber / (myPow(10, digit)) > 0)
    {
        countingSort(array, digit);
        digit++;
    }
    return array;
}

void countingSort(vector<int> &array, int digit)
{
    vector<int> sortedArray(array.size(), 0);
    vector<int> countArray(10, 0);
    int arraySize = array.size();

    int digitColumn = myPow(10, digit);
    for (auto num : array)
    {
        int countIndex = num / digitColumn % 10;
        countArray[countIndex]++;
    }

    for (int idx = 1; idx < 10; idx++)
    {
        countArray[idx] += countArray[idx - 1];
    }

    for (int idx = arraySize - 1; idx >= 0; idx--)
    {
        int countIndex = array[idx] / digitColumn % 10;
        countArray[countIndex]--;
        int sortedIndex = countArray[countIndex];
        sortedArray[sortedIndex] = array[idx];
    }

    for (int idx = 0; idx < arraySize; idx++)
    {
        array[idx] = sortedArray[idx];
    }
}

int myPow(int base, int exponent)
{
    int result = 1;
    for (int i = 1; i <= exponent; i++)
        result *= base;
    return result;
}

int main()
{
    vector<int> source = {8762, 654, 3008, 345, 87, 65, 234, 12, 2};
    source = radixSort(source);
    for(const int e : source)
    {
        cout << e << " ";
    }
    return 0;
}
```

Last Modified 2022-01-27

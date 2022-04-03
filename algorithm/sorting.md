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

void iteration(vector<int> array)
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
    iteration(result);
    result = threeNumberSort2(source, order);
    iteration(result);
    result = threeNumberSort3(source, order);
    iteration(result);
    return 0;
}
```

## 快速排序

```python
# Best: O(n * log(n)) time | O(log(n)) space
# Average: O(n * log(n)) time | O(log(n)) space
# Worst: O(n^2) time | O(log(n)) space
def quick_sort(array):
    # 从数组的两端开始执行递归
    quick_sort_helper(array, 0, len(array) - 1)
    return array


def quick_sort_helper(array, start_idx, end_idx):
    # 如果开始索引大于等于结束索引说明已经排序完成
    if start_idx >= end_idx:
        return False
    # 取开始索引作为中枢元素
    pivot_idx = start_idx
    # 取开始索引加 1 为左指针
    left_idx = start_idx + 1
    # 取结束索引为右指针
    right_idx = end_idx
    # 不停比较并移动左右指针
    while right_idx >= left_idx:
        # 如果左指针的值比中枢值大且右指针的值, 说明左右指针的值需要做交换
        if array[left_idx] > array[pivot_idx] > array[right_idx]:
            swap(left_idx, right_idx, array)
        # 如果左指针的值小于等于中枢值, 说明左指针的值已经在正确的位置, 移动左指针
        if array[left_idx] <= array[pivot_idx]:
            left_idx += 1
        # 如果右指针的值大于等于中枢值, 说明右指针的值已经在正确的位置, 移动右指针
        if array[right_idx] >= array[pivot_idx]:
            right_idx -= 1
    # 处理完成后交换右指针和中枢位置的值, 右指针最后一次指向的位置必定是小于中枢值的
    swap(pivot_idx, right_idx, array)
    # 通过索引判断左右区间大小, 优先处理区间范围小的数据
    left_subarray_is_smaller = right_idx - 1 - start_idx < end_idx - (right_idx + 1)
    if left_subarray_is_smaller:
        # 左边的区间小, 先排序左边的数据
        quick_sort_helper(array, start_idx, right_idx - 1)
        quick_sort_helper(array, right_idx + 1, end_idx)
    else:
        # 右边的区间小, 先排序右边的数据
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

// Best: O(n * log(n)) time | O(log(n)) space
// Average: O(n * log(n)) time | O(log(n)) space
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
# Best: O(n * log(n)) time | O(1) space
# Average: O(n * log(n)) time | O(1) space
# Worst: O(n * log(n)) time | O(1) space
def heap_sort(array):
    # 先创建一个最大堆
    build_max_heap(array)
    # 使用堆算法将值较大的元素放到后面
    # 从数组最后一个位置逆序迭代第二个位置
    for end_idx in reversed(range(1, len(array))):
        # 先交换第一个元素和当前元素
        swap(0, end_idx, array)
        # 然后通过堆算法将当前元素移位到合适的位置
        sift_down(0, end_idx - 1, array)
    return array


def build_max_heap(array):
    # 最大堆的特点是祖先元素总是大于等于子元素, 最小堆则相反
    # 从数组的中间位置开始
    first_parent_idx = (len(array) - 2) // 2
    # 从中间位置逆序到数组开始位置
    for current_idx in reversed(range(first_parent_idx + 1)):
        # 通过堆算法将较大的元素移位到合适的位置
        sift_down(current_idx, len(array) - 1, array)


def sift_down(current_idx, end_idx, heap):
    # 先获得左子树的位置
    child_one_idx = current_idx * 2 + 1
    # 循环直到左子树索引超过结束索引
    while child_one_idx <= end_idx:
        # 获取右子树的索引, 如果已经越界则表示没有右子树, 设置为 -1
        child_two_idx = current_idx * 2 + 2 if current_idx * 2 + 2 <= end_idx else -1
        # 如果存在右子树, 则比较左右子树找出最大的值并标记为待交换的值
        if child_two_idx > -1 and heap[child_two_idx] > heap[child_one_idx]:
            idx_to_swap = child_two_idx
        # 没有右子树则待交换的元素为左子树
        else:
            idx_to_swap = child_one_idx
        # 判断当前元素和待交换的子元素大小, 子元素比当前元素大则进行交换
        if heap[idx_to_swap] > heap[current_idx]:
            # 交换祖先元素和其子元素
            swap(current_idx, idx_to_swap, heap)
            # 将交换后的子元素设置为下一个待处理的祖先元素
            current_idx = idx_to_swap
            # 重新计算左子树位置
            child_one_idx = current_idx * 2 + 1
        else:
            break


def swap(i, j, array):
    array[i], array[j] = array[j], array[i]


if __name__ == '__main__':
    print(heap_sort([8, 9, -2, -6, 0, 5, 1]))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

void buildMaxHeap(vector<int> &array);
void siftDown(int currentIdx, int endIdx, vector<int> &heap);

// Best: O(n * log(n)) time | O(1) space
// Average: O(n * log(n)) time | O(1) space
// Worst: O(n * log(n)) time | O(1) space
vector<int> heapSort(vector<int> array)
{
    buildMaxHeap(array);
    for (int endIdx = array.size() - 1; endIdx > 0; endIdx--)
    {
        swap(array[0], array[endIdx]);
        siftDown(0, endIdx - 1, array);
    }
    return array;
}

void buildMaxHeap(vector<int> &array)
{
    int firstParentIdx = (array.size() - 2) / 2;
    for (int currentIdx = firstParentIdx; currentIdx >= 0; currentIdx--)
    {
        siftDown(currentIdx, array.size() - 1, array);
    }
}

void siftDown(int currentIdx, int endIdx, vector<int> &heap)
{
    int childOneIdx = currentIdx * 2 + 1;
    while (childOneIdx <= endIdx)
    {
        int childTwoIdx = currentIdx * 2 + 2 <= endIdx ? currentIdx * 2 + 2 : -1;
        int idxToSwap;
        if (childTwoIdx != -1 && heap.at(childTwoIdx) > heap.at(childOneIdx))
        {
            idxToSwap = childTwoIdx;
        }
        else
        {
            idxToSwap = childOneIdx;
        }
        if (heap.at(idxToSwap) > heap.at(currentIdx))
        {
            swap(heap[currentIdx], heap[idxToSwap]);
            currentIdx = idxToSwap;
            childOneIdx = currentIdx * 2 + 1;
        }
        else
        {
            return;
        }
    }
}

int main()
{
    vector<int> source = {8, 9, -2, -6, 0, 5, 1};
    source = heapSort(source);
    for (int element : source)
    {
        cout << element << " ";
    }
    return 0;
}
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
    # 找出数组中最大的数
    # 因为基数排序是按最大数的位数来计算并排序的
    max_number = max(array)
    # 从第一位基数开始
    digit = 0
    # 超过最大数的整数位数则表示排序完成
    while max_number // 10 ** digit > 0:
        # 计算基数并排序
        counting_sort(array, digit)
        # 累加基数, 如 个位 -> 十位 -> 百位 以此类推
        digit += 1

    return array


def counting_sort(array, digit):
    # 缓存, 用来记录排序后的数组
    sorted_array = [0 for _ in array]
    # 基数缓存(0-9), 记录每个基数的数量
    # 如当前排序的是每个元素的个位数, 假设数组中有三个元素的个位数为 1, 则缓存数组中索引 1 记录为 3
    count_array = [0 for _ in range(10)]
    # 表示当前处理的是第几位基数, 如 个位 -> 十位 -> 百位 以此类推
    digit_column = 10 ** digit
    for num in array:
        # 取出元素的基数, 如果当前元素没有此基数
        # 如 42 是没有百位数的, 则此数的基数缓存索引为 0
        count_index = (num // digit_column) % 10
        # 记录到基数缓存数组中
        count_array[count_index] += 1
    # 从左向右累加基数缓存数组中的数量, 后面重新调整元素的顺序需要
    for idx in range(1, 10):
        count_array[idx] += count_array[idx - 1]
    # 根据基数缓存数组中的数量来确定元素的位置
    # 逆序处理是为了防止打乱前面已经排序正确的元素位置
    # 比如有两个元素的个位数大小不一样, 但十位数的大小一致, 如 67 和 68
    # 正序处理的话可能会把原来正确的 67, 68 变成了 68, 67
    for idx in reversed(range(len(array))):
        # 获取当前元素的基数
        count_index = (array[idx] // digit_column) % 10
        # 递减基数缓存数组中当前元素的基数记录数量
        count_array[count_index] -= 1
        # 获得该元素的正确排序位置
        sorted_index = count_array[count_index]
        # 根据计算正确的位置重新记录元素的位置
        sorted_array[sorted_index] = array[idx]
    # 将排序好的数组拷贝到原来的数组中
    for idx in range(len(array)):
        array[idx] = sorted_array[idx]


if __name__ == '__main__':
    print(radix_sort([12, 123, 456, 986, 2, 3, 34, 543, 97654, 34200]))
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
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
    const int arraySize = array.size();
    vector<int> sortedArray(arraySize, 0);
    vector<int> countArray(10, 0);

    int digitColumn = myPow(10, digit);
    for (int num : array)
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
    vector<int> source = {12, 123, 456, 986, 2, 3, 34, 543, 97654, 34200};
    source = radixSort(source);
    for (const int element : source)
    {
        cout << element << " ";
    }
    return 0;
}
```

Last Modified 2022-04-03

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
    result = [float('-inf') for _ in range(3)]
    for e in array:
        find_three_largest_numbers_helper(result, e)
    return result


if __name__ == '__main__':
    source = [-81, 103, -2, 55, 99, 151, 815]
    print(find_three_largest_numbers(source))
```

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

void updateLargest(vector<int> &threeLargest, int num);
void shiftAndUpdate(vector<int> &largest, int num, int idx);

// O(n) time | O(1) space
vector<int> findThreeLargestNumbers(vector<int> array)
{
  vector<int> threeLargest{INT_MIN, INT_MIN, INT_MIN};
  for (int num : array)
  {
    updateLargest(threeLargest, num);
  }
  return threeLargest;
}

void updateLargest(vector<int> &threeLargest, int num)
{
  if (num > threeLargest[2])
  {
    shiftAndUpdate(threeLargest, num, 2);
  }
  else if (num > threeLargest[1])
  {
    shiftAndUpdate(threeLargest, num, 1);
  }
  else if (num > threeLargest[0])
  {
    shiftAndUpdate(threeLargest, num, 0);
  }
}

void shiftAndUpdate(vector<int> &array, int num, int idx)
{
  for (int i = 0; i <= idx; i++)
  {
    if (i == idx)
    {
      array[i] = num;
    }
    else
    {
      array[i] = array[i + 1];
    }
  }
}

int main()
{
  vector<int> source = {-81, 103, -2, 55, 99, 151, 815};
  vector<int> result = findThreeLargestNumbers(source);
  cout << result[0] << ", " << result[1] << ", " << result[2];
  return 0;
}
```

## 搜索排序矩阵

```python
# O(n + m) time | O(1) space
def search_in_sorted_matrix(matrix, target):
    height = len(matrix)
    width = len(matrix[0])
    row = 0
    col = width - 1
    while row < height and col >= 0:
        val = matrix[row][col]
        if val > target:
            col -= 1
        elif val < target:
            row += 1
        else:
            return [row, col]
    return [-1, -1]


if __name__ == '__main__':
    source = [
        [1, 4, 7, 12, 15, 1000],
        [2, 5, 19, 31, 32, 1001],
        [3, 8, 24, 33, 35, 1002],
        [40, 41, 42, 44, 45, 1003],
        [99, 100, 103, 106, 128, 1004],
    ]
    print(search_in_sorted_matrix(source, 44))
```

```cpp
#include <iostream>
#include <vector>

using namespace std;

// O(n + m) time | O(1) space
vector<int> searchInSortedMatrix(vector<vector<int>> matrix, int target)
{
  const int height = matrix.size();
  const int width = matrix[0].size();
  int row = 0, col = width - 1;
  while (row < height and col >= 0)
  {
    int val = matrix[row][col];
    if (val > target)
    {
      col--;
    }
    else if (val < target)
    {
      row++;
    }
    else
    {
      return {row, col};
    }
  }

  return {-1, -1};
}

int main()
{
  vector<vector<int>> source = {
      {1, 4, 7, 12, 15, 1000},
      {2, 5, 19, 31, 32, 1001},
      {3, 8, 24, 33, 35, 1002},
      {40, 41, 42, 44, 45, 1003},
      {99, 100, 103, 106, 128, 1004}};
  vector<int> result = searchInSortedMatrix(source, 44);
  cout << result[0] << ", " << result[1];
  return 0;
}
```

## 移位二叉树搜索

给定一个数组和一个值，在数组中查找这个值并返回其索引，找不到则返回 -1 ；数组是被打乱的有序数组，被打乱顺序前是从左向右由小到大排序的；

```python
# O(log(n)) time | O(log(n)) space
def shifted_binary_search1(array, target):
    # 从开头位置和末尾位置开始递归处理
    return shifted_binary_search_helper1(array, target, 0, len(array) - 1)


def shifted_binary_search_helper1(array, target, left, right):
    # 左侧指针已经超过了右侧指针, 说明已经全部搜索完且未找到目标值
    if left > right:
        return -1
    # 计算中间位置的索引, 向下取整
    middle = (left + right) // 2
    # 获得中间位置的实际值
    potential_match = array[middle]
    # 左侧指针指向的值
    left_num = array[left]
    # 右侧指针指向的值
    right_num = array[right]
    # 如果目标值和中间位置的值相等说明找到了, 直接返回目标值的索引
    if target == potential_match:
        return middle
    # 因为传入的数组是被打乱的有序数组 (从左到右, 从小到大)
    # 先根据左侧指针指向的值和中间位置的值判断是否数组从左向右递增
    elif left_num <= potential_match:
        # 判断目标值是否在左侧的范围区间内
        if left_num <= target < potential_match:
            # 在左侧的区间中继续查找
            return shifted_binary_search_helper1(array, target, left, middle - 1)
        else:
            # 否则在右侧的区间中继续查找
            return shifted_binary_search_helper1(array, target, middle + 1, right)
    # 再根据中间位置的值和右侧指针指向的值判断是否数组从左向右递增
    else:
        # 判断目标值是否在右侧的范围区间内
        if potential_match < target <= right_num:
            # 在右侧的区间中继续查找
            return shifted_binary_search_helper1(array, target, middle + 1, right)
        else:
            # 否则在左侧的区间中继续查找
            return shifted_binary_search_helper1(array, target, left, middle - 1)


# O(log(n)) time | O(1) space
def shifted_binary_search2(array, target):
    return shifted_binary_search_helper2(array, target, 0, len(array) - 1)


def shifted_binary_search_helper2(array, target, left, right):
    # 非递归实现
    while left <= right:
        middle = (left + right) // 2
        potential_match = array[middle]
        left_num = array[left]
        right_num = array[right]
        if target == potential_match:
            # 找到目标值直接返回其索引
            return middle
        elif left_num <= potential_match:
            if left_num <= target < potential_match:
                # 修改右指针为中间指针的前一个
                right = middle - 1
            else:
                # 修改左指针为中间指针的后一个
                left = middle + 1
        else:
            if potential_match < target <= right_num:
                # 修改左指针为中间指针的后一个
                left = middle + 1
            else:
                # 修改右指针为中间指针的前一个
                right = middle - 1
    # 循环执行完但没有找到则返回
    return -1


if __name__ == '__main__':
    source = [45, 61, 71, 72, 73, 0, 1, 21, 33, 37]
    print(shifted_binary_search1(source, 33))
    print(shifted_binary_search2(source, 71))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

int shiftedBinarySearch1(vector<int> array, int target);
int shiftedBinarySearchHelper1(vector<int> array, int target, int left, int right);

// O(log(n)) time | O(log(n)) space
int shiftedBinarySearch1(vector<int> array, int target)
{
    return shiftedBinarySearchHelper1(array, target, 0, array.size() - 1);
}

int shiftedBinarySearchHelper1(vector<int> array, int target, int left, int right)
{
    if (left > right)
    {
        return -1;
    }
    int middle = (left + right) / 2;
    int potentialMatch = array[middle];
    int leftNum = array[left];
    int rightNum = array[right];
    if (target == potentialMatch)
    {
        return middle;
    }
    else if (leftNum <= potentialMatch)
    {
        if (target < potentialMatch && target >= leftNum)
        {
            return shiftedBinarySearchHelper1(array, target, left, middle - 1);
        }
        else
        {
            return shiftedBinarySearchHelper1(array, target, middle + 1, right);
        }
    }
    else
    {
        if (target > potentialMatch && target <= rightNum)
        {
            return shiftedBinarySearchHelper1(array, target, middle + 1, right);
        }
        else
        {
            return shiftedBinarySearchHelper1(array, target, left, middle - 1);
        }
    }
}

int shiftedBinarySearch2(vector<int> array, int target);
int shiftedBinarySearchHelper2(vector<int> array, int target, int left, int right);

// O(log(n)) time | O(1) space
int shiftedBinarySearch2(vector<int> array, int target)
{
    return shiftedBinarySearchHelper2(array, target, 0, array.size() - 1);
}

int shiftedBinarySearchHelper2(vector<int> array, int target, int left, int right)
{
    while (left <= right)
    {
        int middle = (left + right) / 2;
        int potentialMatch = array[middle];
        int leftNum = array[left];
        int rightNum = array[right];
        if (target == potentialMatch)
        {
            return middle;
        }
        else if (leftNum <= potentialMatch)
        {
            if (target < potentialMatch && target >= leftNum)
            {
                right = middle - 1;
            }
            else
            {
                left = middle + 1;
            }
        }
        else
        {
            if (target > potentialMatch && target <= rightNum)
            {
                left = middle + 1;
            }
            else
            {
                right = middle - 1;
            }
        }
    }
    return -1;
}

int main()
{
    vector<int> source = {45, 61, 71, 72, 73, 0, 1, 21, 33, 37};
    cout << shiftedBinarySearch1(source, 33) << endl;
    cout << shiftedBinarySearch2(source, 71) << endl;
    return 0;
}
```

## 数值所在范围

给定一个排序好的数组和一个目标值，找出目标值在数组中连续出现的开始和结束位置，未找到则返回 [-1, -1]

```python
# O(log(n)) time | O(log(n)) space
def search_for_range1(array, target):
    # 创建数组存储开始和结束范围
    final_range = [-1, -1]
    # 搜索目标值出现的开始位置
    altered_binary_search1(array, target, 0, len(array) - 1, final_range, True)
    # 搜索目标值出现的结束位置
    altered_binary_search1(array, target, 0, len(array) - 1, final_range, False)
    return final_range


def altered_binary_search1(array, target, left, right, final_range, to_left):
    # 如果左指针位置已经大于右指针位置, 说明已经搜索完数组
    if left > right:
        return
    # 计算中间位置索引
    mid = (left + right) // 2
    # 由于数组是排序的, 所以先判断当前中间位置的值是大于还是小于
    if array[mid] < target:
        # 当前中间位置的值小于目标值, 继续搜索右边
        altered_binary_search1(array, target, mid + 1, right, final_range, to_left)
    elif array[mid] > target:
        # 当前中间位置的值大于目标值, 继续搜索左边
        altered_binary_search1(array, target, left, mid - 1, final_range, to_left)
    else:
        # 这里用一个标记判断要搜索的是开始位置还是结束位置
        if to_left:
            # 如果开始位置是零或者向左一个位置的值不等于目标值, 则记录开始位置并结束搜索
            if mid == 0 or array[mid - 1] != target:
                final_range[0] = mid
            else:
                # 不满足上述条件则继续搜索开始位置
                altered_binary_search1(array, target, left, mid - 1, final_range, to_left)
        else:
            # 如果结束位置是数组末尾或者向右一个位置的值不等于目标值, 则记录结束位置并结束搜索
            if mid == len(array) - 1 or array[mid + 1] != target:
                final_range[1] = mid
            else:
                # 不满足上述条件则继续搜索结束位置
                altered_binary_search1(array, target, mid + 1, right, final_range, to_left)


# O(log(n)) time | O(1) space
def search_for_range2(array, target):
    final_range = [-1, -1]
    # 非递归实现, 搜索目标值的开始和结束位置
    altered_binary_search2(array, target, 0, len(array) - 1, final_range, True)
    altered_binary_search2(array, target, 0, len(array) - 1, final_range, False)
    return final_range


def altered_binary_search2(array, target, left, right, final_range, to_left):
    # 如果左指针位置小于等于右指针位置则继续循环
    while left <= right:
        mid = (left + right) // 2
        if array[mid] < target:
            # 当前中间值小于目标值, 修改左指针为中间位置向右加一
            left = mid + 1
        elif array[mid] > target:
            # 当前中间值大于目标值, 修改左指针为中间位置向左减一
            right = mid - 1
        else:
            if to_left:
                # 如果开始位置是零或者向左一个位置的值不等于目标值, 则记录开始位置并结束搜索
                if mid == 0 or array[mid - 1] != target:
                    final_range[0] = mid
                    # 结束循环
                    break
                else:
                    right = mid - 1
            else:
                # 如果结束位置是数组末尾或者向右一个位置的值不等于目标值, 则记录结束位置并结束搜索
                if mid == len(array) - 1 or array[mid + 1] != target:
                    final_range[1] = mid
                    # 结束循环
                    break
                else:
                    left = mid + 1


if __name__ == '__main__':
    print(search_for_range1([0, 1, 21, 33, 45, 45, 45, 45, 45, 45, 61, 71, 73], 45))
    print(search_for_range2([0, 1, 21, 33, 45, 45, 45, 45, 45, 45, 61, 71, 73], 45))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<int> searchForRange1(vector<int> array, int target);
void alteredBinarySearch1(vector<int> array, int target, int left, int right,
                          vector<int> *finalRange, bool goLeft);

// O(log(n)) time | O(log(n)) space
vector<int> searchForRange1(vector<int> array, int target)
{
    vector<int> finalRange{-1, -1};
    alteredBinarySearch1(array, target, 0, array.size() - 1, &finalRange, true);
    alteredBinarySearch1(array, target, 0, array.size() - 1, &finalRange, false);
    return finalRange;
}

void alteredBinarySearch1(vector<int> array, int target, int left, int right,
                          vector<int> *finalRange, bool goLeft)
{
    if (left > right)
    {
        return;
    }
    int mid = (left + right) / 2;
    if (array[mid] < target)
    {
        alteredBinarySearch1(array, target, mid + 1, right, finalRange, goLeft);
    }
    else if (array[mid] > target)
    {
        alteredBinarySearch1(array, target, left, mid - 1, finalRange, goLeft);
    }
    else
    {
        if (goLeft)
        {
            if (mid == 0 || array[mid - 1] != target)
            {
                finalRange->at(0) = mid;
            }
            else
            {
                alteredBinarySearch1(array, target, left, mid - 1, finalRange, goLeft);
            }
        }
        else
        {
            const int arraySize = array.size();
            if (mid == arraySize - 1 || array[mid + 1] != target)
            {
                finalRange->at(1) = mid;
            }
            else
            {
                alteredBinarySearch1(array, target, mid + 1, right, finalRange, goLeft);
            }
        }
    }
}

vector<int> searchForRange2(vector<int> array, int target);
void alteredBinarySearch2(vector<int> array, int target, int left, int right,
                          vector<int> *finalRange, bool goLeft);

// O(log(n)) time | O(1) space
vector<int> searchForRange2(vector<int> array, int target)
{
    vector<int> finalRange{-1, -1};
    alteredBinarySearch2(array, target, 0, array.size() - 1, &finalRange, true);
    alteredBinarySearch2(array, target, 0, array.size() - 1, &finalRange, false);
    return finalRange;
}

void alteredBinarySearch2(vector<int> array, int target, int left, int right,
                          vector<int> *finalRange, bool goLeft)
{
    while (left <= right)
    {
        int mid = (left + right) / 2;
        if (array[mid] < target)
        {
            left = mid + 1;
        }
        else if (array[mid] > target)
        {
            right = mid - 1;
        }
        else
        {
            if (goLeft)
            {
                if (mid == 0 || array[mid - 1] != target)
                {
                    finalRange->at(0) = mid;
                    return;
                }
                else
                {
                    right = mid - 1;
                }
            }
            else
            {
                const int arraySize = array.size();
                if (mid == arraySize - 1 || array[mid + 1] != target)
                {
                    finalRange->at(1) = mid;
                    return;
                }
                else
                {
                    left = mid + 1;
                }
            }
        }
    }
}

int main()
{
    vector<int> source = {0, 1, 21, 33, 45, 45, 45, 45, 45, 45, 61, 71, 73};
    vector<int> result = searchForRange1(source, 45);
    cout << "[" << result[0] << ", " << result[1] << "]" << endl;
    result = searchForRange2(source, 45);
    cout << "[" << result[0] << ", " << result[1] << "]" << endl;
    return 0;
}
```

## 快速选择

给定一个包含不重复数据且为乱序的数组，找出第 K 个最小值并返回

```python
# Best: O(n) time | O(1) space
# Average: O(n) time | O(1) space
# Worst: O(n^2) time | O(1) space
def quickselect(array, k):
    position = k - 1  # 查找位置对应数组索引
    return quickselect_helper(array, 0, len(array) - 1, position)


def quickselect_helper(array, start_idx, end_idx, position):
    while True:
        if start_idx > end_idx:
            raise Exception("Your algorithm should never arrive here!")
        # 设置一个中枢元素
        pivot_idx = start_idx
        # 左指针
        left_idx = start_idx + 1
        # 右指针
        right_idx = end_idx
        # 处理左右指针范围内的数据
        while left_idx <= right_idx:
            # 如果左指针对应的元素大于中枢元素, 同时中枢元素小于右指针对应的元素, 则交换
            if array[left_idx] > array[pivot_idx] > array[right_idx]:
                swap(left_idx, right_idx, array)
            # 左指针对应的元素小于等于中枢元素则向右移动
            if array[left_idx] <= array[pivot_idx]:
                left_idx += 1
            # 右指针对应的元素大于等于中枢元素则向左移动
            if array[right_idx] >= array[pivot_idx]:
                right_idx -= 1
        # 最后交换中枢元素和右指针对应的元素
        swap(pivot_idx, right_idx, array)
        # 如果右指针指向的位置已经符合待查找的第 K 个最小数则直接返回
        if right_idx == position:
            return array[right_idx]
        # 如果右指针在待查找位置之前, 说明要找的元素在右半部分
        # 则修改开始查找位置为右指针向后一位
        elif right_idx < position:
            start_idx = right_idx + 1
        # 如果右指针在待查找位置之后, 说明要找的元素在左半部分
        # 则修改末尾查找位置为右指针向前一位
        else:
            end_idx = right_idx - 1


def swap(one, two, array):
    array[one], array[two] = array[two], array[one]


if __name__ == '__main__':
    print(quickselect([8, 3, 2, 5, 1, 7, 4, 6], 3))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

int quickselectHelper(vector<int> array, int startIdx, int endIdx, int position);

// Best: O(n) time | O(1) space
// Average: O(n) time | O(1) space
// Worst: O(n^2) time | O(1) space
int quickselect(vector<int> array, int k)
{
    int position = k - 1;
    return quickselectHelper(array, 0, array.size() - 1, position);
}

int quickselectHelper(vector<int> array, int startIdx, int endIdx, int position)
{
    while (true)
    {
        if (startIdx > endIdx)
        {
            perror("Your Algorithm should never arrive here!");
            exit(1);
        }
        int pivotIdx = startIdx;
        int leftIdx = startIdx + 1;
        int rightIdx = endIdx;
        while (leftIdx <= rightIdx)
        {
            if (array[leftIdx] > array[pivotIdx] &&
                array[rightIdx] < array[pivotIdx])
            {
                swap(array[leftIdx], array[rightIdx]);
            }
            if (array[leftIdx] <= array[pivotIdx])
            {
                leftIdx++;
            }
            if (array[rightIdx] >= array[pivotIdx])
            {
                rightIdx--;
            }
        }
        swap(array[pivotIdx], array[rightIdx]);
        if (rightIdx == position)
        {
            return array[rightIdx];
        }
        else if (rightIdx < position)
        {
            startIdx = rightIdx + 1;
        }
        else
        {
            endIdx = rightIdx - 1;
        }
    }
}

int main()
{
    cout << quickselect({8, 3, 2, 5, 1, 7, 4, 6}, 3);
    return 0;
}
```

## 最小等于索引的数

给定一个排序好的数组，找出数组中索引等于其对应值的数，且这个数的索引是最小的

```python
# O(n) time | O(1) space - where n is the length of the input array
def index_equals_value1(array):
    # 从第一个索引向后比较找出最小的索引与值相同的位置
    for index in range(len(array)):
        value = array[index]
        if index == value:
            return index
    # 没有满足条件的数据则返回 -1
    return -1


# O(log(n)) time | O(1) space - where n is the length of the input array
def index_equals_value2(array):
    left_index = 0
    right_index = len(array) - 1
    # 非递归形式的二分查找
    while left_index <= right_index:
        middle_index = left_index + (right_index - left_index) // 2
        middle_value = array[middle_index]

        if middle_value < middle_index:
            # 这里不再是递归而是直接修改左指针为中间指针加 1
            left_index = middle_index + 1
        elif middle_value == middle_index and middle_index == 0:
            return middle_index
        elif middle_value == middle_index and array[middle_index - 1] < middle_index - 1:
            return middle_index
        else:
            # 这里不再是递归而是直接修改右指针为中间指针减 1
            right_index = middle_index - 1

    return -1


# O(log(n)) time | O(log(n)) space - where n is the length of the input array
def index_equals_value3(array):
    # 递归二分查找, 从第一个和最后一个位置开始
    return index_equals_value_helper(array, 0, len(array) - 1)


def index_equals_value_helper(array, left_index, right_index):
    # 超出查找范围后返回默认的 -1
    if left_index > right_index:
        return -1
    # 求出中间位置的索引
    middle_index = left_index + (right_index - left_index) // 2
    middle_value = array[middle_index]
    # 因为传入的数组是已经排序好的, 如果中间位置索引前面的值都比索引要小, 说明前面的数据都是不符合的
    if middle_value < middle_index:
        # 递归查找右区间
        return index_equals_value_helper(array, middle_index + 1, right_index)
    # 如果当前索引等于索引对应的值且为第一个位置则返回, 第一个位置必定是最小索引
    elif middle_value == middle_index and middle_index == 0:
        return middle_index
    # 如果当前索引等于索引且前一个索引的值不是当前索引的递减
    # 说明前面的数据都是不符合的, 当前的树已经属于最小索引了
    elif middle_value == middle_index and array[middle_index - 1] < middle_index - 1:
        return middle_index
    # 上面三个条件都不符合, 递归查找左区间
    else:
        return index_equals_value_helper(array, left_index, middle_index - 1)


if __name__ == '__main__':
    source = [-5, -4, -3, -2, -1, 0, 1, 3, 5, 6, 7, 11, 12, 14, 19, 20]
    print(index_equals_value1(source))
    print(index_equals_value2(source))
    print(index_equals_value3(source))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

// O(n) time | O(1) space - where n is the length of the input array
int indexEqualsValue1(vector<int> array)
{
    const int arraySize = array.size();
    for (int i = 0; i < arraySize; i++)
    {
        if (i == array[i])
        {
            return i;
        }
    }
    return -1;
}

// O(log(n)) time | O(1) space - where n is the length of the input array
int indexEqualsValue2(vector<int> array)
{
    int leftIndex = 0;
    int rightIndex = array.size() - 1;

    while (leftIndex <= rightIndex)
    {
        int middleIndex = leftIndex + (rightIndex - leftIndex) / 2;
        int middleValue = array[middleIndex];

        if (middleValue < middleIndex)
        {
            leftIndex = middleIndex + 1;
        }
        else if (middleValue == middleIndex && middleIndex == 0)
        {
            return middleIndex;
        }
        else if (middleValue == middleIndex &&
                 array[middleIndex - 1] < middleIndex - 1)
        {
            return middleIndex;
        }
        else
        {
            rightIndex = middleIndex - 1;
        }
    }
    return -1;
}

int indexEqualsValueHelper(vector<int> &, int leftIndex, int rightIndex);

// O(log(n)) time | O(log(n)) space - where n is the length of the input array
int indexEqualsValue3(vector<int> array)
{
    return indexEqualsValueHelper(array, 0, array.size() - 1);
}

int indexEqualsValueHelper(vector<int> &array, int leftIndex, int rightIndex)
{
    if (leftIndex > rightIndex)
    {
        return -1;
    }

    int middleIndex = leftIndex + (rightIndex - leftIndex) / 2;
    int middleValue = array[middleIndex];
    if (middleValue < middleIndex)
    {
        return indexEqualsValueHelper(array, middleIndex + 1, rightIndex);
    }
    else if (middleValue == middleIndex && middleIndex == 0)
    {
        return middleIndex;
    }
    else if (middleValue == middleIndex &&
             array[middleIndex - 1] < middleIndex - 1)
    {
        return middleIndex;
    }
    else
    {
        return indexEqualsValueHelper(array, leftIndex, middleIndex - 1);
    }
}

int main()
{
    vector<int> source = {-5, -4, -3, -2, -1, 0, 1, 3, 5, 6, 7, 11, 12, 14, 19, 20};
    cout << indexEqualsValue1(source) << endl;
    cout << indexEqualsValue2(source) << endl;
    cout << indexEqualsValue3(source) << endl;
    return 0;
}
```

Last Modified 2022-03-02

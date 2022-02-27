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

Last Modified 2022-02-27

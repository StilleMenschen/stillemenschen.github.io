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

Last Modified 2022-01-10

# 数组

## 验证子序列

假设有一个主序列存在一个子序列，子序列中的所有元素都存在于主序列中，判断一个序列是否为另一个序列的子序列

```python
# O(n) time | O(1) space
def validate_subsequence1(array, sequence):
    arr_idx = 0
    seq_idx = 0
    while arr_idx < len(array) and seq_idx < len(sequence):
        if array[arr_idx] == sequence[seq_idx]:
            seq_idx += 1
        arr_idx += 1
    return seq_idx == len(sequence)


# O(n) time | O(1) space
def validate_subsequence2(array, sequence):
    seq_idx = 0
    for value in array:
        if seq_idx == len(sequence):
            break
        if sequence[seq_idx] == value:
            seq_idx += 1
    return seq_idx == len(sequence)


if __name__ == '__main__':
    a = [5, 1, 22, 25, 6, -1, 8, 10]
    b = [1, 6, -1, 10]
    print(validate_subsequence1(a, b))
    print(validate_subsequence2(a, b))
```

```cpp
#include<vector>
#include<iostream>

using namespace std;

// O(n) time | O(1) space - where n is the length of the array
bool isValidSubsequence1(vector<int> array, vector<int> sequence)
{
    int arrIdx = 0;
    int seqIdx = 0;
    const int arrSize = array.size();
    const int seqSize = sequence.size();
    while (arrIdx < arrSize && seqIdx < seqSize)
    {
        if (array[arrIdx] == sequence[seqIdx])
        {
            seqIdx++;
        }
        arrIdx++;
    }
    return seqIdx == seqSize;
}


// O(n) time | O(1) space - where n is the length of the array
bool isValidSubsequence2(vector<int> array, vector<int> sequence)
{
    int seqIdx = 0;
    const int seqSize = sequence.size();
    for (auto value : array)
    {
        if (seqIdx == seqSize)
            break;
        if (sequence[seqIdx] == value)
            seqIdx++;
    }
    return seqIdx == seqSize;
}

int main()
{
    const vector<int> array = {5, 1, 22, 25, 6, -1, 8, 10};
    const vector<int> sequence = {1, 6, -1, 10};
    cout << isValidSubsequence1(array, sequence) << endl;
    cout << isValidSubsequence2(array, sequence) << endl;
    return 0;
}
```

## 两数求和

假设在一个序列中存在两个数相加的值等于一个给出的数，现在要快速找出序列中的这两个数并返回其值

```python
# O(n^2) time | O(1) space
def two_number_sum1(array, target_sum):
    for i in range(len(array) - 1):
        first_num = array[i]
        for j in range(i + 1, len(array)):
            second_num = array[j]
            if target_sum == first_num + second_num:
                return first_num, second_num
    return tuple()


# O(n) time | O(n) space
def two_number_sum2(array, target_sum):
    nums = {}
    for num in array:
        potential_match = target_sum - num
        if potential_match in nums:
            return potential_match, num
        else:
            nums[num] = 1
    return tuple()


# O(n * log(n)) time | O(1) space
def two_number_sum3(array, target_sum):
    array.sort()
    right_idx = len(array) - 1
    left_idx = 0
    while left_idx < right_idx:
        current_sum = array[left_idx] + array[right_idx]
        if target_sum == current_sum:
            return array[left_idx], array[right_idx]
        elif target_sum > current_sum:
            left_idx += 1
        elif target_sum < current_sum:
            right_idx -= 1
    return tuple()


if __name__ == '__main__':
    a = [3, 5, -4, 8, 11, 1, -1, 6]
    print(two_number_sum1(a, -3))
    print(two_number_sum2(a, 10))
    print(two_number_sum3(a, 13))
```

## 三数求和

在一个序列中找出符合相加的和等于某个指定的数的三个数，这三个数不能重复，需要找出所有可能的组合

```python
# O(n^2) time | O(n) space
def three_number_sum(array, target_sum):
    array.sort()
    triplets = list()
    length = len(array)
    for i in range(length - 2):
        left_idx = i + 1
        right_idx = length - 1
        while left_idx < right_idx:
            current_sum = array[i] + array[left_idx] + array[right_idx]
            if target_sum == current_sum:
                triplets.append((array[i], array[left_idx], array[right_idx],))
                left_idx += 1
                right_idx -= 1
            elif target_sum > current_sum:
                left_idx += 1
            elif target_sum < current_sum:
                right_idx -= 1
    return triplets


if __name__ == '__main__':
    a = [12, 3, 1, 2, -6, 5, -8, 6]
    print(three_number_sum(a, 0))
```

```cpp
#include <vector>
#include <algorithm>
#include <iostream>

using namespace std;

// O(n^2) time | O(n) space
vector<vector<int>> threeNumberSum(vector<int> array, int targetSum)
{
    if ( array.size() < 3 ) return {};
    sort(array.begin(), array.end());
    int leftIdx, rightIdx, length = array.size();
    int currentValue, leftValue, rightValue, sumOfValue;
    vector<vector<int>> result;
    for (int i=0; i<length; i++)
    {
        leftIdx = i + 1;
        rightIdx = length - 1;
        while ( leftIdx < rightIdx )
        {
            if (leftIdx == i) leftIdx++;
            if (rightIdx == i) rightIdx--;
            leftValue = array[leftIdx];
            rightValue = array[rightIdx];
            currentValue = array[i];
            sumOfValue = leftValue + rightValue + currentValue;
            if ( targetSum == sumOfValue )
            {
                result.push_back({currentValue, leftValue, rightValue});
                leftIdx++;
                rightIdx--;
            }
            if (targetSum > sumOfValue) leftIdx++;
            if (targetSum < sumOfValue) rightIdx--;
        }
    }
    return result;
}


int main()
{
    vector<vector<int>> result = threeNumberSum({12, 3, 1, 2, -6, 5, -8, 6}, 0);
    const int length = result.size();
    for (int i=0; i<length; i++)
    {
        cout << result[i][0] << " " << result[i][1] << " "  << result[i][2] << endl;
    }
    return 0;
}
```

## 最小差异

在两个序列中分别找出序列 1 和序列 2 中相差最小的两个数

```python
# O(n * log(n) + m*log(m)) time | O(1) space
def smallest_difference(array1, array2):
    array1.sort()
    array2.sort()
    len1 = len(array1)
    len2 = len(array2)
    idx1 = 0
    idx2 = 0
    smallest = float('inf')
    current = float('inf')
    smallest_pair = tuple()
    while idx1 < len1 and idx2 < len2:
        first_num = array1[idx1]
        second_num = array2[idx2]
        if first_num < second_num:
            current = second_num - first_num
            idx1 += 1
        elif first_num > second_num:
            current = first_num - second_num
            idx2 += 1
        else:
            return first_num, second_num
        if current < smallest:
            smallest = current
            smallest_pair = (first_num, second_num,)
    return smallest_pair


if __name__ == '__main__':
    print(smallest_difference([-1, 5, 10, 20, 28, 3], [26, 134, 135, 15, 17]))
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// O(n * log(n) + m*log(m)) time | O(1) space
vector<int> smallestDifference(vector<int> arrayOne, vector<int> arrayTwo)
{
    sort(arrayOne.begin(), arrayOne.end());
    sort(arrayTwo.begin(), arrayTwo.end());

    vector<int> result;

    int smaller = INT_MAX, current = INT_MAX;
    int idx1 = 0, idx2 = 0;
    while (idx1 < arrayOne.size() && idx2 < arrayTwo.size())
    {
        int firstNum = arrayOne[idx1];
        int secondNum = arrayTwo[idx2];
        if ( firstNum > secondNum )
        {
            current = firstNum - secondNum;
            idx2++;
        }
        else if (secondNum > firstNum)
        {
            current = secondNum - firstNum;
            idx1++;
        }
        else
        {
            return {firstNum, secondNum};
        }
        if (current < smaller)
        {
            result = {firstNum, secondNum};
            smaller = current;
        }
    }
    return result;
}


int main()
{
    vector<int> r = smallestDifference({-1, 5, 10, 20, 28, 3}, {26, 134, 135, 15, 17});
    cout << r[0] << " " << r[1] << endl;
    return 0;
}
```

## 移动元素至末尾

指定一个序列和一个存在于序列中的数，将所有与指定的数相等的数全部移动到序列的末尾

```python
# O(n) time | O(1) space
def move_element_ti_end(array, to_move):
    i = 0
    j = len(array) - 1
    while i < j:
        while i < j and array[j] == to_move:
            j -= 1
        if array[i] == to_move:
            array[i], array[j] = array[j], array[i]
        i += 1
    return array


if __name__ == '__main__':
    a = [4, 1, 5, 8, 8, 8, 5, 8, 8, 3]
    print(move_element_ti_end(a, 8))
```

```cpp
#include <iostream>
#include <vector>

using namespace std;

vector<int> moveElementToEnd(vector<int> array, int toMove)
{
    int i = 0, j = array.size() - 1;
    while ( i < j )
    {
        while ( i < j && array[j] == toMove )
        {
            j--;
        }
        if ( array[i] == toMove)
        {
            swap(array[i], array[j]);
        }
        i++;
    }
    return array;
}


int main()
{
    vector<int> r = moveElementToEnd({2, 1, 2, 2, 3, 2, 4, 2}, 2);
    for (vector<int>::const_iterator it = r.begin(); it != r.end(); it++)
    {
        cout << *it << " ";
    }
    return 0;
}
```

## 单调数组

判断一个整型序列，是否是升序排序或者降序排序，如果满足两个条件中的一个，就表示它是单调的

```python
# O(n) time | O(1) space
def is_monotonic(array):
    length = len(array)
    if length <= 2:
        return True
    direction = array[1] - array[0]
    for i in range(2, length):
        if direction == 0:
            direction = array[i] - array[i - 1]
            continue
        if not break_direction(direction, array[i - 1], array[i]):
            return False
    return True


def break_direction(direction, previous_int, current_int):
    difference = current_int - previous_int
    if direction > 0:
        return difference > 0
    return difference < 0


if __name__ == '__main__':
    a = [872, 743, 65, 30, 14, 10, 5, 3, 1, -3]
    # a.reverse()
    print(is_monotonic(a))
```

```cpp
#include <iostream>
#include <vector>

using namespace std;

bool breaksDirection( int direction, int previousInt, int currentInt )
{
    int difference = currentInt - previousInt;
    if (direction > 0)
        return difference < 0;
    return difference > 0;
}

// O(n) time | O(1) space
// where n is the length of the array
bool isMonotonic(vector<int> array)
{
    const int arraySize = array.size();
    if (arraySize <= 2)
        return true;
    int direction = array[1] - array[0];
    for (int i = 2; i < arraySize; i++)
    {
        if (direction == 0)
        {
            direction = array[i] - array[i - 1];
            continue;
        }
        if (breaksDirection(direction, array[i - 1], array[i]))
            return false;
    }
    return true;
}


int main()
{
    cout << isMonotonic({-1, 0, 4, 19, 200});
    return 0;
}
```

## 螺旋线

将一个类似顺时针向内螺旋的特殊二维序列转为一维序列

```python
# O(n) time | O(n) space
def spiral_traverse1(array):
    result = list()
    start_row, end_row = 0, len(array) - 1
    start_col, end_col = 0, len(array[0]) - 1
    while start_row <= end_row and start_col <= end_col:
        for col in range(start_col, end_col + 1):
            result.append(array[start_row][col])
        for row in range(start_row + 1, end_row + 1):
            result.append(array[row][end_col])
        # for col in reversed(range(start_col, end_col)):
        for col in range(end_col - 1, start_col - 1, -1):
            result.append(array[end_row][col])
        # for row in reversed(range(start_row + 1, end_row)):
        for row in range(end_row - 1, start_row, -1):
            result.append(array[row][start_col])
        start_row += 1
        end_row -= 1
        start_col += 1
        end_col -= 1
    return result


# O(n) time | O(n) space
def spiral_traverse2(array):
    result = list()
    spiral_fill(array, 0, len(array) - 1, 0, len(array[0]) - 1, result)
    return result


def spiral_fill(array, start_row, end_row, start_col, end_col, result):
    if start_row > end_row and start_col > end_col:
        return False
    for col in range(start_col, end_col + 1):
        result.append(array[start_row][col])
    for row in range(start_row + 1, end_row + 1):
        result.append(array[row][end_col])
    for col in range(end_col - 1, start_col - 1, -1):
        result.append(array[end_row][col])
    for row in range(end_row - 1, start_row, -1):
        result.append(array[row][start_col])
    spiral_fill(array, start_row + 1, end_row - 1, start_col + 1, end_col - 1, result)


if __name__ == '__main__':
    a = [
        [ 1,  2,  3, 4],
        [12, 13, 14, 5],
        [11, 16, 15, 6],
        [10,  9,  8, 7],
    ]
    print(spiral_traverse1(a))
    print(spiral_traverse2(a))
```

```cpp
#include <iostream>
#include <vector>

using namespace std;


// O(n) time | O(n) space - where n is the total number of elements in the array
vector<int> spiralTraverse1(vector<vector<int>> array)
{

    if (array.size() == 0)
        return {};

    vector<int> result = {};
    int startRow = 0;
    int endRow = array.size() - 1;
    int startCol = 0;
    int endCol = array[0].size() - 1;
    while (startRow <= endRow && startCol <= endCol)
    {
        for (int col = startCol; col <= endCol; col++)
        {
            result.push_back(array[startRow][col]);
        }

        for (int row = startRow + 1; row <= endRow; row++)
        {
            result.push_back(array[row][endCol]);
        }

        for (int col = endCol - 1; col >= startCol; col--)
        {
            // Handle the edge case when there's a single row
            // in the middle of the matrix. In this case, we don't
            // want to double-count the values in this row, which
            // we've already counted in the first for loop above.
            if (startRow == endRow)
                break;
            result.push_back(array[endRow][col]);
        }
        for (int row = endRow - 1; row > startRow; row--)
        {
            // Handle the edge case when there's a single column
            // in the middle of the matrix. In this case, we don't
            // want to double-count the values in this column, which
            // we've already counted in the second for loop above.
            if (startCol == endCol)
                break;
            result.push_back(array[row][startCol]);
        }

        startRow++;
        endRow--;
        startCol++;
        endCol--;
    }
    return result;
}

// O(n) time | O(n) space - where n is the total number of elements in the array
void spiralFill(vector<vector<int>> &array, int startRow, int endRow,
                int startCol, int endCol, vector<int> &result)
{
    if (startRow > endRow || startCol > endCol)
    {
        return;
    }
    for (int col = startCol; col <= endCol; col++)
    {
        result.push_back(array[startRow][col]);
    }
    for (int row = startRow + 1; row <= endRow; row++)
    {
        result.push_back(array[row][endCol]);
    }
    for (int col = endCol - 1; col >= startCol; col--)
    {
        // Handle the edge case when there's a single row
        // in the middle of the matrix. In this case, we don't
        // want to double-count the values in this row, which
        // we've already counted in the first for loop above.
        if (startRow == endRow)
            break;
        result.push_back(array[endRow][col]);
    }
    for (int row = endRow - 1; row >= startRow + 1; row--)
    {
        // Handle the edge case when there's a single column
        // in the middle of the matrix. In this case, we don't
        // want to double-count the values in this column, which
        // we've already counted in the second for loop above.
        if (startCol == endCol)
            break;
        result.push_back(array[row][startCol]);
    }
    spiralFill(array, startRow + 1, endRow - 1, startCol + 1, endCol - 1, result);
}

vector<int> spiralTraverse2(vector<vector<int>> array)
{
    if (array.size() == 0)
        return {};
    vector<int> result = {};
    spiralFill(array, 0, array.size() - 1, 0, array[0].size() - 1, result);
    return result;
}

int main()
{
    vector<int> result = spiralTraverse2(
    {
        { 1,  2,  3, 4},
        {12, 13, 14, 5},
        {11, 16, 15, 6},
        {10,  9,  8, 7},
    });
    const int length = result.size();
    for (int i=0; i<length; i++)
        cout << result[i] << " ";
    return 0;
}
```

## 最宽（长）波峰

给出一个整数序列，将序列的索引作为 x 坐标，每个索引的数值作为 y 坐标，将所有的点连接起来形成一个波谱，找出一个波谱中波峰
宽度最长的

```python
# O(n) time | O(1) space
def longest_peak(array):
    longest_peak_length = 0
    length = len(array)
    i = 0
    while i < length - 1:
        is_peak = array[i - 1] < array[i] and array[i] > array[i + 1]
        if not is_peak:
            i += 1
            continue

        left_idx = i - 2
        while left_idx >= 0 and array[left_idx] < array[left_idx + 1]:
            left_idx -= 1
        right_idx = i + 2
        while right_idx < length and array[right_idx] < array[right_idx - 1]:
            right_idx += 1

        current_peak_length = right_idx - left_idx - 1
        longest_peak_length = max(longest_peak_length, current_peak_length)
        i = right_idx
    return longest_peak_length


if __name__ == '__main__':
    a = [1, 2, 3, 3, 4, 0, 10, 6, 5, -1, -3, 2, 3]
    print(longest_peak(a))
```

```cpp
#include <iostream>
#include <vector>

using namespace std;

// O(n) time | O(1) space
int longestPeak(vector<int> array)
{
    if (array.size() <= 1)
        return 0;
    const int arrayLength = array.size();
    int current = 0;
    int longest = 0;
    for (int i = 1; i < arrayLength - 1; i++)
    {
        const bool isPeak = array[i - 1] < array[i] && array[i] > array[i + 1];
        if (!isPeak) continue;
        int leftIdx = i - 2;
        while (leftIdx >= 0 && array[leftIdx] < array[leftIdx + 1]) leftIdx--;
        int rightIdx = i + 2;
        while (rightIdx < arrayLength && array[rightIdx - 1] > array[rightIdx]) rightIdx++;
        current = rightIdx - leftIdx - 1;
        if (current > longest) longest = current;
    }
    return longest;
}

int main()
{
    cout << longestPeak({1, 2, 3, 3, 4, 0, 10, 6, 5, -1, -3, 2, 3});
    return 0;
}
```

## 数组消费者

给定一个整数序列，求出另一个序列满足第 n 个元素是除了其本身之外的其它元素的乘积

```python
# O(n^2) time | O(n) space
def array_of_products1(array):
    length = len(array)
    products = [1 for _ in range(length)]

    for i in range(length):
        running_product = 1
        for j in range(length):
            if i != j:
                running_product *= array[j]
        products[i] = running_product

    return products


# O(n) time | O(n) space
def array_of_products2(array):
    length = len(array)
    products = [1 for _ in range(length)]
    left_products = [1 for _ in range(length)]
    right_products = [1 for _ in range(length)]

    left_running_product = 1
    for i in range(length):
        left_products[i] = left_running_product
        left_running_product *= array[i]

    right_running_product = 1
    # for i in reversed(range(length)):
    for i in range(length - 1, -1, -1):
        right_products[i] = right_running_product
        right_running_product *= array[i]

    for i in range(length):
        products[i] = left_products[i] * right_products[i]

    return products


# O(n) time | O(n) space
def array_of_products3(array):
    length = len(array)
    products = [1 for _ in range(length)]

    left_running_product = 1
    for i in range(length):
        products[i] = left_running_product
        left_running_product *= array[i]

    right_running_product = 1
    # for i in reversed(range(length)):
    for i in range(length - 1, -1, -1):
        products[i] *= right_running_product
        right_running_product *= array[i]

    return products


if __name__ == '__main__':
    a = [1, 5, 10, 20]
    print(array_of_products1(a))
    print(array_of_products2(a))
    a = [5, 1, 4, 2]
    print(array_of_products3(a))
```

```cpp
#include <iostream>
#include <vector>

using namespace std;

// O(n) time | O(n) space
vector<int> arrayOfProducts(vector<int> array)
{
    const int size = array.size();
    vector<int> products(size, 1);
    int sumOfproduct = 1;
    int i;
    for (i = 0; i < size; i++)
    {
        products[i] = sumOfproduct;
        sumOfproduct *= array[i];
    }
    sumOfproduct = 1;
    for (i = size - 1; i >= 0; i--)
    {
        products[i] *= sumOfproduct;
        sumOfproduct *= array[i];
    }
    return products;
}

int main()
{
    vector<int> r = arrayOfProducts({-1, -5, 5, 10});
    for (vector<int>::const_iterator it = r.begin(); it != r.end(); it++)
        cout << *it << " ";
    return 0;
}
```

## 四数求和

在一个序列中找出符合相加的和等于某个指定的数的四个数，这四个数不能重复，如四个数位置不一样但值一样，
像 `[4, 2, 3, 5]` 和 `[5, 3, 4, 2]` 其实是完全一样的，需要找出所有可能的组合

```python
# Average: O(n^2) time | O(n^2) space
# Worst: O(n^3) time | O(n^2) space
def four_number_sum(array, target_sum):
    all_pair_sums = {}  # 两数和记录字典, 将和作为 key 可以实现快速搜索
    quadruplets = []  # 结果集
    # 因为需要找出两个数的组合, 所有第一个和最后一个元素可以不在第一个循环中访问
    for i in range(1, len(array) - 1):
        # 第二个循环, 从当前位置向后
        for j in range(i + 1, len(array)):
            # 求出第一个两数和
            current_sum = array[i] + array[j]
            # 求出第二个两数和, 也就是总和减去第一个两数和
            difference = target_sum - current_sum
            # 如果第二个两数和已经有记录, 则取出并记录到结果数组中
            if difference in all_pair_sums:
                for pair in all_pair_sums[difference]:
                    # 在 Python 中可以使用加号连接两个数组为一个数组
                    quadruplets.append(pair + [array[i], array[j]])
        # 将已经遍历过的两数和加入到记录中, 这里在后面才添加两数和的记录是为了防止记录重复的结果集
        for k in range(0, i):
            current_sum = array[i] + array[k]
            if current_sum not in all_pair_sums:
                all_pair_sums[current_sum] = [[array[k], array[i]]]
            else:
                # 如果两数和记录中已经存在, 则追加
                all_pair_sums[current_sum].append([array[k], array[i]])
    return quadruplets


if __name__ == '__main__':
    print(four_number_sum([7, 6, 4, -1, 1, 2], 16))
```

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <algorithm>
using namespace std;

// Average: O(n^2) time | O(n^2) space
// Worst: O(n^3) time | O(n^2) space
vector<vector<int>> fourNumberSum(vector<int> array, int targetSum)
{
    unordered_map<int, vector<vector<int>>> allPairSums;
    vector<vector<int>> quadruplets{};
    for (int i = 1; i < array.size() - 1; i++)
    {
        for (int j = i + 1; j < array.size(); j++)
        {
            int currentSum = array[i] + array[j];
            int difference = targetSum - currentSum;
            if (allPairSums.find(difference) != allPairSums.end())
            {
                for (vector<int> pair : allPairSums[difference])
                {
                    pair.push_back(array[i]);
                    pair.push_back(array[j]);
                    quadruplets.push_back(pair);
                }
            }
        }
        for (int k = 0; k < i; k++)
        {
            int currentSum = array[i] + array[k];
            if (allPairSums.find(currentSum) == allPairSums.end())
            {
                allPairSums[currentSum] = vector<vector<int>>{{array[k], array[i]}};
            }
            else
            {
                allPairSums[currentSum].push_back(vector<int>{array[k], array[i]});
            }
        }
    }
    return quadruplets;
}

void iteration(vector<vector<int>> array)
{
    for (const vector<int> element : array)
    {
        cout << "[" << element[0] << ", " << element[1] << ", " << element[2] << ", " << element[3] << "] ";
    }
    cout << endl;
}

int main()
{
    vector<int> source = {7, 6, 4, -1, 1, 2};
    iteration(fourNumberSum(source, 16));
    return 0;
}
```

## 子序列排序

编写一个函数，该函数接受一个至少包含两个整数的序列，并返回一个包含输入序列中最小子序列的开始和结束索引的序列，该子序列进
行排序后可以使整个输入序列按升序排序。如果输入数组已经排序，函数应该返回 [-1, -1]

```python
# O(n) time | O(1) space
def subarray_sort1(array):
    # 由于数组必定是从小到大排序的, 所以可以先找出需要排序的最小值和最大值
    min_out_of_order = float("inf")
    max_out_of_order = float("-inf")
    for i in range(len(array)):
        num = array[i]
        # 判断是否存在顺序有误并记录最小值和最大值
        if is_out_of_order(i, num, array):
            min_out_of_order = min(min_out_of_order, num)
            max_out_of_order = max(max_out_of_order, num)
    # 如果没有找到顺序有误的情况, 即最大值或最小值没有发生变化, 则返回默认要求的数据
    if min_out_of_order == float("inf"):
        return [-1, -1]
    # 从第一个位置开始找
    sub_array_left_idx = 0
    # 根据需要排序的最小值, 从头开始定位这个值实际的位置
    while min_out_of_order >= array[sub_array_left_idx]:
        sub_array_left_idx += 1
    # 从最后一个位置开始找
    sub_array_right_idx = len(array) - 1
    # 根据需要排序的最大值, 从末尾开始定位这个值实际的位置
    while max_out_of_order <= array[sub_array_right_idx]:
        sub_array_right_idx -= 1
    return sub_array_left_idx, sub_array_right_idx


def is_out_of_order(i, num, array):
    # 如果是第一个位置, 只判断后面的数是否存在顺序有误
    if i == 0:
        return num > array[i + 1]
    # 如果是末尾的位置, 只判断前面的数是否存在顺序有误
    if i == len(array) - 1:
        return num < array[i - 1]
    # 如果是中间的位置, 需要判断前后两边是否存在顺序有误
    return num > array[i + 1] or num < array[i - 1]


# O(n) time | O(1) space
def subarray_sort2(array):
    max_unsorted_value = float('-inf')
    min_unsorted_value = float('inf')
    # 循环的结束位置不是最后一个, 而是倒数第二个, 防止数组越界
    for idx in range(len(array) - 1):
        # 这里只是换了一种方式判断顺序有误的情况
        if array[idx] > array[idx + 1]:
            max_unsorted_value = max(max_unsorted_value, array[idx])
            min_unsorted_value = min(min_unsorted_value, array[idx + 1])

    if min_unsorted_value == float('inf'):
        return [-1, -1]

    start_idx = 0
    end_idx = len(array) - 1

    while start_idx < len(array) and array[start_idx] <= min_unsorted_value:
        start_idx += 1

    while end_idx >= 0 and array[end_idx] >= max_unsorted_value:
        end_idx -= 1

    return [start_idx, end_idx]


if __name__ == '__main__':
    source = [1, 2, 4, 7, 10, 11, 7, 12, 6, 7, 16, 18, 19]
    print(subarray_sort1(source))
    print(subarray_sort2(source))
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

bool isOutOfOrder(size_t i, int num, vector<int> array);

// O(n) time | O(1) space
vector<int> subarraySort(vector<int> array)
{
    int minOutOfOrder = INT_MAX;
    int maxOutOfOrder = INT_MIN;
    for (size_t i = 0; i < array.size(); i++)
    {
        int num = array[i];
        if (isOutOfOrder(i, num, array))
        {
            minOutOfOrder = min(minOutOfOrder, num);
            maxOutOfOrder = max(maxOutOfOrder, num);
        }
    }
    if (minOutOfOrder == INT_MAX)
    {
        return vector<int>{-1, -1};
    }
    int subarrayLeftIdx = 0;
    while (minOutOfOrder >= array[subarrayLeftIdx])
    {
        subarrayLeftIdx++;
    }
    int subarrayRightIdx = array.size() - 1;
    while (maxOutOfOrder <= array[subarrayRightIdx])
    {
        subarrayRightIdx--;
    }
    return vector<int>{subarrayLeftIdx, subarrayRightIdx};
}

bool isOutOfOrder(size_t i, int num, vector<int> array)
{
    if (i == 0)
    {
        return num > array[i + 1];
    }
    if (i == array.size() - 1)
    {
        return num < array[i - 1];
    }
    return num > array[i + 1] || num < array[i - 1];
}

int main()
{
    vector<int> source = {1, 2, 4, 7, 10, 11, 7, 12, 6, 7, 16, 18, 19};
    source = subarraySort(source);
    cout << source[0] << ", " << source[1];
    return 0;
}
```

## 最大连续范围

找出一个序列中最长的连续数字范围，并返回开始值和结束值

```python
# O(n) time | O(n) space
def largest_range(array):
    best_range = []  # 记录区间
    longest_length = 0  # 记录区间的最大长度
    numbers = {}
    # 首先将所有数记录到字典中
    for value in array:
        # 设置为否, 标记为未访问
        numbers[value] = False

    for value in array:
        # 如果已经访问过则不再访问
        if numbers[value]:
            continue
        # 标记为已访问
        numbers[value] = True
        # 当前值的长度初始为 1
        current_length = 1
        left = value - 1
        right = value + 1
        # 向左查找, 不断累加长度
        while left in numbers:
            # 标记为已访问
            numbers[left] = True
            current_length += 1
            # 如果符合递减值则继续扩大范围
            left -= 1
        # 向右查找, 不断累加长度
        while right in numbers:
            # 标记为已访问
            numbers[right] = True
            current_length += 1
            # 如果符合递增值则继续扩大范围
            right += 1
        # 如果当前长度大于记录的长度则记录新的长度和区间
        if current_length > longest_length:
            longest_length = current_length
            best_range = [left + 1, right - 1]
    return best_range


if __name__ == '__main__':
    print(largest_range([8, 4, 2, 10, 3, 6, 7, 9, 1]))
```

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;

// O(n) time | O(n) space
vector<int> largestRange(vector<int> array)
{
    vector<int> bestRange = {};
    int longestLength = 0;
    unordered_map<int, bool> nums = {};
    for (int num : array)
    {
        nums[num] = true;
    }
    for (int num : array)
    {
        if (!nums[num])
        {
            continue;
        }
        nums[num] = false;
        int currentLength = 1;
        int left = num - 1;
        int right = num + 1;
        while (nums.find(left) != nums.end())
        {
            nums[left] = false;
            currentLength++;
            left--;
        }
        while (nums.find(right) != nums.end())
        {
            nums[right] = false;
            currentLength++;
            right++;
        }
        if (currentLength > longestLength)
        {
            longestLength = currentLength;
            bestRange = {left + 1, right - 1};
        }
    }
    return bestRange;
}

int main()
{
    vector<int> source = {8, 4, 2, 10, 3, 6, 7, 9, 1};
    source = largestRange(source);
    cout << source[0] << ", " << source[1];
    return 0;
}
```

## 最少奖励

老师有一系列学生的分数，老师必须根据学生的分数购买奖励给他们。奖励金额必须遵循 2 个标准：

1. 每个学生必须至少获得一项奖励
2. 两个相邻学生中排名较高的学生必须比另一个获得更多的奖励

```python
# O(n^2) time | O(n) space
def min_rewards1(scores):
    # 先初始化奖励为 1, 因为至少要给一个奖励
    rewards = [1 for _ in scores]
    # 从第二个位置开始循环判断, 防止数组越界
    for i in range(1, len(scores)):
        j = i - 1  # 和前一个比较的
        if scores[i] > scores[j]:
            # 如果当前的分数比前一个大, 则需要增加奖励, 因为相邻的分数之间必须高分的奖励数比低分的奖励数大
            rewards[i] = rewards[j] + 1
        else:
            # 如果当前分数比前一个小, 则需要从后向前累加奖励
            while j >= 0 and scores[j] > scores[j + 1]:
                # 因为前面的判断里面可能已经对奖励进行过累加了
                # 所以这里加了判断取当前奖励和累加奖励中的最大值
                rewards[j] = max(rewards[j], rewards[j + 1] + 1)
                j -= 1
    # 将所有奖励数求和
    return sum(rewards)


# O(n) time | O(n) space
def min_rewards2(scores):
    # 先初始化奖励为 1, 因为至少要给一个奖励
    rewards = [1 for _ in scores]
    # 1. 先找出分数最小的索引, 即当前分数比左边和右边的分数都小的位置
    local_min_idx_list = get_local_min_idx(scores)
    # 2. 通过最小分数位置, 同时向左和向右累加奖励
    # 这里需要注意的是最小分数位置是从左向右计算出来的
    # 所以累加奖励的时候需要注意不能覆盖掉已经累加过的奖励
    for local_min_idx in local_min_idx_list:
        expand_from_local_min_idx(local_min_idx, scores, rewards)
    # 将所有奖励数求和
    return sum(rewards)


def get_local_min_idx(array):
    length = len(array)
    # 如果只有一个
    if length == 1:
        return [0]
    local_min_idx = []
    # 开头位置只需要判断右边的分数
    if array[0] < array[1]:
        local_min_idx.append(0)
    # 末尾位置只需要判断左边的分数
    if array[length - 1] < array[length - 2]:
        local_min_idx.append(length - 1)
    # 由于开头位置和末尾位置已经处理过了, 所以这里的循环排除掉, 直接从第二个位置开始到倒数第二个位置
    for i in range(1, length - 1):
        # 同时判断左边和右边的分数是否都大于当前分数
        if array[i] < array[i + 1] and array[i] < array[i - 1]:
            local_min_idx.append(i)
    return local_min_idx


def expand_from_local_min_idx(local_min_idx, scores, rewards):
    # 左边位置
    left_idx = local_min_idx - 1
    # 如果当前分数总是小于前一个的分数, 则累加奖励
    while left_idx >= 0 and scores[left_idx] > scores[left_idx + 1]:
        # 因为向左查找可能会在向右查找已经执行过之后才执行
        # 所以这里需要判断取当前奖励和累加奖励中的最大值
        rewards[left_idx] = max(rewards[left_idx], rewards[left_idx + 1] + 1)
        left_idx -= 1
    # 右边位置
    right_idx = local_min_idx + 1
    length = len(scores)
    # 如果当前分数总是小于后一个的分数, 则累加奖励
    while right_idx < length and scores[right_idx] > scores[right_idx - 1]:
        # 因为向右查找必定是首次执行的, 所以这里不需要进行最大值的判断
        rewards[right_idx] = rewards[right_idx - 1] + 1
        right_idx += 1


# O(n) time | O(n) space
def min_rewards3(scores):
    # 先初始化奖励为 1, 因为至少要给一个奖励
    rewards = [1 for _ in scores]
    # 1. 从左向右处理, 只处理当前分数比前一个分数大的情况
    # 因为需要判断前一个分数, 所以从第二个位置开始, 防止数组越界
    for i in range(1, len(scores)):
        # 判断前一个分数和当前分数
        if scores[i] > scores[i - 1]:
            # 累加奖励
            rewards[i] = rewards[i - 1] + 1
    # 2. 从右向左处理, 只处理当前分数比后一个分数大的情况
    # 因为需要判断后一个分数, 所以要从倒数第二个位置开始, 防止数组越界
    # for i in reversed(range(len(scores) - 1)):
    for i in range(len(scores) - 2, -1, -1):
        # 判断后一个分数和当前分数
        if scores[i] > scores[i + 1]:
            # 由于前一个步骤中从左向右的处理可能累加过奖励, 这里需要判断最大值, 取最优的奖励
            rewards[i] = max(rewards[i], rewards[i + 1] + 1)
    # 将所有奖励数求和
    return sum(rewards)


if __name__ == '__main__':
    a = [8, 4, 2, 1, 3, 6, 7, 9, 5]
    print(min_rewards1(a))
    print(min_rewards2(a))
    print(min_rewards3(a))
```

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <numeric>
using namespace std;

// O(n^2) time | O(n) space - where n is the length of the input array
int minRewards1(vector<int> scores)
{
    const size_t length = scores.size();
    vector<int> rewards = vector<int>(length, 1);
    for (size_t i = 1; i < length; i++)
    {
        size_t j = i - 1;
        if (scores[i] > scores[j])
        {
            rewards[i] = rewards[j] + 1;
        }
        else
        {
            while (j >= 0 && scores[j] > scores[j + 1])
            {
                rewards[j] = max(rewards[j], rewards[j + 1] + 1);
                j--;
            }
        }
    }
    return accumulate(rewards.begin(), rewards.end(), 0);
}

vector<int> getLocalMinIdxs(vector<int> array);
void expandFromLocalMinIdx(int localMinIdx, vector<int> scores,
                           vector<int> *rewards);

// O(n) time | O(n) space - where n is the length of the input array
int minRewards2(vector<int> scores)
{
    vector<int> rewards = vector<int>(scores.size(), 1);
    vector<int> localMinIdxs = getLocalMinIdxs(scores);
    for (int localMinIdx : localMinIdxs)
    {
        expandFromLocalMinIdx(localMinIdx, scores, &rewards);
    }
    return accumulate(rewards.begin(), rewards.end(), 0);
}

vector<int> getLocalMinIdxs(vector<int> array)
{
    if (array.size() == 1)
        return vector<int>{0};
    vector<int> localMinIdxs = {};
    const int length = array.size();
    for (int i = 0; i < length; i++)
    {
        if (i == 0 && array[i] < array[i + 1])
            localMinIdxs.push_back(i);

        if (i == length - 1 && array[i] < array[i - 1])
            localMinIdxs.push_back(i);

        if (i == 0 || i == length - 1)
            continue;
        if (array[i] < array[i + 1] && array[i] < array[i - 1])
            localMinIdxs.push_back(i);
    }
    return localMinIdxs;
}

void expandFromLocalMinIdx(int localMinIdx, vector<int> scores,
                           vector<int> *rewards)
{
    int leftIdx = localMinIdx - 1;
    while (leftIdx >= 0 && scores[leftIdx] > scores[leftIdx + 1])
    {
        rewards->at(leftIdx) =
            max(rewards->at(leftIdx), rewards->at(leftIdx + 1) + 1);
        leftIdx--;
    }
    int rightIdx = localMinIdx + 1;
    const int length = scores.size();
    while (rightIdx < length && scores[rightIdx] > scores[rightIdx - 1])
    {
        rewards->at(rightIdx) = rewards->at(rightIdx - 1) + 1;
        rightIdx++;
    }
}

// O(n) time | O(n) space - where n is the length of the input array
int minRewards3(vector<int> scores)
{
    vector<int> rewards = vector<int>(scores.size(), 1);
    const int length = scores.size();
    for (int i = 1; i < length; i++)
    {
        if (scores[i] > scores[i - 1])
            rewards[i] = rewards[i - 1] + 1;
    }
    for (int i = length - 2; i >= 0; i--)
    {
        if (scores[i] > scores[i + 1])
        {
            rewards[i] = max(rewards[i], rewards[i + 1] + 1);
        }
    }
    return accumulate(rewards.begin(), rewards.end(), 0);
}

int main()
{
    vector<int> source = {8, 4, 2, 1, 3, 6, 7, 9, 5};
    cout << minRewards1(source) << endl;
    cout << minRewards2(source) << endl;
    cout << minRewards3(source) << endl;
    return 0;
}
```

## Z 型遍历

```python
# O(n) time | O(n) space - where n is the total number of elements in the two-dimensional array
def zigzag_traverse1(array):
    # 记录宽高
    height = len(array) - 1
    width = len(array[0]) - 1
    result = []
    row, col = 0, 0  # 从左上角开始
    going_down = True  # 用来判断方向
    # 没有超出边界则继续遍历
    while not is_out_of_bounds(row, col, height, width):
        result.append(array[row][col])
        if going_down:  # 方向为左下
            # 是否到达边界
            if col == 0 or row == height:
                going_down = False  # 调整方向
                if row == height:  # 达到了下边界
                    col += 1  # 向右移动
                else:  # 达到了左边界
                    row += 1  # 向下移动
            else:
                # 没有达到边界, 继续朝左下方向
                row += 1
                col -= 1
        else:  # 方向为右上
            # 是否到达边界
            if row == 0 or col == width:
                going_down = True  # 调整方向
                if col == width:  # 达到了右边界
                    row += 1  # 向下移动
                else:  # 达到了上边界
                    col += 1  # 向右移动
            else:
                # 没有达到边界, 继续朝右上方向
                row -= 1
                col += 1
    return result


def is_out_of_bounds(row, col, height, width):
    return row < 0 or row > height or col < 0 or col > width


BOTTOM_LEFT = 1
TOP_RIGHT = 2


# O(n) time | O(n) space - where n is the total number of elements in the two-dimensional array
def zigzag_traverse2(array):
    traverse_list = []
    direction = BOTTOM_LEFT  # 用来判断方向
    row, col = 0, 0
    height = len(array)
    width = len(array[row])
    # 由于遍历的数量固定是矩阵的元素数量, 所以可以直接循环对应的次数
    for _ in range(height * width):
        traverse_list.append(array[row][col])
        if direction == TOP_RIGHT:
            if row == 0 or col == width - 1:
                if col < width - 1:  # 判断是否可以继续向右
                    col += 1
                else:
                    row += 1
                direction = BOTTOM_LEFT  # 切换方向为左下
            else:
                row -= 1
                col += 1
            continue  # 跳到下一个循环
        if direction == BOTTOM_LEFT:
            if col == 0 or row == height - 1:
                if row < height - 1:  # 判断是否可以继续向下
                    row += 1
                else:
                    col += 1
                direction = TOP_RIGHT  # 切换方向为右上
            else:
                row += 1
                col -= 1

    return traverse_list


if __name__ == '__main__':
    sources = [
        [[1, 3, 4, 10],
         [2, 5, 9, 11],
         [6, 8, 12, 15],
         [7, 13, 14, 16]],
        [[1, 2, 3, 4, 5]],
        [[1], [2], [3], [4], [5]]
    ]
    for source in sources:
        print(zigzag_traverse1(source))
        print(zigzag_traverse2(source))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

bool isOutOfBounds(int row, int col, int height, int width);

// O(n) time | O(n) space - where n is the total number of elements in the two-dimensional array
vector<int> zigzagTraverse(vector<vector<int>> array)
{
    int height = array.size() - 1;
    int width = array[0].size() - 1;
    vector<int> result = {};
    int row = 0;
    int col = 0;
    bool goingDown = true;
    while (!isOutOfBounds(row, col, height, width))
    {
        result.push_back(array[row][col]);
        if (goingDown)
        {
            if (col == 0 || row == height)
            {
                goingDown = false;
                if (row == height)
                {
                    col++;
                }
                else
                {
                    row++;
                }
            }
            else
            {
                row++;
                col--;
            }
        }
        else
        {
            if (row == 0 || col == width)
            {
                goingDown = true;
                if (col == width)
                {
                    row++;
                }
                else
                {
                    col++;
                }
            }
            else
            {
                row--;
                col++;
            }
        }
    }
    return result;
}

bool isOutOfBounds(int row, int col, int height, int width)
{
    return row < 0 || row > height || col < 0 || col > width;
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
    iteration(zigzagTraverse({{1, 3, 4, 10},
                              {2, 5, 9, 11},
                              {6, 8, 12, 15},
                              {7, 13, 14, 16}}));
    iteration(zigzagTraverse({{1, 2, 3, 4, 5}}));
    iteration(zigzagTraverse({{1}, {2}, {3}, {4}, {5}}));
    return 0;
}
```

## 合适的公寓

给定一组公寓数据和一个需求列表，公寓数据为一个字典数据，里面存储了该公寓是否存在某个需求（如健身房、学校、商店等等），公寓数据的索引也同时表示其位置，
而需求列表则表示期望在公寓中找到特定的需求且每一个需求的相对距离都是最小的，找出最合适的公寓位置（索引）

```python
# O(b^2*r) time | O(b) space - where b is the number of blocks and r is the number of requirements
def apartment_hunting1(blocks, reqs):
    # 记录每个公寓的每个需求中最大距离的需求
    max_distances_at_blocks = [float("-inf") for _ in blocks]
    # 遍历每个公寓
    for i in range(len(blocks)):
        # 在每个公寓中遍历每个需求
        for req in reqs:
            # 根据特定需求, 得出距离此公寓最近的一处位置
            closest_req_distance = float("inf")
            for j in range(len(blocks)):
                # 如果搜索的公寓存在此需求
                if blocks[j][req]:
                    # 计算最小的距离, 由于可能存在小值减去大值的情况, 这里使用绝对值
                    closest_req_distance = min(closest_req_distance, distance_between(i, j))
            # 遍历一个需求完成后, 更新最大距离
            max_distances_at_blocks[i] = max(max_distances_at_blocks[i], closest_req_distance)
    # 由于已经知道了每个公寓中某个需求的最大距离, 只要找出需求最大距离相对于其它公寓是最小的, 就是最佳的位置了
    return get_idx_at_min_value(max_distances_at_blocks)


def get_idx_at_min_value(array):
    idx_at_min_value = 0
    # 最小值设置为无穷大, 方便比较
    min_value = float("inf")
    for i in range(len(array)):
        current_value = array[i]
        if current_value < min_value:
            min_value = current_value
            idx_at_min_value = i
    return idx_at_min_value


def distance_between(a, b):
    return abs(a - b)


# O(br) time | O(br) space - where b is the number of blocks and r is the number of requirements
def apartment_hunting2(blocks, reqs):
    # 直接求出每个需求在每个公寓中的最小距离
    min_distances_from_blocks = list(map(lambda req: get_min_distances(blocks, req), reqs))
    # 通过每个需求在每个公寓中的最小距离得出某个需求最大距离
    max_distances_at_blocks = get_max_distances_at_blocks(blocks, min_distances_from_blocks)
    # 由于已经知道了每个公寓中某个需求的最大距离, 只要找出需求最大距离相对于其它公寓是最小的, 就是最佳的位置了
    return get_idx_at_min_value(max_distances_at_blocks)


def get_min_distances(blocks, req):
    # 记录指定需求相对当前位置的最小距离
    min_distances = [0 for _ in blocks]
    # 记录存在指定需求的位置
    closest_req_idx = float("inf")
    # 1. 从左向右计算一次
    for i in range(len(blocks)):
        # 只要存在指定需求则更新位置缓存
        if blocks[i][req]:
            closest_req_idx = i
        # 计算最小的距离, 由于可能存在小值减去大值的情况, 这里使用绝对值
        min_distances[i] = distance_between(i, closest_req_idx)
    # 2. 从右向左再计算一次
    for i in reversed(range(len(blocks))):
        if blocks[i][req]:
            closest_req_idx = i
        # 由于第一次计算已经有缓存, 这里需要先做最小值比较再进行记录
        min_distances[i] = min(min_distances[i], distance_between(i, closest_req_idx))
    return min_distances


def get_max_distances_at_blocks(blocks, min_distances_from_blocks):
    # 记录每个公寓的每个需求中最大距离的需求
    max_distances_at_blocks = [0 for _ in blocks]
    for i in range(len(blocks)):
        # 由于每个公寓的需求有多个, 这里需要聚合相对于每个公寓索引处的所有需求最小距离
        min_distances_at_block = list(map(lambda distances: distances[i], min_distances_from_blocks))
        # 然后再根据获得的多个需求最小距离计算最大距离
        max_distances_at_blocks[i] = max(min_distances_at_block)
    return max_distances_at_blocks


if __name__ == '__main__':
    apartments = [
        {'gym': False, 'school': True, 'store': False},
        {'gym': True, 'school': False, 'store': False},
        {'gym': True, 'school': True, 'store': False},
        {'gym': False, 'school': True, 'store': False},
        {'gym': False, 'school': True, 'store': True}
    ]
    requirements = ['gym', 'school', 'store']
    print(apartment_hunting1(apartments, requirements))
    print(apartment_hunting2(apartments, requirements))
```

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <climits>
#include <algorithm>
#include <cmath>
using namespace std;

int getIdxAtMinValue(vector<int> array);
int distanceBetween(int a, int b);

// O(b^2*r) time | O(b) space - where b is the number of blocks and r is the number of requirements
int apartmentHunting1(vector<unordered_map<string, bool>> blocks, vector<string> reqs)
{
    const int blocksSize = blocks.size();
    vector<int> maxDistancesAtBlocks(blocksSize, INT_MIN);
    for (int i = 0; i < blocksSize; i++)
    {
        for (string req : reqs)
        {
            int closestReqDistance = INT_MAX;
            for (int j = 0; j < blocksSize; j++)
            {
                if (blocks[j][req])
                {
                    closestReqDistance = min(closestReqDistance, distanceBetween(i, j));
                }
            }
            maxDistancesAtBlocks[i] =
                max(maxDistancesAtBlocks[i], closestReqDistance);
        }
    }
    return getIdxAtMinValue(maxDistancesAtBlocks);
}

int getIdxAtMinValue(vector<int> array)
{
    int idxAtMinValue = 0;
    int minValue = INT_MAX;
    const int arraySize = array.size();
    for (int i = 0; i < arraySize; i++)
    {
        int currentValue = array[i];
        if (currentValue < minValue)
        {
            minValue = currentValue;
            idxAtMinValue = i;
        }
    }
    return idxAtMinValue;
}

int distanceBetween(int a, int b)
{
    return abs(a - b);
}

vector<int> getMinDistances(vector<unordered_map<string, bool>> blocks, string req);
vector<int> getMaxDistancesAtBlocks(vector<unordered_map<string, bool>> blocks,
                                    vector<vector<int>> minDistancesFromBlocks);

// O(br) time | O(br) space - where b is the number of blocks and r is the number of requirements
int apartmentHunting2(vector<unordered_map<string, bool>> blocks,
                      vector<string> reqs)
{
    vector<vector<int>> minDistancesFromBlocks;
    for (string req : reqs)
    {
        minDistancesFromBlocks.push_back(getMinDistances(blocks, req));
    }
    vector<int> maxDistancesAtBlocks = getMaxDistancesAtBlocks(blocks, minDistancesFromBlocks);
    return getIdxAtMinValue(maxDistancesAtBlocks);
}

vector<int> getMinDistances(vector<unordered_map<string, bool>> blocks, string req)
{
    const int blocksSize = blocks.size();
    vector<int> minDistances(blocksSize);
    int closestReqIdx = INT_MAX;

    for (int i = 0; i < blocksSize; i++)
    {
        if (blocks[i][req])
            closestReqIdx = i;
        minDistances[i] = distanceBetween(i, closestReqIdx);
    }
    for (int i = blocksSize - 1; i >= 0; i--)
    {
        if (blocks[i][req])
            closestReqIdx = i;
        minDistances[i] = min(minDistances[i], distanceBetween(i, closestReqIdx));
    }
    return minDistances;
}

vector<int> getMaxDistancesAtBlocks(vector<unordered_map<string, bool>> blocks,
                                    vector<vector<int>> minDistancesFromBlocks)
{
    const int blocksSize = blocks.size();
    vector<int> maxDistancesAtBlocks(blocksSize);
    for (int i = 0; i < blocksSize; i++)
    {
        vector<int> minDistancesAtBlock;
        for (vector<int> distances : minDistancesFromBlocks)
        {
            minDistancesAtBlock.push_back(distances[i]);
        }
        maxDistancesAtBlocks[i] = *max_element(minDistancesAtBlock.begin(), minDistancesAtBlock.end());
    }
    return maxDistancesAtBlocks;
}

int main()
{
    vector<unordered_map<string, bool>> apartments = {
        {{"gym", false}, {"school", true}, {"store", false}},
        {{"gym", true}, {"school", false}, {"store", false}},
        {{"gym", true}, {"school", true}, {"store", false}},
        {{"gym", false}, {"school", true}, {"store", false}},
        {{"gym", false}, {"school", true}, {"store", true}}};
    vector<string> requirements = {"gym", "school", "store"};
    cout << apartmentHunting1(apartments, requirements) << endl;
    cout << apartmentHunting2(apartments, requirements) << endl;
    return 0;
}
```

## 日程表匹配

在两个日程表以及对应日程表的工作时间范围内，找出符合指定会议时间的可用会议时段

```python
# O(c1 + c2) time | O(c1 + c2) space - where c1 and c2 are the respective numbers of meetings in calendar1 and calendar2
def calendar_matching(calendar1, daily_bounds1, calendar2, daily_bounds2, meeting_duration):
    # 先将日程表和工作时间转为一个比较直观的日程表, 非工作时间可以认为是日程中的一部分, 不可以安排会仪
    updated_calendar1 = update_calendar(calendar1, daily_bounds1)
    updated_calendar2 = update_calendar(calendar2, daily_bounds2)
    # 合并两个日程表
    merged_calendar = merge_calendars(updated_calendar1, updated_calendar2)
    # 筛选日程表中时间间隔有重叠的部分, 如 9:30 - 12:00 与 10:25 - 13:45 实际可以合并为 9:30 - 13:45
    flattened_calendar = flatten_calendar(merged_calendar)
    # 搜索筛选后的日程表, 找出合适的会议时间段
    return get_matching_availabilities(flattened_calendar, meeting_duration)


def update_calendar(calendar, daily_bounds):
    # 复制一个新的日程表, 这里注意要复制数据, 保证不修改原始的数据
    updated_calendar = calendar[:]
    # 添加前部分为一天的开始时间到工作的开始时间
    updated_calendar.insert(0, ["0:00", daily_bounds[0]])
    # 添加后部分为工作的结束时间到一天的结束时间
    updated_calendar.append([daily_bounds[1], "23:59"])
    # 将时间字符串转为分钟数, 方便进行比较
    return list(map(time_pair_to_minutes_pair, updated_calendar))


def merge_calendars(calendar1, calendar2):
    merged = []
    i, j = 0, 0
    calendar_one_length = len(calendar1)
    calendar_two_length = len(calendar2)
    # 合并两个日程表
    while i < calendar_one_length and j < calendar_two_length:
        meeting1, meeting2 = calendar1[i], calendar2[j]
        # 简单地比较两个日程表当前位置的日程开始时间, 优先把开始时间小的添加到合并的日程表中
        if meeting1[0] < meeting2[0]:
            merged.append(meeting1)
            i += 1
        else:
            merged.append(meeting2)
            j += 1
    # 如果两个日程表中还存在未添加完的日程则继续将其添加完毕
    while i < calendar_one_length:
        merged.append(calendar1[i])
        i += 1
    while j < calendar_two_length:
        merged.append(calendar2[j])
        j += 1
    return merged


def flatten_calendar(calendar):
    # 合并日程需要与前一个日程进行比较, 所以合并记录表初始值为原日程表中的第一个日程, 这里注意要复制数据, 保证不修改原始的数据
    flattened = [calendar[0][:]]
    # 从第二个日程开始
    for i in range(1, len(calendar)):
        current_meeting = calendar[i]
        # 每次都需要取合并记录表中的最后一个日程进行比较
        previous_meeting = flattened[-1]
        current_start, current_end = current_meeting
        previous_start, previous_end = previous_meeting
        # 判断前一个日程的结束时间是否覆盖到了当前日程开始时间
        if previous_end >= current_start:
            # 如果存在覆盖的情况, 仅更新最后一个日程表
            new_previous_meeting = [previous_start, max(previous_end, current_end)]
            flattened[-1] = new_previous_meeting
        else:
            # 如果没有重叠则将当前日程添加到合并记录表中, 这里注意要复制数据, 保证不修改原始的数据
            flattened.append(current_meeting[:])
    return flattened


def get_matching_availabilities(calendar, meeting_duration):
    matching_availabilities = []
    # 搜索合适的会议时间段, 因为需要和前一个日程进行比较, 所以这里从第二个位置开始
    for i in range(1, len(calendar)):
        # 取前一个日程的结束时间
        start = calendar[i - 1][1]
        # 取当前日程的开始时间
        end = calendar[i][0]
        # 判断时间间隔是否与需求符合
        availability_duration = end - start
        # 如果符合需求则添加到记录中
        if availability_duration >= meeting_duration:
            matching_availabilities.append([start, end])
    # 最后需要再将分钟表示的时间转换为时间字符串
    return list(map(minutes_pair_to_time_pair, matching_availabilities))


def time_to_minutes(time):
    hours, minutes = list(map(int, time.split(":")))
    return hours * 60 + minutes


def time_pair_to_minutes_pair(time_pair):
    return [time_to_minutes(time_pair[0]), time_to_minutes(time_pair[1])]


def minutes_to_time(minutes):
    hours = minutes // 60
    minute = minutes % 60
    hours_string = str(hours)
    minutes_string = "0" + str(minute) if minute < 10 else str(minute)
    return hours_string + ":" + minutes_string


def minutes_pair_to_time_pair(minutes_pair):
    return [minutes_to_time(minutes_pair[0]), minutes_to_time(minutes_pair[1])]


def algo():
    print(calendar_matching(
        [["9:00", "10:30"], ["12:00", "13:00"], ["16:00", "18:00"]],
        ["9:00", "20:00"],
        [["10:00", "11:30"], ["12:30", "14:30"], ["14:30", "15:00"], ["16:00", "17:00"]],
        ["10:00", "18:30"], 30))
```

```cpp
#include <iostream>
#include <cmath>
#include <vector>

using namespace std;

struct StringMeeting
{
    string start;
    string end;
};

struct Meeting
{
    int start;
    int end;
};

vector<Meeting> updateCalendar(vector<StringMeeting> calendar, StringMeeting dailyBounds);
vector<Meeting> mergeCalendars(vector<Meeting> calendar1, vector<Meeting> calendar2);
vector<Meeting> flattenCalendar(vector<Meeting> calendar);
vector<StringMeeting> getMatchingAvailabilities(vector<Meeting> calendar, int meetingDuration);
int timeToMinutes(string time);
string minutesToTime(int minutes);

// O(c1 + c2) time | O(c1 + c2) space - where c1 and c2 are the respective numbers of meetings in calendar1 and calendar2
vector<StringMeeting> calendarMatching(vector<StringMeeting> calendar1,
                                       StringMeeting dailyBounds1,
                                       vector<StringMeeting> calendar2,
                                       StringMeeting dailyBounds2,
                                       int meetingDuration)
{
    vector<Meeting> updatedCalendar1 = updateCalendar(calendar1, dailyBounds1);
    vector<Meeting> updatedCalendar2 = updateCalendar(calendar2, dailyBounds2);
    vector<Meeting> mergedCalendar = mergeCalendars(updatedCalendar1, updatedCalendar2);
    vector<Meeting> flattenedCalendar = flattenCalendar(mergedCalendar);
    return getMatchingAvailabilities(flattenedCalendar, meetingDuration);
}

vector<Meeting> updateCalendar(vector<StringMeeting> calendar,
                               StringMeeting dailyBounds)
{
    vector<StringMeeting> updatedCalendar;
    updatedCalendar.push_back({"0:00", dailyBounds.start});
    updatedCalendar.insert(updatedCalendar.end(), calendar.begin(),
                           calendar.end());
    updatedCalendar.push_back({dailyBounds.end, "23:59"});
    vector<Meeting> calendarInMinutes;
    const int updatedCalendarSize = updatedCalendar.size();
    for (int i = 0; i < updatedCalendarSize; i++)
    {
        calendarInMinutes.push_back({timeToMinutes(updatedCalendar[i].start),
                                     timeToMinutes(updatedCalendar[i].end)});
    }
    return calendarInMinutes;
}

vector<Meeting> mergeCalendars(vector<Meeting> calendar1, vector<Meeting> calendar2)
{
    vector<Meeting> merged;
    int i = 0;
    int j = 0;
    const int calendar1Size = calendar1.size();
    const int calendar2Size = calendar2.size();
    while (i < calendar1Size && j < calendar2Size)
    {
        Meeting meeting1 = calendar1[i];
        Meeting meeting2 = calendar2[j];
        if (meeting1.start < meeting2.start)
        {
            merged.push_back(meeting1);
            i++;
        }
        else
        {
            merged.push_back(meeting2);
            j++;
        }
    }
    while (i < calendar1Size)
        merged.push_back(calendar1[i++]);
    while (j < calendar2Size)
        merged.push_back(calendar2[j++]);
    return merged;
}

vector<Meeting> flattenCalendar(vector<Meeting> calendar)
{
    vector<Meeting> flattened = {calendar[0]};
    const int calendarSize = calendar.size();
    for (int i = 1; i < calendarSize; i++)
    {
        Meeting currentMeeting = calendar[i];
        Meeting previousMeeting = flattened[flattened.size() - 1];
        if (previousMeeting.end >= currentMeeting.start)
        {
            Meeting newPreviousMeeting = {
                previousMeeting.start, max(previousMeeting.end, currentMeeting.end)};
            flattened[flattened.size() - 1] = newPreviousMeeting;
        }
        else
        {
            flattened.push_back(currentMeeting);
        }
    }
    return flattened;
}

vector<StringMeeting> getMatchingAvailabilities(vector<Meeting> calendar, int meetingDuration)
{
    vector<Meeting> matchingAvailabilities;
    const int calendarSize = calendar.size();

    for (int i = 1; i < calendarSize; i++)
    {
        int start = calendar[i - 1].end;
        int end = calendar[i].start;
        int availabilityDuration = end - start;
        if (availabilityDuration >= meetingDuration)
        {
            matchingAvailabilities.push_back({start, end});
        }
    }

    vector<StringMeeting> matchingAvailabilitiesInHours;
    const int matchingAvailabilitiesSize = matchingAvailabilities.size();
    for (int i = 0; i < matchingAvailabilitiesSize; i++)
    {
        matchingAvailabilitiesInHours.push_back(
            {minutesToTime(matchingAvailabilities[i].start),
             minutesToTime(matchingAvailabilities[i].end)});
    }
    return matchingAvailabilitiesInHours;
}

int timeToMinutes(string time)
{
    int delimiterPos = time.find(":");
    int hours = stoi(time.substr(0, delimiterPos));
    int minutes = stoi(time.substr(delimiterPos + 1, time.length()));
    return hours * 60 + minutes;
}

string minutesToTime(int minutes)
{
    int hours = minutes / 60;
    int mins = minutes % 60;
    string hoursString = to_string(hours);
    string minutesString = mins < 10 ? "0" + to_string(mins) : to_string(mins);
    return hoursString + ":" + minutesString;
}

void iteration(vector<StringMeeting> meetings)
{
    cout << "[ ";
    for (StringMeeting element : meetings)
    {
        cout << "[" << element.start << ", " << element.end << "] ";
    }
    cout << "]" << endl;
}

int main()
{
    iteration(calendarMatching({{"9:00", "10:30"}, {"12:00", "13:00"}, {"16:00", "18:00"}},
                               {"9:00", "20:00"},
                               {{"10:00", "11:30"}, {"12:30", "14:30"}, {"14:30", "15:00"}, {"16:00", "17:00"}},
                               {"10:00", "18:30"}, 30));
    return 0;
}
```

## 瀑布流

假设有一个二维序列表示一个向下的瀑布，瀑布的某些地方存在石头会阻碍水流，给定一个开始位置让 100% 的水向下流动，遇到石头一
分为二向左和向右流动，遇到可以向下流动的位置后继续往下，计算出最后到达瀑布底部剩余的水量百分比

```python
# O(w^2 * h) time | O(w) space - where w and h
# are the width and height of the input array
def waterfall_streams(array, source):
    # 取第一行作为起始
    row_above = array[0][:]
    # 使用 -1 表示水流, 1 已经用来表示墙壁了
    row_above[source] = -1
    # 从第二行开始
    for row in range(1, len(array)):
        # 取出当前行, 使用复制, 保证不修改原始的数据
        current_row = array[row][:]
        # 遍历当前行
        for idx in range(len(row_above)):
            # 取出上一行对应列的值
            value_above = row_above[idx]
            # 判断是否包含有水流 -1
            has_water_above = value_above < 0
            # 没有水流则表示当前列不能流动水, 跳过
            if not has_water_above:
                continue
            # 判断是否为墙壁
            has_block = current_row[idx] == 1
            # 如果当前列不是墙壁, 表示水流可以继续向下
            if not has_block:
                # 由于水流可能会汇聚, 所以这里使用加等于计算
                current_row[idx] += value_above
                continue
            # 如果遇到了墙壁, 水流将会平分
            split_water = value_above / 2

            # 向左判断找到水流可以向下的位置
            left_idx = idx
            while left_idx - 1 >= 0:
                left_idx -= 1
                # 如果上一行的当前位置为墙壁, 意味着水流被阻挡, 不能向下流动了
                if row_above[left_idx] == 1:
                    break
                # 如果不是墙壁则让水流继续向下, 由于水流可能会汇聚, 所以这里使用加等于计算
                if current_row[left_idx] != 1:
                    current_row[left_idx] += split_water
                    break
            # 向右判断找到水流可以向下的位置
            right_idx = idx
            while right_idx + 1 < len(row_above):
                right_idx += 1
                # 如果上一行的当前位置为墙壁, 意味着水流被阻挡, 不能向下流动了
                if row_above[right_idx] == 1:
                    break
                # 如果不是墙壁则让水流继续向下, 由于水流可能会汇聚, 所以这里使用加等于计算
                if current_row[right_idx] != 1:
                    current_row[right_idx] += split_water
                    break
        # 将当前行切换为下一行的上一行
        row_above = current_row

    # 由于需要返回实际的百分比, 前面使用 -1 来表示水流的量, 这里需要转换为正数的百分比
    final_percentages = list(map(lambda num: num * -100, row_above))

    return final_percentages


if __name__ == '__main__':
    print(waterfall_streams([
        [0, 0, 0, 0, 0, 0, 0],
        [1, 0, 0, 0, 0, 0, 0],
        [0, 0, 1, 1, 1, 0, 0],
        [0, 0, 0, 0, 0, 0, 0],
        [1, 1, 1, 0, 0, 1, 0],
        [0, 0, 0, 0, 0, 0, 1],
        [0, 0, 0, 0, 0, 0, 0]], 3))
```
```cpp
#include <iostream>
#include <vector>
using namespace std;

// O(w^2 * h) time | O(w) space - where w and h
// are the width and height of the input array
vector<double> waterfallStreams(vector<vector<double>> array, int source)
{
    vector<double> rowAbove = array[0];
    // We'll use -1 to represent water, since 1 is used for a block.
    rowAbove[source] = -1;
    const int arraySize = array.size();
    for (int row = 1; row < arraySize; row++)
    {
        const int rowAboveSize = rowAbove.size();
        vector<double> currentRow = array[row];
        for (int idx = 0; idx < rowAboveSize; idx++)
        {
            double valueAbove = rowAbove[idx];

            bool hasWaterAbove = valueAbove < 0;
            bool hasBlock = currentRow[idx] == 1.0;

            if (!hasWaterAbove)
            {
                continue;
            }

            if (!hasBlock)
            {
                // If there is no block in the current column, move the water down.
                currentRow[idx] += valueAbove;
                continue;
            }

            double splitWater = valueAbove / 2;
            // Move water right.
            int rightIdx = idx;
            while (rightIdx + 1 < rowAboveSize)
            {
                rightIdx += 1;
                if (rowAbove[rightIdx] == 1.0)
                { // if there is a block in the way
                    break;
                }
                if (currentRow[rightIdx] != 1)
                { // if there is no block below us
                    currentRow[rightIdx] += splitWater;
                    break;
                }
            }

            // Move water left.
            int leftIdx = idx;
            while (leftIdx - 1 >= 0)
            {
                leftIdx -= 1;
                if (rowAbove[leftIdx] == 1.0)
                { // if there is a block in the way
                    break;
                }
                if (currentRow[leftIdx] != 1.0)
                { // if there is no block below us
                    currentRow[leftIdx] += splitWater;
                    break;
                }
            }
        }

        rowAbove = currentRow;
    }

    vector<double> finalPercentages;
    for (double num : rowAbove)
    {
        if (num == 0)
        {
            finalPercentages.push_back(num);
        }
        else
        {
            finalPercentages.push_back(num * -100);
        }
    }
    return finalPercentages;
}

int main()
{
    vector<double> result = waterfallStreams(
        {{0, 0, 0, 0, 0, 0, 0},
         {1, 0, 0, 0, 0, 0, 0},
         {0, 0, 1, 1, 1, 0, 0},
         {0, 0, 0, 0, 0, 0, 0},
         {1, 1, 1, 0, 0, 1, 0},
         {0, 0, 0, 0, 0, 0, 1},
         {0, 0, 0, 0, 0, 0, 0}},
        3);
    for (const double element : result)
    {
        cout << element << " ";
    }
    return 0;
}
```

## 锦标赛冠军

```python
HOW_TEAM_WON = 1


def update_scores(team, scores, points):
    if team not in scores:
        scores[team] = 0
    scores[team] += points


# O(n) time | O(k) space
# k is the number of teams
def tournament_winner(competitions, results):
    current_best_team = ''
    scores = {current_best_team: 0}

    for idx, competition in enumerate(competitions):
        result = results[idx]
        first_team, second_team = competition

        winner_team = first_team if result == HOW_TEAM_WON else second_team

        update_scores(winner_team, scores, 3)

        if scores[winner_team] > scores[current_best_team]:
            current_best_team = winner_team

    return current_best_team

    pass


if __name__ == '__main__':
    source_competitions = [["HTML", "C#"],
                    ["C#", "Python"],
                    ["Python", "HTML"]]
    source_results = [0, 0, 1]
    print(tournament_winner(source_competitions, source_results))
```

## 第一个重复值

给定一个整数数组，数组的数值范围在 1 到 N 之间，而 N 为数组的长度，找出数组中第一个出现重复的值

```python
# O(n) time | O(n) space
def find_duplicate_value1(array):
    seen = set()
    for value in array:
        if value in seen:
            return value
        seen.add(value)
    return -1


# O(n) time | O(1) space
def find_duplicate_value2(array):
    for value in array:
        abs_value = abs(value)
        if array[abs_value - 1] < 0:
            return abs_value
        array[abs_value - 1] *= -1
    return -1


if __name__ == '__main__':
    source = [3, 1, 3, 1, 1, 4, 4]
    print(find_duplicate_value1(source))
    print(find_duplicate_value2(source))
```

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;

// O(n) time | O(n) space
int firstDuplicateValue1(vector<int> array)
{
    unordered_map<int, bool> seen;
    for(int value : array)
    {
        if (seen.find(value) != seen.end()) return value;
        seen[value] = true;
    }
    return -1;
}

// O(n) time | O(1) space
int firstDuplicateValue2(vector<int> array)
{
    for(int value : array)
    {
        int absValue = abs(value);
        if (array[absValue - 1] < 0) return absValue;
        array[absValue - 1] *= -1;
    }
    return -1;
}

int main()
{
    vector<int> source = {2, 1, 5, 3, 3, 2, 4};
    cout << firstDuplicateValue1(source) << " " << firstDuplicateValue2(source);
    return 0;
}
```

## 合并重叠区间

```python
# O(n * log(n)) time | O(n) space
def merge_overlapping_intervals(intervals):
    if not len(intervals):
        return [[]]
    intervals.sort(key=lambda a: a[0])
    merged_intervals = intervals[:1]
    for i in range(1, len(intervals)):
        left, right = intervals[i]
        _, current_right = merged_intervals[-1]
        if current_right >= left:
            current_right = max(current_right, right)
            merged_intervals[-1][1] = current_right
        else:
            merged_intervals.append(intervals[i])

    return merged_intervals


if __name__ == '__main__':
    source = [[89, 90], [-10, 20], [-50, 0], [70, 90], [90, 91], [90, 95]]
    print(merge_overlapping_intervals(source))
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

// O(n * log(n)) time | O(n) space - where n is the length of the input array
vector<vector<int>> mergeOverlappingIntervals(vector<vector<int>> intervals)
{
    sort(intervals.begin(), intervals.end(),
         [](vector<int> a, vector<int> b)
    {
        return a[0] < b[0];
    });

    vector<vector<int>> mergedIntervals = { intervals[0] };
    int last_compare_index = 0;
    const int intervalsSize = intervals.size();

    for (int i=1; i < intervalsSize; i++)
    {
        int currentIntervalEnd = mergedIntervals[last_compare_index][1];
        int nextIntervalStart = intervals[i][0];
        int nextIntervalEnd = intervals[i][1];

        if (currentIntervalEnd >= nextIntervalStart)
        {
            mergedIntervals[last_compare_index][1] = max(currentIntervalEnd, nextIntervalEnd);
        }
        else
        {
            mergedIntervals.push_back(intervals[i]);
            last_compare_index++;
        }
    }
    return mergedIntervals;
}

int main()
{
    vector<vector<int>> result = mergeOverlappingIntervals({{89, 90}, {-10, 20}, {-50, 0}, {70, 90}, {90, 91}, {90, 95}});
    for( vector<int> element : result )
    {
        cout << element[0] << " " << element[1] << endl;
    }
    return 0;
}
```

## 相邻子数组最大和

```python
# O(n) time | O(1) space - where n is the length of the input array
def kadanes_algorithm(array):
    max_ending_here = array[0]
    max_so_far = array[0]
    for i in range(1, len(array)):
        num = array[i]
        max_ending_here = max(num, max_ending_here + num)
        max_so_far = max(max_so_far, max_ending_here)
    return max_so_far


if __name__ == '__main__':
    source = [3, 5, -9, 1, 3, -2, 3, 4, 7, 2, -9, 6, 3, 1, -5, 4]
    print(kadanes_algorithm(source))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

// O(n) time | O(1) space
int kadanesAlgorithm(vector<int> array)
{
    int maxEndingHere = array[0];
    int maxSoFar = array[0];
    const int arraySize = array.size();
    for (int i = 1; i < arraySize; i++)
    {
        int num = array[i];
        maxEndingHere = max(num, maxEndingHere + num);
        maxSoFar = max(maxSoFar, maxEndingHere);
    }
    return maxSoFar;
}

int main()
{
    vector<int> source = {3, 5, -9, 1, 3, -2, 3, 4, 7, 2, -9, 6, 3, 1, -5, 4};
    cout << kadanesAlgorithm(source);
    return 0;
}
```

Last Modified 2022-04-06

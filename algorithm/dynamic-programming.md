# 动态规划

## 最大不相邻元素总和

编写一个函数，该函数接受一个正整数数组，并返回数组中不相邻元素的最大总和。如果输入数组为空，则函数应返回 0

```python
def max_subset_sum_no_adjacent(array):
    if not len(array):
        return 0
    elif len(array) == 1:
        return array[0]
    second = array[0]
    first = max(array[0], array[1])

    for idx in range(2, len(array)):
        current = max(first, array[idx] + second)
        second = first
        first = current

    return first


if __name__ == '__main__':
    source = [10, 5, 20, 25, 15, 5, 5, 15]
    print(max_subset_sum_no_adjacent(source))
```

```cpp
#include <iostream>
#include <vector>

using namespace std;

// O(n) time| O(n) space
int maxSubsetSumNoAdjacent1(vector<int> array)
{
    const int arraySize = array.size();
    if (arraySize == 0)
    {
        return 0;
    }
    else if (arraySize == 1)
    {
        return array[0];
    }
    vector<int> maxSums = array;
    maxSums[1] = max(array[0], array[1]);
    for (int i = 2; i < arraySize; i++)
    {
        maxSums [i] = max(maxSums[i - 1], maxSums[i - 2] + array[i] );
    }
    return maxSums[array. size() - 1];
}

// O(n) time| O(1) space
int maxSubsetSumNoAdjacent2(vector<int> array)
{
    const int arraySize = array.size();
    if (arraySize == 0)
    {
        return 0;
    }
    else if (arraySize == 1)
    {
        return array[0];
    }
    int second = array[0];
    int first = max(array[0], array[1]);
    for (int i = 2; i < arraySize; i++)
    {
        int current = max(first, second + array[i]);
        second = first;
        first = current;
    }
    return first;
}

int main()
{
    vector<int> source = {10, 5, 20, 25, 15, 5, 5, 15};
    cout << maxSubsetSumNoAdjacent1(source) << endl;
    cout << maxSubsetSumNoAdjacent2(source) << endl;
    return 0;
}
```

## 组成金额的所有可能

给定一个金额，如 5，给定几种面额的硬币 如 1 2 5，硬币的数量没有限制，找出这几种硬币组合成金额的所有可能

```
F(5) = 5
F(5) = 2 + 2 + 1
F(5) = 2 + 1 + 1 + 1
F(5) = 1 + 1 + 1 + 1 + 1
```

```
F(X, [dₒ..dᵢ]) = F(X, [dₒ..dᵢ-₁]) + F(X - dᵢ, [dₒ..dᵢ])
Fᵢ(X) = Fᵢ-₁(X) + Fᵢ(X - dᵢ)
```

```python
# O(n * d) time | O(n) space
def number_of_ways_to_make_change(n, denoms):
    ways = [0 for _ in range(n + 1)]
    ways[0] = 1
    for denom in denoms:
        for amount in range(1, n + 1):
            if denom <= amount:
                ways[amount] += ways[amount - denom]
    return ways[n]


if __name__ == '__main__':
    print(number_of_ways_to_make_change(10, [1, 5, 10, 25]))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

// O(n * d) time | O(n) space
int numberOfWaysToMakeChange(int n, vector<int> denoms)
{
    vector<int> ways(n + 1, 0);
    ways[0] = 1;
    for (int denom : denoms)
    {
        for (int amount = 1; amount < n + 1; amount++)
        {
            if (denom <= amount)
            {
                ways[amount] += ways[amount - denom];
            }
        }
    }
    return ways[n];
}

int main()
{
    cout << numberOfWaysToMakeChange(10, {1, 5, 10, 25}) << endl;
    return 0;
}
```

## 组成金额的最小组合

给定一个金额，如 6，给定几种面额的硬币 如 1 2 4，硬币的数量没有限制，找出这几种硬币组合成金额的最小组合数，即 `F(6) = 2 + 4`，最少为 `2` 个数组合

```python
# O(n * d) time | O(n) space
def min_number_of_coins_for_change(n, denoms):
    num_of_coins = [float('inf') for _ in range(n + 1)]
    num_of_coins[0] = 0
    for denom in denoms:
        for amount in range(len(num_of_coins)):
            if denom <= amount:
                num_of_coins[amount] = min(
                    num_of_coins[amount],
                    num_of_coins[amount - denom] + 1
                )
    return num_of_coins[n] if num_of_coins[n] != float('inf') else -1


if __name__ == '__main__':
    print(min_number_of_coins_for_change(9, [3, 5]))
```

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

// O(n * d) time | O(n) space
int minNumberOfCoinsForChange(int n, vector<int> denoms)
{
    vector<int> numOfCoins(n + 1, INT_MAX);
    const int numOfCoinsSize = numOfCoins.size();
    numOfCoins[0] = 0;
    int toCompare = 0;
    for (int denom : denoms)
    {
        for (int amount = 0; amount < numOfCoinsSize; amount++)
        {
            if (denom <= amount)
            {
                if (numOfCoins[amount - denom] == INT_MAX)
                {
                    toCompare = numOfCoins[amount - denom];
                }
                else
                {
                    toCompare = numOfCoins[amount - denom] + 1;
                }
                numOfCoins[amount] = min(numOfCoins[amount], toCompare);
            }
        }
    }
    return numOfCoins[n] != INT_MAX ? numOfCoins[n] : -1;
}

int main()
{
    cout << minNumberOfCoinsForChange(6, {1, 2, 5}) << endl;
    return 0;
}
```

## 莱文斯坦距离

```python
# O(nm) time | O(nm) space
def levenshtein_distance1(str1, str2):
    edits = [[x for x in range(len(str1) + 1)] for y in range(len(str2) + 1)]
    for i in range(1, len(str2) + 1):
        edits[i][0] = edits[i - 1][0] + 1
    for i in range(1, len(str2) + 1):
        for j in range(1, len(str1) + 1):
            if str2[i - 1] == str1[j - 1]:
                edits[i][j] = edits[i - 1][j - 1]
            else:
                edits[i][j] = 1 + min(edits[i - 1][j - 1], edits[i - 1][j], edits[i][j - 1])
    return edits[-1][-1]


# O(nm) time| O(min(n, m)) space
def levenshtein_distance2(str1, str2):
    small = str1 if len(str1) < len(str2) else str2
    big = str1 if len(str1) >= len(str2) else str2
    even_edits = [x for x in range(len(small) + 1)]
    odd_edits = [-1 for x in range(len(small) + 1)]
    for i in range(1, len(big) + 1):
        if i % 2 == 1:
            current_edits = odd_edits
            previous_edits = even_edits
        else:
            current_edits = even_edits
            previous_edits = odd_edits
        current_edits[0] = i
        for j in range(1, len(small) + 1):
            if big[i - 1] == small[j - 1]:
                current_edits[j] = previous_edits[j - 1]
            else:
                current_edits[j] = 1 + min(previous_edits[j - 1], previous_edits[j], current_edits[j - 1])
    return even_edits[-1] if len(big) % 2 == 0 else odd_edits[-1]


if __name__ == '__main__':
    print(levenshtein_distance1('abc', 'yabd'))
    print(levenshtein_distance2('abc', 'yabd'))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

// O(n * m) time | O(n * m) space
int levenshteinDistance1(string str1, string str2)
{
    const int str1Length = str1.length();
    const int str2Length = str2.length();
    vector<vector<int>> edits( str2Length + 1,
                               vector<int>(str1Length + 1, 0));
    for (int i = 0; i < str2Length + 1; i++)
    {
        for (int j = 0; j < str1Length + 1; j++)
        {
            edits[i][j] = j;
        }
        edits[i][0] = i;
    }

    for (int i = 1; i < str2Length + 1; i++)
    {
        for (int j = 1; j < str1Length + 1; j++)
        {
            if (str2[i - 1] == str1[j - 1])
            {
                edits[i][j] = edits[i - 1][j - 1];
            }
            else
            {
                edits[i][j] = 1 + min(edits[i - 1][j - 1], min(edits[i - 1][j], edits[i][j - 1]));
            }
        }
    }
    return edits[str2Length][str1Length];
}

// O(n * m) time | O(min(n, m)) space
int levenshteinDistance2(string str1, string str2)
{
    string small = str1.length() < str2.length() ? str1 : str2;
    string big = str1.length() >= str2.length() ? str1 : str2;
    const int bigLength = big.length();
    const int smallLength = small.length();
    vector<int> evenEdits(smallLength + 1);
    vector<int> oddEdits(smallLength + 1);
    for (int j = 0; j < smallLength + 1; j++)
    {
        evenEdits[j] = j;
    }
    vector<int> *currentEdits;
    vector<int> *previousEdits;
    for (int i = 1; i < bigLength + 1; i++)
    {
        if( i % 2 == 1)
        {
            currentEdits = &oddEdits ;
            previousEdits = &evenEdits;
        }
        else
        {
            currentEdits = &evenEdits;
            previousEdits = &oddEdits;
        }
        (*currentEdits)[0] = i;
        for (int j = 1; j < smallLength + 1; j++)
        {
            if (big[i - 1] == small[j - 1])
            {
                (*currentEdits)[j] = previousEdits->at(j - 1);
            }
            else
            {
                (*currentEdits)[j] =
                    1 + min(previousEdits->at(j - 1),
                            min( previousEdits->at(j), currentEdits->at(j - 1)));
            }
        }
    }
    return bigLength % 2 == 0 ? evenEdits[smallLength]
           : oddEdits[smallLength];
}

int main()
{
    cout << levenshteinDistance1("abc", "yabd") << endl;
    cout << levenshteinDistance2("abc", "yabd") << endl;
    return 0;
}
```

## 最大和递增子序列

给定一个数组，找出数组中数值累加和最大的连续递增序列，如`[10, 70, 20, 30, 50, 11, 30]`中，`10 + 20 + 30 + 50 = 110` 是最大累加和的递增子序列

```python
# O(n^2) time | O(n) space
def max_sum_increasing_subsequence(array):
    # 缓存索引递增序列的位置
    sequences = [None for _ in array]
    # 缓存累加的和
    sums = [num for num in array]
    # 记录最大和的序列最后一个值的索引, 默认为数组开头
    max_sum_idx = 0
    # 第一个循环从数组开头开始
    for i in range(len(array)):
        current_num = array[i]
        # 第二个循环从数组开头到第一个循环的当前索引处
        for j in range(0, i):
            other_num = array[j]
            # 逐个判断前面的值是否比当前值小, 且累加的和是否比已经缓存的累加和大
            if other_num < current_num and sums[j] + current_num >= sums[i]:
                # 更新缓存
                sums[i] = sums[j] + current_num
                # 记录索引, 后面需要根据索引来生成递增子序列
                sequences[i] = j
        # 判断最大累加和是否比上一次记录的值大
        if sums[i] >= sums[max_sum_idx]:
            # 更新累加和的索引
            max_sum_idx = i
    # 根据索引缓存数组来生成结果, 即递增子序列
    return [sums[max_sum_idx], build_sequence(array, sequences, max_sum_idx)]


def build_sequence(array, sequences, current_idx):
    sequence = []
    # 缓存中的索引非空则继续添加下一个位置的值
    while current_idx is not None:
        sequence.append(array[current_idx])
        current_idx = sequences[current_idx]
    # 因为是逆向添加递增子序列的, 这里返回时需要反转数组
    return list(reversed(sequence))


if __name__ == '__main__':
    source = [10, 70, 20, 30, 50, 11, 30]
    print(max_sum_increasing_subsequence(source))
```

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

vector<vector<int>> buildSequence(vector<int> array, vector<int> sequences,
                                  int currentIdx, int sum);

// O(n^2) time | O(n) space
vector<vector<int>> maxSumIncreasingSubsequence(vector<int> array)
{
    const int arraySize = array.size();
    vector<int> sequences(arraySize, INT_MIN);
    vector<int> sums = array;
    int maxSumIdx = 0;
    for (int i = 0; i < arraySize; i++)
    {
        int currentNum = array[i];
        for (int j = 0; j < i; j++)
        {
            int otherNum = array[j];
            if (otherNum < currentNum && sums[j] + currentNum >= sums[i])
            {
                sums[i] = sums[j] + currentNum;
                sequences[i] = j;
            }
        }
        if (sums[i] >= sums[maxSumIdx])
        {
            maxSumIdx = i;
        }
    }
    return buildSequence(array, sequences, maxSumIdx, sums[maxSumIdx]);
}

vector<vector<int>> buildSequence(vector<int> array, vector<int> sequences,
                                  int currentIdx, int sum)
{
    vector<vector<int>> sequence = {{}, {}};
    sequence[0].push_back(sum);
    while (currentIdx != INT_MIN)
    {
        sequence[1].insert(sequence[1].begin(), array[currentIdx]);
        currentIdx = sequences[currentIdx];
    }
    return sequence;
}

int main()
{
    vector<int> source = {10, 70, 20, 30, 50, 11, 30};
    vector<vector<int>> result = maxSumIncreasingSubsequence(source);
    cout << result[0][0] << ", ";
    for (const int element : result[1])
    {
        cout << element << " ";
    }
    return 0;
}
```

## 最长公共子序列

给定两个字符串，找出两个字符串中相同的字符组成的公共字符串，如`ABCDEHF`和`ACEHDF`的最长公共字符串为`ACEHF`

```python
# O(nm*min(n, m)) time | O(nm*min(n, m)) space
def longest_common_subsequence1(str1, str2):
    # 根据传入参数的字符串长度创建一个缓存二维数组, 由于要考虑空字符串, 二维数组的宽高分别加 1
    lcs = [[[] for _ in range(len(str1) + 1)] for _ in range(len(str2) + 1)]
    # 两重循环, 逐个比较两个字符串的字符
    for i in range(1, len(str2) + 1):
        for j in range(1, len(str1) + 1):
            # 如果遇到两个字符相同的情况
            if str2[i - 1] == str1[j - 1]:
                # 连接左上位置的字符和当前字符, 这里如果是空的数组则会被 Python 忽略
                # 这里也是导致出现 min(n ,m) 算法复杂度出现的逻辑, 因为拼接数组也占用时间和空间
                lcs[i][j] = lcs[i - 1][j - 1] + [str2[i - 1]]
            # 如果两字符不相等
            else:
                # 取上一行同一列和同一行前一列的字符长度最大的
                lcs[i][j] = max(lcs[i - 1][j], lcs[i][j - 1], key=len)
    # 由于每一步操作都是在拼接字符, 所以最后完成二维数组遍历后, 最后一行最后一列的数据即是结果
    return lcs[-1][-1]


# O(nm*min(n, m)) time | O((min(n, m))^2) space
def longest_common_subsequence2(str1, str2):
    # 这里的处理方式相较于上面的方式节省了一定的空间
    # 计算两个字符串中长度较小的字符串
    small = str1 if len(str1) < len(str2) else str2
    # 计算两个字符串中长度较大的字符串
    big = str1 if len(str1) >= len(str2) else str2
    # 因为每次执行循环实际上只需要上一行的数据, 前面几行的数据其实可以抛弃
    # 这里就声明了两个数组来表示需要的缓存数据, 通过前面找出来较短的字符串创建, 减少空间使用
    even_lcs = [[] for _ in range(len(small) + 1)]
    odd_lcs = [[] for _ in range(len(small) + 1)]
    # 长度较长的数组作为循环矩阵访问中的列
    for i in range(1, len(big) + 1):
        # 通过列索引的奇偶切换两个缓存数组的指针, 重复使用
        if i % 2 == 1:
            current_lcs = odd_lcs
            previous_lcs = even_lcs
        else:
            current_lcs = even_lcs
            previous_lcs = odd_lcs
        # 长度较短的数组作为循环矩阵访问中的行
        for j in range(1, len(small) + 1):
            if big[i - 1] == small[j - 1]:
                current_lcs[j] = previous_lcs[j - 1] + [big[i - 1]]
            else:
                current_lcs[j] = max(previous_lcs[j], current_lcs[j - 1], key=len)
    return even_lcs[-1] if len(big) % 2 == 0 else odd_lcs[-1]


# O(nm) time | O(nm) space
def longest_common_subsequence3(str1, str2):
    # 这里创建的缓存数组相比上面的两种实现, 简化了记录的方式, 只记录以下四个关键数据
    # 1. 出现相同的字符
    # 2. 字符的长度
    # 3. 前一个字符出现位置的 x 坐标 (行索引)
    # 4. 前一个字符出现位置的 y 坐标 (列索引)
    lcs = [[[None, 0, None, None] for _ in range(len(str1) + 1)] for _ in range(len(str2) + 1)]
    for i in range(1, len(str2) + 1):
        for j in range(1, len(str1) + 1):
            # 如果字符出现相同
            if str2[i - 1] == str1[j - 1]:
                # 第一个位置记录相同的字符
                # 第二个位置取左上位置的字符记录长度加 1
                # 第三和第四个位置则记录左上位置的索引 (前一行且前一列)
                lcs[i][j] = [str2[i - 1], lcs[i - 1][j - 1][1] + 1, i - 1, j - 1]
            # 字符不相同
            else:
                # 取上一行同一列和同一行前一列的字符长度最大的数据
                if lcs[i - 1][j][1] > lcs[i][j - 1][1]:
                    lcs[i][j] = [None, lcs[i - 1][j][1], i - 1, j]
                else:
                    lcs[i][j] = [None, lcs[i][j - 1][1], i, j - 1]
    return build_sequence1(lcs)


def build_sequence1(lcs):
    sequence = []
    # 从最后位置开始连接实际的结果
    i = len(lcs) - 1
    j = len(lcs[0]) - 1
    while i != 0 and j != 0:
        current_entry = lcs[i][j]
        if current_entry[0] is not None:
            sequence.append(current_entry[0])
        i = current_entry[2]
        j = current_entry[3]
    # 因为连接的结果数组是从后往前的, 所以这里需要反转数组
    return list(reversed(sequence))


# O(nm) time | O(nm) space
def longest_common_subsequence4(str1, str2):
    # 这里只创建了缓存字符长度的二维数组
    lengths = [[0 for _ in range(len(str1) + 1)] for _ in range(len(str2) + 1)]
    for i in range(1, len(str2) + 1):
        for j in range(1, len(str1) + 1):
            if str2[i - 1] == str1[j - 1]:
                # 如果字符相等则取前一行前一列位置的长度加 1
                lengths[i][j] = lengths[i - 1][j - 1] + 1
            else:
                lengths[i][j] = max(lengths[i - 1][j], lengths[i][j - 1])
    return build_sequence2(lengths, str1)


def build_sequence2(lengths, string):
    sequence = []
    i = len(lengths) - 1
    j = len(lengths[0]) - 1
    while i != 0 and j != 0:
        # 如果字符串长度与前一行同一列的相同则递减行索引
        if lengths[i][j] == lengths[i - 1][j]:
            i -= 1
        # 如果字符串长度与前一列同一行的相同则递减列索引
        elif lengths[i][j] == lengths[i][j - 1]:
            j -= 1
        # 上面两个条件都不满足, 说明当前位置的长度是通过左上位置的字符长度累加的
        # 而在前面的逻辑中, 只有出现字符相同的情况下才会取左上位置的字符长度加 1
        else:
            # 记录字符
            sequence.append(string[j - 1])
            # 同时递减行列索引
            i -= 1
            j -= 1
    # 因为连接的结果数组是从后往前的, 所以这里需要反转数组
    return list(reversed(sequence))


if __name__ == '__main__':
    s1 = 'ZXVVYZW'
    s2 = 'XKYZPW'
    print(longest_common_subsequence1(s1, s2))
    print(longest_common_subsequence2(s1, s2))
    print(longest_common_subsequence3(s1, s2))
    print(longest_common_subsequence4(s1, s2))
```

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;

// O(nm*min(n, m)) time | O(nm*min(n, m)) space
vector<char> longestCommonSubsequence1(string str1, string str2)
{
    const int str1Length = str1.length();
    const int str2Length = str2.length();
    vector<vector<vector<char>>> lcs;
    for (int i = 0; i < str2Length + 1; i++)
    {
        lcs.push_back(vector<vector<char>>());
        for (int j = 0; j < str1Length + 1; j++)
        {
            lcs[i].push_back(vector<char>());
        }
    }
    for (int i = 1; i < str2Length + 1; i++)
    {
        for (int j = 1; j < str1Length + 1; j++)
        {
            if (str2[i - 1] == str1[j - 1])
            {
                vector<char> copy = lcs[i - 1][j - 1];
                copy.push_back(str2[i - 1]);
                lcs[i][j] = copy;
            }
            else
            {
                lcs[i][j] = lcs[i - 1][j].size() > lcs[i][j - 1].size() ? lcs[i - 1][j]
                                                                        : lcs[i][j - 1];
            }
        }
    }
    return lcs[str2Length][str1Length];
}

// O(nm*min(n, m)) time | O((min(n, m))^2) space
vector<char> longestCommonSubsequence2(string str1, string str2)
{
    string small = str1.length() < str2.length() ? str1 : str2;
    string big = str1.length() >= str2.length() ? str1 : str2;
    vector<vector<char>> evenLcs;
    vector<vector<char>> oddLcs;
    const int smallLength = small.length();
    const int bigLength = big.length();
    for (int i = 0; i < smallLength + 1; i++)
    {
        evenLcs.push_back(vector<char>());
    }
    for (int i = 0; i < smallLength + 1; i++)
    {
        oddLcs.push_back(vector<char>());
    }
    for (int i = 1; i < bigLength + 1; i++)
    {
        vector<vector<char>> *currentLcs;
        vector<vector<char>> *previousLcs;
        if (i % 2 == 1)
        {
            currentLcs = &oddLcs;
            previousLcs = &evenLcs;
        }
        else
        {
            currentLcs = &evenLcs;
            previousLcs = &oddLcs;
        }
        for (int j = 1; j < smallLength + 1; j++)
        {
            if (big[i - 1] == small[j - 1])
            {
                vector<char> copy = previousLcs->at(j - 1);
                copy.push_back(big[i - 1]);
                currentLcs->at(j) = copy;
            }
            else
            {
                currentLcs->at(j) =
                    previousLcs->at(j).size() > currentLcs->at(j - 1).size()
                        ? previousLcs->at(j)
                        : currentLcs->at(j - 1);
            }
        }
    }
    return bigLength % 2 == 0 ? evenLcs[smallLength]
                              : oddLcs[smallLength];
}

vector<char> buildSequence1(vector<vector<vector<int>>> lcs);

// O(nm) time | O(nm) space
vector<char> longestCommonSubsequence3(string str1, string str2)
{
    const int str1Length = str1.length();
    const int str2Length = str2.length();
    vector<vector<vector<int>>> lcs(
        str2Length + 1,
        vector<vector<int>>(str1Length + 1, vector<int>(4, 0)));
    for (int i = 1; i < str2Length + 1; i++)
    {
        for (int j = 1; j < str1Length + 1; j++)
        {
            if (str2[i - 1] == str1[j - 1])
            {
                lcs[i][j] = {str2[i - 1], lcs[i - 1][j - 1][1] + 1, i - 1, j - 1};
            }
            else
            {
                if (lcs[i - 1][j][1] > lcs[i][j - 1][1])
                {
                    lcs[i][j] = {-1, lcs[i - 1][j][1], i - 1, j};
                }
                else
                {
                    lcs[i][j] = {-1, lcs[i][j - 1][1], i, j - 1};
                }
            }
        }
    }
    return buildSequence1(lcs);
}

vector<char> buildSequence1(vector<vector<vector<int>>> lcs)
{
    vector<char> sequence;
    int i = lcs.size() - 1;
    int j = lcs[0].size() - 1;
    while (i != 0 && j != 0)
    {
        vector<int> currentEntry = lcs[i][j];
        if (currentEntry[0] != -1)
        {
            sequence.insert(sequence.begin(), currentEntry[0]);
        }
        i = currentEntry[2];
        j = currentEntry[3];
    }
    return sequence;
}

vector<char> buildSequence2(vector<vector<int>> lengths, string str);

// O(nm) time | O(nm) space
vector<char> longestCommonSubsequence4(string str1, string str2)
{
    const int str1Length = str1.length();
    const int str2Length = str2.length();
    vector<vector<int>> lengths(
        str2Length + 1, vector<int>(str1Length + 1, 0));
    for (int i = 1; i < str2Length + 1; i++)
    {
        for (int j = 1; j < str1Length + 1; j++)
        {
            if (str2[i - 1] == str1[j - 1])
            {
                lengths[i][j] = lengths[i - 1][j - 1] + 1;
            }
            else
            {
                lengths[i][j] = max(lengths[i - 1][j], lengths[i][j - 1]);
            }
        }
    }
    return buildSequence2(lengths, str1);
}

vector<char> buildSequence2(vector<vector<int>> lengths, string str)
{
    vector<char> sequence;
    int i = lengths.size() - 1;
    int j = lengths[0].size() - 1;
    while (i != 0 && j != 0)
    {
        if (lengths[i][j] == lengths[i - 1][j])
        {
            i--;
        }
        else if (lengths[i][j] == lengths[i][j - 1])
        {
            j--;
        }
        else
        {
            sequence.insert(sequence.begin(), str[j - 1]);
            i--;
            j--;
        }
    }
    return sequence;
}

void iteration(vector<char> array)
{
    for (const char element : array)
    {
        cout << element << " ";
    }
    cout << endl;
}

int main()
{
    string s1 = "ZXVVYZW";
    string s2 = "XKYZPW";
    iteration(longestCommonSubsequence1(s1, s2));
    iteration(longestCommonSubsequence2(s1, s2));
    iteration(longestCommonSubsequence3(s1, s2));
    iteration(longestCommonSubsequence4(s1, s2));
    return 0;
}
```

## 最小跳跃数

给定一个整数数组，数组中的值表示可前进的步数，计算出穿过数组的最小步数

```python
# O(n^2) time | O(n) space
def min_number_of_jumps1(array):
    # 缓存跳跃数, 默认为无穷大
    jumps = [float("inf") for _ in array]
    # 第一个跳跃数为 0
    jumps[0] = 0
    # 从第二个位置开始循环
    for i in range(1, len(array)):
        # 第二层循环从开始位置到第一层循环的当前位置
        for j in range(0, i):
            # 如果可跳跃步长的比两个位置的距离要大, 则更新
            if array[j] >= i - j:
                # 取缓存和当前获得的步长的最大值
                jumps[i] = min(jumps[j] + 1, jumps[i])
    return jumps[-1]


# O(n) time | O(1) space
def min_number_of_jumps2(array):
    # 长度小于等于 1 的数组无需跳跃
    if len(array) == 1:
        return 0
    # 记录跳跃次数
    jumps = 0
    # 记录最大可跳跃步长, 默认为第一个步长值
    max_reach = array[0]
    # 记录步数
    steps = array[0]
    # 前面已经处理了第一个位置的数据, 直接从第二个位置开始循环到倒数第二个位置
    for i in range(1, len(array) - 1):
        # 每次都更新最大可跳跃步长, 因为前面已经走过一定的步数, 所以要加上索引的值
        max_reach = max(max_reach, i + array[i])
        # 递减步数
        steps -= 1
        # 如果步数为 0
        if steps == 0:
            # 递增跳跃步数
            jumps += 1
            # 重新根据当前最大可跳跃步长更新可用步数
            steps = max_reach - i
    # 上面的循环没有走到最后一个位置, 所以这里的跳跃步数加 1
    return jumps + 1


if __name__ == '__main__':
    print(min_number_of_jumps1([3, 4, 2, 1, 2, 3, 7, 1, 1, 1, 3]))
    print(min_number_of_jumps2([2, 1, 1]))
```

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

// O(n^2) time | O(n) space
int minNumberOfJumps1(vector<int> array)
{
    const int arraySize = array.size();
    vector<int> jumps(arraySize, INT_MAX);
    jumps[0] = 0;
    for (int i = 1; i < arraySize; i++)
    {
        for (int j = 0; j < i; j++)
        {
            if (array[j] >= i - j)
            {
                jumps[i] = min(jumps[j] + 1, jumps[i]);
            }
        }
    }
    return jumps[jumps.size() - 1];
}

// O(n) time | O(1) space
int minNumberOfJumps2(vector<int> array)
{
    const int arraySize = array.size();
    if (arraySize == 1)
    {
        return 0;
    }
    int jumps = 0;
    int maxReach = array[0];
    int steps = array[0];
    for (int i = 1; i < arraySize - 1; i++)
    {
        maxReach = max(maxReach, i + array[i]);
        steps--;
        if (steps == 0)
        {
            jumps++;
            steps = maxReach - i;
        }
    }
    return jumps + 1;
}

int main()
{
    cout << minNumberOfJumps1({2, 1, 1}) << endl;
    cout << minNumberOfJumps2({3, 4, 2, 1, 2, 3, 7, 1, 1, 1, 3}) << endl;
    return 0;
}
```

## 水域面积

传入一个整数数组，数组的元素值表示墙高度，求出墙内的水域面积，如下表示水域面积为`11`

```
            x
            x
  x ~ ~ ~ ~ x
  x ~ ~ ~ ~ x
  x ~ ~ x ~ x
---------------
0 3 0 0 1 0 5 0
```

```python
# O(n) time | O(n) space - where n is the length of the input array
def water_area1(heights):
    # 缓存水面高度
    maxes = [0 for _ in heights]
    # 因为需要挡住水流至少要在数组的边界有一面墙
    # 所以数组开头和末尾其实都是 0 的水域面积
    # 记录最大墙高度, 初始为 0
    left_max = 0
    # 先从左边开始
    for i in range(len(heights)):
        height = heights[i]
        # 1. 根据墙面高度记录水面高度
        maxes[i] = left_max
        # 2. 求出下一个墙面的高度
        # 根据记录的高度和当前的高度比较, 取最大值
        # 由于是仅从左向右计算, 所以可以认为左边的墙越高, 则墙右面的水面也越高
        left_max = max(left_max, height)
    # 记录最大墙高度, 初始为 0
    right_max = 0
    # 然后从右边开始
    for i in reversed(range(len(heights))):
        height = heights[i]
        # 1. 由于前面已经计算过了从左向右的可用水域面积
        # 这里通过从右向左的墙面高度和之前记录的高度取最小值作为水面实际高度
        # 因为两面墙之间的水面高度是取高度较小的那一面墙
        min_height = min(right_max, maxes[i])
        # 2. 判断得出的水面实际高度是否高于当前的墙
        if height < min_height:
            # 减去水面没过的墙
            maxes[i] = min_height - height
        else:
            # 墙比水面还要高则表示水面高度为 0
            maxes[i] = 0
        # 3. 求出下一个墙面的高度
        right_max = max(right_max, height)
    # 最后求和所有的水面高度就是最终的水域面积了
    return sum(maxes)


# O(n) time | O(1) space - where n is the length of the input array
def water_area2(heights):
    if len(heights) == 0:
        return 0
    # 同时从左右两边开始
    left_idx = 0
    right_idx = len(heights) - 1
    # 记录左右两边的最大水面高度
    left_max = heights[left_idx]
    right_max = heights[right_idx]
    # 记录水域面积
    surface_area = 0
    # 循环直到左右指针相遇
    # 因为需要挡住水流至少要在数组的边界有一面墙
    # 初始指针位置都是在边界上, 所以下面都是先移动指针再求值的
    while left_idx < right_idx:
        # 判断左指针的墙是否比右指针的墙高度小
        if heights[left_idx] < heights[right_idx]:
            # 移动左指针
            left_idx += 1
            # 求出下一个位置的墙高度
            left_max = max(left_max, heights[left_idx])
            # 求出水域面积
            surface_area += left_max - heights[left_idx]
        else:
            # 移动右指针
            right_idx -= 1
            # 求出下一个位置的墙高度
            right_max = max(right_max, heights[right_idx])
            # 求出水域面积
            surface_area += right_max - heights[right_idx]

    return surface_area


if __name__ == '__main__':
    source = [0, 8, 0, 0, 5, 0, 0, 10, 0, 0, 1, 1, 0, 3]
    print(water_area1(source))
    print(water_area2(source))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

// O(n) time | O(n) space - where n is the length of the input array
int waterArea1(vector<int> heights)
{
    const int heightsSize = heights.size();
    vector<int> maxes(heightsSize, 0);
    int leftMax = 0;
    for (int i = 0; i < heightsSize; i++)
    {
        int height = heights[i];
        maxes[i] = leftMax;
        leftMax = max(leftMax, height);
    }
    int rightMax = 0;
    for (int i = heightsSize - 1; i >= 0; i--)
    {
        int height = heights[i];
        int minHeight = min(rightMax, maxes[i]);
        if (height < minHeight)
        {
            maxes[i] = minHeight - height;
        }
        else
        {
            maxes[i] = 0;
        }
        rightMax = max(rightMax, height);
    }
    int total = 0;
    for (int i = 0; i < heightsSize; i++)
    {
        total += maxes[i];
    }
    return total;
}

// O(n) time | O(1) space - where n is the length of the input array
int waterArea2(vector<int> heights)
{
    if (heights.size() == 0)
        return 0;

    int leftIdx = 0;
    int rightIdx = heights.size() - 1;
    int leftMax = heights[leftIdx];
    int rightMax = heights[rightIdx];
    int surfaceArea = 0;

    while (leftIdx < rightIdx)
    {
        if (heights[leftIdx] < heights[rightIdx])
        {
            leftIdx++;
            leftMax = max(leftMax, heights[leftIdx]);
            surfaceArea += leftMax - heights[leftIdx];
        }
        else
        {
            rightIdx--;
            rightMax = max(rightMax, heights[rightIdx]);
            surfaceArea += rightMax - heights[rightIdx];
        }
    }

    return surfaceArea;
}

int main()
{
    vector<int> source = {0, 8, 0, 0, 5, 0, 0, 10, 0, 0, 1, 1, 0, 3};
    cout << waterArea1(source) << endl;
    cout << waterArea2(source) << endl;
    return 0;
}
```

## 背包问题

给定一组物品，每种物品都有自己的价值和重量，在限定的总重量内，找出最优的装包方式，能使得物品的总价值达到最高，
如给出三个物品，`[(60, 10,), (100, 20,), (120, 30,)]`，每个物品中第一个值表示其价值，第二个值表示其重量，
限制背包可容纳`50`的重量，则最优的装包为第二和第三个物品，即`[50, [1, 2]]`

>返回的物品位置为数组索引

```python
def get_knapsack_items(knapsack_values, items):
    # 由于计算结果多了一行和一列, 这里需要相应减去
    i = len(knapsack_values) - 1
    # 其实这里得到的也是背包的最大可容纳重量
    c = len(knapsack_values[0]) - 1
    # 记录物品索引的数组
    sequence = []
    while i > 0:
        # 前一次相同重量下的计算结果(物品价值)相同, 说明最优解是取了上一行的
        if knapsack_values[i][c] == knapsack_values[i - 1][c]:
            # 向上一行
            i -= 1
        else:
            # 记录物品在物品数组的索引
            sequence.append(i - 1)
            # 根据物品的重量递减可容纳重量
            c -= items[i - 1][1]
            # 向上一行
            i -= 1
        # 如果可容纳物品重量已经为 0, 则表示已经获取完了所有的物品索引了
        if c == 0:
            break
    # 由于是从后往前计算的, 所以还需要反转记录数组再返回
    return list(reversed(sequence))


# O(nc) time | O(nc) space
def knapsack_problem(items, capacity):
    # 声明缓存二维数组, 行表示可用的物品, 列表示重量, 存储的值表示物品价值
    # 由于下面的逻辑需要考虑重量为 0 和可用物品为空的情况 (本来也是从重量为 0 开始计算)
    # 所以声明的二维数组多了一行一列
    knapsack_values = [[0 for _ in range(capacity + 1)] for _ in range(len(items) + 1)]
    # 忽略第一行, 因为第一行是用来表示默认物品为空的情况
    for i in range(1, len(items) + 1):
        # 获取物品的价值和其占用的重量
        value, weight = items[i - 1]
        # 从重量 0 开始计算, 判断当前物品是否能够放入背包
        # 因为只要物品的占用重量小于背包最大可容纳重量, 都认为是可以放入物品的
        for c in range(capacity + 1):
            # 物品的重量超过了背包重量
            if weight > c:
                # 取前一次相同重量下的计算结果(物品价值)
                knapsack_values[i][c] = knapsack_values[i - 1][c]
            # 物品的重量小于等于背包重量
            else:
                # 这里需要做判断, 为了尽可能地放满背包(重量), 取下面的两个值中的最大值(物品价值)
                knapsack_values[i][c] = max(
                    # 1. 前一次相同重量下的计算结果(物品价值)
                    knapsack_values[i - 1][c],
                    # 2. 减去了当前重量后的前一次的计算结果(物品价值)加上当前物品的价值总和
                    knapsack_values[i - 1][c - weight] + value
                )
    # 返回最早计算出的价值以及背包可以装下的物品在物品数组中的索引
    return [knapsack_values[-1][-1], get_knapsack_items(knapsack_values, items)]


if __name__ == '__main__':
    print(knapsack_problem([(1, 2,), (4, 3,), (5, 6,), (6, 7,)], 10))
    print(knapsack_problem([(60, 10,), (100, 20,), (120, 30,)], 50))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<vector<int>> getKnapsackItems(vector<vector<int>> knapsackValues,
                                     vector<vector<int>> items, int weight);

// O(nc) time | O(nc) space
vector<vector<int>> knapsackProblem(vector<vector<int>> items, int capacity)
{
    const int itemsSize = items.size();
    vector<vector<int>> knapsackValues(itemsSize + 1,
                                       vector<int>(capacity + 1, 0));
    for (int i = 1; i <= itemsSize; i++)
    {
        int currentWeight = items[i - 1][1];
        int currentValue = items[i - 1][0];
        for (int c = 0; c <= capacity; c++)
        {
            if (currentWeight > c)
            {
                knapsackValues[i][c] = knapsackValues[i - 1][c];
            }
            else
            {
                knapsackValues[i][c] =
                    max(knapsackValues[i - 1][c],
                        knapsackValues[i - 1][c - currentWeight] + currentValue);
            }
        }
    }
    return getKnapsackItems(knapsackValues, items,
                            knapsackValues[itemsSize][capacity]);
}

vector<vector<int>> getKnapsackItems(vector<vector<int>> knapsackValues,
                                     vector<vector<int>> items, int weight)
{
    vector<vector<int>> solution = {{}, {}};
    int i = knapsackValues.size() - 1;
    int c = knapsackValues[0].size() - 1;
    while (i > 0)
    {
        if (knapsackValues[i][c] == knapsackValues[i - 1][c])
        {
            i--;
        }
        else
        {
            solution[1].insert(solution[1].begin(), i - 1);
            c -= items[i - 1][1];
            i--;
        }
        if (c == 0)
        {
            break;
        }
    }
    solution[0].push_back(weight);
    return solution;
}

int main()
{
    vector<vector<int>> source = {{1, 2}, {4, 3}, {5, 6}, {6, 7}};
    vector<vector<int>> result = knapsackProblem(source, 10);
    cout << result[0][0] << ", ";
    for (const int element : result[1])
    {
        cout << element << " ";
    }
    return 0;
}
```

## 磁盘堆叠

给定一个数组，数组中包含多个磁盘，每个磁盘信含有三个信息，分别表示每个磁盘的宽度，深度和高度。目标是堆叠磁盘并最大限度地提高堆栈的总高度。
磁盘必须满足比其下方的任何其他磁盘更小的宽度，深度和高度。找出最适合的磁盘堆叠方式

```python
# O(n^2) time | O(n) space
def disk_stacking(disks):
    # 先根据磁盘的高度从小到大排序
    disks.sort(key=lambda disk: disk[2])
    # 缓存磁盘的累加高度, 初始的高度为每个磁盘的高度
    heights = [disk[2] for disk in disks]
    # 记录符合堆叠条件的磁盘索引, 初始全部为空
    sequences = [None for _ in disks]
    # 记录最大累加高度的索引, 参考索引数组
    max_height_idx = 0
    # 因为需要与前面的磁盘做比较, 从第二个位置开始循环
    for i in range(1, len(disks)):
        # 当前索引指向的磁盘
        current_disk = disks[i]
        # 当前索引前面的所有磁盘
        for j in range(i):
            other_disk = disks[j]
            # 判断是否满足堆叠条件, 宽度深度高度都比当前索引指向的磁盘小
            if are_valid_dimensions(other_disk, current_disk):
                # 尝试累加高度并和当前已经记录的高度比较
                if heights[i] <= current_disk[2] + heights[j]:
                    # 符合条件则更新累加高度
                    heights[i] = current_disk[2] + heights[j]
                    # 记录成功累加高度的索引
                    sequences[i] = j
        # 一次循环完成后, 如果当前累加高度大于历史的最大累加高度, 更新最大累加高度索引
        if heights[i] >= heights[max_height_idx]:
            max_height_idx = i
    # 通过缓存的索引生成结果
    return build_sequence(disks, sequences, max_height_idx)


def are_valid_dimensions(o, c):
    # 比较两个磁盘的宽度深度高度
    return o[0] < c[0] and o[1] < c[1] and o[2] < c[2]


def build_sequence(array, sequences, current_idx):
    sequence = []
    # 通过记录的最大累加高度索引从后向前寻找符合堆叠要求的所有磁盘
    while current_idx is not None:
        sequence.append(array[current_idx])
        current_idx = sequences[current_idx]
    # 由于是从后往前添加的磁盘, 需要反转数组
    return list(reversed(sequence))


if __name__ == '__main__':
    print(disk_stacking([(3, 2, 3,), (2, 1, 2,), (2, 3, 4,),
                         (1, 3, 1,), (4, 4, 5,), (2, 2, 8,)]))
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

bool areValidDimensions(vector<int> o, vector<int> c);
vector<vector<int>> buildSequence(vector<vector<int>> array,
                                  vector<int> sequences, int currentIdx);

// O(n^2) time | O(n) space
vector<vector<int>> diskStacking(vector<vector<int>> disks)
{
    const int disksSize = disks.size();
    sort(disks.begin(), disks.end(),
         [](vector<int> &a, vector<int> &b)
         { return a[2] < b[2]; });
    vector<int> heights;
    for (int i = 0; i < disksSize; i++)
    {
        heights.push_back(disks[i][2]);
    }
    vector<int> sequences;
    for (int i = 0; i < disksSize; i++)
    {
        sequences.push_back(INT_MIN);
    }
    int maxHeightIdx = 0;
    for (int i = 1; i < disksSize; i++)
    {
        vector<int> currentDisk = disks[i];
        for (int j = 0; j < i; j++)
        {
            vector<int> otherDisk = disks[j];
            if (areValidDimensions(otherDisk, currentDisk))
            {
                if (heights[i] <= currentDisk[2] + heights[j])
                {
                    heights[i] = currentDisk[2] + heights[j];
                    sequences[i] = j;
                }
            }
        }
        if (heights[i] >= heights[maxHeightIdx])
        {
            maxHeightIdx = i;
        }
    }
    return buildSequence(disks, sequences, maxHeightIdx);
}

bool areValidDimensions(vector<int> o, vector<int> c)
{
    return o[0] < c[0] && o[1] < c[1] && o[2] < c[2];
}

vector<vector<int>> buildSequence(vector<vector<int>> array,
                                  vector<int> sequences, int currentIdx)
{
    vector<vector<int>> sequence;
    while (currentIdx != INT_MIN)
    {
        sequence.insert(sequence.begin(), array[currentIdx]);
        currentIdx = sequences[currentIdx];
    }
    return sequence;
}

int main()
{
    vector<vector<int>> source = {{3, 2, 3}, {2, 1, 2}, {2, 3, 4}, {1, 3, 1}, {4, 4, 5}, {2, 2, 8}};
    vector<vector<int>> result = diskStacking(source);
    for (const vector<int> element : result)
    {
        cout << "(" << element[0] << ", " << element[1] << ", " << element[2] << ") ";
    }
    return 0;
}
```

## 圆周率中的数字

给定一个表示圆周率数字的字符串和一个字符串数组，将圆周率字符串进行切片可以得到数组中的子字符串，找出所需切片的最小次数，
如圆周率为`3141592653589793238462643383279`，给定的数组`["3", "314", "49", "9001", "15926535897", "14", "9323", "8462643383279", "4", "793"]`，
通过最少 3 次切片可以得到`314 | 15926535897 | 9323 | 8462643383279`与数组中的子字符串匹配

```python
# O(n^3 + m) time | O(n + m) space - where n is the number of digits in Pi and m is the number of favorite numbers
def numbers_in_pi1(pi, numbers):
    # 将所有的数值字符串缓存到哈希表, 方便快速查找
    numbers_table = {number: True for number in numbers}
    length_of_pi = len(pi)
    # 从 PI 字符串的开始位置递归
    min_spaces = get_min_spaces(pi, length_of_pi, numbers_table, {}, 0)
    # 如果返回的最小切片数不是无穷大则表示存在有效的结果, 否则返回 -1
    return -1 if min_spaces == float("inf") else min_spaces


def get_min_spaces(pi, length_of_pi, numbers_table, cache, idx):
    # 下标越界则返回 -1
    if idx == length_of_pi:
        return -1
    # 如果已经记录在缓存中则直接返回
    if idx in cache:
        return cache[idx]
    # 最小所需空格数设置为无穷大
    min_spaces = float("inf")
    # 从字符串指定位置开始向后搜索
    for i in range(idx, length_of_pi):
        # 逐个切片作为前缀
        prefix = pi[idx: i + 1]
        # 如果前缀字符串存在于给定的列表中
        if prefix in numbers_table:
            # 递归判断后缀是否也存在
            min_spaces_in_suffix = get_min_spaces(pi, length_of_pi, numbers_table, cache, i + 1)
            # 重新获取最小切片次数, 这里需要将返回的后缀最小切片数加 1, 因为上面的第一个下标越界的判断中会返回 -1
            min_spaces = min(min_spaces, min_spaces_in_suffix + 1)
    # 缓存计算结果
    cache[idx] = min_spaces
    # 返回当前索引处的计算结果
    return cache[idx]


# O(n^3 + m) time | O(n + m) space - where n is the number of digits in Pi and m is the number of favorite numbers
def numbers_in_pi2(pi, numbers):
    # 将所有的数值字符串缓存到哈希表, 方便快速查找
    numbers_table = {number: True for number in numbers}
    # 缓存已经计算过的结果
    cache = {}
    length_of_pi = len(pi)
    # 从后向前计算
    for i in reversed(range(length_of_pi)):
        get_min_spaces(pi, length_of_pi, numbers_table, cache, i)
    # 取缓存中的第一个结果(索引最开始处), 如果最小切片数不是无穷大则表示存在有效的结果, 否则返回 -1
    return -1 if cache[0] == float("inf") else cache[0]


if __name__ == '__main__':
    source = ['14159265358979323846264338327', '314159265358979323846', '327', '26433',
              '8', '3279', '9', '314159265', '35897932384626433832', '79', '3']
    print(numbers_in_pi1('3141592653589793238462643383279', source))
    print(numbers_in_pi2('3141592653589793238462643383279', source))
```

```cpp
#include <iostream>
#include <set>
#include <unordered_map>
#include <algorithm>
#include <climits>
#include <vector>
using namespace std;

int getMinSpaces(string pi, int lengthOfPI, set<string> numbersTable,
                 unordered_map<int, int> *cache, int idx);

// O(n^3 + m) time | O(n + m) space
// where n is the number of digits in Pi and m is the number of favorite numbers
int numbersInPi1(string pi, vector<string> numbers)
{
    set<string> numbersTable;
    for (string number : numbers)
    {
        numbersTable.insert(number);
    }
    unordered_map<int, int> cache;
    const int lengthOfPI = pi.length();
    int minSpaces = getMinSpaces(pi, lengthOfPI, numbersTable, &cache, 0);
    return minSpaces == INT_MAX ? -1 : minSpaces;
}

int getMinSpaces(string pi, int lengthOfPI, set<string> numbersTable,
                 unordered_map<int, int> *cache, int idx)
{
    if (idx == lengthOfPI)
        return -1;
    if (cache->find(idx) != cache->end())
        return cache->at(idx);
    int minSpaces = INT_MAX;
    for (int i = idx; i < lengthOfPI; i++)
    {
        string prefix = pi.substr(idx, i + 1 - idx);
        if (numbersTable.find(prefix) != numbersTable.end())
        {
            int minSpacesInSuffix = getMinSpaces(pi, lengthOfPI, numbersTable, cache, i + 1);
            // Handle int overflow.
            if (minSpacesInSuffix == INT_MAX)
            {
                minSpaces = min(minSpaces, minSpacesInSuffix);
            }
            else
            {
                minSpaces = min(minSpaces, minSpacesInSuffix + 1);
            }
        }
    }
    cache->insert({idx, minSpaces});
    return cache->at(idx);
}

// O(n^3 + m) time | O(n + m) space
// where n is the number of digits in Pi and m is the number of favorite numbers
int numbersInPi2(string pi, vector<string> numbers)
{
    set<string> numbersTable;
    for (string number : numbers)
    {
        numbersTable.insert(number);
    }
    unordered_map<int, int> cache;
    const int lengthOfPI = pi.length();
    for (int i = lengthOfPI - 1; i >= 0; i--)
    {
        getMinSpaces(pi, lengthOfPI, numbersTable, &cache, i);
    }
    return cache.at(0) == INT_MAX ? -1 : cache.at(0);
}

int main()
{
    string pi = "3141592653589793238462643383279";
    vector<string> source = {"314159265358979323846", "26433", "8", "3279", "314159265", "35897932384626433832", "79"};
    cout << numbersInPi1(pi, source) << endl;
    cout << numbersInPi2(pi, source) << endl;
    return 0;
}
```

## 第 K 次交易最大利润

给定一个数组，表示每天股票交易的资金，给定一个交易次数 K ，计算出在 K 次交易中可以获得的最大利润。

>单笔交易包括一次买入和一次卖出，利润 = 卖出获得资金 - 买入消费的资金

```python
# O(nk) time | O(nk) space
def max_profit_with_k_transactions1(prices, k):
    if not len(prices):
        return 0
    # 创建一个二维数组来缓存每一次计算的结果, 计算 K 次交易
    profits = [[0 for _ in prices] for _ in range(k + 1)]
    # 第一行的默认全部为 0, 因为下面的逻辑需要取前一次的计算缓存
    # 所以才需要多一行防止数组访问越界
    for t in range(1, k + 1):
        # 设置最大值为负无穷, 方便后面的比较逻辑
        max_thus_far = float("-inf")
        # 从第二个位置(第二天)开始, 因为第一天只能操作买入无法同时卖出
        for d in range(1, len(prices)):
            # 判断前一次交易的前一天计算的利润是否与大于当前交易的计算利润
            max_thus_far = max(max_thus_far, profits[t - 1][d - 1] - prices[d - 1])
            # 判断当前交易的当前天计算的利润和当前交易的前一天利润最大值
            profits[t][d] = max(profits[t][d - 1], max_thus_far + prices[d])
    # 返回最后一次交易最后一天的计算利润, 为 K 次交易的最大利润
    return profits[-1][-1]


# O(nk) time | O(n) space
def max_profit_with_k_transactions2(prices, k):
    if not len(prices):
        return 0
    # 相较于方法 1 仅使用两个数组来缓存计算结果
    even_profits = [0 for _ in prices]
    odd_profits = [0 for _ in prices]
    for t in range(1, k + 1):
        max_thus_far = float("-inf")
        # 通过判断奇偶性复用缓存数组
        if (t & 1) == 1:
            current_profits = odd_profits
            previous_profits = even_profits
        else:
            current_profits = even_profits
            previous_profits = odd_profits
        for d in range(1, len(prices)):
            max_thus_far = max(max_thus_far, previous_profits[d - 1] - prices[d - 1])
            current_profits[d] = max(current_profits[d - 1], max_thus_far + prices[d])
    # 判断交易次数返回计算结果
    return even_profits[-1] if (k & 1) == 0 else odd_profits[-1]


if __name__ == '__main__':
    source = [5, 11, 3, 50, 60, 90]
    print(max_profit_with_k_transactions1(source, 2))
    print(max_profit_with_k_transactions2(source, 2))
```

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

// O(nk) time | O(nk) space
int maxProfitWithKTransactions1(vector<int> prices, int k)
{
    const int pricesSize = prices.size();
    if (pricesSize == 0)
    {
        return 0;
    }
    vector<vector<int>> profits(k + 1, vector<int>(pricesSize, 0));
    for (int t = 1; t < k + 1; t++)
    {
        int maxThusFar = INT_MIN;
        for (int d = 1; d < pricesSize; d++)
        {
            maxThusFar = max(maxThusFar, profits[t - 1][d - 1] - prices[d - 1]);
            profits[t][d] = max(profits[t][d - 1], maxThusFar + prices[d]);
        }
    }
    return profits[k][pricesSize - 1];
}

// O(nk) time | O(n) space
int maxProfitWithKTransactions2(vector<int> prices, int k)
{
    const int pricesSize = prices.size();
    if (pricesSize == 0)
    {
        return 0;
    }
    vector<int> evenProfits(pricesSize);
    vector<int> oddProfits(pricesSize);
    for (int t = 1; t < k + 1; t++)
    {
        int maxThusFar = INT_MIN;
        vector<int> *currentProfits;
        vector<int> *previousProfits;
        if (t % 2 == 1)
        {
            currentProfits = &oddProfits;
            previousProfits = &evenProfits;
        }
        else
        {
            currentProfits = &evenProfits;
            previousProfits = &oddProfits;
        }
        for (int d = 1; d < pricesSize; d++)
        {
            maxThusFar = max(maxThusFar, previousProfits->at(d - 1) - prices[d - 1]);
            currentProfits->at(d) =
                max(currentProfits->at(d - 1), maxThusFar + prices[d]);
        }
    }
    return k % 2 == 0 ? evenProfits[pricesSize - 1]
                      : oddProfits[pricesSize - 1];
}

int main()
{
    vector<int> source = {5, 11, 3, 50, 60, 90};
    cout << maxProfitWithKTransactions1(source, 2) << endl;
    cout << maxProfitWithKTransactions2(source, 2) << endl;
    return 0;
}
```

## 回文字符串最小分割次数

给定一个字符串，字符串中可能包含有回文的字符串（回文字符串为两端相等的字符串，如`abccba`或`abcba`），找出按每个回文字符串子串分隔的最小次数，注意单个字符也算是回文字符串

```python
# O(n^3) time | O(n^2) space
def palindrome_partitioning_min_cuts1(string):
    # 定义一个二维数组缓存计算回文字符串的结果, 通过二维数组的行列索引
    # 来表示字符串中两个字符位置直接的子字符串是否为回文字符串
    palindromes = [[False for _ in string] for _ in string]
    for i in range(len(string)):
        for j in range(i, len(string)):
            palindromes[i][j] = is_palindrome(string[i: j + 1])
    # 记录最小的切割次数, 初始值为无穷大, 便于后面判断
    cuts = [float("inf") for _ in string]
    for i in range(len(string)):
        # 单个字符构成的字符串必定是回文字符串
        if palindromes[0][i]:
            # 最小分割数为 0
            cuts[i] = 0
        else:
            # 先取前面的分隔记录次数加 1
            cuts[i] = cuts[i - 1] + 1
            # 然后从前面第二个位置开始判断是否有回文字符串
            # 如果有回文字符串且最小分隔次数比当前的小则取其值
            for j in range(1, i):
                if palindromes[j][i] and cuts[j - 1] + 1 < cuts[i]:
                    cuts[i] = cuts[j - 1] + 1
    return cuts[-1]


def is_palindrome(string):
    left_idx = 0
    right_idx = len(string) - 1
    while left_idx < right_idx:
        if string[left_idx] != string[right_idx]:
            return False
        left_idx += 1
        right_idx -= 1
    return True


# O(n^2) time | O(n^2) space
def palindrome_partitioning_min_cuts2(string):
    # 定义一个二维数组缓存计算回文字符串的结果, 通过二维数组的行列索引
    # 来表示字符串中两个字符位置直接的子字符串是否为回文字符串
    palindromes = [[False for _ in string] for _ in string]
    for i in range(len(string)):
        # 单个字符构成的字符串必定是回文字符串
        palindromes[i][i] = True
    # 从两个字符长度开始判断, 前面已经处理了单个字符的情况
    for length in range(2, len(string) + 1):
        # 每次都只判断固定长度的子字符串
        for i in range(0, len(string) - length + 1):
            j = i + length - 1
            # 长度为 2 只需要判断两个字符是否相等
            if length == 2:
                palindromes[i][j] = string[i] == string[j]
            # 长度大于 2 则从外向里判断每两个字符是否相等, 如以索引表示字符串的字符则为
            # 1. 0 == 4
            # 2. 0 == 4 and 1 == 3
            # 3. 0 == 4 and 1 == 3 and 2 == 2
            # 4. ...
            else:
                palindromes[i][j] = string[i] == string[j] and palindromes[i + 1][j - 1]
    cuts = [float("inf") for _ in string]
    for i in range(len(string)):
        if palindromes[0][i]:
            cuts[i] = 0
        else:
            cuts[i] = cuts[i - 1] + 1
            for j in range(1, i):
                if palindromes[j][i] and cuts[j - 1] + 1 < cuts[i]:
                    cuts[i] = cuts[j - 1] + 1
    return cuts[-1]


if __name__ == '__main__':
    print(palindrome_partitioning_min_cuts1('abbbacecffgbgffab'))
    print(palindrome_partitioning_min_cuts2('abbbacecffgbgffab'))
```

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <climits>
using namespace std;

bool isPalindrome(string s);

// O(n^3) time | O(n^2) space
int palindromePartitioningMinCuts1(string s)
{
    const int stringLength = s.length();
    vector<vector<bool>> palindromes(stringLength, vector<bool>(stringLength, false));
    for (int i = 0; i < stringLength; i++)
    {
        for (int j = i; j < stringLength; j++)
        {
            palindromes[i][j] = isPalindrome(s.substr(i, j + 1 - i));
        }
    }
    vector<int> cuts(stringLength, INT_MAX);
    for (int i = 0; i < stringLength; i++)
    {
        if (palindromes[0][i])
        {
            cuts[i] = 0;
        }
        else
        {
            cuts[i] = cuts[i - 1] + 1;
            for (int j = 1; j < i; j++)
            {
                if (palindromes[j][i] && cuts[j - 1] + 1 < cuts[i])
                {
                    cuts[i] = cuts[j - 1] + 1;
                }
            }
        }
    }
    return cuts[stringLength - 1];
}

bool isPalindrome(string s)
{
    int leftIdx = 0;
    int rightIdx = s.length() - 1;
    while (leftIdx < rightIdx)
    {
        if (s[leftIdx] != s[rightIdx])
        {
            return false;
        }
        leftIdx++;
        rightIdx--;
    }
    return true;
}

// O(n^2) time | O(n^2) space
int palindromePartitioningMinCuts2(string s)
{
    const int stringLength = s.length();
    vector<vector<bool>> palindromes(stringLength, vector<bool>(stringLength, false));
    for (int i = 0; i < stringLength; i++)
    {
        palindromes[i][i] = true;
    }
    for (int length = 2; length < stringLength + 1; length++)
    {
        for (int i = 0; i < stringLength - length + 1; i++)
        {
            int j = i + length - 1;
            if (length == 2)
            {
                palindromes[i][j] = (s[i] == s[j]);
            }
            else
            {
                palindromes[i][j] = (s[i] == s[j] && palindromes[i + 1][j - 1]);
            }
        }
    }
    vector<int> cuts(stringLength, INT_MAX);
    for (int i = 0; i < stringLength; i++)
    {
        if (palindromes[0][i])
        {
            cuts[i] = 0;
        }
        else
        {
            cuts[i] = cuts[i - 1] + 1;
            for (int j = 1; j < i; j++)
            {
                if (palindromes[j][i] && cuts[j - 1] + 1 < cuts[i])
                {
                    cuts[i] = cuts[j - 1] + 1;
                }
            }
        }
    }
    return cuts[stringLength - 1];
}

int main()
{
    string source = "abbbacecffgbgffab";
    cout << palindromePartitioningMinCuts1(source) << endl;
    cout << palindromePartitioningMinCuts2(source) << endl;
    return 0;
}
```

## 最长递增子序列

给定一个数组，找出数组中最长的递增子序列（从小到大），如`[5, 7, -24, 12, 10, 2, 3, 12, 5, 6, 35]`中的最长递增子序列为`[-24, 2, 3, 5, 6, 35]`

```python
# O(n^2) time | O(n) space
def longest_increasing_subsequence1(array):
    # 用来记录构成递增子序列的前一个值的索引, 后面用来生成递增子序列, 即结果
    sequences = [-1 for _ in array]
    # 用来记录走到某个位置时最大的递增子序列长度
    lengths = [1 for _ in array]
    # 记录最后一个可以构成最大递增子序列的索引, 后面需要用来生成最终结果
    max_length_idx = 0
    for i in range(len(array)):
        current_num = array[i]
        # 搜索前面的值
        for j in range(0, i):
            other_num = array[j]
            # 判断前面的值是否比当前值要小且前面的值对应位置记录的最大递增子序列长度加 1 后比当前的记录值要大
            if other_num < current_num and lengths[j] + 1 >= lengths[i]:
                # 更新当前位置的最大递增子序列长度
                lengths[i] = lengths[j] + 1
                # 记录构成递增子序列的前一个值的索引
                sequences[i] = j
        # 更新最后一个可以构成最大递增子序列的索引
        if lengths[i] >= lengths[max_length_idx]:
            max_length_idx = i
    return build_sequence(array, sequences, max_length_idx)


def build_sequence(array, sequences, current_idx):
    sequence = []
    while current_idx != -1:
        sequence.append(array[current_idx])
        current_idx = sequences[current_idx]
    return list(reversed(sequence))


# O(n * log(n)) time | O(n) space
def longest_increasing_subsequence2(array):
    # 用来记录索引, 后面用来生成递增子序列
    sequences = [-1 for _ in array]
    # 记录递增子序列指定长度的最后一个最小的元素索引, 本身的索引用来表示递增子序列的长度,
    # 因为仅一个元素的数组可以直接判断为最长递增子序列, 所以这里不会用到第一个值,
    # 增加一个值是因为最大的递增子序列可能刚好等于输入数组的长度
    indices = [-1 for _ in range(len(array) + 1)]
    # 表示最大的递增子序列长度
    length = 0
    for i, num in enumerate(array):
        # 从长度 1 开始搜索, 因为最小的递增子序列至少为 1
        new_length = binary_search(1, length, indices, array, num)
        # 记录前一个小于当前值的元素索引
        sequences[i] = indices[new_length - 1]
        # 满足条件的长度位置设置为当前索引
        # 指定长度的递增子序列可能存在很多满足条件的值, 每次更新都可以保证记录更小的值
        # 较小的长度保存的值越小, 后面可以搜索到更多满足条件(构成递增序列)的元素就越多
        indices[new_length] = i
        # 更新最大的递增子序列长度
        length = max(length, new_length)
    return build_sequence(array, sequences, indices[length])


def binary_search(start_idx, end_idx, indices, array, num):
    if start_idx > end_idx:
        return start_idx
    # 折半搜索当前递增子序列长度中比传入的 num 更小的值
    middle_idx = (start_idx + end_idx) // 2
    # 索引数组中总是会记录指定递增子序列长度中最后一个最小的值
    # 相对于子序列前面的值是较大的值, 但相对于后面将要搜索到的值来说是较小的值
    if array[indices[middle_idx]] < num:
        start_idx = middle_idx + 1
    else:
        end_idx = middle_idx - 1
    return binary_search(start_idx, end_idx, indices, array, num)


if __name__ == '__main__':
    source = [5, 7, -24, 12, 10, 2, 3, 12, 5, 6, 35]
    print(longest_increasing_subsequence1(source))
    print(longest_increasing_subsequence2(source))
```

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

vector<int> buildSequence(vector<int> array, vector<int> sequences, int currentIdx);

// O(n^2) time | O(n) space
vector<int> longestIncreasingSubsequence1(vector<int> array)
{
    const int arraySize = array.size();
    vector<int> sequences(arraySize, INT_MIN);
    vector<int> lengths(arraySize, 1);
    int maxLengthIdx = 0;
    for (int i = 0; i < arraySize; i++)
    {
        int currentNum = array[i];
        for (int j = 0; j < i; j++)
        {
            int otherNum = array[j];
            if (otherNum < currentNum && lengths[j] + 1 >= lengths[i])
            {
                lengths[i] = lengths[j] + 1;
                sequences[i] = j;
            }
        }
        if (lengths[i] >= lengths[maxLengthIdx])
        {
            maxLengthIdx = i;
        }
    }
    return buildSequence(array, sequences, maxLengthIdx);
}

vector<int> buildSequence(vector<int> array, vector<int> sequences, int currentIdx)
{
    vector<int> sequence;
    while (currentIdx != INT_MIN)
    {
        sequence.insert(sequence.begin(), array[currentIdx]);
        currentIdx = sequences[currentIdx];
    }
    return sequence;
}

int binarySearch(int startIdx, int endIdx, vector<int> &indices,
                 vector<int> &array, int num);

// O(nlogn) time | O(n) space
vector<int> longestIncreasingSubsequence2(vector<int> array)
{
    const int arraySize = array.size();
    vector<int> sequences(arraySize, 0);
    vector<int> indices(arraySize + 1, INT_MIN);
    int length = 0;
    for (int i = 0; i < arraySize; i++)
    {
        int num = array[i];
        int newLength = binarySearch(1, length, indices, array, num);
        sequences[i] = indices[newLength - 1];
        indices[newLength] = i;
        length = max(length, newLength);
    }
    return buildSequence(array, sequences, indices[length]);
}

int binarySearch(int startIdx, int endIdx, vector<int> &indices,
                 vector<int> &array, int num)
{
    if (startIdx > endIdx)
    {
        return startIdx;
    }
    int middleIdx = (startIdx + endIdx) / 2;
    if (array[indices[middleIdx]] < num)
    {
        startIdx = middleIdx + 1;
    }
    else
    {
        endIdx = middleIdx - 1;
    }
    return binarySearch(startIdx, endIdx, indices, array, num);
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
    vector<int> source = {5, 7, -24, 12, 10, 2, 3, 12, 5, 6, 35};
    iteration(longestIncreasingSubsequence1(source));
    iteration(longestIncreasingSubsequence2(source));
    return 0;
}
```

## 最长字符串链

给定一组字符串，这组字符串中的每个字符串剪切掉某些字符后可以组成这组字符串中的其它的字符串（单个字符不可以，需要忽略），找出可以组成最多其它字符串的字符串，并给出组成字符串的结果（字符串的数量至少要为 2）；
如`["abde", "abc", "abd", "abcde", "ade", "ae", "labde", "abcdeg"]`中可以组成最多其它字符串的是`abcdeg`，组成结果为`['abcdeg', 'abcde', 'abde', 'ade', 'ae']`

```python
# O(n * m^2 + n * log(n)) time | O(nm) space - where n is the number of strings
# and m is the length of the longest string
def longest_string_chain(strings):
    # 创建一个哈希表缓存每一个字符串, 用来跟踪每个字符串可以构成的子字符串
    string_chains = {}
    for string in strings:
        # 记录下一个可以构成的子字符串和可以构成的子字符串数量, 数量默认为 1
        string_chains[string] = {"next_string": "", "max_chain_length": 1}

    # 将字符串数组按其长度排序, 优先访问长度较小的字符串
    sorted_strings = sorted(strings, key=len)
    for string in sorted_strings:
        # 逐个搜索字符串
        find_longest_string_chain(string, string_chains)

    return build_longest_string_chain(strings, string_chains)


def find_longest_string_chain(string, string_chains):
    # 尝试对每一个字符做剪切
    for i in range(len(string)):
        smaller_string = get_smaller_string(string, i)
        # 不包含在缓存中的忽略
        if smaller_string not in string_chains:
            continue
        # 包含在缓存中的尝试做更新操作, 记录可以构成的字符串链的最大长度
        try_update_longest_string_chain(string, smaller_string, string_chains)


def get_smaller_string(string, index):
    # 拼接字符串但忽略掉指定索引处的字符
    return string[:index] + string[index + 1:]


def try_update_longest_string_chain(current_string, smaller_string, string_chains):
    # 子字符串长度
    smaller_string_chain_length = string_chains[smaller_string]["max_chain_length"]
    # 当前字符串记录的长度
    current_string_chain_length = string_chains[current_string]["max_chain_length"]
    # 比较两个长度, 如果当前记录的长度小于 子字符串长度加 1, 则更新子字符串和构成字符串链的最大长度
    if smaller_string_chain_length + 1 > current_string_chain_length:
        string_chains[current_string]["max_chain_length"] = smaller_string_chain_length + 1
        string_chains[current_string]["next_string"] = smaller_string


def build_longest_string_chain(strings, string_chains):
    # 找到可以构成字符串链最大长度的字符串
    max_chain_length = 0
    chain_starting_string = ""
    for string in strings:
        if string_chains[string]["max_chain_length"] > max_chain_length:
            max_chain_length = string_chains[string]["max_chain_length"]
            chain_starting_string = string

    # 由于已经通过哈希表记录了每个字符串的关联, 可直接从最长的字符串开始构建最终结果
    our_longest_string_chain = []
    current_string = chain_starting_string
    while len(current_string):
        # 添加当前的字符串
        our_longest_string_chain.append(current_string)
        # 读取缓存中下一个子字符串
        current_string = string_chains[current_string]["next_string"]

    return [] if len(our_longest_string_chain) == 1 else our_longest_string_chain


if __name__ == '__main__':
    source = ["abde", "abc", "abd", "abcde", "ade", "ae", "labde", "abcdeg"]
    print(longest_string_chain(source))
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <unordered_map>

using namespace std;

struct stringChain
{
    string nextString;
    int maxChainLength;
};

void findLongestStringChain(string str,
                            unordered_map<string, stringChain> &stringChains);
string getSmallerString(string str, int index);
void tryUpdateLongestStringChain(
    string currentString, string smallerString,
    unordered_map<string, stringChain> &stringChains);
vector<string>
buildLongestStringChain(vector<string> strings,
                        unordered_map<string, stringChain> stringChains);

// O(n * m^2 + n * log(n)) time | O(nm) space - where n is the number of strings
// and m is the length of the longest string
vector<string> longestStringChain(vector<string> strings)
{
    // For every string, imagine the longest string chain that starts with it.
    // Set up every string to point to the next string in its respective longest
    // string chain. Also keep track of the lengths of these longest string
    // chains.
    unordered_map<string, stringChain> stringChains = {};
    for (auto string : strings)
    {
        stringChains[string] = {"", 1};
    }

    // Sort the strings based on their length so that whenever we visit a
    // string (as we iterate through them from left to right), we can
    // already have computed the longest string chains of any smaller strings.
    vector<string> sortedStrings = strings;
    sort(sortedStrings.begin(), sortedStrings.end(),
         [](string a, string b) -> bool
         { return a.size() < b.size(); });

    for (auto string : sortedStrings)
    {
        findLongestStringChain(string, stringChains);
    }

    return buildLongestStringChain(strings, stringChains);
}

void findLongestStringChain(string str,
                            unordered_map<string, stringChain> &stringChains)
{
    // Try removing every letter of the current string to see if the
    // remaining strings form a string chain.
    const int strSize = str.size();
    for (int i = 0; i < strSize; i++)
    {
        string smallerString = getSmallerString(str, i);
        if (stringChains.find(smallerString) == stringChains.end())
            continue;
        tryUpdateLongestStringChain(str, smallerString, stringChains);
    }
}

string getSmallerString(string str, int index)
{
    return str.substr(0, index) + str.substr(index + 1);
}

void tryUpdateLongestStringChain(
    string currentString, string smallerString,
    unordered_map<string, stringChain> &stringChains)
{
    int smallerStringChainLength = stringChains[smallerString].maxChainLength;
    int currentStringChainLength = stringChains[currentString].maxChainLength;
    // Update the string chain of the current string only if the smaller string
    // leads to a longer string chain.
    if (smallerStringChainLength + 1 > currentStringChainLength)
    {
        stringChains[currentString].maxChainLength = smallerStringChainLength + 1;
        stringChains[currentString].nextString = smallerString;
    }
}

vector<string>
buildLongestStringChain(vector<string> strings,
                        unordered_map<string, stringChain> stringChains)
{
    // Find the string that starts the longest string chain.
    int maxChainLength = 0;
    string chainStartingString = "";
    for (auto string : strings)
    {
        if (stringChains[string].maxChainLength > maxChainLength)
        {
            maxChainLength = stringChains[string].maxChainLength;
            chainStartingString = string;
        }
    }

    // Starting at the string found above, build the longest string chain.
    vector<string> ourLongestStringChain;
    string currentString = chainStartingString;
    while (currentString != "")
    {
        ourLongestStringChain.push_back(currentString);
        currentString = stringChains[currentString].nextString;
    }

    return ourLongestStringChain.size() == 1 ? vector<string>{}
                                             : ourLongestStringChain;
}

int main()
{
    vector<string> source = {"abde", "abc", "abd", "abcde", "ade", "ae", "labde", "abcdeg"};
    vector<string> result = longestStringChain(source);
    for (string element : result)
    {
        cout << element << " ";
    }
    return 0;
}
```

## 方形的零

给定一个矩形，矩形中只会包含`0`和`1`两个值，判断矩形中是否存在由`0`构成的一个小矩形（假设输入的矩形只会存在一个由`0`组成的小矩形）

```
[                                [
  [1, 1, 1, 0, 1, 0],              [ ,  ,  ,  ,  ,  ],
  [0, 0, 0, 0, 0, 1],              [0, 0, 0, 0, 0,  ],
  [0, 1, 1, 1, 0, 1],     >>>      [0,  ,  ,  , 0,  ],
  [0, 0, 0, 1, 0, 1],              [0,  ,  ,  , 0,  ],
  [0, 1, 1, 1, 0, 1],              [0,  ,  ,  , 0,  ],
  [0, 0, 0, 0, 0, 1],              [0, 0, 0, 0, 0,  ],
]                                ]
```

```python
# O(n^4) time | O(n^3) space - where n is the height and width of the matrix
def square_of_zeroes1(matrix):
    last_idx = len(matrix) - 1
    # 从最大的矩形开始, 即输入矩形的四个角
    return has_square_of_zeroes1(matrix, 0, 0, last_idx, last_idx, {})


# r1 is the top row, c1 is the left column
# r2 is the bottom row, c2 is the right column
def has_square_of_zeroes1(matrix, r1, c1, r2, c2, cache):
    # 防止索引越界, 递归的终点
    if r1 >= r2 or c1 >= c2:
        return False
    # 记录缓存
    key = str(r1) + "-" + str(c1) + "-" + str(r2) + "-" + str(c2)
    if key in cache:
        # 如果缓存中已经存在则直接返回
        return cache[key]
    # 没有缓存则记录
    cache[key] = (
            # 判断当前的小矩形四条边是否为一个方形的零
            is_square_of_zeroes1(matrix, r1, c1, r2, c2)
            # 同时缩小四个角
            or has_square_of_zeroes1(matrix, r1 + 1, c1 + 1, r2 - 1, c2 - 1, cache)
            # 向右上缩小一个位置判断
            or has_square_of_zeroes1(matrix, r1, c1 + 1, r2 - 1, c2, cache)
            # 向左下缩小一个位置判断
            or has_square_of_zeroes1(matrix, r1 + 1, c1, r2, c2 - 1, cache)
            # 向右下缩小一个位置判断
            or has_square_of_zeroes1(matrix, r1 + 1, c1 + 1, r2, c2, cache)
            # 向左上缩小一个位置判断
            or has_square_of_zeroes1(matrix, r1, c1, r2 - 1, c2 - 1, cache)
    )
    # 返回缓存记录的值
    return cache[key]


# r1 is the top row, c1 is the left column
# r2 is the bottom row, c2 is the right column
def is_square_of_zeroes1(matrix, r1, c1, r2, c2):
    # 逐行判断固定的列是否为零
    for row in range(r1, r2 + 1):
        if matrix[row][c1] != 0 or matrix[row][c2] != 0:
            return False
    # 逐列判断固定的行是否为零
    for col in range(c1, c2 + 1):
        if matrix[r1][col] != 0 or matrix[r2][col] != 0:
            return False
    return True


# O(n^4) time | O(1) space - where n is the height and width of the matrix
def square_of_zeroes2(matrix):
    n = len(matrix)
    # 两个循环表示矩形坐标点的左上角相对位置
    for top_row in range(n):
        for left_col in range(n):
            # 矩形至少为四个角故行和列的长度至少为 2
            square_length = 2
            # 1. 小矩形的宽小于最大列数减去最后一列的长度
            # 2. 小矩形的长小于最大行数减去最后一行的长度
            # 满足条件则继续循环
            while square_length <= n - left_col and square_length <= n - top_row:
                # 根据长度计算出左下位置行索引
                bottom_row = top_row + square_length - 1
                # 根据长度计算出右上位置列索引
                right_col = left_col + square_length - 1
                # 判断当前的小矩形是否由零构成
                if is_square_of_zeroes1(matrix, top_row, left_col, bottom_row, right_col):
                    return True
                # 递增计算长度
                square_length += 1
    return False


# O(n^3) time | O(n^3) space - where n is the height and width of the matrix
def square_of_zeroes3(matrix):
    # 优先计算出每个位置相对于下方和右边的零的数量
    info_matrix = pre_compute_num_of_zeroes(matrix)
    last_idx = len(matrix) - 1
    # 从最大的矩形开始, 即输入矩形的四个角
    return has_square_of_zeroes2(info_matrix, 0, 0, last_idx, last_idx, {})


# r1 is the top row, c1 is the left column
# r2 is the bottom row, c2 is the right column
def has_square_of_zeroes2(info_matrix, r1, c1, r2, c2, cache):
    if r1 >= r2 or c1 >= c2:
        return False

    key = str(r1) + "-" + str(c1) + "-" + str(r2) + "-" + str(c2)
    if key in cache:
        return cache[key]

    cache[key] = (
            is_square_of_zeroes2(info_matrix, r1, c1, r2, c2)
            or has_square_of_zeroes2(info_matrix, r1 + 1, c1 + 1, r2 - 1, c2 - 1, cache)
            or has_square_of_zeroes2(info_matrix, r1, c1 + 1, r2 - 1, c2, cache)
            or has_square_of_zeroes2(info_matrix, r1 + 1, c1, r2, c2 - 1, cache)
            or has_square_of_zeroes2(info_matrix, r1 + 1, c1 + 1, r2, c2, cache)
            or has_square_of_zeroes2(info_matrix, r1, c1, r2 - 1, c2 - 1, cache)
    )

    return cache[key]


# r1 is the top row, c1 is the left column
# r2 is the bottom row, c2 is the right column
def is_square_of_zeroes2(info_matrix, r1, c1, r2, c2):
    # 计算出矩形的边长
    square_length = c2 - c1 + 1
    # 判断上面的边零的数量大于等于要求的小矩形边长
    has_top_border = info_matrix[r1][c1]["num_zeroes_right"] >= square_length
    # 判断左面的边零的数量大于等于要求的小矩形边长
    has_left_border = info_matrix[r1][c1]["num_zeroes_below"] >= square_length
    # 判断下面的边零的数量大于等于要求的小矩形边长
    has_bottom_border = info_matrix[r2][c1]["num_zeroes_right"] >= square_length
    # 判断右面的边零的数量大于等于要求的小矩形边长
    has_right_border = info_matrix[r1][c2]["num_zeroes_below"] >= square_length
    return has_top_border and has_left_border and has_bottom_border and has_right_border


def pre_compute_num_of_zeroes(matrix):
    info_matrix = [[x for x in row] for row in matrix]

    n = len(matrix)
    # 第一次循环, 从矩形的左上角开始不停向右向下
    for row in range(n):
        for col in range(n):
            # 判断当前位置是否为零
            num_zeroes = 1 if matrix[row][col] == 0 else 0
            info_matrix[row][col] = {
                # 为零则表示当前位置向下有一个零
                "num_zeroes_below": num_zeroes,
                # 为零则表示当前位置向右有一个零
                "num_zeroes_right": num_zeroes,
            }

    last_idx = len(matrix) - 1
    # 第二次循环, 从矩形的右下角开始不停向左向上处理
    for row in reversed(range(n)):
        for col in reversed(range(n)):
            # 如果不是零则跳过
            if matrix[row][col] == 1:
                continue
            # 行索引不越界的情况下
            if row < last_idx:
                # 累加下方记录的零数量
                info_matrix[row][col]["num_zeroes_below"] += info_matrix[row + 1][col]["num_zeroes_below"]
            # 列索引不越界的情况下
            if col < last_idx:
                # 累加右边记录的零数量
                info_matrix[row][col]["num_zeroes_right"] += info_matrix[row][col + 1]["num_zeroes_right"]

    return info_matrix


# O(n^3) time | O(n^2) space - where n is the height and width of the matrix
def square_of_zeroes4(matrix):
    info_matrix = pre_compute_num_of_zeroes(matrix)
    n = len(matrix)
    for top_row in range(n):
        for left_col in range(n):
            square_length = 2
            while square_length <= n - left_col and square_length <= n - top_row:
                bottom_row = top_row + square_length - 1
                right_col = left_col + square_length - 1
                if is_square_of_zeroes2(info_matrix, top_row, left_col, bottom_row, right_col):
                    return True
                square_length += 1
    return False


if __name__ == '__main__':
    source = [[1, 1, 1, 0, 1, 0],
              [0, 0, 0, 0, 0, 1],
              [0, 1, 1, 1, 0, 1],
              [0, 0, 0, 1, 0, 1],
              [0, 1, 1, 1, 0, 1],
              [0, 0, 0, 0, 0, 1]]
    print(square_of_zeroes1(source))
    print(square_of_zeroes2(source))
    print(square_of_zeroes3(source))
    print(square_of_zeroes4(source))
```

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <unordered_map>

using namespace std;

bool hasSquareOfZeroes1(vector<vector<int>> &matrix, int r1, int c1, int r2,
                        int c2, unordered_map<string, bool> &cache);
bool isSquareOfZeroes1(vector<vector<int>> &matrix, int r1, int c1, int r2,
                       int c2);

// O(n^4) time | O(n^3) space - where n is the height and width of the matrix
bool squareOfZeroes1(vector<vector<int>> matrix)
{
    const int lastIdx = matrix.size() - 1;
    unordered_map<string, bool> cache;
    return hasSquareOfZeroes1(matrix, 0, 0, lastIdx, lastIdx, cache);
}

// r1 is the top row, c1 is the left column
// r2 is the bottom row, c2 is the right column
bool hasSquareOfZeroes1(vector<vector<int>> &matrix, int r1, int c1, int r2,
                        int c2, unordered_map<string, bool> &cache)
{
    if (r1 >= r2 || c1 >= c2)
        return false;

    string key = to_string(r1) + '-' + to_string(c1) + '-' + to_string(r2) + '-' +
                 to_string(c2);
    if (cache.find(key) != cache.end())
        return cache[key];

    cache[key] =
        isSquareOfZeroes1(matrix, r1, c1, r2, c2) ||
        hasSquareOfZeroes1(matrix, r1 + 1, c1 + 1, r2 - 1, c2 - 1, cache) ||
        hasSquareOfZeroes1(matrix, r1, c1 + 1, r2 - 1, c2, cache) ||
        hasSquareOfZeroes1(matrix, r1 + 1, c1, r2, c2 - 1, cache) ||
        hasSquareOfZeroes1(matrix, r1 + 1, c1 + 1, r2, c2, cache) ||
        hasSquareOfZeroes1(matrix, r1, c1, r2 - 1, c2 - 1, cache);

    return cache[key];
}

// r1 is the top row, c1 is the left column
// r2 is the bottom row, c2 is the right column
bool isSquareOfZeroes1(vector<vector<int>> &matrix, int r1, int c1, int r2,
                       int c2)
{
    for (int row = r1; row < r2 + 1; row++)
    {
        if (matrix[row][c1] != 0 || matrix[row][c2] != 0)
            return false;
    }
    for (int col = c1; col < c2 + 1; col++)
    {
        if (matrix[r1][col] != 0 || matrix[r2][col] != 0)
            return false;
    }
    return true;
}

// O(n^4) time | O(1) space - where n is the height and width of the matrix
bool squareOfZeroes2(vector<vector<int>> matrix)
{
    const int n = matrix.size();
    for (int topRow = 0; topRow < n; topRow++)
    {
        for (int leftCol = 0; leftCol < n; leftCol++)
        {
            int squareLength = 2;
            while (squareLength <= n - leftCol && squareLength <= n - topRow)
            {
                int bottomRow = topRow + squareLength - 1;
                int rightCol = leftCol + squareLength - 1;
                if (isSquareOfZeroes1(matrix, topRow, leftCol, bottomRow, rightCol))
                    return true;
                squareLength++;
            }
        }
    }
    return false;
}

struct InfoMatrixItem
{
    int numZeroesBelow;
    int numZeroesRight;
};

bool hasSquareOfZeroes2(vector<vector<InfoMatrixItem>> &infoMatrix, int r1,
                        int c1, int r2, int c2,
                        unordered_map<string, bool> &cache);
bool isSquareOfZeroes2(vector<vector<InfoMatrixItem>> &infoMatrix, int r1,
                       int c1, int r2, int c2);
vector<vector<InfoMatrixItem>> preComputedNumOfZeroes(vector<vector<int>> matrix);

// O(n^3) time | O(n^3) space - where n is the height and width of the matrix
bool squareOfZeroes3(vector<vector<int>> matrix)
{
    vector<vector<InfoMatrixItem>> infoMatrix = preComputedNumOfZeroes(matrix);
    int lastIdx = matrix.size() - 1;
    unordered_map<string, bool> cache;
    return hasSquareOfZeroes2(infoMatrix, 0, 0, lastIdx, lastIdx, cache);
}

// r1 is the top row, c1 is the left column
// r2 is the bottom row, c2 is the right column
bool hasSquareOfZeroes2(vector<vector<InfoMatrixItem>> &infoMatrix, int r1,
                        int c1, int r2, int c2,
                        unordered_map<string, bool> &cache)
{
    if (r1 >= r2 || c1 >= c2)
        return false;

    string key = to_string(r1) + '-' + to_string(c1) + '-' + to_string(r2) + '-' +
                 to_string(c2);
    if (cache.find(key) != cache.end())
        return cache[key];

    cache[key] =
        isSquareOfZeroes2(infoMatrix, r1, c1, r2, c2) ||
        hasSquareOfZeroes2(infoMatrix, r1 + 1, c1 + 1, r2 - 1, c2 - 1, cache) ||
        hasSquareOfZeroes2(infoMatrix, r1, c1 + 1, r2 - 1, c2, cache) ||
        hasSquareOfZeroes2(infoMatrix, r1 + 1, c1, r2, c2 - 1, cache) ||
        hasSquareOfZeroes2(infoMatrix, r1 + 1, c1 + 1, r2, c2, cache) ||
        hasSquareOfZeroes2(infoMatrix, r1, c1, r2 - 1, c2 - 1, cache);

    return cache[key];
}

// r1 is the top row, c1 is the left column
// r2 is the bottom row, c2 is the right column
bool isSquareOfZeroes2(vector<vector<InfoMatrixItem>> &infoMatrix, int r1,
                       int c1, int r2, int c2)
{
    int squareLength = c2 - c1 + 1;
    bool hasTopBorder = infoMatrix[r1][c1].numZeroesRight >= squareLength;
    bool hasLeftBorder = infoMatrix[r1][c1].numZeroesBelow >= squareLength;
    bool hasBottomBorder = infoMatrix[r2][c1].numZeroesRight >= squareLength;
    bool hasRightBorder = infoMatrix[r1][c2].numZeroesBelow >= squareLength;
    return hasTopBorder && hasLeftBorder && hasBottomBorder && hasRightBorder;
}

vector<vector<InfoMatrixItem>> preComputedNumOfZeroes(vector<vector<int>> matrix)
{
    vector<vector<InfoMatrixItem>> infoMatrix;
    const int matrixSize = matrix.size();
    for (int i = 0; i < matrixSize; i++)
    {
        vector<InfoMatrixItem> inner;
        const int subMatrixSize = matrix[i].size();
        for (int j = 0; j < subMatrixSize; j++)
        {
            int numZeroes = matrix[i][j] == 0 ? 1 : 0;
            inner.push_back(InfoMatrixItem{numZeroes, numZeroes});
        }
        infoMatrix.push_back(inner);
    }

    int lastIdx = matrixSize - 1;
    for (int row = lastIdx; row >= 0; row--)
    {
        for (int col = lastIdx; col >= 0; col--)
        {
            if (matrix[row][col] == 1)
                continue;
            if (row < lastIdx)
            {
                infoMatrix[row][col].numZeroesBelow +=
                    infoMatrix[row + 1][col].numZeroesBelow;
            }
            if (col < lastIdx)
            {
                infoMatrix[row][col].numZeroesRight +=
                    infoMatrix[row][col + 1].numZeroesRight;
            }
        }
    }

    return infoMatrix;
}

// O(n^3) time | O(n^2) space - where n is the height and width of the matrix
bool squareOfZeroes4(vector<vector<int>> matrix)
{
    vector<vector<InfoMatrixItem>> infoMatrix = preComputedNumOfZeroes(matrix);
    int n = matrix.size();
    for (int topRow = 0; topRow < n; topRow++)
    {
        for (int leftCol = 0; leftCol < n; leftCol++)
        {
            int squareLength = 2;
            while (squareLength <= n - leftCol && squareLength <= n - topRow)
            {
                int bottomRow = topRow + squareLength - 1;
                int rightCol = leftCol + squareLength - 1;
                if (isSquareOfZeroes2(infoMatrix, topRow, leftCol, bottomRow, rightCol))
                    return true;
                squareLength++;
            }
        }
    }
    return false;
}

int main()
{
    vector<vector<int>> source = {{1, 1, 1, 0, 1, 0},
                                  {0, 0, 0, 0, 0, 1},
                                  {0, 1, 1, 1, 0, 1},
                                  {0, 0, 0, 1, 0, 1},
                                  {0, 1, 1, 1, 0, 1},
                                  {0, 0, 0, 0, 0, 1}};
    cout << squareOfZeroes1(source) << endl;
    cout << squareOfZeroes2(source) << endl;
    cout << squareOfZeroes3(source) << endl;
    cout << squareOfZeroes4(source) << endl;
    return 0;
}
```

## 遍历图的路线数

现有一个矩阵图，指定宽高，定义两个顶点分别放在矩阵的左上角和右下角，左上角为起点，右下角为终点，找出从起点到终点的所有路径数量

```
---------------------------------
| start |       |       |       |
---------------------------------
|       |       |       |       |
---------------------------------
|       |       |       |  end  |
---------------------------------
```

```python
# O(2 ^ (n + m)) time | O(n + m) space
# where n is the width of the graph and m is the height
def number_of_ways_to_traverse_graph1(width, height):
    if width == 1 or height == 1:
        return 1

    width_number = number_of_ways_to_traverse_graph1(width - 1, height)
    height_number = number_of_ways_to_traverse_graph1(width, height - 1)
    return width_number + height_number


# O(n * m) time | O(n * m) space
# where n is the width of the graph and m is the height
def number_of_ways_to_traverse_graph2(width, height):
    number_of_ways = [[0 for _ in range(width + 1)] for _ in range(height + 1)]

    for width_idx in range(1, width + 1):
        for height_idx in range(1, height + 1):
            if width_idx == 1 or height_idx == 1:
                number_of_ways[height_idx][width_idx] = 1
            else:
                ways_left = number_of_ways[height_idx][width_idx - 1]
                ways_up = number_of_ways[height_idx - 1][width_idx]
                number_of_ways[height_idx][width_idx] = ways_left + ways_up

    return number_of_ways[height][width]


# O(n + m) time | O(1) space
# where n is the width of the graph and m is the height
def number_of_ways_to_traverse_graph3(width, height):
    x_distance_to_corner = width - 1
    y_distance_to_corner = height - 1

    # The number of permutations of right and down movements
    # is the number of ways to reach the bottom right corner，
    numerator = factorial(x_distance_to_corner + y_distance_to_corner)
    denominator = factorial(x_distance_to_corner) * factorial(y_distance_to_corner)
    return numerator // denominator


def factorial(num):
    result = 1
    for n in range(2, num + 1):
        result *= n
    return result


if __name__ == '__main__':
    print(number_of_ways_to_traverse_graph1(4, 3))
    print(number_of_ways_to_traverse_graph2(4, 3))
    print(number_of_ways_to_traverse_graph3(4, 3))
```

```cpp
#include <iostream>
#include <vector>

using namespace std;

int factorial(int num);

// O(2 ^ (n + m)) time | O(n + m) space
// where n is the width of the graph and m is the height
int numberOfWaysToTraverseGraph1(int width, int height)
{
    if (width == 1 || height == 1)
        return 1;
    return numberOfWaysToTraverseGraph1(width - 1, height) +
           numberOfWaysToTraverseGraph1(width, height - 1);
}

// O(n * m) time | O(n * m) space
int numberOfWaysToTraverseGraph2(int width, int height)
{
    vector<vector<int>> numberOfWays;
    for (int i = 0; i < height + 1; i++)
    {
        numberOfWays.push_back(vector<int>{});
        for (int j = 0; j < width + 1; j++)
        {
            numberOfWays[i].push_back(0);
        }
    }
    for (int widthIdx = 1; widthIdx < width + 1; widthIdx++)
    {
        for (int heightIdx = 1; heightIdx < height + 1; heightIdx++)
        {
            if (widthIdx == 1 || heightIdx == 1)
            {
                numberOfWays[heightIdx][widthIdx] = 1;
            }
            else
            {
                int waysLeft = numberOfWays[heightIdx][widthIdx - 1];
                int waysUp = numberOfWays[heightIdx - 1][widthIdx];
                numberOfWays[heightIdx][widthIdx] = waysLeft + waysUp;
            }
        }
    }
    return numberOfWays[height][width];
}

// O(n + m) time | O(1) space
int numberOfWaysToTraverseGraph3(int width, int height)
{
    int xDistanceToCorner = width - 1;
    int yDistanceToCorner = height - 1;

    // The number of permutations of right and down movements
    // is the number of ways to reach the bottom right corner.
    int numerator = factorial(xDistanceToCorner + yDistanceToCorner);
    int denominator = factorial(xDistanceToCorner) * factorial(yDistanceToCorner);
    return int(numerator / denominator);
}

int factorial(int num)
{
    int result = 1;
    for (int n = 2; n < num + 1; n++)
    {
        result *= n;
    }
    return result;
}

int main()
{
    cout << numberOfWaysToTraverseGraph1(4, 3) << endl;
    cout << numberOfWaysToTraverseGraph2(4, 3) << endl;
    cout << numberOfWaysToTraverseGraph3(4, 3) << endl;
    return 0;
}
```

## 最大和子矩阵

给出一个二维数组表示的矩阵，给定一个小于矩阵大小的尺寸值（如给定的矩阵大小为`10 x 10`，则给定尺寸为`3`表示找出`3 x 3`的子矩阵），
找出在矩阵内所有符合尺寸的子矩阵的最大累加和（子矩阵的每个元素数值想加得出）

```python
# O(w * h) time | O(w * h) space
# where w is the width of the matrix and h is the height
def maximum_sum_sub_matrix(matrix, size):
    # 创建一个累加和缓存矩阵
    sums = create_sum_matrix(matrix)
    # 最大值设置为无穷小
    max_sub_matrix_sum = float('-inf')
    # 第一行第一列不需要处理
    for row in range(size - 1, len(matrix)):
        for col in range(size - 1, len(matrix[row])):
            # 先获得当前单元格的累加和
            total = sums[row][col]
            # 判断是否靠近左侧边界
            touches_top_border = row - size < 0
            # 不靠近左侧边界还需要减去左侧多累加的数值
            if not touches_top_border:
                total -= sums[row - size][col]
            # 判断是否靠近上边界
            touches_left_border = col - size < 0
            # 不靠近上边界还需要减去上面多累加的数值
            if not touches_left_border:
                total -= sums[row][col - size]
            # 判断是否靠近上面或者左侧的边界
            touches_top_or_left_border = touches_top_border or touches_left_border
            # 如果存在未靠近的边界的情况, 需要把行与列交界点的数值再加回来一次
            # 上面的两个判断逻辑中可能多减去了一次
            if not touches_top_or_left_border:
                total += sums[row - size][col - size]
            # 判断子矩阵的最大累加和
            max_sub_matrix_sum = max(max_sub_matrix_sum, total)

    return max_sub_matrix_sum


def create_sum_matrix(matrix):
    sums = [[0 for _ in range(len(matrix[row]))] for row in range(len(matrix))]
    # 第一行第一列默认取矩阵的数值, 因为从左或从上都没有可累加的数值
    sums[0][0] = matrix[0][0]

    # 填充第一行的每一列, 第一行的每一列都会累加前一列的数值
    # 这样第一行的每一列都会包含有它前面所有列的累加和
    for idx in range(1, len(matrix[0])):
        sums[0][idx] = sums[0][idx - 1] + matrix[0][idx]

    # 填充第一列的每一行, 第一列的每一行都会累加前一行的数值
    # 这样第一列的每一行都会包含有它前面所有行的累加和
    for idx in range(1, len(matrix)):
        sums[idx][0] = sums[idx - 1][0] + matrix[idx][0]

    # 填充第一行第一列之外的其它单元格
    for row in range(1, len(matrix)):
        for col in range(1, len(matrix[row])):
            # 取前一行同一列/同一行前一列/矩阵的三个数值想加
            # 然后减去前一行前一列的数值
            sums[row][col] = sums[row - 1][col] + sums[row][col - 1] - sums[row - 1][col - 1] + matrix[row][col]

    return sums


if __name__ == '__main__':
    source = [[-1, -2, -3, -4, -5],
              [1, 1, 1, 1, 1],
              [-1, -10, -10, -4, -5],
              [5, 5, 5, 5, 5],
              [-5, -4, -3, -10, -1]]
    print(maximum_sum_sub_matrix(source, 2))
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <limits>
using namespace std;

vector<vector<int>> createSumMatrix(vector<vector<int>> matrix);

// O(w * h) time | O(w * h) space - where w is
// the width of the matrix and h is the height
int maximumSumSubmatrix(vector<vector<int>> matrix, int size)
{
    vector<vector<int>> sums = createSumMatrix(matrix);
    int maxSubMatrixSum = numeric_limits<int>::min();
    const int matrixSize = matrix.size();
    for (int row = size - 1; row < matrixSize; row++)
    {
        const int matrixRowSize = matrix[row].size();
        for (int col = size - 1; col < matrixRowSize; col++)
        {
            int total = sums[row][col];

            int touchesTopBorder = row - size < 0;
            if (!touchesTopBorder)
                total -= sums[row - size][col];

            int touchesLeftBorder = col - size < 0;
            if (!touchesLeftBorder)
                total -= sums[row][col - size];

            int touchesTopOrLeftBorder = touchesTopBorder || touchesLeftBorder;
            if (!touchesTopOrLeftBorder)
                total += sums[row - size][col - size];

            maxSubMatrixSum = max(maxSubMatrixSum, total);
        }
    }

    return maxSubMatrixSum;
}

vector<vector<int>> createSumMatrix(vector<vector<int>> matrix)
{
    vector<vector<int>> sums;
    const int matrixSize = matrix.size();
    for (int row = 0; row < matrixSize; row++)
    {
        sums.push_back({});
        const int matrixRowSize = matrix[row].size();
        for (int col = 0; col < matrixRowSize; col++)
        {
            sums[row].push_back(0);
        }
    }
    sums[0][0] = matrix[0][0];

    // Fill the first row.
    const int matrixRowSize = matrix[0].size();
    for (int idx = 1; idx < matrixRowSize; idx++)
    {
        sums[0][idx] = sums[0][idx - 1] + matrix[0][idx];
    }

    // Fill the first column.
    for (int idx = 1; idx < matrixSize; idx++)
    {
        sums[idx][0] = sums[idx - 1][0] + matrix[idx][0];
    }

    // Fill the rest of the matrix.
    for (int row = 1; row < matrixSize; row++)
    {
        const int matrixRowSize = matrix[row].size();
        for (int col = 1; col < matrixRowSize; col++)
        {
            sums[row][col] = sums[row - 1][col] + sums[row][col - 1] -
                             sums[row - 1][col - 1] + matrix[row][col];
        }
    }

    return sums;
}

int main()
{
    vector<vector<int>> source = {
        {-1, -2, -3, -4, -5},
        {1, 1, 1, 1, 1},
        {-1, -10, -10, -4, -5},
        {5, 5, 5, 5, 5},
        {-5, -4, -3, -10, -1}};
    cout << maximumSumSubmatrix(source, 2);
    return 0;
}
```

## 求表达式最大值

给定一个整数数组，找出四个不同索引处的数值以表达式`A - B + C - D`求出一个最终值，要求四个元素的索引位置满足`A < B < C < D`，
找出最大的最终值，如`[3, 6, 1, -3, 2, 7]`中可以得到最大值的表达式为` 6 - (-3) + 2 - 7 = 4`

```python
# O(n^4) time | O(1) space - where n is the length of the array
def maximize_expression1(array):
    if len(array) < 4:
        return 0

    maximum_value_found = float("-inf")

    for a in range(len(array)):
        a_value = array[a]
        for b in range(a + 1, len(array)):
            b_value = array[b]
            for c in range(b + 1, len(array)):
                c_value = array[c]
                for d in range(c + 1, len(array)):
                    d_value = array[d]
                    expression_value = evaluate_expression(a_value, b_value, c_value, d_value)
                    maximum_value_found = max(expression_value, maximum_value_found)

    return maximum_value_found


def evaluate_expression(a, b, c, d):
    return a - b + c - d


# O(n) time | O(n) space - where n is the length of the array
def maximize_expression2(array):
    length = len(array)
    # 小于四个元素无法满足表达式 A - B + C - D
    if length < 4:
        return 0
    # 创建两个缓存数组, 反复使用
    maximum_temp_one = [float('-inf') for _ in array]
    maximum_temp_two = [float('-inf') for _ in array]
    # 因为每一步计算都是取最大值, 其实可以分步骤计算
    # 1. 找出 A 的最大值, 从第二个元素开始
    maximum_temp_one[0] = array[0]
    for idx in range(1, length):
        # 循环判断保留最大值
        maximum_temp_one[idx] = max(maximum_temp_one[idx - 1], array[idx])
    # 2. 根据步骤 1 的缓存, 找出 A - B 的最大值, 从第二个元素开始
    for idx in range(1, length):
        # 循环判断保留最大值
        maximum_temp_two[idx] = max(maximum_temp_two[idx - 1], maximum_temp_one[idx - 1] - array[idx])
    # 3. 根据步骤 2 的缓存, 找出 A - B + C 的最大值, 从第三个元素开始
    maximum_temp_one[1] = float('-inf')  # 设置前一个数组为无穷小, 便于下面的最大值判断
    for idx in range(2, length):
        maximum_temp_one[idx] = max(maximum_temp_one[idx - 1], maximum_temp_two[idx - 1] + array[idx])
    maximum_temp_two[2] = float('-inf')  # 设置前一个数组为无穷小, 便于下面的最大值判断
    # 4. 根据步骤 3 的缓存, 找出 A - B + C - D 的最大值, 从第四个元素开始
    for idx in range(3, length):
        maximum_temp_two[idx] = max(maximum_temp_two[idx - 1], maximum_temp_one[idx - 1] - array[idx])
    return maximum_temp_two[-1]


if __name__ == '__main__':
    print(maximize_expression1([3, 6, 1, -3, 2, 7]))
    print(maximize_expression2([3, 6, 1, -3, 2, 7]))
```

```cpp
#include <iostream>
#include <vector>
#include <limits>
#include <algorithm>
using namespace std;

int evaluateExpression(int a, int b, int c, int d);

// O(n^4) time | O(1) space - where n is the length of the array
int maximizeExpression1(vector<int> array)
{
    const int arraySize = array.size();
    if (arraySize < 4)
    {
        return 0;
    }

    int maximumValueFound = numeric_limits<int>::min();

    for (int a = 0; a < arraySize; a++)
    {
        int aValue = array[a];
        for (int b = a + 1; b < arraySize; b++)
        {
            int bValue = array[b];
            for (int c = b + 1; c < arraySize; c++)
            {
                int cValue = array[c];
                for (int d = c + 1; d < arraySize; d++)
                {
                    int dValue = array[d];
                    int expressionValue =
                        evaluateExpression(aValue, bValue, cValue, dValue);
                    maximumValueFound = max(expressionValue, maximumValueFound);
                }
            }
        }
    }

    return maximumValueFound;
}

int evaluateExpression(int a, int b, int c, int d) { return a - b + c - d; }

// O(n) time | O(n) space - where n is the length of the array
int maximizeExpression2(vector<int> array)
{
    const int arraySize = array.size();
    if (arraySize < 4)
    {
        return 0;
    }
    vector<int> maxOfA = {array[0]};
    vector<int> maxOfAMinusB = {numeric_limits<int>::min()};
    vector<int> maxOfAMinusBPlusC(2, numeric_limits<int>::min());
    vector<int> maxOfAMinusBPlusCMinusD(3, numeric_limits<int>::min());

    for (int idx = 1; idx < arraySize; idx++)
    {
        int currentMax = max(maxOfA[idx - 1], array[idx]);
        maxOfA.push_back(currentMax);
    }

    for (int idx = 1; idx < arraySize; idx++)
    {
        int currentMax = max(maxOfAMinusB[idx - 1], maxOfA[idx - 1] - array[idx]);
        maxOfAMinusB.push_back(currentMax);
    }

    for (int idx = 2; idx < arraySize; idx++)
    {
        int currentMax =
            max(maxOfAMinusBPlusC[idx - 1], maxOfAMinusB[idx - 1] + array[idx]);
        maxOfAMinusBPlusC.push_back(currentMax);
    }

    for (int idx = 3; idx < arraySize; idx++)
    {
        int currentMax = max(maxOfAMinusBPlusCMinusD[idx - 1],
                             maxOfAMinusBPlusC[idx - 1] - array[idx]);
        maxOfAMinusBPlusCMinusD.push_back(currentMax);
    }

    return maxOfAMinusBPlusCMinusD[maxOfAMinusBPlusCMinusD.size() - 1];
}

int main()
{
    vector<int> source = {3, 6, 1, -3, 2, 7};
    cout << maximizeExpression1(source) << endl;
    cout << maximizeExpression2(source) << endl;
    return 0;
}
```

## 全局匹配

在操作系统的命令行中，可以使用通配符来匹配文件或文件夹路径，最简单的是`*`和`?`这两个符号，`*`表示匹配任意数量的任意字符，而`?`表示匹配任意单个字符

```python
# O(n * m) time | O(n * m) space - where n is the length
# of the file_name and m is the length of the pattern.
def glob_matching(file_name, pattern):
    match_table = initialize_match_table(file_name, pattern)
    for i in range(1, len(file_name) + 1):
        for j in range(1, len(pattern) + 1):
            # 星号与任意数量字符匹配
            if pattern[j - 1] == "*":
                # 取前一次循环的匹配结果或者前一个字符的匹配结果
                match_table[i][j] = match_table[i][j - 1] or match_table[i - 1][j]
            # 问号与任意单个字符匹配, 如果不是问号则直接判断模板与字符串的字符是否相等
            elif pattern[j - 1] == "?" or pattern[j - 1] == file_name[i - 1]:
                match_table[i][j] = match_table[i - 1][j - 1]
    return match_table[-1][-1]


def initialize_match_table(file_name, pattern):
    match_table = [[False for _ in range(len(pattern) + 1)] for _ in range(len(file_name) + 1)]

    match_table[0][0] = True
    for j in range(1, len(pattern) + 1):
        # 星号与任意数量字符匹配, 初始第一行如果遇到星号则表示匹配
        if pattern[j - 1] == "*":
            match_table[0][j] = match_table[0][j - 1]

    return match_table


if __name__ == '__main__':
    print(glob_matching('abcdef', 'a*d?f'))
```

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

vector<vector<bool>> initializeMatchTable(string fileName, string pattern);
// O(n * m) time | O(n * m) space - where n is the length
// of the fileName and m is the length of the pattern.
bool globMatching(string fileName, string pattern)
{
    auto matchTable = initializeMatchTable(fileName, pattern);
    const int fileNameLength = fileName.length();
    const int patternLength = pattern.length();
    for (auto i = 1; i <= fileNameLength; i++)
    {
        for (auto j = 1; j <= patternLength; j++)
        {
            if (pattern[j - 1] == '*')
            {
                matchTable[i][j] = matchTable[i][j - 1] || matchTable[i - 1][j];
            }
            else if (pattern[j - 1] == '?' || pattern[j - 1] == fileName[i - 1])
            {
                matchTable[i][j] = matchTable[i - 1][j - 1];
            }
        }
    }
    return matchTable[fileNameLength][patternLength];
}

vector<vector<bool>> initializeMatchTable(string fileName, string pattern)
{
    vector<vector<bool>> matchTable = {};
    const int fileNameLength = fileName.length();
    const int patternLength = pattern.length();
    for (auto i = 0; i <= fileNameLength; i++)
    {
        vector<bool> row = {};
        for (auto j = 0; j <= patternLength; j++)
        {
            row.push_back(false);
        }
        matchTable.push_back(row);
    }

    matchTable[0][0] = true;
    for (auto j = 1; j <= patternLength; j++)
    {
        if (pattern[j - 1] == '*')
            matchTable[0][j] = matchTable[0][j - 1];
    }

    return matchTable;
}

int main()
{
    cout << globMatching("abcdef", "*d?f");
    return 0;
}
```

## 最大子序列点积

给定两个表示向量的整数数组，找出使用当前两个数组可以计算出的最大点积（在数学中，两个向量的点积 a = [a1, a2, ..., an]
和 b = [b1, b2, ..., bn] 等于 a1 * b1 + a2 * b2 + ... + an * bn.）虽然输入数组可能具有不同的长度，但根据点积的定义，
产生最大点积的两个子序列的长度必须相等。数组的子序列是一组数字，它们在数组中不一定相邻，但与它们在数组中出现的顺序相同。

```python
# O(n * m) time | O(n * m) space - where n is the length of the
# first input array and m is the length of the second input array
def max_subsequence_dot_product(array_one, array_two):
    max_dot_products = initialize_dot_products(array_one, array_two)
    # 空元素的数组不需要计算, 所以从第二个位置开始
    for i in range(1, len(array_one) + 1):
        for j in range(1, len(array_two) + 1):
            # 计算出当前的点积
            current_product = array_one[i - 1] * array_two[j - 1]
            # 记录最大的点积
            max_dot_products[i][j] = max(
                current_product,
                # 前一次计算相对位置的前一个点积加上当前的点积
                max_dot_products[i - 1][j - 1] + current_product,
                # 前一次计算相对位置的前一个点积
                max_dot_products[i - 1][j - 1],
                # 前一次计算相同位置的点积
                max_dot_products[i - 1][j],
                # 当前计算前一个位置的点积
                max_dot_products[i][j - 1],
            )
    return max_dot_products[-1][-1]


def initialize_dot_products(array_one, array_two):
    # 创建一个二维数组, 用来记录每个元素相乘的点积, 多出来的一行和一列分别表示空的元素值, 每个值初始化为无穷小
    dot_products = [[float("-inf") for _ in range(len(array_two) + 1)] for _ in range(len(array_one) + 1)]
    return dot_products


if __name__ == '__main__':
    print(max_subsequence_dot_product([4, 7, 9, -6, 6], [5, 1, -1, -3, -2, -10]))
```

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

vector<vector<int>> initializeDotProducts(vector<int> arrayOne,
                                          vector<int> arrayTwo);
int maxOfList(vector<int> numbers);
int handleOverflow(int number, int toAdd);

// O(n * m) time | O(n * m) space - where n is the length of the
// first input array and m is the length of the second input array
int maxSubsequenceDotProduct(vector<int> arrayOne, vector<int> arrayTwo)
{
    vector<vector<int>> maxDotProducts = initializeDotProducts(arrayOne, arrayTwo);
    const int arrayOneSize = arrayOne.size();
    const int arrayTwoSize = arrayTwo.size();
    for (int i = 1; i <= arrayOneSize; i++)
    {
        for (int j = 1; j <= arrayTwoSize; j++)
        {
            int currentProduct = arrayOne[i - 1] * arrayTwo[j - 1];
            int plusCurrent = handleOverflow(maxDotProducts[i - 1][j - 1], currentProduct);
            maxDotProducts[i][j] = maxOfList(vector<int>{
                currentProduct,
                plusCurrent,
                maxDotProducts[i - 1][j - 1], maxDotProducts[i - 1][j],
                maxDotProducts[i][j - 1]});
        }
    }
    return maxDotProducts[arrayOneSize][arrayTwoSize];
}

vector<vector<int>> initializeDotProducts(vector<int> arrayOne,
                                          vector<int> arrayTwo)
{
    vector<vector<int>> dotProducts = {};
    const int arrayOneSize = arrayOne.size();
    const int arrayTwoSize = arrayTwo.size();
    for (int i = 0; i <= arrayOneSize; i++)
    {
        vector<int> row = {};
        for (int j = 0; j <= arrayTwoSize; j++)
        {
            row.push_back(INT_MIN);
        }
        dotProducts.push_back(row);
    }
    return dotProducts;
}

int maxOfList(vector<int> numbers)
{
    int currMax = numbers[0];
    for (int num : numbers)
    {
        if (num > currMax)
            currMax = num;
    }
    return currMax;
}

int handleOverflow(int number, int toAdd)
{
    if (toAdd > 0)
        return number + toAdd;

    bool willOverflow = toAdd < 0 && number == INT_MIN;
    if (willOverflow)
        return number;

    return number + toAdd;
}

int main()
{
    cout << maxSubsequenceDotProduct({4, 7, 9, -6, 6}, {5, 1, -1, -3, -2, -10});
    return 0;
}
```

Last Modified 2022-06-29

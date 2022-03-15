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

```c
#include <stdio.h>
#include <stdlib.h>

int max(int a, int b)
{
    if ( a > b ) return a;
    return b;
}

int min(int a, int b)
{
    if ( a < b ) return a;
    return b;
}

/* O(n) time | O(n) space */
int waterArea(int heights[], int length)
{
    int maxes[length] = {0};
    int leftMax=0, rigthMax=0, i, sum;
    for ( i=0; i<length; i++ )
    {
        maxes[i] = leftMax;
        leftMax = max(leftMax, heights[i]);
    }
    for ( i=length - 1; i>=0; i-- )
    {
        sum = heights[i];
        leftMax = min(rigthMax, maxes[i]);
        if ( sum < leftMax )
            maxes[i] = leftMax - sum;
        else
            maxes[i] = 0;
        rigthMax = max(rigthMax, sum);
    }
    for ( i=0,sum=0; i<length; i++ )
        sum += maxes[i];
    return sum;
}

int main()
{
    int heights[] = {0, 8, 0, 0, 5, 0, 0, 10, 0, 0, 1, 1, 0, 3};
    int length = sizeof(heights) / sizeof(heights[0]);
    printf("%d", waterArea(heights, length));
    return 0;
}
```

## 背包问题

给定一组物品，每种物品都有自己的重量和价格，在限定的总重量内，我们如何选择，才能使得物品的总价格最高

```python
# O(n * c) time | O(n * c) space
def knapsack_problem(items, capacity):
    knapsack_values = [[0 for _ in range(capacity + 1)] for _ in range(len(items) + 1)]
    for i in range(1, len(items) + 1):
        current_weight = items[i - 1][1]
        current_value = items[i - 1][0]
        for c in range(capacity + 1):
            if current_weight > c:
                knapsack_values[i][c] = knapsack_values[i - 1][c]
            else:
                knapsack_values[i][c] = max(knapsack_values[i - 1][c],
                                            knapsack_values[i - 1][c - current_weight] + current_value)
    return [knapsack_values[-1][-1], get_knapsack_items(knapsack_values, items)]


def get_knapsack_items(knapsack_values, items):
    sequence = list()
    i = len(knapsack_values) - 1
    c = len(knapsack_values[0]) - 1
    while i > 0:
        if knapsack_values[i][c] == knapsack_values[i - 1][c]:
            i -= 1
        else:
            sequence.append(i - 1)
            c -= items[i - 1][1]
            i -= 1
        if c == 0:
            break
    return list(reversed(sequence))


if __name__ == '__main__':
    print(knapsack_problem([(60, 10,), (100, 20,), (120, 30,)], 50))
```

## 磁盘堆叠

每个子阵列都包含三个整数并表示磁盘。这些整数分别表示每个磁盘的宽度，深度和高度。目标是堆叠磁盘并最大限度地提高堆栈的总高度。磁盘必须具有比其下方的任何其他磁盘更小的宽度，深度和高度。写一个函数，返回 nal 堆栈中的磁盘数组，从顶部磁盘开始，并以底部磁盘结尾。

```python
# O(n^2) time | O(n) space
def disk_problem(disks):
    disks.sort(key=lambda disk: disk[2])
    heights = [disk[2] for disk in disks]
    sequences = [-1 for _ in disks]
    max_height_idx = 0
    for i in range(1, len(disks)):
        current_disk = disks[i]
        for j in range(i):
            other_disk = disks[j]
            if are_valid_dimensions(other_disk, current_disk):
                if heights[i] <= current_disk[2] + heights[j]:
                    heights[i] = current_disk[2] + heights[j]
                    sequences[i] = j
        if heights[i] >= heights[max_height_idx]:
            max_height_idx = i
    return build_sequence(disks, sequences, max_height_idx)


def are_valid_dimensions(o, c):
    return o[0] < c[0] and o[1] < c[1] and o[2] < c[2]


def build_sequence(array, sequences, current_idx):
    sequence = list()
    while current_idx != -1:
        sequence.append(array[current_idx])
        current_idx = sequences[current_idx]
    return list(reversed(sequence))


if __name__ == '__main__':
    print(disk_problem([(2, 2, 1,), (2, 1, 2,), (2, 3, 4,), (3, 2, 3,), (2, 2, 8,), (4, 4, 5,), ]))
```

## 圆周率中的数字

```python
# O(n^3 + m) time | O(n + m) space
def numbers_in_pi1(pi, numbers):
    numbers_set = set(numbers)
    min_space = get_min_space(pi, numbers_set, dict(), 0)
    return -1 if min_space == float('inf') else min_space


# O(n^3 + m) time | O(n + m) space
def numbers_in_pi2(pi, numbers):
    numbers_set = set(numbers)
    cache = dict()
    for i in reversed(range(len(pi))):
        get_min_space(pi, numbers_set, cache, i)
    return -1 if cache[0] == float('inf') else cache[0]


def get_min_space(pi, numbers_set, cache, idx):
    if idx == len(pi):
        return -1
    if idx in cache:
        return cache[idx]
    min_space = float('inf')
    for i in range(idx, len(pi)):
        prefix = pi[idx: i + 1]
        if prefix in numbers_set:
            min_space_in_suffix = get_min_space(pi, numbers_set, cache, i + 1)
            min_space = min(min_space, min_space_in_suffix + 1)
    cache[idx] = min_space
    return cache[idx]


# O(n^2 + m) time | O(m) space
def numbers_in_pi3(pi, numbers):
    numbers_set = set(numbers)
    count = 0
    for i in range(len(pi) + 1):
        if pi[:i] in numbers_set:
            if helper(i, pi, numbers_set):
                count += 1
    return count


def helper(start, pi, numbers_set):
    i = start + 1
    while i <= len(pi):
        if pi[start:i] in numbers_set:
            start = i
        i += 1
    return start == i - 1


if __name__ == '__main__':
    print(numbers_in_pi1("3141592", ["3141", "5", "31", "2", "4159", "9", "42", "314"]))
    print(numbers_in_pi2("3141592", ["3141", "5", "31", "2", "4159", "9", "42", "314"]))
    print(numbers_in_pi3("3141592", ["3141", "5", "31", "2", "4159", "9", "42", "314"]))
```

## 第 K 次投资最大利润

```python
# O(nk) time | O(nk) space
def max_profit_with_k_transactions1(prices, k):
    if not len(prices):
        return 0
    profits = [[0 for _ in prices] for _ in range(k + 1)]
    for t in range(1, k + 1):
        max_thus_far = float('-inf')
        for d in range(1, len(prices)):
            max_thus_far = max(max_thus_far,
                               profits[t - 1][d - 1] - prices[d - 1])
            profits[t][d] = max(profits[t][d - 1], max_thus_far + prices[d])
    return profits[-1][-1]


# O(nk) time | O(n) space
def max_profit_with_k_transactions2(prices, k):
    if not len(prices):
        return 0
    even_profits = [0 for _ in prices]
    odd_profits = [0 for _ in prices]
    for t in range(1, k + 1):
        max_thus_far = float('-inf')
        if t & 1 == 1:
            current_profits = odd_profits
            previous_profits = even_profits
        else:
            current_profits = even_profits
            previous_profits = odd_profits
        for d in range(1, len(prices)):
            max_thus_far = max(max_thus_far,
                               previous_profits[d - 1] - prices[d - 1])
            current_profits[d] = max(current_profits[d - 1],
                                     max_thus_far + prices[d])
    return even_profits[-1] if k & 1 == 0 else odd_profits[-1]


if __name__ == '__main__':
    print(max_profit_with_k_transactions1([5, 11, 3, 50, 60, 90], 2))
    print(max_profit_with_k_transactions2([5, 11, 3, 50, 60, 90], 2))
```

## 回文字符串最小分割次数

```python
# O(n^3) time | O(n^2) space
def palindrome_partitioning_min_cuts1(string):
    palindromes = [[False for _ in string] for _ in string]
    for i in range(len(string)):
        for j in range(i, len(string)):
            palindromes[i][j] = is_palindrome(string[i:j + 1])
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


# O(n^2) time | O(n^2) space
def palindrome_partitioning_min_cuts2(string):
    palindromes = [[False for _ in string] for _ in string]
    for i in range(len(string)):
        palindromes[i][i] = True
    for length in range(2, len(string) + 1):
        for i in range(0, len(string) - length + 1):
            j = i + length - 1
            if length == 2:
                palindromes[i][j] = string[i] == string[j]
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


def is_palindrome(string):
    left_idx = 0
    right_idx = len(string) - 1
    while left_idx < right_idx:
        if string[left_idx] != string[right_idx]:
            return False
        left_idx += 1
        right_idx -= 1
    return True


if __name__ == '__main__':
    print(palindrome_partitioning_min_cuts1('noonabbad'))
    print(palindrome_partitioning_min_cuts2('noonabbad'))
```

```c
#include <stdio.h>
#include <stdlib.h>

int getStringLength(char* string)
{
    char *p = string;
    int length = 0;
    while ( *p++ ) length++;
    return length;
}

int isPalindorme(char string[], int i, int j)
{
    for ( ; i < j; i++, j--)
        if ( string[i] != string[j] )
            return 0;
    return 1;
}

/* O(n^3) time | O(1) space */
int palindromePartitioningMinCuts(char string[])
{
    int length = getStringLength(string);
    int i = 0, j, countOfCuts = 0, leftIdx = 0, rightIdx = 0;
    for ( ; i<length; i++)
    {
        for ( j=i+1; j<length; j++)
        {
            if ( isPalindorme(string, i, j) )
            {
                if ( i >= leftIdx && j <= rightIdx )
                {
                    continue;
                }
                leftIdx = i;
                rightIdx = j;
                if ( i > leftIdx && i < rightIdx && rightIdx - leftIdx <= j - i )
                {
                    continue;
                }
                countOfCuts++;
            }
        }
    }
    return countOfCuts;
}

int main()
{
    char string[] = "noonaddab";
    printf("%d", palindromePartitioningMinCuts(string));
    return 0;
}
```

## 最长递增子序列

```python
# O(n^2) time | O(n) space
def longest_increasing_subsequence1(array):
    sequences = [None for _ in array]
    lengths = [1 for _ in array]
    max_length_idx = 0
    for i in range(len(array)):
        current_num = array[i]
        for j in range(0, i):
            other_num = array[j]
            if other_num < current_num and lengths[j] + 1 >= lengths[i]:
                lengths[i] = lengths[j] + 1
                sequences[i] = j
        if lengths[i] >= lengths[max_length_idx]:
            max_length_idx = i
    return build_sequence(array, sequences, max_length_idx)


# O(n * log(n)) time | O(n) space
def longest_increasing_subsequence2(array):
    sequences = [None for _ in array]
    indices = [None for _ in range(len(array) + 1)]
    length = 0
    for i, num in enumerate(array):
        new_length = binary_search(1, length, indices, array, num)
        sequences[i] = indices[new_length - 1]
        indices[new_length] = i
        length = max(length, new_length)
    return build_sequence(array, sequences, indices[length])


def binary_search(start_idx, end_idx, indices, array, num):
    if start_idx > end_idx:
        return start_idx
    middle_idx = (start_idx + end_idx) // 2
    if array[indices[middle_idx]] < num:
        start_idx = middle_idx + 1
    else:
        end_idx = middle_idx - 1
    return binary_search(start_idx, end_idx, indices, array, num)


def build_sequence(array, sequences, current_idx):
    sequence = list()
    while current_idx is not None:
        sequence.append(array[current_idx])
        current_idx = sequences[current_idx]
    return list(reversed(sequence))


if __name__ == '__main__':
    print(longest_increasing_subsequence1([5, 7, -24, 12, 10, 2, 3, 12, 5, 6, 35]))
    print(longest_increasing_subsequence2([5, 7, -24, 12, 10, 2, 3, 12, 5, 6, 35]))
```

## 最长字符串链

```python
def get_smaller_string(string, index):
    return string[0:index] + string[index + 1:]


def try_update_longest_string_chain(current_string, smaller_string, string_chain):
    smaller_string_chain_length = string_chain[smaller_string]["maxChainLength"]
    current_string_chain_length = string_chain[current_string]["maxChainLength"]
    if smaller_string_chain_length + 1 > current_string_chain_length:
        string_chain[current_string]["maxChainLength"] = smaller_string_chain_length + 1
        string_chain[current_string]["nextString"] = smaller_string


def find_longest_string_chain(string, string_chain):
    for i in range(len(string)):
        smaller_string = get_smaller_string(string, i)
        if smaller_string not in string_chain:
            continue
        try_update_longest_string_chain(string, smaller_string, string_chain)


def build_longest_string_chain(strings, string_chain):
    max_chain_length = 0
    chain_starting_string = ""
    for string in strings:
        if string_chain[string]["maxChainLength"] > max_chain_length:
            max_chain_length = string_chain[string]["maxChainLength"]
            chain_starting_string = string

    our_longest_string_chain = list()
    current_string = chain_starting_string
    while current_string != "":
        our_longest_string_chain.append(current_string)
        current_string = string_chain[current_string]["nextString"]

    return list() if len(our_longest_string_chain) == 1 else our_longest_string_chain


# O(n * m^2 + n * log(n)) time | O(nm) space
# n is the number of elements in strings
# m is the element length of strings
def longest_string_chain(strings):
    string_chain = {}
    for string in strings:
        string_chain[string] = {"nextString": "", "maxChainLength": 1}

    sorted_string = sorted(strings, key=len)
    for string in sorted_string:
        find_longest_string_chain(string, string_chain)

    return build_longest_string_chain(strings, string_chain)


if __name__ == '__main__':
    strings = ["abde", "abc", "abd", "abcde", "ade", "ae", "labde", "abcdeg"]
    print(longest_string_chain(strings))
```

## 方形的零

```python
def is_square_of_zeroes1(matrix, r1, c1, r2, c2):
    for row in range(r1, r2 + 1):
        if matrix[row][c1] != 0 or matrix[row][c2] != 0:
            return False
    for col in range(c1, c2 + 1):
        if matrix[r1][col] != 0 or matrix[r2][col] != 0:
            return False
    return True


def is_square_of_zeroes2(info_matrix, r1, c1, r2, c2):
    square_length = c2 - c1 + 1
    has_top_border = info_matrix[r1][c1]['numZeroesRight'] >= square_length
    has_left_border = info_matrix[r1][c1]['numZeroesBelow'] >= square_length
    has_bottom_border = info_matrix[r2][c1]['numZeroesRight'] >= square_length
    has_right_border = info_matrix[r1][c2]['numZeroesBelow'] >= square_length
    return all((has_top_border, has_left_border, has_bottom_border, has_right_border,))


def has_square_of_zeroes1(matrix, r1, c1, r2, c2, cache):
    if r1 >= r2 or c1 >= c2:
        return False
    key = f'{r1}-{c1}-{r2}-{c2}'
    if key in cache:
        return cache[key]
    return any(
        (is_square_of_zeroes1(matrix, r1, c1, r2, c2),
         has_square_of_zeroes1(matrix, r1 + 1, c1 + 1, r2 - 1, c2 - 1, cache),
         has_square_of_zeroes1(matrix, r1, c1 + 1, r2 - 1, c2, cache),
         has_square_of_zeroes1(matrix, r1 + 1, c1, r2, c2 - 1, cache),
         has_square_of_zeroes1(matrix, r1 + 1, c1 + 1, r2, c2, cache),
         has_square_of_zeroes1(matrix, r1, c1, r2 - 1, c2 - 1, cache),)
    )


def has_square_of_zeroes2(info_matrix, r1, c1, r2, c2, cache):
    if r1 >= r2 or c1 >= c2:
        return False
    key = f'{r1}-{c1}-{r2}-{c2}'
    if key in cache:
        return cache[key]
    return any(
        (is_square_of_zeroes2(info_matrix, r1, c1, r2, c2),
         has_square_of_zeroes2(info_matrix, r1 + 1, c1 + 1, r2 - 1, c2 - 1, cache),
         has_square_of_zeroes2(info_matrix, r1, c1 + 1, r2 - 1, c2, cache),
         has_square_of_zeroes2(info_matrix, r1 + 1, c1, r2, c2 - 1, cache),
         has_square_of_zeroes2(info_matrix, r1 + 1, c1 + 1, r2, c2, cache),
         has_square_of_zeroes2(info_matrix, r1, c1, r2 - 1, c2 - 1, cache),)
    )


# O(n^4) time | O(n^3) space
def square_of_zeroes1(matrix):
    last_idx = len(matrix) - 1
    return has_square_of_zeroes1(matrix, 0, 0, last_idx, last_idx, dict())


# O(n^4) time | O(1) space
def square_of_zeroes2(matrix):
    n = len(matrix)
    for top_row in range(n):
        for left_col in range(n):
            square_length = 2
            while square_length <= n - left_col and square_length <= n - top_row:
                bottom_row = top_row + square_length - 1
                right_col = left_col + square_length - 1
                if is_square_of_zeroes1(matrix, top_row, left_col, bottom_row, right_col):
                    return True
                square_length += 1
    return False


def pre_compute_num_of_zeroes(matrix):
    info_matrix = [[dict() for _ in row] for row in matrix]
    n = len(matrix)
    for row in range(n):
        for col in range(n):
            num_zeroes = 1 if matrix[row][col] == 0 else 0
            info_matrix[row][col] = {
                "numZeroesBelow": num_zeroes,
                "numZeroesRight": num_zeroes
            }

    last_idx = len(matrix) - 1
    for row in reversed(range(n)):
        for col in reversed(range(n)):
            if matrix[row][col] == 1:
                continue
            if row < last_idx:
                info_matrix[row][col]['numZeroesBelow'] += info_matrix[row + 1][col]['numZeroesBelow']
            if col < last_idx:
                info_matrix[row][col]['numZeroesRight'] += info_matrix[row][col + 1]['numZeroesRight']

    return info_matrix


# O(n^3) time | O(n^3) space
def square_of_zeroes3(matrix):
    info_matrix = pre_compute_num_of_zeroes(matrix)
    last_idx = len(matrix) - 1
    return has_square_of_zeroes2(info_matrix, 0, 0, last_idx, last_idx, dict())


# O(n^3) time | O(n^2) space
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
    matrix1 = [
        (1, 1, 1, 0, 1, 0,),
        (0, 0, 0, 0, 0, 1,),
        (0, 1, 1, 1, 0, 1,),
        (0, 0, 0, 1, 0, 1,),
        (0, 1, 1, 1, 0, 1,),
        (0, 0, 0, 0, 0, 1,),
    ]
    # (1, 0) (1, 4) (5, 0) (5, 4)
    print(square_of_zeroes1(matrix1))
    print(square_of_zeroes2(matrix1))
    print(square_of_zeroes3(matrix1))
    print(square_of_zeroes4(matrix1))
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

Last Modified 2022-03-15

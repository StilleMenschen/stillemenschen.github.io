# 动态规划

## 最大不相邻元素总和

编写一个函数，该函数接受一个正整数数组，并返回数组中不相邻元素的最大总和。如果输入数组为空，则函数应返回 0

```c
#include <stdio.h>

int max(int a, int b)
{
    if ( a > b ) return a;
    return b;
}

/* O(n) time | O(1) space */
int* maxSubsetSumNoAdjacent(int array[], int length)
{
    if ( length <= 3 ) return array;
    int current = 2, first = 0, second = 1;
    while ( current < length )
    {
        array[current] = max(array[second], array[current] + array[first]);
        first = second;
        second = current;
        current++;
    }
    return array;
}

int main(void)
{
    int numbers[] = {7, 10, 12, 7, 9, 14};
    int length = sizeof(numbers) / sizeof(numbers[0]);
    maxSubsetSumNoAdjacent(numbers, length);
    int i = 0;
    while ( i < length ) printf("%d ", numbers[i++]);
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

```c
#include <stdio.h>
#include <stdlib.h>

/* O(n * d) time | O(n) space */
int numberOfWaysToMakeChange(int n, int denoms[], int denomsLength)
{
    if ( n <=0 ) return 0;
    int ways[n + 1] = {0};
    int i, amount;
    ways[0] = 1;
    for ( i=0; i<denomsLength; i++)
        for ( amount=1; amount<=n; amount++)
            if ( denoms[i] <= amount )
                ways[amount] += ways[amount - denoms[i]];
    return ways[n];
}

int main(void)
{
    int denoms[] = {3, 5, 10};
    int length = sizeof(denoms) / sizeof(denoms[0]);
    printf("%d", numberOfWaysToMakeChange(15, denoms, length));
    return 0;
}
```

## 组成金额的最小组合

给定一个金额，如 6，给定几种面额的硬币 如 1 2 4，硬币的数量没有限制，找出这几种硬币组合成金额的最小组合数，即 `F(6) = 2 + 4`，最少为 `2` 个数组合

```c
#include <stdio.h>
#include <stdlib.h>
#define MAX_INTEGER 2147483646

int min(int a, int b)
{
    if ( a < b ) return a;
    return b;
}

/* O(n * d) time | O(n) space */
int minNumberOfCoinsForChange(int n, int denoms[], int denomsLength)
{
    if ( n <=0 ) return 0;
    int numOfCoins[n + 1] = {0};
    int i, amount;
    for ( i=1; i<=n; i++)
        numOfCoins[i] = MAX_INTEGER;
    for ( i=0; i<denomsLength; i++)
        for ( amount=0; amount<=n; amount++)
            if ( denoms[i] <= amount )
                numOfCoins[amount] = min( numOfCoins[amount], 1 + numOfCoins[amount - denoms[i]] );
    if ( numOfCoins[n] != MAX_INTEGER )
        return numOfCoins[n];
    return -1;
}

int main(void)
{
    int denoms[] = {2, 4, 9, 25, 11};
    int length = sizeof(denoms) / sizeof(denoms[0]);
    printf("%d", minNumberOfCoinsForChange(7, denoms, length));
    return 0;
}
```

## 莱文斯坦距离

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

int min(int a, int b, int c)
{
    if ( b < a ) a = b;
    if ( c < a ) a = c;
    return a;
}

/* O(n * m) time | O(n * m) space */
int levenshteinDistance1(char str1[], char str2[])
{
    int str1Length = getStringLength(str1) + 1;
    int str2Length = getStringLength(str2) + 1;
    int edits[str1Length][str2Length], row, col;
    for ( col=0; col<str2Length; col++)
        edits[0][col] = col;
    for ( row=0; row<str1Length; row++)
        edits[row][0] = row;
    for ( row=1; row<str1Length; row++)
    {
        for ( col=1; col<str2Length; col++)
        {
            if ( str1[row - 1] == str2[col - 1] )
            {
                edits[row][col] = edits[row - 1][col - 1];
            }
            else
            {
                edits[row][col] = 1 + min(edits[row][col - 1], edits[row - 1][col], edits[row - 1][col - 1]);
            }
        }
    }
    return edits[str1Length - 1][str2Length - 1];
}

/* O(n * m) time | O(min(n,m)) space */
int levenshteinDistance2(char str1[], char str2[])
{
    int str1Length = getStringLength(str1) + 1;
    int str2Length = getStringLength(str2) + 1;
    int smallLength = str1Length, bigLength = str2Length;
    char *small, *big;
    int i, j;
    int evenEdits[smallLength], oddEdits[smallLength];
    int *currentEdits, *previousEdits;
    for ( i=0; i<smallLength; i++)
    {
        evenEdits[i] = i;
        oddEdits[i] = -1;
    }
    if ( str1Length < str2Length )
    {
        small = str1;
        big = str2;
    }
    else
    {
        big = str1;
        small = str2;
        smallLength = str2Length;
        bigLength = str1Length;
    }
    for ( i=1; i<bigLength; i++)
    {
        if ( i & 1 == 1 )
        {
            currentEdits = oddEdits;
            previousEdits = evenEdits;
        }
        else
        {
            currentEdits = evenEdits;
            previousEdits = oddEdits;
        }
        currentEdits[0] = i;
        for ( j=0; j<smallLength; j++)
        {
            if ( big[i - 1] == small[j - 1] )
                currentEdits[j] = previousEdits[j - 1];
            else
                currentEdits[j] = 1 + min( previousEdits[j - 1], previousEdits[j], currentEdits[j -1] );
        }
    }
    if ( bigLength & 1 == 0)
        return evenEdits[smallLength - 1];
    return oddEdits[smallLength - 1];
}

int main()
{
    char str1[] = "abc5";
    char str2[] = "yabcd";
    printf("%d ", levenshteinDistance1(str1, str2));
    printf("%d ", levenshteinDistance2(str1, str2));
    return 0;
}
```

## 最大递增子序列

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Result
{
    int sequenceLength;
    int sum;
    int *sequence;
} Result, *pResult;

pResult buildSequence(int array[], int sequence[], int maxSumIdx)
{
    pResult r = (Result*)malloc(sizeof(Result));
    r->sequenceLength = r->sum = 0;
    int i;
    for ( i=maxSumIdx; i>=0; )
    {
        r->sequenceLength++;
        i = sequence[i];
    }
    r->sequence = (int*)malloc(r->sequenceLength * sizeof(int));
    for ( i=r->sequenceLength-1; i>=0; i--)
    {
        r->sequence[i] = array[maxSumIdx];
        r->sum += array[maxSumIdx];
        maxSumIdx = sequence[maxSumIdx];
    }
    return r;
}

/* O(n^2) time | O(n) space */
pResult maxSumIncreasingSubsequence(int array[], int length)
{
    int sequence[length] = {0}, sums[length] = {0};
    int i, j, currentNum, otherNum, maxSumIdx = 0;
    for ( i=0; i<length; i++)
    {
        sequence[i] = -1;
        sums[i] = array[i];
    }
    for ( i=0; i<length; i++)
    {
        currentNum = array[i];
        for ( j=0; j<i; j++)
        {
            otherNum = array[j];
            if ( otherNum < currentNum && sums[j] + currentNum >= sums[i] )
            {
                sums[i] = sums[j] + currentNum;
                sequence[i] = j;
            }
        }
        if ( sums[i] >= sums[maxSumIdx] )
        {
            maxSumIdx = i;
        }
    }
    return buildSequence(array, sequence, maxSumIdx);
}

int main(void)
{
    int array[] = {8, 13, 2, 3, 15, 2, 6};
    int length = sizeof(array) / sizeof(array[0]);
    pResult result = maxSumIncreasingSubsequence(array, length);
    int i = 0;
    printf("%d, ", result->sum);
    while ( i < result->sequenceLength )
        printf("%d ", result->sequence[i++]);
    return 0;
}
```

## 最长公共子序列

```python
# O(n * m * min(n, m)) time | O(n * m * min(n, m)) space
def longest_common_subsequence1(s1, s2):
    lcs = [[[] for _ in range(len(s1) + 1)] for _ in range(len(s2) + 1)]
    for i in range(1, len(s2) + 1):
        for j in range(len(s1) + 1):
            if s2[i - 1] == s1[j - 1]:
                lcs[i][j] = lcs[i - 1][j - 1] + [s2[i - 1]]
            else:
                lcs[i][j] = max(lcs[i - 1][j], lcs[i][j - 1], key=len)
    return lcs[-1][-1]


# O(n * m) time | O(n * m) space
def longest_common_subsequence2(s1, s2):
    lcs = [[[None, 0, None, None] for _ in range(len(s1) + 1)] for _ in range(len(s2) + 1)]
    for i in range(len(s2) + 1):
        for j in range(len(s1) + 1):
            if s2[i - 1] == s1[j - 1]:
                lcs[i][j] = [s2[i - 1], lcs[i - 1][j - 1][1] + 1, i - 1, j - 1]
            else:
                if lcs[i - 1][j][1] > lcs[i][j - 1][1]:
                    lcs[i][j] = [None, lcs[i - 1][j][1], i - 1, j]
                else:
                    lcs[i][j] = [None, lcs[i][j - 1][1], i, j - 1]
    return build_sequence(lcs)


def build_sequence(lcs):
    sequence = []
    i = len(lcs) - 1
    j = len(lcs[0]) - 1
    while i != 0 and j != 0:
        current_entry = lcs[i][j]
        if current_entry[0] is not None:
            sequence.append(current_entry[0])
        i = current_entry[2]
        j = current_entry[3]
    return list(reversed(sequence))


if __name__ == '__main__':
    print(longest_common_subsequence1('zxvAyzw', 'xkykzpw'))
    print(longest_common_subsequence2('zxvAyzw', 'xkykzpw'))
```

```cpp
#include <iostream>
#include <set>
#include <vector>
#include <string>

using namespace std;

/* O(n + m) time | O(min(n, m)) space */
vector<char> longestCommonSubsequence(string s1, string s2)
{
    set<char> lcs;
    vector<char> result;
    unsigned int i;
    for ( i=0; i<s1.size(); i++ )
        lcs.insert(s1[i]);
    for ( i=0; i<s2.size(); i++ )
    {
        if ( lcs.count(s2[i]) > 0 )
            result.push_back(s2[i]);
    }
    return result;
}

int main()
{
    string s1 = "zxvvyzw";
    string s2 = "xkykzpw";
    vector<char> result = longestCommonSubsequence(s1, s2);
    vector<char>::iterator iter = result.begin();
    while ( iter != result.end() )
        cout << *iter++ << " ";
    return 0;
}
```

## 最小跳跃数

给定一个整数数组，数组中的值表示可前进的步数，计算出穿过数组的最小步数

```c
#include <stdio.h>
#include <stdlib.h>
#define MAX_INTEGER 2147483647

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

/* O(n^2) time | O(n) space */
int minNumberOfJumps1(int array[], int length)
{
    int jumps[length] = {0}, i, j;
    for ( i=1; i<length; i++) jumps[i] = MAX_INTEGER;
    for ( i=1; i<length; i++)
    {
        for ( j=0; j<i; j++ )
        {
            if ( array[j] + j >= i )
                jumps[i] = min(jumps[j] + 1, jumps[i]);
        }
    }
    if ( jumps[length - 1] == MAX_INTEGER ) return 1;
    return jumps[length - 1];
}

/* O(n) time | O(1) space */
int minNumberOfJumps2(int array[], int length)
{
    if ( length == 1 ) return 1;
    length--;
    int maxReach = array[0], steps = array[0], jumps = 0, i;
    for ( i=1; i<length; i++)
    {
        maxReach = max(maxReach, array[i] + i);
        steps -= 1;
        if ( steps == 0 )
        {
            jumps += 1;
            steps = maxReach - i;
        }
        else if ( steps < 0 ) break;
    }
    return jumps + 1;
}

int main()
{
    int array[] = {3, 4, 2, 1, 2, 3, 7, 1, 1, 1, 3};
    int length = sizeof(array) / sizeof(array[0]);
    printf("%d ", minNumberOfJumps1(array, length));
    printf("%d ", minNumberOfJumps2(array, length));
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

每个子阵列都包含三个整数并表示磁盘。这些整数分别表示每个磁盘的宽度，深度和高度。目标是堆叠磁盘并最大限度地提高堆栈的总高度。磁盘必须具有比其下方的任何其他磁盘更小的宽度，深度和高度。写一个函数，返回nal堆栈中的磁盘数组，从顶部磁盘开始，并以底部磁盘结尾。

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

## 第K次投资最大利润

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
def palindrome_partitioning_min_cuts(string):
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
    print(palindrome_partitioning_min_cuts('noonaddab'))
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
                    continue;
                if ( i > leftIdx && i < rightIdx && rightIdx - leftIdx <= j - i )
                {
                    leftIdx = i;
                    rightIdx = j;
                    continue;
                }
                countOfCuts++;
                leftIdx = i;
                rightIdx = j;
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

Last Modified 2021-08-21

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
        if ( i % 2 == 1)
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
    if ( bigLength % 2 == 0)
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

Last Modified 2021-08-06

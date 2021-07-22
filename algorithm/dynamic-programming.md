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
        numOfCoins[i] = 2147483646;
    for ( i=0; i<denomsLength; i++)
        for ( amount=0; amount<=n; amount++)
            if ( denoms[i] <= amount )
                numOfCoins[amount] = min( numOfCoins[amount], 1 + numOfCoins[amount - denoms[i]] );
    if ( numOfCoins[n] != 2147483646 )
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

Last Modified 2021-07-22

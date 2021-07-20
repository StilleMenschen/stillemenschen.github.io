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

Last Modified 2021-07-20

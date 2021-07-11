# 字符串

## 回文字符串

```c
#include <stdio.h>
#define TRUE 1
#define FALSE 0

int getStringLength(const char *string)
{
    const char *p = string;
    int length = 0;
    while ( *p++ != '\0' ) length++;
    return length;
}

/* O(n) time | O(1) space */
int palindromeCheck(const char string[])
{
    const int lastIdx = getStringLength(string) - 1;
    int i = 0;
    while ( i < lastIdx - i )
    {
        if ( string[i] != string[lastIdx - i] ) return FALSE;
        i++;
    }
    return TRUE;
}

int main()
{
    char str[] = "abcddcba";
    printf("%d", palindromeCheck(str));
    return 0;
}
```

## 凯撒密码加密器

```c
#include <stdio.h>

int getStringLength(const char *string)
{
    const char *p = string;
    int length = 0;
    while ( *p++ != '\0' ) length++;
    return length;
}

/* O(n) time | O(1) space */
char* caesarCipherEncryptor(char string[], int key)
{
    const int length = getStringLength(string);
    const int firstLetters = 'a', lastLetters = 'z';
    int i = 0;
    key = key % 26; // make sure range of 26 English letters
    for (; i < length; i++)
    {
        int c = string[i] + key;
        if ( c > lastLetters )
            c = ( firstLetters + c ) % lastLetters;
        string[i] = c;
    }
    return string;
}

int main()
{
    char str[] = "xyz";
    printf("%s", caesarCipherEncryptor(str, 2));
    return 0;
}
```

## 行程编码

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node
{
    struct Node *next;
    char value[2];
} Node, *pNode;

int getStringLength(const char *string)
{
    const char *p = string;
    int length = 0;
    while ( *p++ != '\0' ) length++;
    return length;
}

char* concatLists(Node* head, int listsSize)
{
    char *str = (char*)malloc(listsSize * 2 * sizeof(char));
    char *p;
    int i = 0, j;
    while ( head != NULL )
    {
        for ( j = 0; j < 2; i++, j++) str[i] = head->value[j];
        head = head->next;
    }
    str[i] = '\0';
    return str;
}

/* O(n) time | O(n) space */
char* runLengthEncoding(char string[])
{
    int length = getStringLength(string);
    pNode head, tail;
    head = (Node*)malloc(sizeof(Node));
    tail = head;
    int i = 1, size = 1, listsSize = 0;
    for (; i < length; i++)
    {
        if ( string[i] != string[i - 1] || size == 9)
        {
            tail->value[0] = (char)(size + 48);
            tail->value[1] = string[i - 1];
            pNode node = (Node*)malloc(sizeof(Node));
            node->next = NULL;
            tail->next = node;
            tail = node;
            size = 0;
            listsSize++;
        }
        size++;
    }
    tail->value[0] = (char)(size + 48);
    tail->value[1] = string[length - 1];
    return concatLists(head, listsSize + 1);
}

int main()
{
    char str[] = "AAAAAAAAAAAAAABBBBCCCCDDDD";
    printf("%s", runLengthEncoding(str));
    return 0;
}
```

## 最长回文字符串

```c
#include <stdio.h>
#include <stdlib.h>

int getStringLength(const char *string)
{
    const char *p = string;
    int length = 0;
    while ( *p++ != '\0' ) length++;
    return length;
}

char* subString(const char string[], int startIdx, int endIdx)
{
    char *result = (char*)malloc( (endIdx - startIdx) * sizeof(char) );
    char *p = result;
    while ( startIdx < endIdx )
        *p++ = string[startIdx++];
    *p = '\0';
    return result;
}

int* getLongestPalindromeFrom(const char string[], int leftIdx, int rightIdx, const int length)
{
    int *longest = (int*)malloc(2 * sizeof(int) );
    while ( leftIdx >= 0 && rightIdx < length )
    {
        if ( string[leftIdx] != string[rightIdx] )
            break;
        leftIdx--;
        rightIdx++;
    }
    longest[0] = leftIdx + 1;
    longest[1] = rightIdx;
    return longest;
}

/* O(n^2) time | O(1) space */
char* longestPalindromicString(const char string[])
{
    const int lastIdx = getStringLength(string) - 1;
    int maxLeftIdx = 1, maxRightIdx = 1, currentIdx = 1;
    while ( currentIdx < lastIdx )
    {
        int *odd = getLongestPalindromeFrom(string, currentIdx - 1, currentIdx + 1, lastIdx + 1);
        int *even = getLongestPalindromeFrom(string, currentIdx - 1, currentIdx, lastIdx + 1);
        if ( odd[1] - odd[0] > maxRightIdx - maxLeftIdx )
        {
            maxLeftIdx = odd[0];
            maxRightIdx = odd[1];
        }
        if ( even[1] - even[0] > maxRightIdx - maxLeftIdx )
        {
            maxLeftIdx = even[0];
            maxRightIdx = even[1];
        }
        currentIdx++;
    }
    return subString(string, maxLeftIdx, maxRightIdx);
}

int main()
{
    const char str[] = "abacdefghhhgfedaa";
    printf("%s", longestPalindromicString(str));
    return 0;
}
```

## 分组字谜

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node
{
    const char *value;
    int code;
} Node, *pNode;

typedef struct Stack
{
    struct Stack *next;
    int array[2];
} Stack, *pStack;

pStack pushStack(pStack stack, int leftIdx, int rightIdx)
{
    pStack node = (Stack*)malloc(sizeof(Stack));
    node->array[0] = leftIdx;
    node->array[1] = rightIdx;
    if ( stack != NULL )
        node->next = stack;
    else
        node->next = NULL;
    return node;
}

int compareCode(Node array[], int i, int j)
{
    return array[i].code - array[j].code;
}

pNode quickSort(Node array[], int length)
{
    pStack stack = (Stack*)malloc(sizeof(Stack));
    stack->array[0] = 0;
    stack->array[1] = length - 1;
    stack->next = NULL;
    int startIdx, endIdx;
    while ( stack != NULL )
    {
        startIdx = stack->array[0];
        endIdx = stack->array[1];
        stack = stack->next;
        if ( startIdx >= endIdx )
            continue;
        int pivotIdx = startIdx;
        int leftIdx = startIdx + 1;
        int rightIdx = endIdx;
        while ( rightIdx >= leftIdx )
        {
            if ( compareCode(array, leftIdx, pivotIdx) > 0 && compareCode(array, pivotIdx, rightIdx) > 0 )
            {
                const Node node = array[leftIdx];
                array[leftIdx] = array[rightIdx];
                array[rightIdx] = node;
            }
            if ( compareCode(array, leftIdx, pivotIdx) <= 0 )
                leftIdx++;
            if ( compareCode(array, rightIdx, pivotIdx) >= 0 )
                rightIdx--;
        }
        const Node node = array[pivotIdx];
        array[pivotIdx] = array[rightIdx];
        array[rightIdx] = node;
        if ( rightIdx - 1 - startIdx < endIdx - (rightIdx + 1) )
        {
            stack = pushStack(stack, startIdx, rightIdx - 1);
            stack = pushStack(stack, rightIdx + 1, endIdx);
        }
        else
        {
            stack = pushStack(stack, rightIdx + 1, endIdx);
            stack = pushStack(stack, startIdx, rightIdx - 1);
        }
    }
    return array;
}

int wordCode(const char *word)
{
    int code = 0;
    while ( *word )
        code += (int)*word++;
    return code;
}

/* O(w * n * log(n)) time | O(w * n) space */
Node* groupAnagrams(Node words[], int wordsLength)
{
    int i = 0;
    for ( ; i < wordsLength; i++)
        words[i].code = wordCode( words[i].value );
    return quickSort(words, wordsLength);
}

int main()
{
    Node words[] =
    {
        { "yo", -1 },
        { "act", -1 },
        { "blop", -1 },
        { "tac", -1 },
        { "cat", -1 },
        { "oy", -1 },
        { "olbp", -1 }
    };
    int wordsLength = sizeof(words) / sizeof(words[0]);
    int i = 0;
    pNode sortedWords = groupAnagrams(words, wordsLength);
    for ( ; i < wordsLength ; i++)
        printf("%s ", sortedWords[i].value );
    return 0;
}
```

Last Modified 2021-07-11

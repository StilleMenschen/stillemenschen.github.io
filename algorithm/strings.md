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

Last Modified 2021-07-11

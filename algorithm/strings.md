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

## 不重复子字符串

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

/* O(n) time | O(min(n, a)) space */
char* longestSubstringWithoutDuplication(char string[])
{
    int map[95];
    int moveIdx = 0;
    while ( moveIdx < 95 ) map[moveIdx++] = -1;
    const int length = getStringLength(string);
    int startIdx = -1, position, start = 0, stop = 0;
    moveIdx = 0;
    for ( ; moveIdx < length; moveIdx++ )
    {
        position = string[moveIdx] - ' ';
        if ( map[position] >= 0 && map[position] + 1 > startIdx )
            startIdx = map[position] + 1;
        if ( stop - start < moveIdx + 1 - startIdx)
        {
            start = startIdx;
            stop = moveIdx + 1;
        }
        map[position] = moveIdx;
    }
    char *p = &string[start];
    string[stop] = '\0';
    return p;
}

int main()
{
    char words[] = "cleMent3isacap";
    printf("%s", longestSubstringWithoutDuplication(words));
    return 0;
}
```

## 标记子字符串

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node
{
    struct Node *next;
    int position[2];
} Node, *pNode;

int getStringLength(char* string)
{
    char *p = string;
    int length = 0;
    while ( *p++ ) length++;
    return length;
}

int findSubstring(char string[], int startIdx, int length1, char substring[], int length2)
{
    int j = 0, position = 0;
    length1 = length1 - length2 + 1;
    while ( startIdx < length1 )
    {
        if ( string[startIdx] == substring[j] )
        {
            position = startIdx;
            while ( j < length2 && string[++position] == substring[++j] ) {} ;
            if ( j == length2 ) return startIdx;
            j = 0;
        }
        startIdx++;
    }
    return -1;
}

char* underscorify(char string[], int stringLength, int realLength, pNode head)
{
    char *p = (char*)malloc((realLength + 1) * sizeof(char));
    char *q = p;
    int startIdx = 0;
    while ( head != NULL )
    {
        while ( startIdx < head->position[0] )
            *q++ = string[startIdx++];
        *q++ = '_';
        while ( startIdx < head->position[1] )
            *q++ = string[startIdx++];
        *q++ = '_';
        head = head->next;
    }
    while ( startIdx < stringLength )
        *q++ = string[startIdx++];
    *q = '\0';
    return p;
}

/* O(n + m) time | O(n) space */
char* underscorifySubstring(char string[], char substring[])
{
    int stringLength = getStringLength(string);
    int substringLength = getStringLength(substring);
    if ( stringLength <= 0 || substringLength <= 0 || stringLength < substringLength) return string;
    int startIdx = 0, total = 0, locate;
    pNode head = NULL, tail = NULL;
    while ( startIdx < stringLength )
    {
        locate = findSubstring(string, startIdx, stringLength, substring, substringLength);
        if ( locate < 0 ) break;
        if ( head == NULL )
        {
            head = (Node*)malloc(sizeof(Node));
            tail = head;
            tail->position[0] = locate;
            tail->position[1] = locate + substringLength;
        }
        else
        {
            if ( locate <= tail->position[1] )
            {
                tail->position[1] = locate + substringLength;
            }
            else
            {
                tail->next = (Node*)malloc(sizeof(Node));
                tail = tail->next;
                tail->position[0] = locate;
                tail->position[1] = locate + substringLength;
            }
        }
        startIdx = locate + 1;
        total++;
    }
    tail->next = NULL;
    return underscorify(string, stringLength, total * 2 + stringLength, head);
}

int main()
{
    char string[] = "alpha a that a alphaalpha pre to seen if alphalphalpha it works";
    char substring[] = "alpha";
    printf("%s", underscorifySubstring(string, substring));
    return 0;
}
```

## 模式匹配器

```python
def get_new_pattern(pattern):
    """调换模式中的x和y的位置"""
    pattern_letters = list(pattern)
    if pattern[0] == 'x':
        return pattern_letters
    else:
        return list(map(lambda char: 'x' if char == 'y' else 'y', pattern_letters))


def get_counts_and_first_y_pos(pattern, counts):
    """找出y的第一次出现位置"""
    first_y_pos = None
    for i, char in enumerate(pattern):
        counts[char] += 1
        if char == 'y' and first_y_pos is None:
            first_y_pos = i
    return first_y_pos


# O(n^2 + m) time | O(n + m) space
def pattern_matcher(pattern, string):
    if len(pattern) > len(string):
        return list()
    new_pattern = get_new_pattern(pattern)
    # 由于下面是从x开始猜测, 所以此处确定是否在最后猜出字符串时将x和y调换回来
    did_switch = new_pattern[0] != pattern[0]
    counts = {'x': 0, 'y': 0}
    first_y_pos = get_counts_and_first_y_pos(new_pattern, counts)
    string_length = len(string)
    # 如果x和y都有
    if counts['y'] != 0:
        # 从长度1开始猜测x单词的长度
        for len_of_x in range(1, string_length):
            # 逆向算出y的长度
            len_of_y = (string_length - len_of_x * counts['x']) / counts['y']
            # y的长度必须是正整数
            if len_of_y <= 0 or len_of_y % 1 != 0:
                continue
            # 切片源字符串并填充x和y
            len_of_y = int(len_of_y)
            y_idx = first_y_pos * len_of_x
            x = string[:len_of_x]
            y = string[y_idx:y_idx + len_of_y]
            potential_match = map(lambda char: x if char == 'x' else y, new_pattern)
            # 比较拼接好的字符串是否与源字符串完全相等
            if string == ''.join(potential_match):
                # 完全相等则表示猜测正确
                return [x, y] if not did_switch else [y, x]
    # 如果只有x
    else:
        # 得出x单词的长度
        len_of_x = string_length / counts['x']
        if len_of_x % 1 == 0:
            len_of_x = int(len_of_x)
            # 切片源字符串并填充x
            x = string[:len_of_x]
            potential_match = map(lambda char: x, new_pattern)
            # 比较拼接好的字符串是否与源字符串完全相等
            if string == ''.join(potential_match):
                # 完全相等则表示猜测正确
                return [x, ''] if not did_switch else ['', x]
    return list()


if __name__ == '__main__':
    print(pattern_matcher('xxyxxy', 'gogopowerrangegogopowerrange'))
```

Last Modified 2021-07-16

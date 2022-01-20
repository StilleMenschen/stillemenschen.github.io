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
void caesarCipherEncryptor(char string[], int key)
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

```python
# O(n^3) time | O(n) space
def longest_palindromic_substring1(string):
    longest = ""
    for i in range(len(string)):
        for j in range(i, len(string)):
            substring = string[i: j + 1]
            if len(substring) > len(longest) and is_palindrome(substring):
                longest = substring
    return longest


def is_palindrome(string):
    left_idx = 0
    right_idx = len(string) - 1
    while left_idx < right_idx:
        if string[left_idx] != string[right_idx]:
            return False
        left_idx += 1
        right_idx -= 1
    return True


# O(n^2) time | O(n) space
def longest_palindromic_substring2(string):
    current_longest = [0, 1]
    for i in range(1, len(string)):
        odd = get_longest_palindrome_from(string, i - 1, i + 1)
        even = get_longest_palindrome_from(string, i - 1, i)
        current_longest = max(odd, even, current_longest, key=lambda x: x[1] - x[0])
    return string[current_longest[0]: current_longest[1]]


def get_longest_palindrome_from(string, left_idx, right_idx):
    while left_idx >= 0 and right_idx < len(string):
        if string[left_idx] != string[right_idx]:
            break
        left_idx -= 1
        right_idx += 1
    return [left_idx + 1, right_idx]


if __name__ == '__main__':
    source = 'abacdefghhhgfedaa'
    print(longest_palindromic_substring1(source))
    print(longest_palindromic_substring2(source))
```

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

bool isPalindrome(string str);
vector<size_t> getLongestPalindromeFrom(string str, size_t leftIdx, size_t rightIdx);

// O(n^3) time | O(n) space
string longestPalindromicSubstring1(string str)
{
    string longest = "";
    for (size_t i = 0; i < str.length(); i++)
    {
        for (size_t j = i; j < str.length(); j++)
        {
            string substring = str.substr(i, j + 1 - i);
            if (substring.length() > longest.length() && isPalindrome(substring))
            {
                longest = substring;
            }
        }
    }
    return longest;
}

bool isPalindrome(string str)
{
    int leftIdx = 0;
    int rightIdx = str.length() - 1;
    while (leftIdx < rightIdx)
    {
        if (str[leftIdx] != str[rightIdx])
        {
            return false;
        }
        leftIdx++;
        rightIdx--;
    }
    return true;
}

// O(n^2) time | O(n) space
string longestPalindromicSubstring2(string str)
{
    vector<size_t> currentLongest{0, 1};
    for (size_t i = 1; i < str.length(); i++)
    {
        vector<size_t> odd = getLongestPalindromeFrom(str, i - 1, i + 1);
        vector<size_t> even = getLongestPalindromeFrom(str, i - 1, i);
        vector<size_t> longest = odd[1] - odd[0] > even[1] - even[0] ? odd : even;
        currentLongest =
            currentLongest[1] - currentLongest[0] > longest[1] - longest[0]
                ? currentLongest
                : longest;
    }
    return str.substr(currentLongest[0], currentLongest[1] - currentLongest[0]);
}

vector<size_t> getLongestPalindromeFrom(string str, size_t leftIdx, size_t rightIdx)
{
    while (leftIdx >= 0 && rightIdx < str.length())
    {
        if (str[leftIdx] != str[rightIdx])
        {
            break;
        }
        leftIdx--;
        rightIdx++;
    }
    return vector<size_t>{leftIdx + 1, rightIdx};
}

int main()
{
    string source = "abacdefghhhgfedaa";
    cout << longestPalindromicSubstring1(source) << endl;
    cout << longestPalindromicSubstring2(source) << endl;
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

```cpp
#include <unordered_map>
#include <vector>
#include <iostream>

using namespace std;

// O(n) time | O(min(n, a)) space
string longestSubstringWithoutDuplication(string str)
{

    unordered_map<char, int> lastSeen;
    vector<int> longest{0, 1};
    int startIdx = 0;

    for (int i = 0; i < str.length(); i++)
    {
        char character = str[i];
        if (lastSeen.find(character) != lastSeen.end())
        {
            startIdx = max(startIdx, lastSeen[character] + 1);
        }
        if (longest[1] - longest[0] < i + 1 - startIdx)
        {
            longest = {startIdx, i + 1};
        }
        lastSeen[character] = i;
    }

    string result = str.substr(longest[0], longest[1] - longest[0]);
    return result;
}

int main()
{
    cout << longestSubstringWithoutDuplication("clementisacap") << endl;
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

## 找出最小子字符串

```python
# O(b + s) time | O(b + s) space
def smallest_substring_containing(big_string, small_string):
    target_char_counts = get_char_counts(small_string)
    substring_bounds = get_substring_bounds(big_string, target_char_counts)
    return get_string_from_bounds(big_string, substring_bounds)


def get_substring_bounds(string, target_char_counts):
    # 初始最小范围为无限大
    substring_bounds = (0, float('inf'),)
    substring_char_counts = dict()
    # 计算待查找的字符数
    num_unique_chars = len(target_char_counts.keys())
    num_unique_chars_done = 0
    # 创建左右指针不停地缩小范围查找
    left_idx = 0
    right_idx = 0
    string_length = len(string)
    while right_idx < string_length:
        # 移动右指针递增找到的字符数
        right_char = string[right_idx]
        if right_char not in target_char_counts:
            # 不存在于待查找字符串的字符直接跳过
            right_idx += 1
            continue
        # 递增待查找字符数
        increase_char_count(right_char, substring_char_counts)
        if substring_char_counts[right_char] == target_char_counts[right_char]:
            # 如果找到了符合条件的字符则待查找字符总计数加1
            num_unique_chars_done += 1
        # 移动左指针缩小字符串范围
        while num_unique_chars_done == num_unique_chars and left_idx <= right_idx:
            substring_bounds = get_closer_bounds(left_idx, right_idx, substring_bounds[0], substring_bounds[1])
            left_char = string[left_idx]
            if left_char not in target_char_counts:
                # 不存在于待查找字符串的字符直接跳过
                left_idx += 1
                continue
            if substring_char_counts[left_char] == target_char_counts[left_char]:
                # 如果找到了符合条件的字符则待查找字符总计数减1 (停止移动左指针)
                num_unique_chars_done -= 1
            # 递减待查找字符数
            decrease_char_count(left_char, substring_char_counts)
            left_idx += 1
        right_idx += 1
    return substring_bounds


def get_closer_bounds(first_idx1, first_idx2, second_idx1, second_idx2):
    """求出范围最小的字符串"""
    if first_idx2 - first_idx1 < second_idx2 - second_idx1:
        return first_idx1, first_idx2
    else:
        return second_idx1, second_idx2


def get_string_from_bounds(string, substring_bounds):
    """返回找到的子字符串"""
    start, end = substring_bounds
    if end == float('inf'):
        # 如果最大值仍然是无限大则表示没有找到
        return ""
    return string[start:end + 1]


def get_char_counts(string):
    """统计待查找子字符串的字符个数"""
    char_counts = dict()
    for char in string:
        increase_char_count(char, char_counts)
    return char_counts


def increase_char_count(char, char_counts):
    if char not in char_counts:
        char_counts[char] = 0
    char_counts[char] += 1


def decrease_char_count(char, char_counts):
    char_counts[char] -= 1


if __name__ == '__main__':
    print(smallest_substring_containing('abcd#ef#axb#c#', '##abf'))
```

## 最长匹配括号数

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node
{
    struct Node *next;
    int value;
} Node, *pNode;

int getStringLength(char* string)
{
    char *p = string;
    int length = 0;
    while ( *p++ ) length++;
    return length;
}

int max(int one, int two)
{
    if ( one > two )
        return one;
    return two;
}

/* O(n) time | O(n) space */
int longestBalancedSubstring1(char string[])
{
    int i, maxLength = 0;
    const int length = getStringLength(string);
    pNode idxStack = (Node*)malloc(sizeof(Node));
    idxStack->value = -1;
    idxStack->next = NULL;
    for ( i=0; i<length; i++)
    {
        if ( string[i] == '(' )
        {
            pNode node = (Node*)malloc(sizeof(Node));
            node->value = i;
            node->next = idxStack;
            idxStack = node;
        }
        else
        {
            idxStack = idxStack->next;
            if ( idxStack == NULL )
            {
                idxStack = (Node*)malloc(sizeof(Node));
                idxStack->value = i;
                idxStack->next = NULL;
            }
            else
            {
                const int balancedSubstringStartIdx = idxStack->value;
                const int currentLength = i - balancedSubstringStartIdx;
                maxLength = max(maxLength, currentLength);
            }
        }
    }
    return maxLength;
}

/* O(n) time | O(1) space */
int longestBalancedSubstring2(char string[])
{
    int i, maxLength = 0;
    const int length = getStringLength(string);
    int openingCount = 0, closingCount = 0;
    for ( i=0; i<length; i++)
    {
        if ( string[i] == '(' )
            openingCount++;
        else
            closingCount++;
        if ( openingCount == closingCount )
            maxLength = max(maxLength, openingCount + closingCount);
        else if ( closingCount > openingCount )
            openingCount = closingCount = 0;
    }
    openingCount = closingCount = 0;
    for ( i=length-1; i>=0; i--)
    {
        if ( string[i] == '(' )
            openingCount++;
        else
            closingCount++;
        if ( openingCount == closingCount )
            maxLength = max(maxLength, openingCount + closingCount);
        else if ( openingCount > closingCount )
            openingCount = closingCount = 0;
    }
    return maxLength;
}

int getLongestBalancedInRange(char string[], int start, int stop, int step)
{
    const char openingParens = step > 0 ? '(' : ')';
    int maxLength = 0;
    int openingCount = 0, closingCount = 0;
    for ( ; start <= stop; start+=step )
    {
        if ( string[start] == openingParens )
            openingCount++;
        else
            closingCount++;
        if ( openingCount == closingCount )
            maxLength = max(maxLength, openingCount + closingCount);
        else if ( closingCount > openingCount )
            openingCount = closingCount = 0;
    }
    return maxLength;
}

/* O(n) time | O(1) space */
int longestBalancedSubstring3(char string[])
{
    const int length = getStringLength(string);
    return max(
               getLongestBalancedInRange(string, 0, length - 1, 1),
               getLongestBalancedInRange(string, length - 1, 0, -1)
           );
}

int main(void)
{
    char string[] = ")((()))()";
    printf("%d ", longestBalancedSubstring1(string));
    printf("%d ", longestBalancedSubstring2(string));
    printf("%d ", longestBalancedSubstring3(string));
    return 0;
}
```

## 生成文档

```python
def count_character_frequency(character, target):
    frequency = 0
    for char in target:
        if char == character:
            frequency += 1
    return frequency


# O(c * (n + m)) time | O(c) space
# c is the number of unique characters in the document
def generate_document1(characters, document):
    if not len(document):
        return True
    already_counted = set()
    for character in document:
        if character in already_counted:
            continue

        document_frequency = count_character_frequency(character, document)
        characters_frequency = count_character_frequency(character, characters)
        if document_frequency > characters_frequency:
            return False

        already_counted.add(character)

    return True


# O(n + m) time | O(n) space
# n is the number of characters
# m is the number of document
def generate_document2(characters, document):
    if not len(document):
        return True
    counts = dict()
    for character in characters:
        if character not in counts:
            counts[character] = 1
        else:
            counts[character] += 1
    for character in document:
        if character not in counts or counts[character] == 0:
            return False
        counts[character] -= 1
    return True


if __name__ == '__main__':
    print(generate_document1('ahealolabbhb', 'hello'))
    print(generate_document2('ahealolabbhb', 'hello'))
```

## 第一个不重复字符

```cpp
#include <iostream>

using namespace std;

// O(n^2) time | O(1) space - where n is the length of the input string
int firstNonRepeatingCharacter(string string)
{
    for (int idx = 0; idx < string.size(); idx++)
    {
        int foundDuplicate = false;
        for (int idx2 = 0; idx2 < string.size(); idx2++)
        {
            if ( string[idx] == string[idx2] && idx != idx2 )
            {
                foundDuplicate = true;
                break;
            }
        }
        if (!foundDuplicate)
            return idx;
    }
    return -1;
}

int main()
{
    cout << firstNonRepeatingCharacter("abcdacff") << endl;
    return 0;
}
```

Last Modified 2021-11-07

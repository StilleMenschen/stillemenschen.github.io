# 字符串

## 回文字符串

```python
# O(n) time | O(1) space
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
    print(is_palindrome("abcba"))
```

```cpp
#include <string>
using namespace std;

// O(n) time | O(1) space
bool isPalindrome(string str) {
  int leftIdx = 0;
  int rightIdx = str.length() - 1;
  while (leftIdx < rightIdx) {
    if (str[leftIdx] != str[rightIdx]) {
      return false;
    }
    leftIdx++;
    rightIdx--;
  }
  return true;
}
```

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

```python
# O(n) time | O(n) space
def caesar_cipher_encryptor1(string, key):
    new_letters = []
    new_key = key % 26
    for letter in string:
        new_letters.append(get_new_letter1(letter, new_key))
    return "".join(new_letters)


def get_new_letter1(letter, key):
    new_letter_code = ord(letter) + key
    return chr(new_letter_code) if new_letter_code <= 122 else chr(96 + new_letter_code % 122)


# O(n) time | O(n) space
def caesar_cipher_encryptor2(string, key):
    new_letters = []
    new_key = key % 26
    alphabet = list("abcdefghijklmnopqrstuvwxyz")
    for letter in string:
        new_letters.append(get_new_letter2(letter, new_key, alphabet))
    return "".join(new_letters)


def get_new_letter2(letter, key, alphabet):
    new_letter_code = alphabet.index(letter) + key
    return alphabet[new_letter_code % 26]


if __name__ == '__main__':
    print(caesar_cipher_encryptor1('zxd', 2))
    print(caesar_cipher_encryptor2('zxd', 2))
```

```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <string>
using namespace std;

char getNewLetter1(char letter, int key);
char getNewLetter2(char letter, int key, string alphabet);

// O(n) time | O(n) space
string caesarCypherEncryptor1(string str, int key)
{
    vector<char> newLetters;
    int newKey = key % 26;
    for (size_t i = 0; i < str.length(); i++)
    {
        newLetters.push_back(getNewLetter1(str[i], newKey));
    }
    return string(newLetters.begin(), newLetters.end());
}

char getNewLetter1(char letter, int key)
{
    int newLetterCode = letter + key;
    return newLetterCode <= 122 ? newLetterCode : 96 + newLetterCode % 122;
}

// O(n) time | O(n) space
string caesarCypherEncryptor2(string str, int key)
{
    vector<char> newLetters;
    int newKey = key % 26;
    string alphabet = "abcdefghijklmnopqrstuvwxyz";
    for (size_t i = 0; i < str.length(); i++)
    {
        newLetters.push_back(getNewLetter2(str[i], newKey, alphabet));
    }
    return string(newLetters.begin(), newLetters.end());
}

char getNewLetter2(char letter, int key, string alphabet)
{
    int newLetterCode = alphabet.find(letter) + key;
    return alphabet[newLetterCode % 26];
}

int main()
{
    string source = "xyz";
    cout << caesarCypherEncryptor1(source, 2) << endl;
    cout << caesarCypherEncryptor2(source, 2) << endl;
    return 0;
}
```

```c
#include <stdio.h>

int getStringLength(const char *string)
{
    const char *p = string;
    int length = 0;
    while (*p++ != '\0')
        length++;
    return length;
}

/* O(n) time | O(1) space */
char *caesarCipherEncryptor(char string[], int key)
{
    const int length = getStringLength(string);
    const int firstLetters = 'a' - 1, lastLetters = 'z';
    int i = 0;
    key = key % 26; // make sure range of 26 English letters
    for (; i < length; i++)
    {
        int c = string[i] + key;
        if (c > lastLetters)
            c = (firstLetters + c) % lastLetters;
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

```python
# O(n) time | O(n) space - where n is the length of the input string
def run_length_encoding(string):
    # The input string is guaranteed to be non-empty,
    # so our first run will be of at least length 1.
    encoded_string_characters = []
    current_run_length = 1

    for i in range(1, len(string)):
        current_character = string[i]
        previous_character = string[i - 1]

        if current_character != previous_character or current_run_length == 9:
            encoded_string_characters.append(str(current_run_length))
            encoded_string_characters.append(previous_character)
            current_run_length = 0

        current_run_length += 1

    # Handle the last run.
    encoded_string_characters.append(str(current_run_length))
    encoded_string_characters.append(string[len(string) - 1])

    return "".join(encoded_string_characters)


if __name__ == '__main__':
    print(run_length_encoding('AAAAAAAAAAAAABBCCCCDD'))
```

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;

// O(n) time | O(n) space - where n is the length of the input string
string runLengthEncoding(string str)
{
    // The input string is guaranteed to be non-empty,
    // so our first run will be of at least length 1.
    vector<char> encodedStringCharacters;
    int currentRunLength = 1;

    for (size_t i = 1; i < str.size(); i++)
    {
        char currentCharacter = str[i];
        char previousCharacter = str[i - 1];

        if (currentCharacter != previousCharacter || currentRunLength == 9)
        {
            encodedStringCharacters.push_back(to_string(currentRunLength)[0]);
            encodedStringCharacters.push_back(previousCharacter);
            currentRunLength = 0;
        }

        currentRunLength++;
    }

    // Handle the last run.
    encodedStringCharacters.push_back(to_string(currentRunLength)[0]);
    encodedStringCharacters.push_back(str[str.size() - 1]);

    string encodedString(encodedStringCharacters.begin(),
                         encodedStringCharacters.end());
    return encodedString;
}

int main()
{
    string source = "AAAAAAAAAAAAABBCCCCDD";
    cout << runLengthEncoding(source);
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

给定一个字符串数组，根据字符串的组成字符相似性归为一组字符串并返回，如 "abc" 和 "cab" 属于字符组成相似的字符串

```python
# O(w * n * log(n)) time | O(wn) space
# where w is the number of words and n is the length of the longest word
def group_anagrams(words):
    anagrams = {}
    for word in words:
        sorted_word = "".join(sorted(word))
        if sorted_word in anagrams:
            anagrams[sorted_word].append(word)
        else:
            anagrams[sorted_word] = [word]
    return list(anagrams.values())


if __name__ == '__main__':
    print(group_anagrams(["yo", "act", "flop", "tac", "foo", "cat", "oy", "olfp"]))
```

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <unordered_map>
using namespace std;

// O(w * n * log(n)) time | O(wn) space
// where w is the number of words and n is the length of the longest word
vector<vector<string>> groupAnagrams(vector<string> words)
{
    unordered_map<string, vector<string>> anagrams;

    for (auto word : words)
    {
        string sortedWord = word;
        sort(sortedWord.begin(), sortedWord.end());

        if (anagrams.find(sortedWord) != anagrams.end())
        {
            anagrams[sortedWord].push_back(word);
        }
        else
        {
            anagrams[sortedWord] = vector<string>{word};
        }
    }

    vector<vector<string>> output = {};
    for (auto it : anagrams)
    {
        output.push_back(it.second);
    }
    return output;
}

void iteration(vector<vector<string>> array)
{
    for (const vector<string> elements : array)
    {
        cout << "[ ";
        for (const string element : elements)
        {
            cout << element << " ";
        }
        cout << "] ";
    }
    cout << endl;
}

int main()
{
    vector<string> source = {"yo", "act", "flop", "tac", "foo", "cat", "oy", "olfp"};
    iteration(groupAnagrams(source));
    return 0;
}
```

## 不重复子字符串

```python
# O(n) time | O(min(n, a)) space
# where a is the length of the unique substring
def longest_substring_without_duplication(string):
    last_seen = {}  # 记录字符的最后一次出现位置
    start_idx = 0  # 记录起始位置
    longest = (0, 1,)  # 第一个字符认为是不重复的子字符串
    for idx, character in enumerate(string):
        if character in last_seen:
            # 如果字符存在重复则重新记录不重复字符串的开始位置
            start_idx = max(start_idx, last_seen[character] + 1)
        # 判断新发现的字符串长度是否大于当前已经记录的最长字符串长度
        if longest[1] - longest[0] < idx + 1 - start_idx:
            longest = (start_idx, idx + 1,)
        # 更新每个字符最后一次发现的位置
        last_seen[character] = idx

    # 由于 Python 的切片会忽略第二个切片位置, 所以前面记录位置时都需要加上 1
    return string[longest[0]:longest[1]]


if __name__ == '__main__':
    print(longest_substring_without_duplication('clementisacap'))
```

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

```python
# Average case: O(n + m) | O(n) space - where n is the length
# of the main string and m is the length of the substring
def under_scorify_substring(string, substring):
    locations = collapse(get_locations(string, substring))
    return under_scorify(string, locations)


def get_locations(string, substring):
    locations = []
    start_idx = 0  # 搜索开始位置为字符串第一个字符位置
    while start_idx < len(string):
        # 逐个搜索子字符串并记录开始和结束位置
        next_idx = string.find(substring, start_idx)
        if next_idx != -1:  # 在 Python 中如果找不到子字符串则会返回 -1
            locations.append([next_idx, next_idx + len(substring)])
            start_idx = next_idx + 1  # 调整搜索开始位置
        else:
            # 找不到则结束搜索
            break
    return locations


def collapse(locations):
    """合并重叠的位置区间"""
    if not len(locations):
        return locations
    new_locations = [locations[0]]
    previous = new_locations[0]  # 指向最后一个记录的区间
    for i in range(1, len(locations)):
        current = locations[i]  # 获取下一个区间
        if current[0] <= previous[1]:
            # 如果最后一个区间的结束位置和下一个区间的开始位置有重叠
            # 则更新最后一个区间的结束位置为下一个区间的结束位置
            previous[1] = current[1]
        else:
            # 没有重叠则将区间加入到记录中
            new_locations.append(current)
            # 这里将指针重新指向最后一个记录的区间
            # 类似 previous = new_locations[len(new_locations) - 1]
            previous = current
    return new_locations


def under_scorify(string, locations):
    """标记字符串中的子字符串"""
    locations_idx = 0  # 读取记录区间的索引
    string_idx = 0  # 原字符串的索引
    in_between_underscores = False  # 标记是否处于区间范围内
    final_chars = []  # 记录标记后的字符
    i = 0  # 记录区间的开始结束位置
    while string_idx < len(string) and locations_idx < len(locations):
        # 判断是否到达了记录区间的位置
        if string_idx == locations[locations_idx][i]:
            # 增加一个标记
            final_chars.append("_")
            # 切换范围标记
            in_between_underscores = not in_between_underscores
            # 如果已经离开了区间范围, 表示可以切换到下一个区间进行标记
            if not in_between_underscores:
                locations_idx += 1
            # 循环切换区间的开始和结束位置
            i = 0 if i == 1 else 1
        # 添加原始的字符串
        final_chars.append(string[string_idx])
        string_idx += 1
    # 如果子字符串刚刚好在原字符串的末尾, 则在末尾再追加一个标记
    if locations_idx < len(locations):
        final_chars.append("_")
    # 如果记录区间已经标记完成, 则将剩下的原始字符串添加到新的带标记的字符串中
    elif string_idx < len(string):
        final_chars.append(string[string_idx:])
    return "".join(final_chars)  # 拼接字符串并返回


if __name__ == '__main__':
    print(under_scorify_substring('broken s broken de broken', 'broken'))
    print(under_scorify_substring('alphathis is a alphaalpha to see if alphalphalpha it works', 'alpha'))
```

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <numeric>
using namespace std;

vector<vector<int>> getLocations(string str, string subStr);
vector<vector<int>> collapse(vector<vector<int>> locations);
string underscorify(string str, vector<vector<int>> locations);

// Average case: O(n + m) | O(n) space - where n is the length
// of the main string and m is the length of the substring
string underscorifySubstring(string str, string subStr)
{
    vector<vector<int>> locations = collapse(getLocations(str, subStr));
    return underscorify(str, locations);
}

vector<vector<int>> getLocations(string str, string subStr)
{
    vector<vector<int>> locations{};
    int startIdx = 0;
    const int strLength = str.length();
    const int subStrLength = subStr.length();
    while (startIdx < strLength)
    {
        int nextIdx = str.find(subStr, startIdx);
        if (nextIdx != string::npos)
        {
            locations.push_back(vector<int>{nextIdx, int(nextIdx + subStrLength)});
            startIdx = nextIdx + 1;
        }
        else
        {
            break;
        }
    }
    return locations;
}

vector<vector<int>> collapse(vector<vector<int>> locations)
{
    if (locations.empty())
    {
        return locations;
    }
    vector<vector<int>> newLocations{locations[0]};
    vector<int> *previous = &newLocations[0];
    for (size_t i = 1; i < locations.size(); i++)
    {
        vector<int> *current = &locations[i];
        if (current->at(0) <= previous->at(1))
        {
            previous->at(1) = current->at(1);
        }
        else
        {
            newLocations.push_back(*current);
            previous = &newLocations[newLocations.size() - 1];
        }
    }
    return newLocations;
}

string underscorify(string str, vector<vector<int>> locations)
{
    size_t locationsIdx = 0;
    int stringIdx = 0;
    bool inBetweenUnderscores = false;
    vector<string> finalChars{};
    int i = 0;
    const int strLength = str.length();
    while (stringIdx < strLength && locationsIdx < locations.size())
    {
        if (stringIdx == locations[locationsIdx][i % 2])
        {
            finalChars.push_back("_");
            inBetweenUnderscores = !inBetweenUnderscores;
            if (!inBetweenUnderscores)
            {
                locationsIdx++;
            }
            i++;
        }
        string s(1, str[stringIdx]);
        finalChars.push_back(s);
        stringIdx++;
    }
    if (locationsIdx < locations.size())
    {
        finalChars.push_back("_");
    }
    else if (stringIdx < strLength)
    {
        finalChars.push_back(str.substr(stringIdx));
    }
    return accumulate(finalChars.begin(), finalChars.end(), string());
}

int main()
{
    cout << underscorifySubstring("broken s broken de broken", "broken") << endl;
    cout << underscorifySubstring("alpha a that a alphaalpha pre to seen if alphalphalpha it works", "alpha");
    return 0;
}
```

## 模式匹配器

```python
# O(n^2 + m) time | O(n + m) space
def pattern_matcher(pattern, string):
    # 如果匹配模板的长度大于字符串则不处理直接返回
    if len(pattern) > len(string):
        return []
    # 创建模板对应的数组
    new_pattern = get_new_pattern(pattern)
    # 因为需要参考第一个字符模板为 x 来计算
    did_switch = new_pattern[0] != pattern[0]
    # 记录模板中 x 和 y 出现的次数
    counts = {'x': 0, 'y': 0}
    # 计算 x 和 y 的出现次数以及 y 的首个出现位置
    first_y_pos = get_counts_and_first_y_pos(new_pattern, counts)
    # 如果模板中包含有 x 和 y
    if counts['y'] != 0:
        # 从第一个字符长度开始尝试 x 和 y
        for len_of_x in range(1, len(string)):
            # 计算 y 的长度, 根据字符串的总长度减去所有 x 的长度并除以 y 的出现次数
            len_of_y = (len(string) - len_of_x * counts['x']) / counts['y']
            # y 的长度必须为大于零的整数, 如果 y 的长度是负数或者是小数则跳过
            if len_of_y <= 0 or len_of_y % 1 != 0:
                continue
            # 由于 Python 计算除法后可能会将数据转为 float 类型, 如 10.0,
            # 因此需要先转为整数, 而且 Python 中数组的索引不能是小数
            len_of_y = int(len_of_y)
            # 通过 x 的长度乘以 y 首次出现的位置计算 y 的实际所在位置
            y_idx = first_y_pos * len_of_x
            # 根据 x 的长度通过字符串切片得出 x, 因为上面确保了第一个模板字符是 x, 所以这里可以直接得出 x
            x = string[:len_of_x]
            # 根据 y 的位置和长度通过字符串切片得出 y
            y = string[y_idx: y_idx + len_of_y]
            # 通过模板求出字符串
            potential_match = map(lambda char: x if char == 'x' else y, new_pattern)
            # 比较计算出来的字符串与原始的字符串是否相等
            if string == ''.join(potential_match):
                # 根据上面是否切换了 x 和 y 的位置来返回匹配的 x 字符串和 y 字符串
                return [x, y] if not did_switch else [y, x]
    # 如果模板中仅仅包含了一个模板字符
    else:
        # 通过模板 x 的出现次数和原始字符串长度, 计算出 x 的实际字符长度
        len_of_x = len(string) / counts['x']
        # x 的长度必须为大于零的整数, 如果 x 的长度是负数或者是小数则认为模板与字符串不匹配
        if len_of_x % 1 == 0:
            len_of_x = int(len_of_x)  # 转为整数, Python 中的索引不能是小数
            x = string[:len_of_x]  # 字符串切片
            potential_match = map(lambda char: x, new_pattern)  # 通过模板求出字符串
            # 比较计算出来的字符串与原始的字符串是否相等
            if string == ''.join(potential_match):
                # 根据上面是否切换了 x 和 y 的位置来返回匹配的 x 字符串和 y 字符串
                # 因为第二个模板字符串是空的, 所以这里有一个是空字符串
                return [x, ''] if not did_switch else ['', x]
    return []


def get_new_pattern(pattern):
    # 直接将模板转为数组
    pattern_letters = list(pattern)
    if pattern[0] == 'x':
        return pattern_letters
    # 如果模板中首个字符不是 x 则切换 x 和 y 的位置
    else:
        return list(map(lambda char: 'x' if char == 'y' else 'y', pattern_letters))


def get_counts_and_first_y_pos(pattern, counts):
    first_y_pos = None
    for i, char in enumerate(pattern):
        counts[char] += 1  # 计算模板字符的出现次数
        if char == 'y' and first_y_pos is None:
            first_y_pos = i  # 记录第一个 y 出现的位置
    return first_y_pos


if __name__ == '__main__':
    print(pattern_matcher('xyxxy', 'bloodflushbloodbloodflush'))
```

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <numeric>
#include <algorithm>
#include <unordered_map>
#include <math.h>
using namespace std;

vector<char> getNewPattern(string pattern);
int getCountsAndFirstYPos(vector<char> pattern,
                          unordered_map<char, int> *counts);

// O(n^2 + m) time | O(n + m) space
vector<string> patternMatcher(string pattern, string str)
{
    const int strLength = str.length();
    if (pattern.length() > strLength)
    {
        return vector<string>{};
    }
    vector<char> newPattern = getNewPattern(pattern);
    bool didSwitch = newPattern[0] != pattern[0];
    unordered_map<char, int> counts({{'x', 0}, {'y', 0}});
    int firstYPos = getCountsAndFirstYPos(newPattern, &counts);
    if (counts['y'] != 0)
    {
        for (int lenOfX = 1; lenOfX < strLength; lenOfX++)
        {
            double lenOfY =
                ((double)strLength - (double)lenOfX * (double)counts['x']) /
                (double)counts['y'];
            if (lenOfY <= 0 || fmod(lenOfY, 1) != 0)
            {
                continue;
            }
            int yIdx = firstYPos * lenOfX;
            string x = str.substr(0, lenOfX);
            string y = str.substr(yIdx, lenOfY);
            vector<string> potentialMatch(newPattern.size(), "");
            transform(newPattern.begin(), newPattern.end(), potentialMatch.begin(),
                      [x, y](char c) -> string
                      { return c == 'x' ? x : y; });
            if (str == accumulate(potentialMatch.begin(), potentialMatch.end(),
                                  string("")))
            {
                return !didSwitch ? vector<string>{x, y} : vector<string>{y, x};
            }
        }
    }
    else
    {
        double lenOfX = strLength / counts['x'];
        if (fmod(lenOfX, 1) == 0)
        {
            string x = str.substr(0, lenOfX);
            vector<string> potentialMatch(newPattern.size(), "");
            transform(newPattern.begin(), newPattern.end(), potentialMatch.begin(),
                      [x](char c) -> string
                      { return x; });
            if (str == accumulate(potentialMatch.begin(), potentialMatch.end(),
                                  string("")))
            {
                return !didSwitch ? vector<string>{x, ""} : vector<string>{"", x};
            }
        }
    }
    return vector<string>{};
}

vector<char> getNewPattern(string pattern)
{
    vector<char> patternLetters(pattern.begin(), pattern.end());
    if (pattern[0] == 'x')
    {
        return patternLetters;
    }
    else
    {
        transform(patternLetters.begin(), patternLetters.end(),
                  patternLetters.begin(),
                  [](char c) -> char
                  { return c == 'y' ? 'x' : 'y'; });
        return patternLetters;
    }
}

int getCountsAndFirstYPos(vector<char> pattern,
                          unordered_map<char, int> *counts)
{
    int firstYPos = -1;
    const int patternSize = pattern.size();
    for (int i = 0; i < patternSize; i++)
    {
        char c = pattern[i];
        counts->at(c)++;
        if (c == 'y' && firstYPos == -1)
        {
            firstYPos = i;
        }
    }
    return firstYPos;
}

int main()
{
    vector<string> result = patternMatcher("xyxxy", "bloodflushbloodbloodflush");
    if (result.size() > 0)
    {
        cout << result[0] << ", " << result[1];
    }
    return 0;
}
```

## 找出最小子字符串

给出两个字符串，一个字符串较大（长度更长）一个字符串较小，在较大的字符串中找出可以构成小字符串的子字符串，这个子字符串的字符顺序可以和小字符串不一样，但是字符数量（包括重复的字符）必须一致，如大字符串`abcd$ef$axbdc$`中查找小字符串`d$abf`，可以在大字符串搜索到的匹配子字符串为`f$axbd`


```python
# O(b + s) time | O(b + s) space - where b is the length of the big
# input string and s is the length of the small input string
def smallest_substring_containing(big_string, small_string):
    # 将小字符串存入哈希表中, key 为字符, value 为字符出现的次数
    target_char_counts = get_char_counts(small_string)
    # 搜索最小出现的字符串区间
    substring_bounds = get_substring_bounds(big_string, target_char_counts)
    # 根据查找到的字符串区间计算出结果
    return get_string_from_bounds(big_string, substring_bounds)


def get_char_counts(string):
    char_counts = {}
    for char in string:
        # 记录并累加计算字符出现的次数
        increase_char_count(char, char_counts)
    return char_counts


def get_substring_bounds(string, target_char_counts):
    # 预设切割区间为 0 到无穷大, 如果没有找到合适的结果可以根据无穷大来判断
    substring_bounds = [0, float("inf")]
    # 记录在大字符串中找到的目标字符出现次数
    substring_char_counts = {}
    # 小字符串中非重复字符数量
    num_unique_chars = len(target_char_counts.keys())
    # 记录在大字符串中找到的对应小字符串非重复字符数量
    num_unique_chars_done = 0
    # 创建两个指针来查找
    left_idx = 0
    right_idx = 0
    while right_idx < len(string):
        right_char = string[right_idx]
        # 大字符串中的字符与小字符串中的字符不匹配则移动右指针
        if right_char not in target_char_counts:
            right_idx += 1
            continue
        # 有相同的字符, 递增字符数量
        increase_char_count(right_char, substring_char_counts)
        # 当前缓存的非重复字符数量与目标字符数量相等则递增总计数量
        if substring_char_counts[right_char] == target_char_counts[right_char]:
            num_unique_chars_done += 1
        # 如果总计非重复字符数量与待查找的字符数量相等时, 重新计算最小的字符切割区间
        while num_unique_chars_done == num_unique_chars and left_idx <= right_idx:
            # 重新计算最小的字符切割区间
            substring_bounds = get_closer_bounds(left_idx, right_idx, substring_bounds[0], substring_bounds[1])
            left_char = string[left_idx]
            # 如果切割区间的最左侧字符不在目标字符记录中则移动左指针
            if left_char not in target_char_counts:
                left_idx += 1
                continue
            # 如果当前缓存中左指针指向的字符在目标字符中且非重复字符计数相等, 则递减总计非重复字符数
            if substring_char_counts[left_char] == target_char_counts[left_char]:
                num_unique_chars_done -= 1
            # 递减查找缓存中对应字符的计算数量
            decrease_char_count(left_char, substring_char_counts)
            # 移动左指针
            left_idx += 1
        # 前面已经重新计算了分割区间, 继续移动右指针
        right_idx += 1
    return substring_bounds


def get_closer_bounds(first_left_idx, first_right_idx, second_left_idx, second_right_idx):
    # 对比两个切割区间大小返回更小的切割区间
    if first_right_idx - first_left_idx < second_right_idx - second_left_idx:
        return [first_left_idx, first_right_idx]
    else:
        return [second_left_idx, second_right_idx]


def get_string_from_bounds(string, bounds):
    start, end = bounds
    # 如果结束区间仍然是无穷大, 则表示没有找到合适的结果, 返回空的字符串
    if end == float("inf"):
        return ""
    return string[start: end + 1]


def increase_char_count(char, char_counts):
    if char not in char_counts:
        char_counts[char] = 0
    char_counts[char] += 1


def decrease_char_count(char, char_counts):
    char_counts[char] -= 1


if __name__ == '__main__':
    print(smallest_substring_containing('abcd$ef$axbdc$', 'd$abf'))
```

```cpp
#include <iostream>
#include <climits>
#include <string>
#include <unordered_map>
#include <vector>

using namespace std;

string smallestSubstringContaining(string bigString, string smallString);
unordered_map<char, int> getCharCounts(string str);
vector<int> getSubstringBounds(string str, unordered_map<char, int> targetCharCounts);
vector<int> getCloserBounds(int idx1, int idx2, int idx3, int idx4);
string getStringFromBounds(string str, vector<int> bounds);
void increaseCharCount(char c, unordered_map<char, int> &charCounts);
void decreaseCharCount(char c, unordered_map<char, int> &charCounts);

// O(b + s) time | O(b + s) space - where b is the length of the big
// input string and s is the length of the small input string
string smallestSubstringContaining(string bigString, string smallString)
{
    unordered_map<char, int> targetCharCounts = getCharCounts(smallString);
    vector<int> substringBounds = getSubstringBounds(bigString, targetCharCounts);
    return getStringFromBounds(bigString, substringBounds);
}

unordered_map<char, int> getCharCounts(string str)
{
    unordered_map<char, int> charCounts;
    for (auto c : str)
    {
        increaseCharCount(c, charCounts);
    }
    return charCounts;
}

vector<int> getSubstringBounds(string str, unordered_map<char, int> targetCharCounts)
{
    vector<int> substringBounds = {0, INT_MAX};
    unordered_map<char, int> substringCharCounts;
    int numUniqueChars = targetCharCounts.size();
    int numUniqueCharsDone = 0;
    int leftIdx = 0;
    int rightIdx = 0;
    // Move the rightIdx to the right in the string until you've counted
    // all of the target characters enough times.
    const int strSize = str.size();
    while (rightIdx < strSize)
    {
        char rightChar = str[rightIdx];
        if (targetCharCounts.find(rightChar) == targetCharCounts.end())
        {
            rightIdx++;
            continue;
        }
        increaseCharCount(rightChar, substringCharCounts);
        if (substringCharCounts[rightChar] == targetCharCounts[rightChar])
        {
            numUniqueCharsDone++;
        }
        // Move the leftIdx to the right in the string until you no longer
        // have enough of the target characters in between the leftIdx and
        // the rightIdx. Update the substringBounds accordingly.
        while (numUniqueCharsDone == numUniqueChars && leftIdx <= rightIdx)
        {
            substringBounds = getCloserBounds(leftIdx, rightIdx, substringBounds[0],
                                              substringBounds[1]);
            char leftChar = str[leftIdx];
            if (targetCharCounts.find(leftChar) == targetCharCounts.end())
            {
                leftIdx++;
                continue;
            }
            if (substringCharCounts[leftChar] == targetCharCounts[leftChar])
            {
                numUniqueCharsDone--;
            }
            decreaseCharCount(leftChar, substringCharCounts);
            leftIdx++;
        }
        rightIdx++;
    }
    return substringBounds;
}

vector<int> getCloserBounds(int idx1, int idx2, int idx3, int idx4)
{
    return idx2 - idx1 < idx4 - idx3 ? vector<int>{idx1, idx2}
                                     : vector<int>{idx3, idx4};
}

string getStringFromBounds(string str, vector<int> bounds)
{
    int start = bounds[0];
    int end = bounds[1];
    if (end == INT_MAX)
        return "";
    return str.substr(start, end - start + 1);
}

void increaseCharCount(char c, unordered_map<char, int> &charCounts)
{
    if (charCounts.find(c) == charCounts.end())
    {
        charCounts[c] = 1;
    }
    else
    {
        charCounts[c]++;
    }
}

void decreaseCharCount(char c, unordered_map<char, int> &charCounts)
{
    charCounts[c]--;
}

int main()
{
    cout << smallestSubstringContaining("abcd$ef$axbdc$", "d$abf");
    return 0;
}
```

## 最长匹配括号数

给定一个只包括左括号`(`和右括号`)`的字符串，找出字符串中最长的匹配括号，如`())(()))(`中最长的匹配括号是索引`3`到索引`6`，即`(())`，数量为`4`

```python
# O(n^3) time | O(n) space - where n is the length of the input string
def longest_balanced_substring1(string):
    # 最长平衡括号数
    max_length = 0
    # 穷举法, 遍历所有的子字符串
    for i in range(len(string)):
        # 因为括号至少需要两个才是成对的, 这里的起始值和步长都是 2
        for j in range(i + 2, len(string) + 1, 2):
            if is_balanced(string[i:j]):
                current_length = j - i
                max_length = max(max_length, current_length)

    return max_length


def is_balanced(string):
    open_parens_stack = []

    for char in string:
        # 左括号入栈
        if char == "(":
            open_parens_stack.append("(")
        # 栈非空则出栈
        elif len(open_parens_stack) > 0:
            open_parens_stack.pop()
        # 栈为空表示存在不平衡的括号
        else:
            return False
    # 遍历结束后栈非空也表示存在平衡的括号
    return len(open_parens_stack) == 0


# O(n) time | O(n) space - where n is the length of the input string
def longest_balanced_substring2(string):
    # 最长平衡括号数
    max_length = 0
    # 栈, 记录字符所在的索引位置
    idx_stack = [-1]

    for i in range(len(string)):
        # 遇到左括号入栈
        if string[i] == "(":
            idx_stack.append(i)
        else:
            # 遇到右括号出栈
            idx_stack.pop()
            # 如果栈为空则入栈
            if len(idx_stack) == 0:
                idx_stack.append(i)
            # 如果栈非空, 表明有成对的括号出现
            else:
                # 对应上一次记录的左括号出现的索引位置, 也就是栈顶的记录索引
                balanced_substring_start_idx = idx_stack[len(idx_stack) - 1]
                # 计算括号数量 (成对括号的数量总会是偶数)
                current_length = i - balanced_substring_start_idx
                # 更新最大的记录长度
                max_length = max(max_length, current_length)

    return max_length


# O(n) time | O(1) space - where n is the length of the input string
def longest_balanced_substring3(string):
    # 最长平衡括号数
    max_length = 0
    # 记录左括号数量
    opening_count = 0
    # 记录右括号数量
    closing_count = 0

    for char in string:
        # 分别递增记录左右括号数量
        if char == "(":
            opening_count += 1
        else:
            closing_count += 1
        # 判断左右括号数量是否相等, 相等则说明存在左右平衡的括号
        if opening_count == closing_count:
            # 左右括号是成对出现的, 且左括号与右括号数量相等, 直接乘于 2 计算括号数量
            # 并更新最长平衡括号数
            max_length = max(max_length, closing_count * 2)
        # 右括号数量大于记录的左括号数量, 说明存在无法构成平衡的括号
        # 重置左右括号的记录数量
        elif closing_count > opening_count:
            opening_count = 0
            closing_count = 0

    opening_count = 0
    closing_count = 0
    # 方形相反从后向前判断
    for i in reversed(range(len(string))):
        char = string[i]

        if char == "(":
            opening_count += 1
        else:
            closing_count += 1

        if opening_count == closing_count:
            max_length = max(max_length, opening_count * 2)
        # 左括号数量大于记录的右括号数量, 说明存在无法构成平衡的括号
        # 重置左右括号的记录数量 (这里的方向相反所以判断也是相反)
        elif opening_count > closing_count:
            opening_count = 0
            closing_count = 0

    return max_length


# O(n) time | O(1) space - where n is the length of the input string
def longest_balanced_substring4(string):
    # 方法 3 的优化版本, 遍历计算括号数量作为一个公共方法, 传入遍历的方向以计算最长平衡括号数量
    return max(
        get_longest_balanced_in_direction(string, True),
        get_longest_balanced_in_direction(string, False),
    )


def get_longest_balanced_in_direction(string, left_to_right):
    # 根据遍历方向来初始化
    # 初始化括号记录数的判断条件, 从左向右为左括号, 否则为右括号
    opening_parens = "(" if left_to_right else ")"
    # 开始位置
    start_idx = 0 if left_to_right else len(string) - 1
    # 步长, 负数表示反向遍历
    step = 1 if left_to_right else -1

    max_length = 0

    opening_count = 0
    closing_count = 0

    idx = start_idx
    # 因为不知道具体的遍历方向, 所以这里判断索引在数组的范围内则持续循环
    while 0 <= idx < len(string):
        char = string[idx]
        # 分别累加计算左右括号数量
        if char == opening_parens:
            opening_count += 1
        else:
            closing_count += 1
        # 左右括号相等则计算并更新最长平衡括号数
        if opening_count == closing_count:
            max_length = max(max_length, closing_count * 2)
        # 存在括号不平衡的情况则重置记录数
        elif closing_count > opening_count:
            opening_count = 0
            closing_count = 0
        # 根据步长递增或递减
        idx += step

    return max_length


if __name__ == '__main__':
    source = '())(()))('
    print(longest_balanced_substring1(source))
    print(longest_balanced_substring2(source))
    print(longest_balanced_substring3(source))
    print(longest_balanced_substring4(source))
```

```cpp
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

bool isBalanced(string str);

// O(n^3) time | O(n) space - where n is the length of the input string
int longestBalancedSubstring1(string str)
{
    int maxLength = 0;
    const int strLength = str.length();
    for (int i = 0; i < strLength; i++)
    {
        for (int j = i + 2; j < strLength + 1; j++)
        {
            if (isBalanced(str.substr(i, j - i)))
            {
                int currentLength = j - i;
                maxLength = max(maxLength, currentLength);
            }
        }
    }
    return maxLength;
}

bool isBalanced(string str)
{
    vector<char> openParensStack;

    for (char c : str)
    {
        if (c == '(')
        {
            openParensStack.push_back('(');
        }
        else if (openParensStack.size() > 0)
        {
            openParensStack.pop_back();
        }
        else
        {
            return false;
        }
    }

    return openParensStack.size() == 0;
}

// O(n) time | O(n) space - where n is the length of the input string
int longestBalancedSubstring2(string str)
{
    int maxLength = 0;
    vector<int> idxStack = {-1};
    const int strLength = str.length();
    for (int i = 0; i < strLength; i++)
    {
        if (str[i] == '(')
        {
            idxStack.push_back(i);
        }
        else
        {
            idxStack.pop_back();
            if (idxStack.size() == 0)
            {
                idxStack.push_back(i);
            }
            else
            {
                int balancedSubstringStartIdx = idxStack[idxStack.size() - 1];
                int currentLength = i - balancedSubstringStartIdx;
                maxLength = max(maxLength, currentLength);
            }
        }
    }
    return maxLength;
}

// O(n) time | O(1) space - where n is the length of the input string
int longestBalancedSubstring3(string str)
{
    int maxLength = 0;

    int openingCount = 0;
    int closingCount = 0;

    for (char c : str)
    {
        if (c == '(')
        {
            openingCount++;
        }
        else
        {
            closingCount++;
        }

        if (openingCount == closingCount)
        {
            maxLength = max(maxLength, closingCount * 2);
        }
        else if (closingCount > openingCount)
        {
            openingCount = 0;
            closingCount = 0;
        }
    }

    openingCount = 0;
    closingCount = 0;
    const int strLength = str.length();
    for (int i = strLength - 1; i >= 0; i--)
    {
        char c = str[i];

        if (c == '(')
        {
            openingCount++;
        }
        else
        {
            closingCount++;
        }

        if (openingCount == closingCount)
        {
            maxLength = max(maxLength, openingCount * 2);
        }
        else if (openingCount > closingCount)
        {
            openingCount = 0;
            closingCount = 0;
        }
    }

    return maxLength;
}

int getLongestBalancedInDirection(string str, bool leftToRight);

// O(n) time | O(1) space - where n is the length of the input string
int longestBalancedSubstring4(string str)
{
    return max(getLongestBalancedInDirection(str, true),
               getLongestBalancedInDirection(str, false));
}

int getLongestBalancedInDirection(string str, bool leftToRight)
{
    const int strLength = str.length();
    char openingParens = leftToRight ? '(' : ')';
    int startIdx = leftToRight ? 0 : strLength - 1;
    int step = leftToRight ? 1 : -1;

    int maxLength = 0;

    int openingCount = 0;
    int closingCount = 0;

    int idx = startIdx;
    while (idx >= 0 && idx < strLength)
    {
        char c = str[idx];

        if (c == openingParens)
        {
            openingCount++;
        }
        else
        {
            closingCount++;
        }

        if (openingCount == closingCount)
        {
            maxLength = max(maxLength, closingCount * 2);
        }
        else if (closingCount > openingCount)
        {
            openingCount = 0;
            closingCount = 0;
        }

        idx += step;
    }

    return maxLength;
}

int main()
{
    string source = "())(()))(";
    cout << longestBalancedSubstring1(source) << endl;
    cout << longestBalancedSubstring2(source) << endl;
    cout << longestBalancedSubstring3(source) << endl;
    cout << longestBalancedSubstring4(source) << endl;
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

```cpp
#include <iostream>
#include <unordered_set>
#include <unordered_map>
using namespace std;

int countCharacterFrequency(char character, string target);

// O(c * (n + m)) time | O(c) space - where n is the number of characters, m is
// the length of the document, and c is the number of unique characters in the
// document
bool generateDocument1(string characters, string document)
{
    unordered_set<char> alreadyCounted;

    for (auto character : document)
    {
        if (alreadyCounted.find(character) != alreadyCounted.end())
            continue;

        auto documentFrequency = countCharacterFrequency(character, document);
        auto charactersFrequency = countCharacterFrequency(character, characters);
        if (documentFrequency > charactersFrequency)
            return false;

        alreadyCounted.insert(character);
    }

    return true;
}

int countCharacterFrequency(char character, string target)
{
    int frequency = 0;
    for (auto c : target)
    {
        if (c == character)
            frequency++;
    }

    return frequency;
}

// O(n + m) time | O(c) space - where n is the number of characters, m is
// the length of the document, and c is the number of unique characters in the
// characters string
bool generateDocument2(string characters, string document)
{
    unordered_map<char, int> characterCounts;

    for (auto character : characters)
    {
        if (characterCounts.find(character) == characterCounts.end())
            characterCounts[character] = 0;

        characterCounts[character]++;
    }

    for (auto character : document)
    {
        if (characterCounts.find(character) == characterCounts.end() ||
            characterCounts[character] == 0)
            return false;

        characterCounts[character]--;
    }

    return true;
}

int main()
{
    cout << generateDocument1("aheaollabbhb", "hello") << endl;
    cout << generateDocument2("aheaollabbhb", "hello") << endl;
    return 0;
}
```

## 第一个不重复字符

```python
# O(n^2) time | O(1) space - where n is the length of the input string
def first_non_repeating_character1(string):
    for idx in range(len(string)):
        found_duplicate = False
        for idx2 in range(len(string)):
            if string[idx] == string[idx2] and idx != idx2:
                found_duplicate = True

        if not found_duplicate:
            return idx

    return -1


# O(n) time | O(1) space - where n is the length of the input string
# The constant space is because the input string only has lowercase
# English-alphabet letters; thus, our hash table will never have more
# than 26 character frequencies.
def first_non_repeating_character2(string):
    character_frequencies = {}

    for character in string:
        character_frequencies[character] = character_frequencies.get(character, 0) + 1

    for idx in range(len(string)):
        character = string[idx]
        if character_frequencies[character] == 1:
            return idx

    return -1


if __name__ == '__main__':
    print(first_non_repeating_character1('faadabcbbebdf'))
    print(first_non_repeating_character2('faadabcbbebdf'))
```

```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

// O(n^2) time | O(1) space - where n is the length of the input string
int firstNonRepeatingCharacter1(string string)
{
    for (size_t idx = 0; idx < string.size(); idx++)
    {
        int foundDuplicate = false;
        for (size_t idx2 = 0; idx2 < string.size(); idx2++)
        {
            if (string[idx] == string[idx2] && idx != idx2)
                foundDuplicate = true;
        }

        if (!foundDuplicate)
            return idx;
    }

    return -1;
}

// O(n) time | O(1) space - where n is the length of the input string
// The constant space is because the input string only has lowercase
// English-alphabet letters; thus, our hash table will never have more
// than 26 character frequencies.
int firstNonRepeatingCharacter2(string string)
{
    unordered_map<char, int> characterFrequencies;

    for (auto character : string)
    {
        if (characterFrequencies.find(character) == characterFrequencies.end())
            characterFrequencies[character] = 0;
        characterFrequencies[character]++;
    }

    for (size_t idx = 0; idx < string.size(); idx++)
    {
        char character = string[idx];
        if (characterFrequencies[character] == 1)
            return idx;
    }

    return -1;
}

int main()
{
    cout << firstNonRepeatingCharacter1("faadabcbbebdf") << endl;
    cout << firstNonRepeatingCharacter2("faadabcbbebdf") << endl;
    return 0;
}
```

## 有效 IP 地址

```python
# O(1) time | O(1) space
# input string must be less than or equal to 12
def valid_ip_addresses(string):
    ip_addresses_found = []

    length = len(string)
    for i in range(1, min(length, 4)):
        current_ip_address_parts = ["", "", "", ""]

        current_ip_address_parts[0] = string[:i]
        if not is_valid_part(current_ip_address_parts[0]):
            continue

        for j in range(i + 1, min(length, i + 4)):
            current_ip_address_parts[1] = string[i:j]
            if not is_valid_part(current_ip_address_parts[1]):
                continue

            for k in range(j + 1, min(length, j + 4)):
                current_ip_address_parts[2] = string[j:k]
                current_ip_address_parts[3] = string[k:]

                if is_valid_part(current_ip_address_parts[2]) and is_valid_part(current_ip_address_parts[3]):
                    ip_addresses_found.append(".".join(current_ip_address_parts))

    return ip_addresses_found


def is_valid_part(string):
    string_as_int = int(string)
    if string_as_int > 255:
        return False

    return len(string) == len(str(string_as_int))  # check for leading 0


if __name__ == '__main__':
    print(valid_ip_addresses('1921680'))
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>

using namespace std;

bool isValidPart(string str);
string join(vector<string> strings);

// O(1) time | O(1) space
// input string must be less than or equal to 12
vector<string> validIPAddresses(string str)
{
    vector<string> ipAddressesFound;

    for (int i = 1; i < min((int)str.length(), 4); i++)
    {
        vector<string> currentIPAddressParts = {"", "", "", ""};

        currentIPAddressParts[0] = str.substr(0, i);
        if (!isValidPart(currentIPAddressParts[0]))
        {
            continue;
        }

        for (int j = i + 1; j < i + min((int)str.length() - i, 4); j++)
        {
            currentIPAddressParts[1] = str.substr(i, j - i);
            if (!isValidPart(currentIPAddressParts[1]))
            {
                continue;
            }

            for (int k = j + 1; k < j + min((int)str.length() - j, 4); k++)
            {
                currentIPAddressParts[2] = str.substr(j, k - j);
                currentIPAddressParts[3] = str.substr(k);

                if (isValidPart(currentIPAddressParts[2]) &&
                    isValidPart(currentIPAddressParts[3]))
                {
                    ipAddressesFound.push_back(join(currentIPAddressParts));
                }
            }
        }
    }

    return ipAddressesFound;
}

bool isValidPart(string str)
{
    int stringAsInt = stoi(str);

    if (stringAsInt > 255)
    {
        return false;
    }

    return str.length() == to_string(stringAsInt).length(); // check for leading 0
}

string join(vector<string> strings)
{
    string s;
    for (size_t l = 0; l < strings.size(); l++)
    {
        s += strings[l];
        if (l < strings.size() - 1)
        {
            s += ".";
        }
    }
    return s;
}

int main()
{
    vector<string> result = validIPAddresses("1921680");
    for (const string element : result)
    {
        cout << element << endl;
    }
    return 0;
}
```

## 反转单词位置

```python
# O(n) time | O(n) space - where n is the length of the string
def reverse_words_in_string1(string):
    words = []
    start_of_word = 0
    for idx in range(len(string)):
        character = string[idx]

        if character == " ":
            words.append(string[start_of_word:idx])
            start_of_word = idx
        elif string[start_of_word] == " ":
            words.append(" ")
            start_of_word = idx

    words.append(string[start_of_word:])

    reverse_list(words)
    return "".join(words)


def reverse_list(array):
    start, end = 0, len(array) - 1
    while start < end:
        array[start], array[end] = array[end], array[start]
        start += 1
        end -= 1


# O(n) time | O(n) space - where n is the length of the string
def reverse_words_in_string2(string):
    characters = [char for char in string]
    reverse_list_range(characters, 0, len(characters) - 1)

    start_of_word = 0
    while start_of_word < len(characters):
        end_of_word = start_of_word
        while end_of_word < len(characters) and characters[end_of_word] != " ":
            end_of_word += 1

        reverse_list_range(characters, start_of_word, end_of_word - 1)
        start_of_word = end_of_word + 1

    return "".join(characters)


def reverse_list_range(array, start, end):
    while start < end:
        array[start], array[end] = array[end], array[start]
        start += 1
        end -= 1


# O(n) time | O(n) space
def reverse_words_in_string3(string):
    length = len(string)
    if not length:
        return string
    characters = [' ' for _ in range(length)]
    idx = 0
    while idx < length:
        if string[idx] == ' ':
            idx += 1
            continue
        word_start_idx = idx
        while idx < length and string[idx] != ' ':
            idx += 1
        word_idx = length - idx
        for i in range(word_start_idx, idx):
            characters[word_idx] = string[i]
            word_idx += 1
        idx += 1

    return ''.join(characters)


# O(n) time | O(n) space
def reverse_words_in_string4(string):
    length = len(string)
    if not length:
        return string
    characters = []
    i = 0
    while i < length:
        character = string[i]
        if character == ' ':
            space_idx = i
            while i < length and string[i] == ' ':
                i += 1
            characters.insert(0, string[space_idx:i])
            continue
        word_idx = i
        while i < length and string[i] != ' ':
            i += 1
        characters.insert(0, string[word_idx:i])

    return ''.join(characters)


if __name__ == '__main__':
    print(reverse_words_in_string1('  System Expert is require!'))
    print(reverse_words_in_string2('System   Expert is require!  '))
    print(reverse_words_in_string3('  System Expert is require!'))
    print(reverse_words_in_string4('System   Expert is require!  '))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

void reverseList(vector<string> &list);

// O(n) time | O(n) space - where n is the length of the string
string reverseWordsInString1(string str)
{
    vector<string> words;
    int startOfWord = 0;
    for (size_t idx = 0; idx < str.size(); idx++)
    {
        char character = str[idx];

        if (character == ' ')
        {
            words.push_back(str.substr(startOfWord, idx - startOfWord));
            startOfWord = idx;
        }
        else if (str[startOfWord] == ' ')
        {
            words.push_back(" ");
            startOfWord = idx;
        }
    }

    words.push_back(str.substr(startOfWord));

    reverseList(words);
    string output;
    for (auto word : words)
    {
        output += word;
    }
    return output;
}

void reverseList(vector<string> &list)
{
    size_t start = 0;
    size_t end = list.size() - 1;
    while (start < end)
    {
        string temp = list[start];
        list[start] = list[end];
        list[end] = temp;
        start++;
        end--;
    }
}

void reverseListRange(vector<char> &list, int start, int end);

// O(n) time | O(n) space - where n is the length of the string
string reverseWordsInString2(string str)
{
    vector<char> characters;
    for (auto character : str)
    {
        characters.push_back(character);
    }
    reverseListRange(characters, 0, characters.size() - 1);

    size_t startOfWord = 0;
    while (startOfWord < characters.size())
    {
        size_t endOfWord = startOfWord;
        while (endOfWord < characters.size() && characters[endOfWord] != ' ')
        {
            endOfWord++;
        }

        reverseListRange(characters, startOfWord, endOfWord - 1);
        startOfWord = endOfWord + 1;
    }

    string output;
    for (auto character : characters)
    {
        output += character;
    }
    return output;
}

void reverseListRange(vector<char> &list, int start, int end)
{
    while (start < end)
    {
        char temp = list[start];
        list[start] = list[end];
        list[end] = temp;
        start++;
        end--;
    }
}

// O(n) time | O(n) space
string reverseWordsInString3(string str)
{
    size_t length = str.length();
    if (length <= 0)
    {
        return str;
    }
    vector<char> characters(length, ' ');
    size_t idx = 0;
    while (idx < length)
    {
        if (str[idx] == ' ')
        {
            idx++;
            continue;
        }
        size_t wordStartIdx = idx;
        while (idx < length && str[idx] != ' ')
        {
            idx++;
        }
        size_t wordIdx = length - idx;
        for (size_t i = wordStartIdx; i < idx; i++)
        {
            characters[wordIdx] = str[i];
            wordIdx++;
        }
        idx++;
    }
    return string(characters.begin(), characters.end());
}

int main()
{
    cout << reverseWordsInString1("  System Expert is require!") << endl;
    cout << reverseWordsInString2("System   Expert is require!  ") << endl;
    cout << reverseWordsInString3("  System   Expert is yap!  ") << endl;
    return 0;
}
```

## 组成单词最少字符

给定多个单词，找出组成每一个单词的最少字符数，注意单词可能存在两个以上相同的字符，
如 `["AC", "AAAC"]` 所需的字符最少为 `['A', 'A', 'A', 'C']`

```python
# O(n * l) time | O(c) space - where n is the number of words,
# l is the length of the longest word, and c is the number of
# unique characters across all words
# See notes under video explanation for details about the space complexity.
def minimum_characters_for_words1(words):
    maximum_character_frequencies = {}

    for word in words:
        character_frequencies = count_character_frequencies(word)
        update_maximum_frequencies(character_frequencies, maximum_character_frequencies)

    return make_array_from_character_frequencies(maximum_character_frequencies)


def count_character_frequencies(string):
    character_frequencies = {}

    for character in string:
        if character not in character_frequencies:
            character_frequencies[character] = 0

        character_frequencies[character] += 1

    return character_frequencies


def update_maximum_frequencies(frequencies, maximum_frequencies):
    for character in frequencies:
        frequency = frequencies[character]

        if character in maximum_frequencies:
            maximum_frequencies[character] = max(frequency, maximum_frequencies[character])
        else:
            maximum_frequencies[character] = frequency


def make_array_from_character_frequencies(character_frequencies):
    characters = []

    for character in character_frequencies:
        frequency = character_frequencies[character]

        for _ in range(frequency):
            characters.append(character)

    return characters


# O(n * l) time | O(c) space
def minimum_characters_for_words2(words):
    characters = {}
    for word in words:
        current_characters = {}
        for character in word:
            if character not in current_characters:
                current_characters[character] = 1
            else:
                current_characters[character] += 1

            if character not in characters:
                characters[character] = 1
            elif current_characters[character] > characters[character]:
                characters[character] = current_characters[character]

    characters_for_words = []
    for key, value in characters.items():
        for _ in range(value):
            characters_for_words.append(key)
    return characters_for_words


if __name__ == '__main__':
    print(minimum_characters_for_words1(["this", "that", "did", "deed", "them!", "a"]))
    print(minimum_characters_for_words2(["this", "that", "did", "deed", "them!", "a"]))
```

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <algorithm>
using namespace std;

unordered_map<char, int> countCharacterFrequencies(const string &string);
void updateMaximumFrequencies(const unordered_map<char, int> &frequencies,
                              unordered_map<char, int> &maximumFrequencies);
vector<char> makeArrayFromCharacterFrequencies(
    const unordered_map<char, int> &characterFrequencies);

// O(n * l) time | O(c) space - where n is the number of words,
// l is the length of the longest word, and c is the number of
// unique characters across all words
// See notes under video explanation for details about the space complexity.
vector<char> minimumCharactersForWords(vector<string> words)
{
    unordered_map<char, int> maximumCharacterFrequencies;

    for (auto const &word : words)
    {
        auto characterFrequencies = countCharacterFrequencies(word);
        updateMaximumFrequencies(characterFrequencies, maximumCharacterFrequencies);
    }

    return makeArrayFromCharacterFrequencies(maximumCharacterFrequencies);
}

unordered_map<char, int> countCharacterFrequencies(const string &string)
{
    unordered_map<char, int> characterFrequencies;

    for (auto character : string)
    {
        if (characterFrequencies.find(character) == characterFrequencies.end())
        {
            characterFrequencies[character] = 0;
        }

        characterFrequencies[character] += 1;
    }

    return characterFrequencies;
}

void updateMaximumFrequencies(const unordered_map<char, int> &frequencies,
                              unordered_map<char, int> &maximumFrequencies)
{
    for (auto iter = frequencies.cbegin(); iter != frequencies.cend(); ++iter)
    {
        const char character = iter->first;
        const int frequency = iter->second;
        if (maximumFrequencies.find(character) != maximumFrequencies.end())
        {
            maximumFrequencies[character] =
                max(frequency, maximumFrequencies[character]);
        }
        else
        {
            maximumFrequencies[character] = frequency;
        }
    }
}

vector<char> makeArrayFromCharacterFrequencies(
    const unordered_map<char, int> &characterFrequencies)
{
    vector<char> characters;

    for (auto iter = characterFrequencies.cbegin(); iter != characterFrequencies.cend(); ++iter)
    {
        const char character = iter->first;
        const int frequency = iter->second;
        for (int idx = 0; idx < frequency; idx++)
        {
            characters.push_back(character);
        }
    }

    return characters;
}

int main()
{
    vector<string> source = {"this", "that", "did", "deed", "them!", "a"};
    vector<char> result = minimumCharactersForWords(source);
    for (const char element : result)
    {
        cout << element << " ";
    }
    return 0;
}
```

## KMP 查找子字符串

Knuth-Morris-Pratt 字符串查找算法，可在一个字符串 S 内查找一个词 W 的出现位置。
一个词在不匹配时本身就包含足够的信息来确定下一个匹配可能的开始位置，此算法利用这一特性以避免重新检查先前匹配的字符。

```python
# O(m) time | O(m) space
def get_pattern(substring):
    # 初始化模式数组, 全部标记为 -1, 表示没有索引关联
    pattern = [-1 for _ in substring]
    substring_length = len(substring)
    # 创建两个指针, 第一个指针为 i, 从第二个位置开始
    # 因为不需要重复比较第一个位置已经是相同的字符
    # 第二个指针 j 则从开头开始
    i, j = 1, 0
    while i < substring_length:
        # 如果两个位置的字符相同
        if substring[i] == substring[j]:
            # 记录相同的字符到数组中
            pattern[i] = j
            # 移动两个指针
            i += 1
            j += 1
        # 如果字符不相同, 且第二个指针没有在起始位置
        # 移动第二个指针回到上一次匹配位置的下一个位置
        elif j > 0:
            j = pattern[j - 1] + 1
        # 上面两个条件都不满足, 则继续移动第一个指针
        else:
            i += 1
    return pattern


# O(n) time | O(1) space
def does_match(string, substring, pattern):
    substring_length = len(substring)
    string_length = len(string)
    # 第一个指针 i 指向原始字符串开头
    # 第二个指针 j 指向待查找字符串开头
    i, j = 0, 0
    # 循环原始字符串, 判断原始字符串剩余未匹配的字符数量与
    # 待查找的字符串字符数量不足以比较时则直接结束查找
    while i + substring_length - j <= string_length:
        # 如果原始字符串与待查找字符串的字符相同
        if string[i] == substring[j]:
            # 如果子字符串的指针已经走到结束位置, 说明存在匹配的子字符串, 返回匹配成功
            if j == substring_length - 1:
                # return i - substring_length + 1
                return True
            # 移动两个指针
            i += 1
            j += 1
        # 如果字符不相同, 移动第二个指针回到上一次匹配位置的下一个位置
        elif j > 0:
            j = pattern[j - 1] + 1
        # 上面两个条件都不满足, 则继续移动第一个指针
        else:
            i += 1
    # return -1
    return False


# O(n + m) time | O(m) space
def knuth_morris_pratt_algorithm(string, substring):
    # 如果长度不符合比较要求则直接返回
    if not len(string) or not len(substring) or len(string) < len(substring):
        return False
    # 创建一个匹配模式
    pattern = get_pattern(substring)
    # 根据匹配模式来搜索子字符串
    # 由于创建了一个模式数组, 加快了匹配的速度, 避免重复地从头开始匹配的步骤
    # 让时间复杂的从 O(n^2) 或 O(n * m) 降低到 O(n + m)
    return does_match(string, substring, pattern)


if __name__ == '__main__':
    print(knuth_morris_pratt_algorithm('alg normal algorithm is', 'algo'))
    print(knuth_morris_pratt_algorithm('abxabcabcaby', 'abcaby'))
```

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;

vector<int> buildPattern(string substr);
bool doesMatch(string str, string substr, vector<int> pattern);

// O(n + m) time | O(m) space
bool knuthMorrisPrattAlgorithm(string str, string substr)
{
    vector<int> pattern = buildPattern(substr);
    return doesMatch(str, substr, pattern);
}

vector<int> buildPattern(string substr)
{
    vector<int> pattern(substr.size(), -1);
    int j = 0;
    int i = 1;
    const int substringLength = substr.size();
    while (i < substringLength)
    {
        if (substr[i] == substr[j])
        {
            pattern[i] = j;
            i++;
            j++;
        }
        else if (j > 0)
        {
            j = pattern[j - 1] + 1;
        }
        else
        {
            i++;
        }
    }
    return pattern;
}

bool doesMatch(string str, string substr, vector<int> pattern)
{
    int i = 0;
    int j = 0;
    const int substringLength = substr.size();
    const int stringLength = str.size();
    while (i + substringLength - j <= stringLength)
    {
        if (str[i] == substr[j])
        {
            if (j == substringLength - 1)
                return true;
            i++;
            j++;
        }
        else if (j > 0)
        {
            j = pattern[j - 1] + 1;
        }
        else
        {
            i++;
        }
    }
    return false;
}

int main()
{
    cout << knuthMorrisPrattAlgorithm("abcdefaefbcdefefabcdef", "fbcdefef") << endl;
    return 0;
}
```

## 计算字符串排列数

给定两个字符串，一个长度较大的字符串和一个长度较小的字符串，计算在大字符串中可以通过字符的排列组合出小字符串的所有子字符串数量

```python
# O(b + s) time | O(s) space - where b is the length of the big
# input string and s is the length of the small input string
def count_contained_permutations(big_string, small_string):
    # 统计小字符串中的字符数量
    small_string_char_counts = get_char_counts(small_string)
    # 计算小字符串中不重复字符数量
    num_unique_chars = len(small_string_char_counts.keys())
    # 记录在大字符串中找到的字符统计
    running_char_counts = {}
    # 记录符合排列组合的数量
    permutations_count = 0
    # 记录当前完成的每个字符计数数量
    num_unique_chars_done = 0
    # 分为两个指针位置
    left_idx = 0
    right_idx = 0
    while right_idx < len(big_string):
        # 先处理右指针指向的字符
        right_char = big_string[right_idx]
        # 如果包含在期望的字符列表中
        if right_char in small_string_char_counts:
            # 累加当前记录的字符
            increase_char_count(right_char, running_char_counts)
            # 如果某个字符达到了预期的字符数量
            if running_char_counts[right_char] == small_string_char_counts[right_char]:
                # 累加已统计完成的不重复字符统计
                num_unique_chars_done += 1
        # 如果累计已统计完的字符数与期望的总计不重复字符树一致
        if num_unique_chars_done == num_unique_chars:
            # 累加符合排列组合的数量
            permutations_count += 1
        # 累加右指针
        right_idx += 1
        # 判断左右指针的距离是否等于小字符串长度
        should_increment_left_idx = right_idx - left_idx == len(small_string)
        # 不符合则跳过, 符合则走到下面的逻辑
        if not should_increment_left_idx:
            continue

        left_char = big_string[left_idx]
        # 判断左指针指向的, 如果包含在期望的字符列表中
        if left_char in small_string_char_counts:
            # 如果某个字符达到了预期的字符数量
            if running_char_counts[left_char] == small_string_char_counts[left_char]:
                num_unique_chars_done -= 1
            # 递减当前记录的字符
            decrease_char_count(left_char, running_char_counts)
        # 移动左指针
        left_idx += 1

    return permutations_count


def get_char_counts(string):
    char_counts = {}
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
    print(count_contained_permutations('cbabcacabca', 'abc'))
```

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;

unordered_map<char, int> getCharCounts(string str);
void increaseCharCount(char c, unordered_map<char, int> &charCounts);
void decreaseCharCount(char c, unordered_map<char, int> &charCounts);

// O(b + s) time | O(s) space - where b is the length of the big
// input string and s is the length of the small input string
int countContainedPermutations(string bigString, string smallString)
{
    auto smallStringCharCounts = getCharCounts(smallString);
    const int numUniqueChars = smallStringCharCounts.size();

    unordered_map<char, int> runningCharCounts = {};
    int permutationsCount = 0;
    int numUniqueCharsDone = 0;
    int leftIdx = 0;
    int rightIdx = 0;
    const int bigStringSize = bigString.size();
    const int smallStringSize = smallString.size();
    while (rightIdx < bigStringSize)
    {
        auto rightChar = bigString[rightIdx];
        if (smallStringCharCounts.find(rightChar) != smallStringCharCounts.end())
        {
            increaseCharCount(rightChar, runningCharCounts);

            if (runningCharCounts[rightChar] == smallStringCharCounts[rightChar])
            {
                numUniqueCharsDone++;
            }
        }

        if (numUniqueCharsDone == numUniqueChars)
        {
            permutationsCount++;
        }
        rightIdx++;

        auto shouldIncrementLeftIdx = (rightIdx - leftIdx == smallStringSize);
        if (!shouldIncrementLeftIdx)
            continue;

        auto leftChar = bigString[leftIdx];
        if (smallStringCharCounts.find(leftChar) != smallStringCharCounts.end())
        {
            if (runningCharCounts[leftChar] == smallStringCharCounts[leftChar])
            {
                numUniqueCharsDone--;
            }

            decreaseCharCount(leftChar, runningCharCounts);
        }

        leftIdx++;
    }
    return permutationsCount;
}

unordered_map<char, int> getCharCounts(string str)
{
    unordered_map<char, int> charCounts = {};
    for (auto c : str)
    {
        increaseCharCount(c, charCounts);
    }
    return charCounts;
}

void increaseCharCount(char c, unordered_map<char, int> &charCounts)
{
    if (charCounts.find(c) == charCounts.end())
    {
        charCounts[c] = 0;
    }
    charCounts[c]++;
}

void decreaseCharCount(char c, unordered_map<char, int> &charCounts)
{
    if (charCounts.find(c) == charCounts.end())
    {
        charCounts[c] = 0;
    }
    charCounts[c]--;
}

int main()
{
    cout << countContainedPermutations("cbabcacabca", "abc");
    return 0;
}
```

Last Modified 2023-08-19

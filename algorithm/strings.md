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

void iterArray(vector<vector<string>> array)
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
    iterArray(groupAnagrams(source));
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
string underscorifySubstring(string str, string subStr) {
  vector<vector<int>> locations = collapse(getLocations(str, subStr));
  return underscorify(str, locations);
}

vector<vector<int>> getLocations(string str, string subStr) {
  vector<vector<int>> locations{};
  int startIdx = 0;
  const int strLength = str.length();
  const int subStrLength = subStr.length();
  while (startIdx < strLength) {
    int nextIdx = str.find(subStr, startIdx);
    if (nextIdx != string::npos) {
      locations.push_back(vector<int>{nextIdx, int(nextIdx + subStrLength)});
      startIdx = nextIdx + 1;
    } else {
      break;
    }
  }
  return locations;
}

vector<vector<int>> collapse(vector<vector<int>> locations) {
  if (locations.empty()) {
    return locations;
  }
  vector<vector<int>> newLocations{locations[0]};
  vector<int> *previous = &newLocations[0];
  for (size_t i = 1; i < locations.size(); i++) {
    vector<int> *current = &locations[i];
    if (current->at(0) <= previous->at(1)) {
      previous->at(1) = current->at(1);
    } else {
      newLocations.push_back(*current);
      previous = &newLocations[newLocations.size() - 1];
    }
  }
  return newLocations;
}

string underscorify(string str, vector<vector<int>> locations) {
  size_t locationsIdx = 0;
  int stringIdx = 0;
  bool inBetweenUnderscores = false;
  vector<string> finalChars{};
  int i = 0;
  const int strLength = str.length();
  while (stringIdx < strLength && locationsIdx < locations.size()) {
    if (stringIdx == locations[locationsIdx][i % 2]) {
      finalChars.push_back("_");
      inBetweenUnderscores = !inBetweenUnderscores;
      if (!inBetweenUnderscores) {
        locationsIdx++;
      }
      i++;
    }
    string s(1, str[stringIdx]);
    finalChars.push_back(s);
    stringIdx++;
  }
  if (locationsIdx < locations.size()) {
    finalChars.push_back("_");
  } else if (stringIdx < strLength) {
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

void iterArray(vector<string> array)
{
    for (const string element : array)
    {
        cout << element << endl;
    }
    cout << endl;
}

int main()
{
    iterArray(validIPAddresses("1921680"));
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
        # 如果两个位置的字符不相同, 移动第二个指针回到上一次匹配位置的下一个位置
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

Last Modified 2022-02-09

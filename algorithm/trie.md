# 字典树（前缀树）

## 构建字典树

```python
class SuffixTrie:
    def __init__(self, string):
        self.root = {}
        self.end_symbol = "*"
        self.populate_suffix_trie_from(string)

    # O(n^2) time | O(n^2) space
    def populate_suffix_trie_from(self, string):
        for idx in range(len(string)):
            self.insert_substring_at(idx, string)

    def insert_substring_at(self, starting_idx, string):
        node = self.root
        for i in range(starting_idx, len(string)):
            letter = string[i]
            if letter not in node:
                node[letter] = {}
            node = node[letter]
        node[self.end_symbol] = True

    # O(m) time | O(1) space
    def contains(self, string):
        node = self.root
        for letter in string:
            if letter not in node:
                return False
            node = node[letter]
        return self.end_symbol in node


if __name__ == '__main__':
    trie = SuffixTrie('invisible')
    print(trie.contains('visible'))
    print(trie.contains('sible'))
    print(trie.contains('inv'))
```

```cpp
#include <iostream>
#include <string>
#include <unordered_map>
using namespace std;

class TrieNode
{
public:
    unordered_map<char, TrieNode *> children;
};

class SuffixTrie
{
public:
    TrieNode *root;
    char endSymbol;

    SuffixTrie(string str)
    {
        this->root = new TrieNode();
        this->endSymbol = '*';
        this->populateSuffixTrieFrom(str);
    }

    // O(n^2) time | O(n^2) space
    void populateSuffixTrieFrom(string str)
    {
        for (size_t i = 0; i < str.length(); i++)
        {
            this->insertSubstringStartingAt(i, str);
        }
    }

    void insertSubstringStartingAt(int i, string str)
    {
        TrieNode *node = this->root;
        for (size_t j = i; j < str.length(); j++)
        {
            char letter = str[j];
            if (node->children.find(letter) == node->children.end())
            {
                TrieNode *newNode = new TrieNode();
                node->children.insert({letter, newNode});
            }
            node = node->children[letter];
        }
        node->children.insert({this->endSymbol, nullptr});
    }

    // O(m) time | O(1) space
    bool contains(string str)
    {
        TrieNode *node = this->root;
        for (char letter : str)
        {
            if (node->children.find(letter) == node->children.end())
            {
                return false;
            }
            node = node->children[letter];
        }
        return node->children.find(this->endSymbol) != node->children.end();
    }
};

int main()
{
    string source = "invisible";
    SuffixTrie trie(source);
    cout << trie.contains("visible") << endl;
    cout << trie.contains("ble") << endl;
    cout << trie.contains("inv") << endl;
    return 0;
}
```

## 多字符串匹配

```python
# O(bns) time | O(n) space
def multi_string_search1(big_string, small_strings):
    # 逐个判断字符串是否包含在较大的字符串中
    return [is_in_big_string(big_string, small_string) for small_string in small_strings]


def is_in_big_string(big_string, small_string):
    # 大字符串剩余的字符数量小于查找字符串的长度时则可以认为后面的字符不会再有匹配的可能
    for i in range(len(big_string) - len(small_string) + 1):
        # if i + len(small_string) > len(big_string):
        #     break
        # 如果找到了匹配则直接返回
        if is_in_big_string_helper(big_string, small_string, i):
            return True
    return False


def is_in_big_string_helper(big_string, small_string, start_idx):
    left_big_idx = start_idx
    right_big_idx = start_idx + len(small_string) - 1
    left_small_idx = 0
    right_small_idx = len(small_string) - 1
    # 从大字符串两边同时开始判断, 减少循环的次数
    while left_big_idx <= right_big_idx:
        # 左边或右边的字符存在不相等时返回否
        if (big_string[left_big_idx] != small_string[left_small_idx]
                or big_string[right_big_idx] != small_string[right_small_idx]):
            return False
        left_big_idx += 1
        right_big_idx -= 1
        left_small_idx += 1
        right_small_idx -= 1
    return True


# O(b^2 + ns) time | O(b^2 + n) space
def multi_string_search2(big_string, small_strings):
    # 将较大的字符串构建成字典树
    modified_suffix_trie = ModifiedSuffixTrie(big_string)
    # 逐个判断字符串是否包含在较大的字符串中
    return [modified_suffix_trie.contains(string) for string in small_strings]


class ModifiedSuffixTrie:
    def __init__(self, string):
        self.root = {}
        self.populate_modified_suffix_trie_from(string)

    def populate_modified_suffix_trie_from(self, string):
        # 考虑到字符串中可能存在相同的字符构成不同的子字符串
        # 所以需要遍历每一个字符来构建较为完整的字典树
        for i in range(len(string)):
            self.insert_substring_starting_at(i, string)

    def insert_substring_starting_at(self, i, string):
        node = self.root
        for j in range(i, len(string)):
            letters = string[j]
            if letters not in node:
                node[letters] = {}
            node = node[letters]

    def contains(self, string):
        node = self.root
        for letters in string:
            if letters not in node:
                return False
            node = node[letters]
        return True


# O(ns + bs) time | O(ns) space
def multi_string_search3(big_string, small_strings):
    trie = Trie()
    # 将每个小字符串构建到字典树中
    for string in small_strings:
        trie.insert(string)
    # 记录找到的子字符串
    contained_strings = {}
    # 遍历大字符串, 与字典树匹配
    for i in range(len(big_string)):
        find_small_strings_in(big_string, i, trie, contained_strings)
    # 将所有找到的小字符串标记并返回
    return [string in contained_strings for string in small_strings]


def find_small_strings_in(string, start_idx, trie, contained_strings):
    current_node = trie.root
    # 从指定位置开始查找
    for i in range(start_idx, len(string)):
        current_char = string[i]
        # 有字符不匹配则结束查找
        if current_char not in current_node:
            break
        current_node = current_node[current_char]
        if trie.end_symbol in current_node:
            # 如果到达了字典树中的某个末尾则表示找到了一个匹配的字符串
            # 添加到记录中
            contained_strings[current_node[trie.end_symbol]] = True


class Trie:
    def __init__(self):
        self.root = {}
        self.end_symbol = "*"

    def insert(self, string):
        current = self.root
        for i in range(len(string)):
            if string[i] not in current:
                current[string[i]] = {}
            current = current[string[i]]
        # 末尾位置记录字符串
        current[self.end_symbol] = string

    def add(self, idx, string):
        current = self.root
        for letters in string:
            if letters not in current:
                current[letters] = {}
            current = current[letters]
        # 末尾位置记录字符串相对于字符串数组的索引, 方便标记
        current[self.end_symbol] = idx


def match_in_small_strings(start_idx, big_string, trie_node, markers):
    # 从指定位置开始搜索
    for idx in range(start_idx, len(big_string)):
        letters = big_string[idx]
        # 有字符不匹配则结束搜索
        if letters not in trie_node:
            break
        trie_node = trie_node[letters]
        if '*' in trie_node:
            # 如果到达了字典树中的某个末尾则表示找到了一个匹配的字符串
            markers[trie_node['*']] = True

# O(ns + bs) time | O(ns) space
def multi_string_search4(big_string, small_strings):
    # 记录小字符串是否找到, 默认全部都是未找到
    markers = [False for _ in small_strings]
    trie = Trie()
    # 将每个小字符串构建到字典树中
    for idx, string in enumerate(small_strings):
        trie.add(idx, string)
    # 遍历大字符串, 与字典树匹配
    for idx in range(len(big_string)):
        # 如果能找到完全匹配的子字符串则修改标记
        match_in_small_strings(idx, big_string, trie.root, markers)
    return markers


if __name__ == '__main__':
    big_str = 'abcdefghijklmnopqrstuvwxyz'
    small_strs = ['bcdefghijklmnopqrstuvwxyz', 'abc', 'j', 'mnopqr', 'pqrstuvwxyz', 'xyzz', 'defh']
    print(multi_string_search1(big_str, small_strs))
    print(multi_string_search2(big_str, small_strs))
    print(multi_string_search3(big_str, small_strs))
    print(multi_string_search4(big_str, small_strs))
```

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <string>
using namespace std;

bool isInBigString(string bigString, string smallString);
bool isInBigStringHelper(string bigString, string smallString, int startIdx);

// O(bns) time | O(n) space
vector<bool> multiStringSearch1(string bigString, vector<string> smallStrings)
{
    vector<bool> solution;
    for (string smallString : smallStrings)
    {
        solution.push_back(isInBigString(bigString, smallString));
    }
    return solution;
}

bool isInBigString(string bigString, string smallString)
{
    const int bigStringLength = bigString.length();
    const int smallStringLength = smallString.length();
    for (int i = 0; i < bigStringLength; i++)
    {
        if (i + smallStringLength > bigStringLength)
        {
            break;
        }
        if (isInBigStringHelper(bigString, smallString, i))
        {
            return true;
        }
    }
    return false;
}

bool isInBigStringHelper(string bigString, string smallString, int startIdx)
{
    int leftBigIdx = startIdx;
    int rightBigIdx = startIdx + smallString.length() - 1;
    int leftSmallIdx = 0;
    int rightSmallIdx = smallString.length() - 1;
    while (leftBigIdx <= rightBigIdx)
    {
        if (bigString[leftBigIdx] != smallString[leftSmallIdx] ||
            bigString[rightBigIdx] != smallString[rightSmallIdx])
        {
            return false;
        }
        leftBigIdx++;
        rightBigIdx--;
        leftSmallIdx++;
        rightSmallIdx--;
    }
    return true;
}

class TrieNode
{
public:
    unordered_map<char, TrieNode *> children;
    string word;
    size_t index;
};

class ModifiedSuffixTrie
{
public:
    TrieNode *root;

    ModifiedSuffixTrie(string str)
    {
        this->root = new TrieNode();
        this->populateModifiedSuffixTrieFrom(str);
    }

    void populateModifiedSuffixTrieFrom(string str)
    {
        const int strLength = str.length();
        for (int i = 0; i < strLength; i++)
        {
            this->insertSubstringStartingAt(i, str);
        }
    }

    void insertSubstringStartingAt(int i, string str)
    {
        TrieNode *node = this->root;
        const int strLength = str.length();
        for (int j = i; j < strLength; j++)
        {
            char letter = str[j];
            if (node->children.find(letter) == node->children.end())
            {
                TrieNode *newNode = new TrieNode();
                node->children.insert({letter, newNode});
            }
            node = node->children[letter];
        }
    }

    bool contains(string str)
    {
        TrieNode *node = this->root;
        for (char letter : str)
        {
            if (node->children.find(letter) == node->children.end())
            {
                return false;
            }
            node = node->children[letter];
        }
        return true;
    }
};

// O(b^2 + ns) time | O(b^2 + n) space
vector<bool> multiStringSearch2(string bigString, vector<string> smallStrings)
{
    ModifiedSuffixTrie modifiedSuffixTrie(bigString);
    vector<bool> solution;
    for (string smallString : smallStrings)
    {
        solution.push_back(modifiedSuffixTrie.contains(smallString));
    }
    return solution;
}

class Trie
{
public:
    TrieNode *root;
    char endSymbol;

    Trie()
    {
        this->root = new TrieNode();
        this->endSymbol = '*';
    }

    void insert(string str)
    {
        TrieNode *current = this->root;
        const int strLength = str.length();
        for (int i = 0; i < strLength; i++)
        {
            char letter = str[i];
            if (current->children.find(letter) == current->children.end())
            {
                TrieNode *newNode = new TrieNode();
                current->children.insert({letter, newNode});
            }
            current = current->children[letter];
        }
        current->children.insert({this->endSymbol, nullptr});
        current->word = str;
    }

    void add(int idx, string str)
    {
        TrieNode *current = this->root;
        const int strLength = str.length();
        for (int i = 0; i < strLength; i++)
        {
            char letter = str[i];
            if (current->children.find(letter) == current->children.end())
            {
                TrieNode *newNode = new TrieNode();
                current->children.insert({letter, newNode});
            }
            current = current->children[letter];
        }
        current->children.insert({this->endSymbol, nullptr});
        current->index = idx;
    }
};

void findSmallStringsIn(string str, int startIdx, Trie *trie,
                        unordered_map<string, bool> *containedStrings);

// O(ns + bs) time | O(ns) space
vector<bool> multiStringSearch3(string bigString, vector<string> smallStrings)
{
    Trie *trie = new Trie();
    for (string smallString : smallStrings)
    {
        trie->insert(smallString);
    }
    unordered_map<string, bool> containedStrings;
    const int bigStringLength = bigString.length();
    for (int i = 0; i < bigStringLength; i++)
    {
        findSmallStringsIn(bigString, i, trie, &containedStrings);
    }
    vector<bool> solution;
    for (string smallString : smallStrings)
    {
        solution.push_back(containedStrings.find(smallString) !=
                           containedStrings.end());
    }
    return solution;
}

void findSmallStringsIn(string str, int startIdx, Trie *trie,
                        unordered_map<string, bool> *containedStrings)
{
    TrieNode *currentNode = trie->root;
    const int strLength = str.length();
    for (int i = startIdx; i < strLength; i++)
    {
        if (currentNode->children.find(str[i]) == currentNode->children.end())
        {
            break;
        }
        currentNode = currentNode->children[str[i]];
        if (currentNode->children.find(trie->endSymbol) !=
            currentNode->children.end())
        {
            containedStrings->insert({currentNode->word, true});
        }
    }
}

void matchInSmallStrings(string str, int startIdx, Trie *trie, vector<bool> *markers)
{
    TrieNode *currentNode = trie->root;
    const int strLength = str.length();
    for (int i = startIdx; i < strLength; i++)
    {
        if (currentNode->children.find(str[i]) == currentNode->children.end())
        {
            break;
        }
        currentNode = currentNode->children[str[i]];
        if (currentNode->children.find(trie->endSymbol) !=
            currentNode->children.end())
        {
            (*markers)[currentNode->index] = true;
        }
    }
}

// O(ns + bs) time | O(ns) space
vector<bool> multiStringSearch4(string bigString, vector<string> smallStrings)
{
    Trie *trie = new Trie();
    vector<bool> markers;
    const int smallStringsSize = smallStrings.size();
    for (int i = 0; i < smallStringsSize; i++)
    {
        trie->add(i, smallStrings[i]);
        markers.push_back(false);
    }
    const int bigStringLength = bigString.length();
    for (int i = 0; i < bigStringLength; i++)
    {
        matchInSmallStrings(bigString, i, trie, &markers);
    }
    return markers;
}

void iteration(vector<bool> array)
{
    for (const bool element : array)
    {
        cout << element << " ";
    }
    cout << endl;
}

int main()
{
    string bigString = "abcdefghijklmnopqrstuvwxyz";
    vector<string> smallStrings = {"abcdefghijklmnopqrstuvwxyz", "abc", "j", "mnopqr", "pqrstuvwxyz", "xyzz", "defh"};
    iteration(multiStringSearch1(bigString, smallStrings));
    iteration(multiStringSearch2(bigString, smallStrings));
    iteration(multiStringSearch3(bigString, smallStrings));
    iteration(multiStringSearch4(bigString, smallStrings));
    return 0;
}
```

## 特殊字符串

给出一个字符串数组，找出数组中满足条件为该字符串完全由数组中的其它字符串拼接而成

```python
# Average case: when there aren't too many strings with
# identical prefixes that are found in another string
# O(n * m) time | O(n * m) space - where n is the number
# of strings and m is the length of the longest string
def special_strings(strings):
    # 创建一个字典树
    trie = Trie()
    # 将所有字符串添加到字典树中
    for string in strings:
        trie.insert(string)
    # 逐个判断字符串是否由其它字符串组成, 符合判断条件则返回, 初始的匹配计数值为 0
    return list(filter(lambda string_element: is_special(string_element, trie.root, 0, 0, trie), strings))


def is_special(string, trie_node, idx, count, trie):
    char = string[idx]
    # 如果字符不包含在字典树中则返回否
    if char not in trie_node:
        return False
    # 判断是否已经到达字符串的最后一个字符
    at_end_of_string = idx == len(string) - 1
    if at_end_of_string:
        # 匹配字符串的计数大于 1 且在字典树中, 说明这个字符串是由其它字符串拼接组成的
        return trie.end_symbol in trie_node[char] and count + 1 >= 2
    # 如果达到了字典树的枝干末端
    if trie.end_symbol in trie_node[char]:
        # 重新从字典树的父节点开始匹配剩下未匹配的字符串, 并累加一个匹配成功的计数
        rest_is_special = is_special(string, trie.root, idx + 1, count + 1, trie)
        # 如果剩下的字符串也包含在字典树中, 则认为这个字符串是由其它字符串拼接组成的
        if rest_is_special:
            return True

    return is_special(string, trie_node[char], idx + 1, count, trie)


class Trie:
    def __init__(self):
        self.root = {}
        self.end_symbol = "*"

    def insert(self, string):
        current_trie_node = self.root
        for i in range(len(string)):
            if string[i] not in current_trie_node:
                current_trie_node[string[i]] = {}
            current_trie_node = current_trie_node[string[i]]
        current_trie_node[self.end_symbol] = string


if __name__ == '__main__':
    print(special_strings(["foobarbaz", "foo", "bar", "foobarfoo", "baz", "foobaz", "foofoofoo", "foobazar"]))
```

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
#include <string>
using namespace std;

class TrieNode
{
public:
    unordered_map<char, TrieNode *> children;
};

class Trie
{
public:
    TrieNode *root = new TrieNode();
    char endSymbol = '*';

    void insert(string string)
    {
        TrieNode *node = this->root;
        const int stringLength = string.length();
        for (int i = 0; i < stringLength; i++)
        {
            if (node->children.find(string[i]) == node->children.end())
            {
                TrieNode *newNode = new TrieNode();
                node->children.insert({string[i], newNode});
            }
            node = node->children[string[i]];
        }
        node->children.insert({this->endSymbol, nullptr});
    }
};

bool isSpecial(string string, TrieNode *trieNode, int idx, int count,
               Trie *trie);

// Average case: when there aren't too many strings with
// identical prefixes that are found in another string
// O(n * m) time | O(n * m) space - where n is the number
// of strings and m is the length of the longest string
vector<string> specialStrings(vector<string> strings)
{
    auto trie = new Trie();
    for (auto string : strings)
    {
        trie->insert(string);
    }
    vector<string> result = {};
    for (auto string : strings)
    {
        if (isSpecial(string, trie->root, 0, 0, trie))
            result.push_back(string);
    }
    return result;
}

bool isSpecial(string string, TrieNode *trieNode, int idx, int count,
               Trie *trie)
{
    auto c = string[idx];
    if (trieNode->children.find(c) == trieNode->children.end())
        return false;

    auto nextTrieNode = trieNode->children[c];

    auto atEndOfString = idx == string.length() - 1;
    if (atEndOfString)
        return nextTrieNode->children.find(trie->endSymbol) !=
                   nextTrieNode->children.end() &&
               count + 1 >= 2;

    if (nextTrieNode->children.find(trie->endSymbol) !=
        nextTrieNode->children.end())
    {
        auto restIsSpecial =
            isSpecial(string, trie->root, idx + 1, count + 1, trie);
        if (restIsSpecial)
            return true;
    }

    return isSpecial(string, nextTrieNode, idx + 1, count, trie);
}

void iteration(vector<string> arrays)
{
    for (string element : arrays)
    {
        cout << element << " ";
    }
}

int main()
{
    iteration(specialStrings({"foobarbaz", "foo", "bar", "foobarfoo", "baz", "foobaz", "foofoofoo", "foobazar"}));
    return 0;
}
```

## 拨号盘号码中的单词

手机上的拨号盘的每个数字都有对应的字母，给定一组单词和一串数字，找出使用这串数字是否可以拼出给定的单词，如果可以则返回能够拼出的所有单词

```python
# O(m * w + n * 3^n) time | O(m * w + n) space - where n is the length of the
# phone number, m is the number of words, and w is the length of the longest word
def words_in_phone_number1(phone_number, words):
    # 将所有的词放入字典树中
    word_trie = get_word_trie(words)
    final_words = {}
    # 从手机拨号数字的所有位置开始向后推断
    for i in range(len(phone_number)):
        # 递归执行推断
        explore_trie(phone_number, i, word_trie.root, final_words)
    return list(final_words.keys())


def get_word_trie(words):
    word_trie = Trie()
    for word in words:
        word_trie.insert(word)
    return word_trie


def explore_trie(phone_number, digit_idx, trie_node, final_words):
    if "*" in trie_node:
        word = trie_node["*"]
        final_words[word] = True

    if digit_idx == len(phone_number):
        return
    # 获得当前索引处的数字
    current_digit = phone_number[digit_idx]
    # 根据数字获得拨号盘对应的所有字母
    possible_letters = DIGIT_LETTERS[current_digit]
    # 遍历所有的字母, 如果发现字母在字典树中存在则继续递归
    for letter in possible_letters:
        if letter not in trie_node:
            continue
        # 索引递增, 字典树移动到对应字母的子节点
        explore_trie(phone_number, digit_idx + 1, trie_node[letter], final_words)


DIGIT_LETTERS = {
    "0": [],
    "1": [],
    "2": ["a", "b", "c"],
    "3": ["d", "e", "f"],
    "4": ["g", "h", "i"],
    "5": ["j", "k", "l"],
    "6": ["m", "n", "o"],
    "7": ["p", "q", "r", "s"],
    "8": ["t", "u", "v"],
    "9": ["w", "x", "y", "z"],
}


class Trie:
    def __init__(self):
        self.root = {}
        self.end_symbol = "*"

    def insert(self, word):
        current_trie_node = self.root
        for letter in word:
            if letter not in current_trie_node:
                current_trie_node[letter] = {}
            current_trie_node = current_trie_node[letter]
        current_trie_node[self.end_symbol] = word


# O(n^2 + m * w) time | O(n^2 + m * w) space - where n is the length of the
# phone number, m is the number of words, and w is the length of the longest word
def words_in_phone_number2(phone_number, words):
    # 根据手机拨号数字生成字典树
    # Note: 这里也可以考虑使用字符串的 KMP 查找算法替代字典树, 以降低空间复杂度
    phone_number_suffix_trie = ModifiedSuffixTrie(phone_number)
    # 通过拨号盘的字母与数字的对照表将字母转换为相应的数字, 然后通过字典树查找这串数字, 存在则表示可以通过这串数字拼出字符串
    return list(filter(lambda word: word_is_in_phone_number(phone_number_suffix_trie, word), words))


def word_is_in_phone_number(phone_number_suffix_trie, word):
    # 将字符串转为拨号盘的数字字符串
    digit_word = word_to_digits(word)
    # 判断数字字符串是否在字典树中
    return phone_number_suffix_trie.contains(digit_word)


def word_to_digits(word):
    return "".join(map(lambda letter: LETTER_DIGITS[letter], list(word)))


LETTER_DIGITS = {
    "a": "2", "b": "2", "c": "2",
    "d": "3", "e": "3", "f": "3",
    "g": "4", "h": "4", "i": "4",
    "j": "5", "k": "5", "l": "5",
    "m": "6", "n": "6", "o": "6",
    "p": "7", "q": "7", "r": "7", "s": "7",
    "t": "8", "u": "8", "v": "8",
    "w": "9", "x": "9", "y": "9", "z": "9",
}


class ModifiedSuffixTrie:
    def __init__(self, string):
        self.root = {}
        self.populate_modified_suffix_trie_from(string)

    def populate_modified_suffix_trie_from(self, string):
        for i in range(len(string)):
            self.insert_substring_starting_at(i, string)

    def insert_substring_starting_at(self, i, string):
        current_trie_node = self.root
        for j in range(i, len(string)):
            letter = string[j]
            if letter not in current_trie_node:
                current_trie_node[letter] = {}
            current_trie_node = current_trie_node[letter]

    def contains(self, string):
        current_trie_node = self.root
        for letter in string:
            if letter not in current_trie_node:
                return False
            current_trie_node = current_trie_node[letter]
        return True


if __name__ == '__main__':
    print(words_in_phone_number1('3662277', ["foo", "bar", "baz", "foobar", "emo", "cap", "car", "cat"]))
    print(words_in_phone_number2('3662277', ["foo", "bar", "baz", "foobar", "emo", "cap", "car", "cat"]))
```

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <unordered_set>
#include <unordered_map>

using namespace std;

unordered_map<char, vector<char>> DIGIT_LETTERS = {
    {'0', vector<char>()},
    {'1', vector<char>()},
    {'2', vector<char>{'a', 'b', 'c'}},
    {'3', vector<char>{'d', 'e', 'f'}},
    {'4', vector<char>{'g', 'h', 'i'}},
    {'5', vector<char>{'j', 'k', 'l'}},
    {'6', vector<char>{'m', 'n', 'o'}},
    {'7', vector<char>{'p', 'q', 'r', 's'}},
    {'8', vector<char>{'t', 'u', 'v'}},
    {'9', vector<char>{'w', 'x', 'y', 'z'}},
};

class TrieNode
{
public:
    unordered_map<char, TrieNode *> children;
    string word;
};

class Trie
{
public:
    TrieNode *root;
    char endSymbol;

    Trie()
    {
        this->root = new TrieNode();
        this->endSymbol = '*';
    }

    void insert(string str)
    {
        TrieNode *current = this->root;
        const int strLength = str.length();
        for (int i = 0; i < strLength; i++)
        {
            char letter = str[i];
            if (current->children.find(letter) == current->children.end())
            {
                TrieNode *newNode = new TrieNode();
                current->children.insert({letter, newNode});
            }
            current = current->children[letter];
        }
        current->children.insert({this->endSymbol, nullptr});
        current->word = str;
    }
};

Trie *getWordTrie(vector<string> words);
void exploreTrie(string phoneNumber, int digitIdx, TrieNode *trieNode,
                 unordered_set<string> &finalWords);

// O(m * w + n * 3^n) time | O(m * w + n) space - where n is the length of the
// phone number, m is the number of words, and w is the length of the longest
// word
vector<string> wordsInPhoneNumber1(string phoneNumber, vector<string> words)
{
    auto wordTrie = getWordTrie(words);
    unordered_set<string> finalWords;
    const int phoneNumberLength = phoneNumber.length();
    for (auto i = 0; i < phoneNumberLength; i++)
    {
        exploreTrie(phoneNumber, i, wordTrie->root, finalWords);
    }
    return vector<string>(finalWords.begin(), finalWords.end());
}

Trie *getWordTrie(vector<string> words)
{
    auto wordTrie = new Trie();
    for (auto word : words)
    {
        wordTrie->insert(word);
    }
    return wordTrie;
}

void exploreTrie(string phoneNumber, int digitIdx, TrieNode *trieNode,
                 unordered_set<string> &finalWords)
{
    if (trieNode->children.find('*') != trieNode->children.end())
    {
        finalWords.insert(trieNode->word);
    }

    if (digitIdx == phoneNumber.size())
        return;

    auto currentDigit = phoneNumber[digitIdx];
    auto possibleLetters = DIGIT_LETTERS[currentDigit];

    for (auto letter : possibleLetters)
    {
        if (trieNode->children.find(letter) == trieNode->children.end())
            continue;
        exploreTrie(phoneNumber, digitIdx + 1, trieNode->children[letter],
                    finalWords);
    }
}

unordered_map<char, char> LETTER_DIGITS = {
    {'a', '2'},
    {'b', '2'},
    {'c', '2'},
    {'d', '3'},
    {'e', '3'},
    {'f', '3'},
    {'g', '4'},
    {'h', '4'},
    {'i', '4'},
    {'j', '5'},
    {'k', '5'},
    {'l', '5'},
    {'m', '6'},
    {'n', '6'},
    {'o', '6'},
    {'p', '7'},
    {'q', '7'},
    {'r', '7'},
    {'s', '7'},
    {'t', '8'},
    {'u', '8'},
    {'v', '8'},
    {'w', '9'},
    {'x', '9'},
    {'y', '9'},
    {'z', '9'},
};

class ModifiedSuffixTrie
{
public:
    TrieNode *root;

    ModifiedSuffixTrie(string str)
    {
        this->root = new TrieNode();
        this->populateModifiedSuffixTrieFrom(str);
    }

    void populateModifiedSuffixTrieFrom(string str)
    {
        const int strLength = str.length();
        for (int i = 0; i < strLength; i++)
        {
            this->insertSubstringStartingAt(i, str);
        }
    }

    void insertSubstringStartingAt(int i, string str)
    {
        TrieNode *node = this->root;
        const int strLength = str.length();
        for (int j = i; j < strLength; j++)
        {
            char letter = str[j];
            if (node->children.find(letter) == node->children.end())
            {
                TrieNode *newNode = new TrieNode();
                node->children.insert({letter, newNode});
            }
            node = node->children[letter];
        }
    }

    bool contains(string str)
    {
        TrieNode *node = this->root;
        for (char letter : str)
        {
            if (node->children.find(letter) == node->children.end())
            {
                return false;
            }
            node = node->children[letter];
        }
        return true;
    }
};

string wordToDigits(string word);

// O(n^2 + m * w) time | O(n^2 + m * w) space - where n is the length of the
// phone number, m is the number of words, and w is the length of the longest
// word
vector<string> wordsInPhoneNumber2(string phoneNumber, vector<string> words)
{
    auto phoneNumberSuffixTrie = new ModifiedSuffixTrie(phoneNumber);
    vector<string> output;
    for (auto word : words)
    {
        auto digitWord = wordToDigits(word);
        if (phoneNumberSuffixTrie->contains(digitWord))
        {
            output.push_back(word);
        }
    }
    return output;
}

string wordToDigits(string word)
{
    string digits = "";
    for (auto letter : word)
    {
        digits += LETTER_DIGITS[letter];
    }
    return digits;
}

void iteration(vector<string> array)
{
    for (const string e : array)
    {
        cout << e << " ";
    }
    cout << endl;
}

int main()
{
    string phoneNumber = "36612277";
    vector<string> source = {"foo", "bar", "baz", "foobar", "emo", "cap", "car", "cat"};
    iteration(wordsInPhoneNumber1(phoneNumber, source));
    iteration(wordsInPhoneNumber2(phoneNumber, source));
    return 0;
}
```

Last Modified 2022-07-02

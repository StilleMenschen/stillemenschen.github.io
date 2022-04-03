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

Last Modified 2022-04-03

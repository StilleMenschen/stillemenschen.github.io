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

Last Modified 2022-01-26

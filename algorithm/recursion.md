# 递归

## 斐波那契数列

```python
# O(2^n) time | O(n) space
def get_nth_fib1(n):
    if n == 2:
        return 1
    if n <= 1:
        return 0
    else:
        return get_nth_fib1(n - 1) + get_nth_fib1(n - 2)


# O(n) time | O(n) space
def get_nth_fib2(n, memoize={1: 0, 2: 1}):
    if n in memoize or n < 1:
        return memoize[n]
    else:
        memoize[n] = get_nth_fib2(n - 1, memoize) + get_nth_fib2(n - 2, memoize)
        return memoize[n]


# O(n) time | O(1) space
def get_nth_fib3(n):
    first = 0
    second = 1
    counter = 3
    while counter <= n:
        next_fib = first + second
        first = second
        second = next_fib
        counter += 1
    return second if n > 1 else first


# O(n) time | O(1) space
def get_nth_fib4(n, first = 0, second = 1):
    if n <= 1:
        return first
    return get_nth_fib4(n - 1, second, first + second)


if __name__ == '__main__':
    print(get_nth_fib1(42))
    print(get_nth_fib2(42))
    print(get_nth_fib3(42))
    print(get_nth_fib4(42))
```

## 多维数组求和

```python
# O(n) time | O(d) space
# n is all number element in array
# d is the array dimension
def product_sum(array, multiplier=1):
    if not len(array):
        return 0
    total = 0
    for e in array:
        if isinstance(e, list):
            total += product_sum(e, multiplier + 1)
        else:
            total += e
    return total * multiplier


if __name__ == '__main__':
    source = [5, 2, [7, -1], 3, [6, [-13, 8], 4]]
    print(product_sum(source))
```

## 排列数组

```python
# Upper Bound: O(n^2*n!) time | O(n*n!) space
# Roughly: O(n*n!) time | O(n*n!) space
def get_permutations1(array):
    permutations = []
    permutations_helper1(array, [], permutations)
    return permutations


def permutations_helper1(array, current_permutation, permutations):
    if not len(array) and len(current_permutation):
        permutations.append(current_permutation)
    else:
        for i in range(len(array)):
            new_array = array[:i] + array[i + 1:]
            new_permutation = current_permutation + [array[i]]
            permutations_helper1(new_array, new_permutation, permutations)


# O(n*n!) time | O(n*n!) space
def get_permutations2(array):
    permutations = []
    permutations_helper2(0, array, permutations)
    return permutations


def permutations_helper2(i, array, permutations):
    if i == len(array) - 1:
        permutations.append(array[:])
    else:
        for j in range(i, len(array)):
            swap(array, i, j)
            permutations_helper2(i + 1, array, permutations)
            swap(array, i, j)


def swap(array, i, j):
    array[i], array[j] = array[j], array[i]


if __name__ == '__main__':
    print(get_permutations1([1, 2, 3]))
    print(get_permutations2([1, 2, 3]))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

void permutationsHelper1(vector<int> array, vector<int> currentPermutation,
                         vector<vector<int>> *permutations);
void permutationsHelper2(size_t i, vector<int> &array,
                         vector<vector<int>> &permutations);

// Upper Bound: O(n^2*n!) time | O(n*n!) space
// Roughly: O(n*n!) time | O(n*n!) space
vector<vector<int>> getPermutations1(vector<int> array)
{
    vector<vector<int>> permutations;
    permutationsHelper1(array, {}, &permutations);
    return permutations;
}

void permutationsHelper1(vector<int> array, vector<int> currentPermutation,
                         vector<vector<int>> *permutations)
{
    if (array.size() == 0 && currentPermutation.size() > 0)
    {
        permutations->push_back(currentPermutation);
    }
    else
    {
        for (size_t i = 0; i < array.size(); i++)
        {
            vector<int> newArray;
            newArray.insert(newArray.end(), array.begin(), array.begin() + i);
            newArray.insert(newArray.end(), array.begin() + i + 1, array.end());
            vector<int> newPermutation = currentPermutation;
            newPermutation.push_back(array[i]);
            permutationsHelper1(newArray, newPermutation, permutations);
        }
    }
}

// O(n*n!) time | O(n*n!) space
vector<vector<int>> getPermutations2(vector<int> array)
{
    vector<vector<int>> permutations;
    permutationsHelper2(0, array, permutations);
    return permutations;
}

void permutationsHelper2(size_t i, vector<int> &array,
                         vector<vector<int>> &permutations)
{
    if (i == array.size() - 1)
    {
        permutations.push_back(array);
    }
    else
    {
        for (size_t j = i; j < array.size(); j++)
        {
            swap(array[i], array[j]);
            permutationsHelper2(i + 1, array, permutations);
            swap(array[i], array[j]);
        }
    }
}

void iterationArray(const vector<vector<int>> &array)
{
    for (size_t i = 0; i < array.size(); i++)
    {
        const vector<int> elements = array[i];
        cout << "[ ";
        for (int element : elements)
        {
            cout << element << ", ";
        }
        cout << "], ";
    }
    cout << endl;
}

int main()
{
    vector<int> source = {1, 2, 3};
    vector<vector<int>> result;
    result = getPermutations1(source);
    iterationArray(result);
    result = getPermutations2(source);
    iterationArray(result);
    return 0;
}
```

## 所有子元素集合

给出一个包含整数的数组，表示一个集合，找出使用该集合的元素可以构成的所有集合，
如 `[1, 2]` 的所有集合为 `[[], [1], [2], [1, 2]]`

```python
# O(n*2^n) time | O(n*2^n) space
def powerset1(array, idx=None):
    if idx is None:
        idx = len(array) - 1
    if idx < 0:
        return [[]]
    ele = array[idx]
    subsets = powerset1(array, idx - 1)
    for i in range(len(subsets)):
        current_subset = subsets[i]
        subsets.append(current_subset + [ele])
    return subsets


# O(n*2^n) time | O(n*2^n) space
def powerset2(array):
    subsets = [[]]
    for ele in array:
        for i in range(len(subsets)):
            current_subset = subsets[i]
            subsets.append(current_subset + [ele])
    return subsets


if __name__ == '__main__':
    source = [1, 2, 3]
    print(powerset1(source))
    print(powerset2(source))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<vector<int>> powersetHelper1(vector<int> &array, int idx);

// O(n*2^n) time | O(n*2^n) space
vector<vector<int>> powerset1(vector<int> array)
{
    return powersetHelper1(array, array.size() - 1);
}

vector<vector<int>> powersetHelper1(vector<int> &array, int idx)
{
    if (idx < 0)
    {
        return vector<vector<int>>{{}};
    }
    int ele = array[idx];
    vector<vector<int>> subsets = powersetHelper1(array, idx - 1);
    int length = subsets.size();
    for (int i = 0; i < length; i++)
    {
        vector<int> currentSubset = subsets[i];
        vector<int> newSubset = currentSubset;
        newSubset.push_back(ele);
        subsets.push_back(newSubset);
    }
    return subsets;
}

// O(n*2^n) time | O(n*2^n) space
vector<vector<int>> powerset2(vector<int> array)
{
    vector<vector<int>> subsets = {{}};
    for (int ele : array)
    {
        int length = subsets.size();
        for (int i = 0; i < length; i++)
        {
            vector<int> currentSubset = subsets[i];
            currentSubset.push_back(ele);
            subsets.push_back(currentSubset);
        }
    }
    return subsets;
}

void iterationArray(const vector<vector<int>> &array)
{
    for (size_t i = 0; i < array.size(); i++)
    {
        const vector<int> elements = array[i];
        cout << "[ ";
        for (int element : elements)
        {
            cout << element << ", ";
        }
        cout << "], ";
    }
    cout << endl;
}

int main()
{
    vector<int> source = {1, 2, 3};
    vector<vector<int>> result;
    result = powerset1(source);
    iterationArray(result);
    result = powerset2(source);
    iterationArray(result);
    return 0;
}
```

## 拨号盘拼词

```python
# O(4^n * n) time | O(4^n * n) space - where
# n is the length of the phone number
def phone_number_mnemonics(phone_number):
    current_mnemonic = ["0"] * len(phone_number)
    mnemonics_found = []

    phone_number_mnemonics_helper(0, phone_number, current_mnemonic, mnemonics_found)
    return mnemonics_found


def phone_number_mnemonics_helper(idx, phone_number, current_mnemonic, mnemonics_found):
    if idx == len(phone_number):
        mnemonic = "".join(current_mnemonic)
        mnemonics_found.append(mnemonic)
    else:
        digit = phone_number[idx]
        letters = DIGIT_LETTERS[digit]
        for letter in letters:
            current_mnemonic[idx] = letter
            phone_number_mnemonics_helper(idx + 1, phone_number, current_mnemonic, mnemonics_found)


DIGIT_LETTERS = {
    "0": ["0"],
    "1": ["1"],
    "2": ["a", "b", "c"],
    "3": ["d", "e", "f"],
    "4": ["g", "h", "i"],
    "5": ["j", "k", "l"],
    "6": ["m", "n", "o"],
    "7": ["p", "q", "r", "s"],
    "8": ["t", "u", "v"],
    "9": ["w", "x", "y", "z"],
}


if __name__ == '__main__':
    print(phone_number_mnemonics('1097'))
    print(phone_number_mnemonics('10'))
```

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <numeric>
#include <unordered_map>
using namespace std;

void phoneNumberMnemonicsHelper(size_t idx, string phoneNumber,
                                vector<char> &currentMnemonic,
                                vector<string> &mnemonicsFound);
unordered_map<int, vector<char>> DIGIT_LETTERS{
    {0, {'0'}}, {1, {'1'}}, {2, {'a', 'b', 'c'}}, {3, {'d', 'e', 'f'}}, {4, {'g', 'h', 'i'}},
    {5, {'j', 'k', 'l'}}, {6, {'m', 'n', 'o'}}, {7, {'p', 'q', 'r', 's'}}, {8, {'t', 'u', 'v'}},
    {9, {'w', 'x', 'y', 'z'}}};

// O(4^n * n) time | O(4^n * n) space - where
// n is the length of the phone number
vector<string> phoneNumberMnemonics(string phoneNumber)
{
    vector<char> currentMnemonic(phoneNumber.size(), '0');
    vector<string> mnemonicsFound;
    phoneNumberMnemonicsHelper(0, phoneNumber, currentMnemonic, mnemonicsFound);
    return mnemonicsFound;
}

void phoneNumberMnemonicsHelper(size_t idx, string phoneNumber,
                                vector<char> &currentMnemonic,
                                vector<string> &mnemonicsFound)
{
    if (idx == phoneNumber.size())
    {
        string mnemonic =
            accumulate(currentMnemonic.begin(), currentMnemonic.end(), string{});
        mnemonicsFound.push_back(mnemonic);
    }
    else
    {
        int digit = phoneNumber[idx] - '0';
        vector<char> letters = DIGIT_LETTERS[digit];
        for (auto letter : letters)
        {
            currentMnemonic[idx] = letter;
            phoneNumberMnemonicsHelper(idx + 1, phoneNumber, currentMnemonic,
                                       mnemonicsFound);
        }
    }
}

int main()
{
    string source = "1097";
    vector<string> result = phoneNumberMnemonics(source);
    for (string element : result)
        cout << element << " ";
    return 0;
}
```

Last Modified 2022-01-09

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

```cpp
#include <iostream>
using namespace std;

// O(2^n) time | O(n) space
int getNthFib1(int n)
{
  if (n == 2)
    return 1;
  if (n <= 1)
    return 0;
  return getNthFib1(n - 1) + getNthFib1(n - 2);
}

int fibHelper(int n, int first, int second)
{
  if (n <= 1)
    return first;
  return fibHelper(n - 1, second, first + second);
}

// O(n) time | O(1) space
int getNthFib2(int n)
{
  return fibHelper(n, 0, 1);
}

// O(n) time | O(1) space
int getNthFib3(int n)
{
  int first = 0;
  int second = 1;
  int counter = 3;
  while (counter <= n)
  {
    int nextFib = first + second;
    first = second;
    second = nextFib;
    counter++;
  }
  return n > 1 ? second : first;
}

int main()
{
  cout << getNthFib3(42) << endl;
  cout << getNthFib2(42) << endl;
  cout << getNthFib1(42) << endl;
  return 0;
}
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

## 楼梯穿行

给定两个参数 height 和 max steps，表示楼梯的高度和最大可以走多少步上楼，求出在最大步数限制下有多少种方式可以完成上楼

```python
# O(k^n) time | O(n) space
# where n is the height of the staircase and k is the number of allowed steps
def staircase_traversal1(height, max_steps):
    return number_of_ways_to_top1(height, max_steps)


def number_of_ways_to_top1(height, max_steps):
    if height <= 1:
        return 1

    number_of_ways = 0
    for step in range(1, min(max_steps, height) + 1):
        number_of_ways += number_of_ways_to_top1(height - step, max_steps)

    return number_of_ways


# O(n * k) time | O(n) space
# where n is the height of the staircase and k is the number of allowed steps
def staircase_traversal2(height, max_steps):
    return number_of_ways_to_top2(height, max_steps, {0: 1, 1: 1})


def number_of_ways_to_top2(height, max_steps, memoize):
    if height in memoize:
        return memoize[height]

    number_of_ways = 0
    for step in range(1, min(max_steps, height) + 1):
        number_of_ways += number_of_ways_to_top2(height - step, max_steps, memoize)

    memoize[height] = number_of_ways

    return number_of_ways


# O(n * k) time | O(n) space
# where n is the height of the staircase and k is the number of allowed steps
def staircase_traversal3(height, max_steps):
    ways_to_top = [0 for _ in range(height + 1)]
    ways_to_top[0] = 1
    ways_to_top[1] = 1

    for current_height in range(2, height + 1):
        step = 1
        while step <= max_steps and step <= current_height:
            ways_to_top[current_height] = ways_to_top[current_height] + ways_to_top[current_height - step]
            step += 1

    return ways_to_top[height]


# O(n) time | O(n) space - where n is the height of the staircase
def staircase_traversal4(height, max_steps):
    current_number_of_ways = 0
    ways_to_top = [1]

    for current_height in range(1, height + 1):
        start_of_window = current_height - max_steps - 1
        end_of_window = current_height - 1
        if start_of_window >= 0:
            current_number_of_ways -= ways_to_top[start_of_window]

        current_number_of_ways += ways_to_top[end_of_window]
        ways_to_top.append(current_number_of_ways)

    return ways_to_top[height]


if __name__ == '__main__':
    print(staircase_traversal1(15, 5))
    print(staircase_traversal2(15, 5))
    print(staircase_traversal3(15, 5))
    print(staircase_traversal4(15, 5))
```

```cpp
#include <iostream>
#include <algorithm>
#include <unordered_map>
#include <vector>
using namespace std;

int numberOfWaysToTop1(int height, int maxSteps);

// O(k^n) time | O(n) space
// where n is the height of the staircase and k is the number of allowed steps
int staircaseTraversal1(int height, int maxSteps)
{
  return numberOfWaysToTop1(height, maxSteps);
}

int numberOfWaysToTop1(int height, int maxSteps)
{
  if (height <= 1)
    return 1;

  int numberOfWays = 0;
  for (int step = 1; step < min(maxSteps, height) + 1; step++)
  {
    numberOfWays += numberOfWaysToTop1(height - step, maxSteps);
  }

  return numberOfWays;
}

int numberOfWaysToTop2(int height, int maxSteps,
                       unordered_map<int, int> &memoize);

// O(n * k) time | O(n) space
// where n is the height of the staircase and k is the number of allowed steps
int staircaseTraversal2(int height, int maxSteps)
{
  unordered_map<int, int> memoize = {{0, 1}, {1, 1}};
  return numberOfWaysToTop2(height, maxSteps, memoize);
}

int numberOfWaysToTop2(int height, int maxSteps,
                       unordered_map<int, int> &memoize)
{
  if (memoize.find(height) != memoize.end())
    return memoize[height];

  int numberOfWays = 0;
  for (int step = 1; step < min(maxSteps, height) + 1; step++)
  {
    numberOfWays += numberOfWaysToTop2(height - step, maxSteps, memoize);
  }

  memoize[height] = numberOfWays;

  return numberOfWays;
}

// O(n * k) time | O(n) space
// where n is the height of the staircase and k is the number of allowed steps
int staircaseTraversal3(int height, int maxSteps)
{
  vector<int> waysToTop(height + 1, 0);
  waysToTop[0] = 1;
  waysToTop[1] = 1;

  for (int currentHeight = 2; currentHeight < height + 1; currentHeight++)
  {
    int step = 1;
    while (step <= maxSteps && step <= currentHeight)
    {
      waysToTop[currentHeight] =
          waysToTop[currentHeight] + waysToTop[currentHeight - step];
      step++;
    }
  }

  return waysToTop[height];
}

// O(n) time | O(n) space - where n is the height of the staircase
int staircaseTraversal4(int height, int maxSteps)
{
  int currentNumberOfWays = 0;
  vector<int> waysToTop = {1};

  for (int currentHeight = 1; currentHeight < height + 1; currentHeight++)
  {
    int startOfWindow = currentHeight - maxSteps - 1;
    int endOfWindow = currentHeight - 1;
    if (startOfWindow >= 0)
      currentNumberOfWays -= waysToTop[startOfWindow];

    currentNumberOfWays += waysToTop[endOfWindow];
    waysToTop.push_back(currentNumberOfWays);
  }

  return waysToTop[height];
}

int main()
{
  cout << staircaseTraversal1(15, 5) << endl;
  cout << staircaseTraversal2(15, 5) << endl;
  cout << staircaseTraversal3(15, 5) << endl;
  cout << staircaseTraversal4(15, 5) << endl;
  return 0;
}
```

Last Modified 2022-01-09

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

void iteration(const vector<vector<int>> &array)
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
    iteration(result);
    result = getPermutations2(source);
    iteration(result);
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

void iteration(const vector<vector<int>> &array)
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
    iteration(result);
    result = powerset2(source);
    iteration(result);
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

## 查找公共父节点

给定一个类树结构的组织架构表示，树的根节点表示最高级别的管理者，给定两个数中的节点，找出在树中这两个节点的公共父节点。
如下的树中节点 E 和 I 的公共父节点为 B

```
       A
      /  \
     B    C
    / \  /  \
   D   E F   G
 /  \
H    I
```

```python
# O(n) time | O(d) space - where n is the number of people
# in the org and d is the depth (height) of the org chart
def get_lowest_common_manager(top_manager, report_one, report_two):
    # 通过递归来进行树的深度遍历找出公共父节点
    return get_org_info(top_manager, report_one, report_two).lowest_common_manager


def get_org_info(manager, report_one, report_two):
    num_important_reports = 0  # 记录易找到匹配子节点的数量
    for direct_report in manager.direct_reports:
        # 先递归获取子节点的数据
        org_info = get_org_info(direct_report, report_one, report_two)
        # 如果在子级递归中已经找到公共父节点则直接返回
        if org_info.lowest_common_manager is not None:
            return org_info
        # 累加匹配子节点的数量
        num_important_reports += org_info.num_important_reports
    # 如果当前的节点与任意一个待查找的节点匹配则数量增加
    if manager == report_one or manager == report_two:
        num_important_reports += 1
    # 判断是否已经找到两个子节点, 如果是则当前节点为公共父节点, 否则为空
    lowest_common_manager = manager if num_important_reports == 2 else None
    return OrgInfo(lowest_common_manager, num_important_reports)


class OrgInfo:
    def __init__(self, lowest_common_manager, num_important_reports):
        self.lowest_common_manager = lowest_common_manager
        self.num_important_reports = num_important_reports


# This is the input class.
class OrgChart:
    def __init__(self, name):
        self.name = name
        self.direct_reports = []


def initialize():
    D = OrgChart('D')
    I = OrgChart('I')
    D.direct_reports = [OrgChart('H'), I]
    B = OrgChart('B')
    E = OrgChart('E')
    B.direct_reports = [D, E]
    C = OrgChart('C')
    C.direct_reports = [OrgChart('F'), OrgChart('G')]
    A = OrgChart('A')
    A.direct_reports = [B, C]
    return A, E, I


if __name__ == '__main__':
    data = get_lowest_common_manager(*initialize())
    print(data.name)
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

class OrgChart
{
public:
    char name;
    vector<OrgChart *> directReports;

    OrgChart(char name)
    {
        this->name = name;
        this->directReports = {};
    }

    void addDirectReports(vector<OrgChart *> directReports)
    {
        this->directReports = directReports;
    }
};

struct OrgInfo
{
    OrgChart *lowestCommonManager;
    int numImportantReports;
};

OrgInfo getOrgInfo(OrgChart *manager, OrgChart *reportOne, OrgChart *reportTwo);

// O(n) time | O(d) space - where n is the number of people
// in the org and d is the depth (height) of the org chart
OrgChart *getLowestCommonManager(OrgChart *topManager, OrgChart *reportOne,
                                 OrgChart *reportTwo)
{
    return getOrgInfo(topManager, reportOne, reportTwo).lowestCommonManager;
}

OrgInfo getOrgInfo(OrgChart *manager, OrgChart *reportOne,
                   OrgChart *reportTwo)
{
    int numImportantReports = 0;
    for (OrgChart *directReport : manager->directReports)
    {
        OrgInfo orgInfo = getOrgInfo(directReport, reportOne, reportTwo);
        if (orgInfo.lowestCommonManager != nullptr)
            return orgInfo;
        numImportantReports += orgInfo.numImportantReports;
    }
    if (manager == reportOne || manager == reportTwo)
        numImportantReports++;
    OrgChart *lowestCommonManager = numImportantReports == 2 ? manager : nullptr;
    OrgInfo newOrgInfo = {lowestCommonManager, numImportantReports};
    return newOrgInfo;
}

vector<OrgChart *> initialize()
{
    OrgChart *D = new OrgChart('D');
    OrgChart *I = new OrgChart('I');
    D->addDirectReports({new OrgChart('H'), I});
    OrgChart *B = new OrgChart('B');
    OrgChart *E = new OrgChart('E');
    B->addDirectReports({D, E});
    OrgChart *C = new OrgChart('C');
    C->addDirectReports({new OrgChart('F'), new OrgChart('G')});
    OrgChart *A = new OrgChart('A');
    A->addDirectReports({B, C});
    return {A, E, I};
}

int main()
{
    vector<OrgChart *> source = initialize();
    OrgChart *result = getLowestCommonManager(source[0], source[1], source[2]);
    cout << result->name;
    return 0;
}
```

## 交织字符串

给定三个字符串，第一和第二个字符串通过拆解可以组成第三个字符串，且字符的顺序在拆解并重新拼接后相对的顺序不会产生变化，
如 abcd-f 和 e-ghijk 可以拆解拼接成 abcd-ef-ghijk

```python
def are_interwoven1(one, two, three, i, j, cache):
    # 如果缓存记录中已经访问过这个位置则直接返回
    # 这里可能会有 i 或者 j 索引超过实际字符串长度的情况
    if cache[i][j] is not None:
        return cache[i][j]
    # 第三个字符串的索引可以通过第一第二个字符串的索引想加获得
    k = i + j
    # 如果两个索引想加已经等于第三个字符串的长度则表示匹配直接返回对
    if k == len(three):
        return True

    # 判断第一个字符串中的字符是否与第三个字符串的字符相等
    if i < len(one) and one[i] == three[k]:
        # 接着往下匹配
        cache[i][j] = are_interwoven1(one, two, three, i + 1, j, cache)
        # 如果完全匹配则返回, 因为下面还有一个判断步骤, 这里不能直接返回
        if cache[i][j]:
            return True

    # 判断第二个字符串中的字符是否与第三个字符串的字符相等
    if j < len(two) and two[j] == three[k]:
        # 接着往下匹配
        cache[i][j] = are_interwoven1(one, two, three, i, j + 1, cache)
        # 由于下面没有判断的步骤了, 所以这里直接返回
        return cache[i][j]
    # 上面的三个条件都不满足, 记录到缓存中并返回否
    cache[i][j] = False
    return False


# O(n * m) time | O(n * m) space - where n is the length
# of the first string and m is the length of the second string
def interweaving_strings1(one, two, three):
    # 第一第二个字符串的长度想加与第三个字符串不匹配可以直接返回否
    if len(three) != len(one) + len(two):
        return False
    # 缓存已经访问过的位置, 缓存的行和列都加一是为了防止数组越界访问
    cache = [[None for _ in range(len(two) + 1)] for _ in range(len(one) + 1)]
    return are_interwoven1(one, two, three, 0, 0, cache)


def are_interwoven2(one, two, three, i, j):
    # 第三个字符串的索引可以通过第一第二个字符串的索引想加获得
    k = i + j
    # 如果两个索引想加已经等于第三个字符串的长度则表示匹配直接返回对
    if k == len(three):
        return True

    # 判断第一个字符串中的字符是否与第三个字符串的字符相等
    if i < len(one) and one[i] == three[k]:
        # 如果完全匹配则返回, 因为下面还有一个判断步骤, 这里不能直接返回
        if are_interwoven2(one, two, three, i + 1, j):
            return True

    # 判断第二个字符串中的字符是否与第三个字符串的字符相等
    if j < len(two) and two[j] == three[k]:
        # 由于下面没有判断的步骤了, 所以这里直接返回
        return are_interwoven2(one, two, three, i, j + 1)

    # 上面的三个条件都不满足返回否
    return False


# O(2^(n + m)) time | O(n + m) space - where n is the length
# of the first string and m is the length of the second string
def interweaving_strings2(one, two, three):
    # 第一第二个字符串的长度想加与第三个字符串不匹配可以直接返回否
    if len(three) != len(one) + len(two):
        return False
    # 从第一和第二个字符串的首个位置开始搜索
    return are_interwoven2(one, two, three, 0, 0)


if __name__ == '__main__':
    print(interweaving_strings1('algoexpert', 'your-dream-job', 'your-algodream-expertjob'))
    print(interweaving_strings2('algoexpert', 'your-dream-job', 'your-algodream-expertjob'))
```

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

bool areInterwoven1(string one, string two, string three, size_t i, size_t j,
                    vector<vector<int>> &cache);

// O(nm) time | O(nm) space - where n is the length of the
// first string and m is the length of the second string
bool interweavingStrings1(string one, string two, string three)
{
    if (three.size() != one.size() + two.size())
    {
        return false;
    }

    vector<vector<int>> cache;
    for (size_t i = 0; i < one.size() + 1; i++)
    {
        cache.push_back(vector<int>{});
        for (size_t j = 0; j < two.size() + 1; j++)
        {
            cache[i].push_back(-1);
        }
    }

    return areInterwoven1(one, two, three, 0, 0, cache);
}

bool areInterwoven1(string one, string two, string three, size_t i, size_t j,
                    vector<vector<int>> &cache)
{
    if (cache[i][j] != -1)
        return cache[i][j];

    size_t k = i + j;
    if (k == three.size())
        return true;

    if (i < one.size() && one[i] == three[k])
    {
        cache[i][j] = areInterwoven1(one, two, three, i + 1, j, cache);
        if (cache[i][j] == true)
            return true;
    }

    if (j < two.size() && two[j] == three[k])
    {
        cache[i][j] = areInterwoven1(one, two, three, i, j + 1, cache);
        return cache[i][j];
    }

    cache[i][j] = false;
    return false;
}

bool areInterwoven2(string one, string two, string three, size_t i, size_t j);

// O(2^(n + m)) time | O(n + m) space - where n is the length
// of the first string and m is the length of the second string
bool interweavingStrings2(string one, string two, string three)
{
    if (three.size() != one.size() + two.size())
    {
        return false;
    }

    return areInterwoven2(one, two, three, 0, 0);
}

bool areInterwoven2(string one, string two, string three, size_t i, size_t j)
{
    size_t k = i + j;
    if (k == three.size())
        return true;

    if (i < one.size() && one[i] == three[k])
    {
        if (areInterwoven2(one, two, three, i + 1, j))
            return true;
    }

    if (j < two.size() && two[j] == three[k])
    {
        return areInterwoven2(one, two, three, i, j + 1);
    }

    return false;
}

int main()
{
    cout << interweavingStrings1("algoexpert", "your-dream-job", "your-algodream-expertjob") << endl;
    cout << interweavingStrings2("algoexpert", "your-dream-job", "your-algodream-expertjob") << endl;
    return 0;
}
```

## 解决数独

```python
# O(1) time | O(1) space - assuming a 9x9 input board
def solve_sudoku(board):
    # 从左上角的位置开始
    solve_partial_sudoku(0, 0, board)
    return board


def solve_partial_sudoku(row, col, board):
    current_row = row
    current_col = col

    # 如果列索引越界则切换到下一行
    if current_col == len(board[current_row]):
        current_row += 1  # 切换到下一行
        current_col = 0  # 重置列索引到开头
        # 如果行索引越界则表示已经处理完成
        if current_row == len(board):
            return True  # board is completed

    # 需要填空的格子值为 0
    if board[current_row][current_col] == 0:
        # 尝试填写数字
        return try_digits_at_position(current_row, current_col, board)
    # 如果是不需要处理的格子则继续向右走
    return solve_partial_sudoku(current_row, current_col + 1, board)


def try_digits_at_position(row, col, board):
    # 尝试填写数字 1 到 9
    for digit in range(1, 10):
        # 如果行, 列, 宫格都符合条件则填写
        if is_valid_at_position(digit, row, col, board):
            board[row][col] = digit
            # 接着尝试下一个格子, 这里是产生递归的关键, 因为第一个需要填写的格子一定存在一个有效的值
            # 所以尝试填写第一个格子不会走到下面的返回逻辑, 从而实现调用递归
            if solve_partial_sudoku(row, col + 1, board):
                # 第一个需要填写的格子必定会走到这一步
                return True
    # 没有符合条件的值, 还原为 0 表示未填写
    board[row][col] = 0
    # 返回否表示这个格子尝试失败
    return False


def is_valid_at_position(value, row, col, board):
    # 规则一 行不能有重复的值
    row_is_valid = value not in board[row]
    # 规则二 列不能有重复的值
    column_is_valid = value not in map(lambda r: r[col], board)

    if not row_is_valid or not column_is_valid:
        return False

    # Check subgrid constraint.
    subgrid_row_start = (row // 3) * 3
    subgrid_col_start = (col // 3) * 3
    # 规则三 格子所在的九宫格区域里不能有重复的值
    for row_idx in range(3):
        for col_idx in range(3):
            row_to_check = subgrid_row_start + row_idx
            col_to_check = subgrid_col_start + col_idx
            existing_value = board[row_to_check][col_to_check]

            if existing_value == value:
                return False

    # 上面三个规则都符合则返回对
    return True


if __name__ == '__main__':
    source = [
        [7, 8, 0, 4, 0, 0, 1, 2, 0],
        [6, 0, 0, 0, 7, 5, 0, 0, 9],
        [0, 0, 0, 6, 0, 1, 0, 7, 8],
        [0, 0, 7, 0, 4, 0, 2, 6, 0],
        [0, 0, 1, 0, 5, 0, 9, 3, 0],
        [9, 0, 4, 0, 6, 0, 0, 0, 5],
        [0, 7, 0, 3, 0, 0, 0, 1, 2],
        [1, 2, 0, 0, 0, 7, 4, 0, 0],
        [0, 4, 9, 2, 0, 6, 0, 0, 7]
    ]
    for rows in solve_sudoku(source):
        print(rows)
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

bool solvePartialSudoku(int row, int col, vector<vector<int>> &board);
bool tryDigitsAtPosition(int row, int col, vector<vector<int>> &board);
bool isValidAtPosition(int value, int row, int col, vector<vector<int>> &board);

// O(1) time | O(1) space - assuming a 9x9 input board
vector<vector<int>> solveSudoku(vector<vector<int>> board)
{
    solvePartialSudoku(0, 0, board);
    return board;
}

bool solvePartialSudoku(int row, int col, vector<vector<int>> &board)
{
    int currentRow = row;
    int currentCol = col;

    const int rowSize = board[currentRow].size();
    if (currentCol == rowSize)
    {
        currentRow++;
        currentCol = 0;
        const int boardSize = board.size();
        if (currentRow == boardSize)
            return true;
    }

    if (board[currentRow][currentCol] == 0)
    {
        return tryDigitsAtPosition(currentRow, currentCol, board);
    }

    return solvePartialSudoku(currentRow, currentCol + 1, board);
}

bool tryDigitsAtPosition(int row, int col, vector<vector<int>> &board)
{
    for (int digit = 1; digit < 10; digit++)
    {
        if (isValidAtPosition(digit, row, col, board))
        {
            board[row][col] = digit;
            if (solvePartialSudoku(row, col + 1, board))
                return true;
        }
    }

    board[row][col] = 0;
    return false;
}

bool isValidAtPosition(int value, int row, int col,
                       vector<vector<int>> &board)
{
    bool rowIsValid =
        find(board[row].begin(), board[row].end(), value) == board[row].end();
    bool colIsValid = true;
    for (auto arr : board)
    {
        if (arr[col] == value)
        {
            colIsValid = false;
            break;
        }
    }

    if (!rowIsValid || !colIsValid)
        return false;

    // Check subgrid constraint.
    int subgridRowStart = row / 3 * 3;
    int subgridColStart = col / 3 * 3;
    for (int rowIdx = 0; rowIdx < 3; rowIdx++)
    {
        for (int colIdx = 0; colIdx < 3; colIdx++)
        {
            int rowToCheck = subgridRowStart + rowIdx;
            int colToCheck = subgridColStart + colIdx;
            int existingValue = board[rowToCheck][colToCheck];

            if (existingValue == value)
                return false;
        }
    }

    return true;
}

int main()
{
    vector<vector<int>> source = {
        {7, 8, 0, 4, 0, 0, 1, 2, 0},
        {6, 0, 0, 0, 7, 5, 0, 0, 9},
        {0, 0, 0, 6, 0, 1, 0, 7, 8},
        {0, 0, 7, 0, 4, 0, 2, 6, 0},
        {0, 0, 1, 0, 5, 0, 9, 3, 0},
        {9, 0, 4, 0, 6, 0, 0, 0, 5},
        {0, 7, 0, 3, 0, 0, 0, 1, 2},
        {1, 2, 0, 0, 0, 7, 4, 0, 0},
        {0, 4, 9, 2, 0, 6, 0, 0, 7}};
    source = solveSudoku(source);
    for (vector<int> rows : source)
    {
        cout << "[ ";
        for (int element : rows)
        {
            cout << element << " ";
        }
        cout << "]" << endl;
    }
    return 0;
}
```

## 生成 DIV 标签

```python
# O((2n)!/((n!((n + 1)!)))) time | O((2n)!/((n!((n + 1)!)))) space -
# where n is the input number
def generate_div_tags(number_of_tags):
    matched_div_tags = []
    # 递归创建, 传递所需的开始标签数量和结束标签数量
    generate_div_tags_from_prefix(number_of_tags, number_of_tags, '', matched_div_tags)
    return matched_div_tags


def generate_div_tags_from_prefix(opening_tags_needed, closing_tags_needed, prefix, result):
    # 如果开始标签大于零
    if opening_tags_needed > 0:
        # 追加开始标签
        new_prefix = prefix + '<div>'
        # 开始标签数量减一, 继续递归
        generate_div_tags_from_prefix(opening_tags_needed - 1, closing_tags_needed, new_prefix, result)

    # 如果开始标签小于结束标签
    if opening_tags_needed < closing_tags_needed:
        # 追加结束标签
        new_prefix = prefix + '</div>'
        # 结束标签数量减一, 继续递归
        generate_div_tags_from_prefix(opening_tags_needed, closing_tags_needed - 1, new_prefix, result)

    # 由于是先减少开始标签数量, 所以这里只需要判断结束标签数量为零时存储计算结果
    if closing_tags_needed == 0:
        result.append(prefix)


if __name__ == '__main__':
    print(generate_div_tags(3))
```

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

void generateDivTagsFromPrefix(int openingTagsNeeded, int closingTagsNeeded,
                               string prefix, vector<string> &result);

// O((2n)!/((n!((n + 1)!)))) time | O((2n)!/((n!((n + 1)!)))) space -
// where n is the input number
vector<string> generateDivTags(int numberOfTags)
{
    vector<string> matchedDivTags;
    generateDivTagsFromPrefix(numberOfTags, numberOfTags, "", matchedDivTags);
    return matchedDivTags;
}

void generateDivTagsFromPrefix(int openingTagsNeeded, int closingTagsNeeded,
                               string prefix, vector<string> &result)
{
    if (openingTagsNeeded > 0)
    {
        string newPrefix = prefix + "<div>";
        generateDivTagsFromPrefix(openingTagsNeeded - 1, closingTagsNeeded,
                                  newPrefix, result);
    }

    if (openingTagsNeeded < closingTagsNeeded)
    {
        string newPrefix = prefix + "</div>";
        generateDivTagsFromPrefix(openingTagsNeeded, closingTagsNeeded - 1,
                                  newPrefix, result);
    }

    if (closingTagsNeeded == 0)
        result.push_back(prefix);
}

int main()
{
    vector<string> result = generateDivTags(3);
    for (string element : result)
    {
        cout << element << endl;
    }
    return 0;
}
```

## 计算测量范围

给定一组量杯，每组量杯只有一个最小值刻度和最大值刻度，给定一个测量范围，判断这个测量范围是否可以使用这一组量杯完成测量。
量杯可以多次使用，但是每次量杯装下的范围必须是在量杯的范围之内，不能超出量杯的可测量最小或最大范围

```python
# O(low * high * n) time | O(low * high) space - where n is the number of measuring cups
def ambiguous_measurements(measuring_cups, low, high):
    # 缓存已经处理过的测量范围
    memoization = {}
    # 递归处理
    return can_measure_in_range(measuring_cups, low, high, memoization)


def can_measure_in_range(measuring_cups, low, high, memoization):
    # 使用测量范围创建一个 hash key
    memoize_key = create_hashable_key(low, high)
    # 如果已经有缓存则直接返回
    if memoize_key in memoization:
        return memoization[memoize_key]

    # 测量范围不能是负数或者零
    if low <= 0 and high <= 0:
        return False

    # 先预置一个值缓存当前函数的判断结果
    can_measure = False
    for cup_low, cup_high in measuring_cups:
        # 如果量杯的测量范围能够测量当前的范围
        # 量杯的最小范围大于等于测量最小范围
        # 量杯的最大范围小于等于测量最大范围
        if low <= cup_low and cup_high <= high:
            can_measure = True
            # 已经有满足条件的情况, 不用再继续递归
            break

        # 根据量杯的测量范围计算新的测量范围
        new_low = max(0, low - cup_low)
        new_high = max(0, high - cup_high)
        # 递归处理
        can_measure = can_measure_in_range(measuring_cups, new_low, new_high, memoization)
        if can_measure:
            # 已经有满足条件的情况, 不用再继续递归
            break

    # 缓存当前的已经处理过的测量范围
    memoization[memoize_key] = can_measure
    return can_measure


def create_hashable_key(low, high):
    return '{0}:{1}'.format(low, high)


if __name__ == '__main__':
    print(ambiguous_measurements(
        [[200, 210],
         [450, 465],
         [800, 850]], 2100, 2300
    ))
```

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <string>
using namespace std;

bool canMeasureInRange(vector<vector<int>> &measuringCups, int low, int high,
                       unordered_map<string, bool> &memoization);
string createHashableKey(int low, int high);

// O(low * high * n) time | O(low * high) space - where n is the number of
// measuring cups
bool ambiguousMeasurements(vector<vector<int>> measuringCups, int low,
                           int high)
{
    unordered_map<string, bool> memoization;
    return canMeasureInRange(measuringCups, low, high, memoization);
}

bool canMeasureInRange(vector<vector<int>> &measuringCups, int low, int high,
                       unordered_map<string, bool> &memoization)
{
    string memoizeKey = createHashableKey(low, high);
    if (memoization.find(memoizeKey) != memoization.end())
    {
        return memoization[memoizeKey];
    }

    if (low <= 0 && high <= 0)
    {
        return false;
    }

    bool canMeasure = false;
    for (auto cup : measuringCups)
    {
        int cupLow = cup[0];
        int cupHigh = cup[1];
        if (low <= cupLow && cupHigh <= high)
        {
            canMeasure = true;
            break;
        }

        int newLow = max(0, low - cupLow);
        int newHigh = max(0, high - cupHigh);
        canMeasure = canMeasureInRange(measuringCups, newLow, newHigh, memoization);
        if (canMeasure)
            break;
    }

    memoization[memoizeKey] = canMeasure;
    return canMeasure;
}

string createHashableKey(int low, int high)
{
    return to_string(low) + ":" + to_string(high);
}

int main()
{
    cout << ambiguousMeasurements({{200, 210},
                                   {450, 465},
                                   {800, 850}},
                                  2100, 2300);
    return 0;
}
```

Last Modified 2022-04-03

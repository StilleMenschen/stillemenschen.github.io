# 栈

## 最小最大栈

```python
class MinMaxStack:
    def __init__(self):
        self.min_max_stack = []
        self.stack = []

    # O(1) time | O(1) space
    def peek(self):
        if len(self.stack):
            return self.stack[-1]
        else:
            return None

    # O(1) time | O(1) space
    def pop(self):
        if len(self.stack):
            self.min_max_stack.pop()
            return self.stack.pop()
        else:
            return None

    # O(1) time | O(1) space
    def push(self, number):
        self.stack.append(number)
        if len(self.min_max_stack):
            mi = min(self.min_max_stack[-1][0], number)
            ma = max(self.min_max_stack[-1][1], number)
        else:
            mi = ma = number
        self.min_max_stack.append([mi, ma])

    # O(1) time | O(1) space
    def get_min(self):
        if len(self.min_max_stack):
            return self.min_max_stack[-1][0]
        else:
            return None

    # O(1) time | O(1) space
    def get_max(self):
        if len(self.min_max_stack):
            return self.min_max_stack[-1][1]
        else:
            return None

def print_stack(s):
    print(f'min {s.get_min()}, max {s.get_max()}, peek {s.peek()}')

if __name__ == '__main__':
    s = MinMaxStack()
    s.push(5)
    print_stack(s)
    s.push(7)
    print_stack(s)
    s.push(2)
    print_stack(s)
    s.pop()
    print_stack(s)
    s.pop()
    print_stack(s)
```

```cpp
#include <iostream>
using namespace std;

typedef struct Node
{
    struct Node *next;
    int value;
} Node, *pNode;

typedef struct MinMaxStack
{
    struct MinMaxStack *next;
    int min, max;
} MinMaxStack, *pMinMaxStack;

class Stack
{
private:
    pNode stack = nullptr;
    pMinMaxStack minMaxStack = nullptr;

public:
    /* O(1) time | O(1) space */
    int peek()
    {
        if (this->stack != nullptr)
            return this->stack->value;
        return -1;
    }
    /* O(1) time | O(1) space */
    int pop()
    {
        const pNode first = this->stack;
        if (this->stack->next != nullptr)
            this->stack = this->stack->next;
        else
            this->stack = nullptr;
        if (this->minMaxStack != nullptr)
        {
            this->minMaxStack = this->minMaxStack->next;
        }
        if (first != nullptr)
            return first->value;
        return -1;
    }
    /* O(1) time | O(1) space */
    void push(const int number)
    {
        pMinMaxStack newMinMax = new MinMaxStack;
        newMinMax->min = number;
        newMinMax->max = number;
        newMinMax->next = nullptr;
        if (this->minMaxStack != nullptr)
        {
            pMinMaxStack last = this->minMaxStack;
            newMinMax->min = last->min < number ? last->min : number;
            newMinMax->max = last->max > number ? last->max : number;
            newMinMax->next = this->minMaxStack;
        }
        this->minMaxStack = newMinMax;
        pNode top = new Node;
        top->value = number;
        if (this->stack != nullptr)
            top->next = this->stack;
        else
            top->next = nullptr;
        this->stack = top;
    }
    /* O(1) time | O(1) space */
    int getMin()
    {
        if (this->minMaxStack != nullptr)
            return this->minMaxStack->min;
        return -1;
    }
    /* O(1) time | O(1) space */
    int getMax()
    {
        if (this->minMaxStack != nullptr)
            return this->minMaxStack->max;
        return -1;
    }
};

void printStack(Stack stk)
{
    cout << "min " << stk.getMin() << ", max " << stk.getMax() << ", peek " << stk.peek() << endl;
}

int main()
{
    Stack stk;
    stk.push(2);
    printStack(stk);
    stk.push(7);
    stk.push(5);
    cout << "push 2, 7, 5 to stack" << endl;
    printStack(stk);
    stk.pop();
    stk.pop();
    printStack(stk);
    stk.pop();
    cout << "pop stack 3 times" << endl;
    printStack(stk);
    return 0;
}
```

```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class MinMaxStack {
public:
  vector<unordered_map<string, int>> minMaxStack = {};
  vector<int> stack = {};

  // O(1) time | O(1) space
  int peek() { return stack[stack.size() - 1]; }

  // O(1) time | O(1) space
  int pop() {
    minMaxStack.pop_back();
    int result = stack[stack.size() - 1];
    stack.pop_back();
    return result;
  }

  // O(1) time | O(1) space
  void push(int number) {
    unordered_map<string, int> newMinMax = {{"min", number}, {"max", number}};
    if (minMaxStack.size()) {
      unordered_map<string, int> lastMinMax =
          minMaxStack[minMaxStack.size() - 1];
      newMinMax["min"] = min(lastMinMax["min"], number);
      newMinMax["max"] = max(lastMinMax["max"], number);
    }
    minMaxStack.push_back(newMinMax);
    stack.push_back(number);
  }

  // O(1) time | O(1) space
  int getMin() { return minMaxStack[minMaxStack.size() - 1]["min"]; }

  // O(1) time | O(1) space
  int getMax() { return minMaxStack[minMaxStack.size() - 1]["max"]; }
};
```

## 平衡括号

```python
# O(n) time | O(n) space
def balanced_brackets(string):
    opening_brackets = '([{'
    closing_brackets = ')]}'
    matching_brackets = {')': '(', ']': '[', '}': '{'}
    stack = []
    for char in string:
        if char in opening_brackets:
            stack.append(char)
        elif char in closing_brackets:
            if len(stack) == 0:
                return False
            if stack[-1] == matching_brackets[char]:
                stack.pop()
            else:
                return False
    return len(stack) == 0


if __name__ == '__main__':
    source = '([])(){}(())()()'
    print(balanced_brackets(source))
```

```cpp
#include <iostream>
#include <string>
#include <unordered_map>
#include <stack>

using namespace std;

// O(n) time | O(n) space
bool balancedBrackets(string str)
{
    string openingBrackets = "([{";
    string closingBrackets = ")]}";
    unordered_map<char, char> matchingBrackets{
        {')', '('}, {']', '['}, {'}', '{'}};
    stack<char> stack;
    for (char character : str)
    {
        if (openingBrackets.find(character) != string::npos)
        {
            stack.push(character);
        }
        else if (closingBrackets.find(character) != string::npos)
        {
            if (stack.size() == 0)
            {
                return false;
            }
            if (stack.top() == matchingBrackets[character])
            {
                stack.pop();
            }
            else
            {
                return false;
            }
        }
    }
    return stack.size() == 0;
}

int main()
{
    string source = "([])(){}(())()()";
    cout << balancedBrackets(source);
    return 0;
}
```

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node
{
    struct Node *next;
    char value;
} Node, *pNode;

/* O(n) time | O(n) space */
int balancedBrackets(char* brackets)
{
    pNode head, node;
    head = node = NULL;
    while  ( *brackets != '\0' )
    {
        if ( *brackets == '[' || *brackets == '(' || *brackets == '{' || *brackets == '<' )
        {
            if ( head == NULL )
            {
                head = (Node*)malloc(sizeof(Node));
                head->value = *brackets;
                head->next = NULL;
            }
            else
            {
                node = (Node*)malloc(sizeof(Node));
                node->value = *brackets;
                node->next = head;
                head = node;
            }
        }
        else if ( *brackets == ']' || *brackets == ')' || *brackets == '}' || *brackets == '>' )
        {
            if ( head == NULL ) return 0;
            else
            {
                switch (*brackets)
                {
                case ']':
                    if ( head->value != '[') return 0;
                    break;
                case ')':
                    if ( head->value != '(') return 0;
                    break;
                case '}':
                    if ( head->value != '{') return 0;
                    break;
                case '>':
                    if ( head->value != '<') return 0;
                    break;
                }
                head = head->next;
            }
        }
        else
        {
            return 0;
        }
        *brackets++;
    }
    if ( head != NULL ) return 0;
    return 1;
}

int main()
{
    char brackets[] = "[[<>(())]]{}{()}";
    printf("%s is %d", brackets, balancedBrackets(brackets));
    return 0;
}
```

## 短路径生成

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct Node
{
    struct Node *prev, *next;
    char *value;
} Node, *pNode;

typedef struct LinkedList
{
    pNode head, tail;
    int size;
} LinkedList, *pLinkedList;

char* join(char* a, char* b)
{
    char *c = (char*)malloc(strlen(a) + strlen(b) + 1);
    char *head = c;
    while( *a != '\0') *c++ = *a++;
    while( (*c++ = *b++) != '\0') ;
    return head;
}

/* O(n) time | O(n) space */
LinkedList* shortenPath(char brackets[])
{
    const unsigned int startsWithSlash = brackets[0] == '/' ? 1 : 0;
    const char s[2] = "/";
    char *token;
    pNode node;
    pLinkedList lists = (LinkedList*)malloc(sizeof(LinkedList));
    lists->head = (Node*)malloc(sizeof(Node));
    lists->head->prev = NULL;
    lists->size = 0;

    /* 获取第一个子字符串 */
    token = strtok(brackets, s);
    while ( token != NULL && ( strlen(token) <=0 || strcmp(token, ".") == 0 ) )
        token = strtok(NULL, s);
    lists->head->value = token;
    lists->size++;
    lists->tail = lists->head;


    /* 继续获取其他的子字符串 */
    while( token != NULL )
    {
        token = strtok(NULL, s);
        if ( token == NULL || strlen(token) <= 0 || strcmp(token, ".") == 0 ) continue;
        if ( strcmp(token, "..") == 0 && lists->size > 0 )
        {
            lists->tail = lists->tail->prev;
            lists->size--;
            continue;
        }
        node = (Node*)malloc(sizeof(Node));
        node->value = join(token, (char*)"/");
        if ( lists->size == 0 )
        {
            lists->head = node;
            lists->head->prev = NULL;
            lists->tail = node;
            lists->tail->next = NULL;
            lists->size++;
            continue;
        }
        node->prev = lists->tail;
        lists->tail->next = node;
        lists->tail = node;
        lists->size++;
    }
    lists->tail->next = NULL;
    if ( startsWithSlash )
    {
        node = (Node*)malloc(sizeof(Node));
        node->value = (char*)"/";
        node->prev = NULL;
        node->next = lists->head;
        lists->head = node;
    }
    return lists;
}

void printLinkedLists(Node* node)
{
    while ( node != NULL )
    {
        printf("%s", node->value);
        node = node->next;
    }
    printf("\n");
}

int main()
{
    char brackets[] = "/foo/../test/../test/../foo//bar/./baz";
    pLinkedList lists = shortenPath(brackets);
    printLinkedLists(lists->head);
    return 0;
}
```

## 日落景观

给出两个输入参数，第一个参数为一个数组，表示建筑物的高度，第二个参数表示建筑朝向，固定值为`EAST`和`WEST`，
`EAST`表示向右看可以看见日落，而`WEST`表示向左看可以看见日落，找出可以看见日落的建筑物数组下标

```python
# O(n) time | O(n) space - where n is the length of the input array
def sunset_views1(buildings, direction):
    length = len(buildings)
    if not length:
        return []
    idx = 0
    step = 1
    if direction == 'EAST':
        idx = length - 1
        step = -1
    stack = []
    last_max_height = float('-inf')
    while 0 <= idx < length:
        height = buildings[idx]
        if last_max_height < height:
            last_max_height = height
            if direction == 'EAST':
                stack.insert(0, idx)
            else:
                stack.append(idx)
        idx += step
    return stack


# O(n) time | O(n) space - where n is the length of the input array
def sunset_views2(buildings, direction):
    candidate_buildings = []

    start_idx = 0 if direction == "EAST" else len(buildings) - 1
    step = 1 if direction == "EAST" else -1

    idx = start_idx
    while 0 <= idx < len(buildings):
        building_height = buildings[idx]

        while len(candidate_buildings) > 0 and buildings[candidate_buildings[-1]] <= building_height:
            candidate_buildings.pop()

        candidate_buildings.append(idx)

        idx += step

    if direction == "WEST":
        return candidate_buildings[::-1]

    return candidate_buildings


if __name__ == '__main__':
    source = [3, 5, 4, 4, 3, 1, 3, 2]
    print(sunset_views1(source, 'EAST'))
    print(sunset_views2(source, 'WEST'))
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// O(n) time | O(n) space - where n is the length of the input array
vector<int> sunsetViews1(vector<int> buildings, string direction)
{
    vector<int> buildingsWithSunsetViews;

    int startIdx = buildings.size() - 1;
    int step = -1;

    if (direction == "WEST")
    {
        startIdx = 0;
        step = 1;
    }

    size_t idx = startIdx;
    int runningMaxHeight = 0;
    // int runningMaxHeight = INT_MIN;

    while (idx >= 0 && idx < buildings.size())
    {
        int buildingHeight = buildings[idx];

        if (buildingHeight > runningMaxHeight)
        {
            buildingsWithSunsetViews.push_back(idx);
            // runningMaxHeight = buildingHeight;
        }

        runningMaxHeight = max(runningMaxHeight, buildingHeight);

        idx += step;
    }

    if (direction == "EAST")
    {
        reverse(buildingsWithSunsetViews.begin(), buildingsWithSunsetViews.end());
    }

    return buildingsWithSunsetViews;
}

// O(n) time | O(n) space - where n is the length of the input array
vector<int> sunsetViews2(vector<int> buildings, string direction)
{
    vector<int> candidateBuildings;

    int startIdx = buildings.size() - 1;
    int step = -1;

    if (direction == "EAST")
    {
        startIdx = 0;
        step = 1;
    }

    size_t idx = startIdx;
    while (idx >= 0 && idx < buildings.size())
    {
        int buildingHeight = buildings[idx];

        while (candidateBuildings.size() > 0 &&
               buildings[candidateBuildings[candidateBuildings.size() - 1]] <=
                   buildingHeight)
        {
            candidateBuildings.pop_back();
        }

        candidateBuildings.push_back(idx);

        idx += step;
    }

    if (direction == "WEST")
    {
        reverse(candidateBuildings.begin(), candidateBuildings.end());
    }

    return candidateBuildings;
}

void iterArray(vector<int> array)
{
    for (const int element : array)
    {
        cout << element << " ";
    }
    cout << endl;
}

int main()
{
    vector<int> source = {3, 5, 4, 4, 3, 1, 3, 2};
    iterArray(sunsetViews1(source, "EAST"));
    iterArray(sunsetViews1(source, "WEST"));
    return 0;
}
```

## 栈排序

排序栈中的所有元素，只能调用出栈、入栈和查看栈顶元素三个方法，且必须使用递归实现

```python
def insert_in_sorted_order(stack, value):
    if not len(stack) or stack[-1] <= value:
        stack.append(value)
    else:
        top = stack.pop()
        insert_in_sorted_order(stack, value)
        stack.append(top)


# O(n^2) time | O(n) space
def sort_stack(stack):
    if not len(stack):
        return stack

    top = stack.pop()

    sort_stack(stack)
    insert_in_sorted_order(stack, top)

    return stack


if __name__ == '__main__':
    source = [-3, -5, 1, 4, 6, 8, 2]
    print(sort_stack(source))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

void insertInSortedOrder(vector<int> &stack, int value)
{
    if (stack.empty() || stack.back() <= value)
    {
        stack.push_back(value);
    }
    else
    {
        int top = stack.back();
        stack.pop_back();
        insertInSortedOrder(stack, value);
        stack.push_back(top);
    }
}

// O(n^2) time | O(n) space
vector<int> sortStack(vector<int> &stack)
{
    if (stack.empty())
    {
        return stack;
    }

    int top = stack.back();
    stack.pop_back();

    sortStack(stack);
    insertInSortedOrder(stack, top);

    return stack;
}

int main()
{
    vector<int> stack = {-3, -5, 1, 4, 6, 8, 2};
    stack = sortStack(stack);
    for (const int element : stack)
    {
        cout << element << " ";
    }
    return 0;
}
```

## 下一个大于的元素

给出一个数组，找出数组中每个元素向后查找大于此元素的元素，并记录到新的数组中。
查找允许循环，既查找到最后一个元素时可以从头开始查找

```python
# O(n) time | O(n) space - where n is the length of the array
def next_greater_element1(array):
    result = [-1] * len(array)
    stack = []

    for idx in range(2 * len(array)):
        circular_idx = idx % len(array)

        while len(stack) > 0 and array[stack[-1]] < array[circular_idx]:
            top = stack.pop()
            result[top] = array[circular_idx]

        stack.append(circular_idx)

    return result


# O(n) time | O(n) space - where n is the length of the array
def next_greater_element2(array):
    result = [-1] * len(array)
    stack = []

    for idx in range(2 * len(array) - 1, -1, -1):
        circular_idx = idx % len(array)

        while len(stack) > 0:
            if stack[len(stack) - 1] <= array[circular_idx]:
                stack.pop()
            else:
                result[circular_idx] = stack[len(stack) - 1]
                break

        stack.append(array[circular_idx])

    return result


if __name__ == '__main__':
    source = [2, 5, -3, -4, 6, 7, 2]
    print(next_greater_element1(source))
    print(next_greater_element2(source))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

// O(n) time | O(n) space - where n is the length of the array
vector<int> nextGreaterElement1(vector<int> array)
{
    vector<int> result(array.size(), -1);
    vector<int> stack;

    for (size_t idx = 0; idx < 2 * array.size(); idx++)
    {
        int circularIdx = idx % array.size();

        while (stack.size() > 0 &&
               array[stack.back()] < array[circularIdx])
        {
            int top = stack.back();
            stack.pop_back();
            result[top] = array[circularIdx];
        }

        stack.push_back(circularIdx);
    }

    return result;
}

// O(n) time | O(n) space - where n is the length of the array
vector<int> nextGreaterElement2(vector<int> array)
{
    vector<int> result(array.size(), -1);
    vector<int> stack;

    for (int idx = 2 * array.size() - 1; idx > -1; idx--)
    {
        int circularIdx = idx % array.size();

        while (stack.size() > 0)
        {
            if (stack[stack.size() - 1] <= array[circularIdx])
            {
                stack.pop_back();
            }
            else
            {
                result[circularIdx] = stack[stack.size() - 1];
                break;
            }
        }

        stack.push_back(array[circularIdx]);
    }

    return result;
}

void iterArray(vector<int> array)
{
    for (const int element : array)
    {
        cout << element << " ";
    }
    cout << endl;
}

int main()
{
    vector<int> source = {-3, -5, 1, 4, 6, 8, 2};
    iterArray(nextGreaterElement1(source));
    iterArray(nextGreaterElement2(source));
    return 0;
}
```

Last Modified 2022-01-19

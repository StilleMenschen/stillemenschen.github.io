# 二叉搜索树

它或者是一棵空树，或者是具有下列性质的二叉树： 若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值； 若它的右子
树不空，则右子树上所有结点的值均大于它的根结点的值； 它的左、右子树也分别为二叉排序树。

## 构建二叉搜索数

```python
class BST:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

    # Average: O(log(n)) time | O(1) space
    # Worst: O(n) time | O(1) space
    def insert(self, value):
        current_node = self
        while True:
            if value < current_node.value:
                if current_node.left is None:
                    current_node.left = BST(value)
                    break
                else:
                    current_node = current_node.left
            else:
                if current_node.right is None:
                    current_node.right = BST(value)
                    break
                else:
                    current_node = current_node.right
        return self

    # Average: O(log(n)) time | O(1) space
    # Worst: O(n) time | O(1) space
    def contains(self, value):
        current_node = self
        while current_node is not None:
            if value < current_node.value:
                current_node = current_node.left
            elif value > current_node.value:
                current_node = current_node.right
            else:
                return True
        return False

    # Average: O(log(n)) time | O(1) space
    # Worst: O(n) time | O(1) space
    def remove(self, value, parent_node=None):
        current_node = self
        while current_node is not None:
            if value < current_node.value:
                parent_node = current_node
                current_node = current_node.left
            elif value > current_node.value:
                parent_node = current_node
                current_node = current_node.right
            else:
                if current_node.left is not None and current_node.right is not None:
                    current_node.value = current_node.right.get_min_value()
                    current_node.right.remove(current_node.value, current_node)
                elif parent_node is None:
                    if current_node.left is not None:
                        current_node.value = current_node.left.value
                        current_node.right = current_node.left.right
                        current_node.left = current_node.left.left
                    elif current_node.right is not None:
                        current_node.value = current_node.right.value
                        current_node.left = current_node.right.left
                        current_node.right = current_node.right.right
                    else:
                        current_node.value = None
                elif parent_node.left == current_node:
                    parent_node.left = current_node.left if current_node.left is not None else current_node.right
                elif parent_node.right == current_node:
                    parent_node.right = current_node.left if current_node.left is not None else current_node.right
                break
        return self

    def get_min_value(self):
        current_node = self
        while current_node.left is not None:
            current_node = current_node.left
        return current_node.value


def in_order_traverse(tree, array):
    arr = list()
    while tree is not None or len(arr) > 0:
        while tree is not None:
            arr.append(tree)
            tree = tree.left
        if len(arr) > 0:
            tree = arr.pop()
            array.append(tree.value)
            tree = tree.right
    return array


if __name__ == '__main__':
    root_node = BST(10)
    for e in [2, 5, 5, 1, 14, 22, 13, 15]:
        root_node = root_node.insert(e)
    root_node.remove(5)
    print(root_node.contains(5))
    print(in_order_traverse(root_node, list()))
```

```cpp
#include <vector>
using namespace std;

class BST
{
public:
    int value;
    BST *left;
    BST *right;

    BST(int val)
    {
        value = val;
        left = nullptr;
        right = nullptr;
    }

    // Average: O(log(n)) time | O(1) space
    // Worst: O(n) time | O(1) space
    BST &insert(int val)
    {
        if (val < value )
        {
            if ( left == nullptr )
            {
                BST *item = new BST(val);
                left = item;
            }
            else
            {
                left->insert(val);
            }
        }
        else
        {
            if ( right == nullptr )
            {
                BST *item = new BST(val);
                right = item;
            }
            else
            {
                right->insert(val);
            }
        }
        return *this;
    }

    // Average: O(log(n)) time | O(1) space
    // Worst: O(n) time | O(1) space
    bool contains(int val)
    {
        if (val < value )
        {
            if ( left == nullptr )
            {
                return false;
            }
            else
            {
                return left->contains(val);
            }
        }
        else if (val > value )
        {
            if ( right == nullptr )
            {
                return false;
            }
            else
            {
                return right->contains(val);
            }
        }
        else
        {
            return true;
        }
    }

    // Average: O(log(n)) time | O(1) space
    // Worst: O(n) time | O(1) space
    BST &remove(int val, BST *parent = nullptr)
    {
        if ( val < value )
        {
            left->remove(val, this);
        }
        else if ( val > value )
        {
            right->remove(val, this);
        }
        else
        {
            if ( left != nullptr && right != nullptr )
            {
                value = right->getMinValue();
                right->remove(value, this);
            }
            else if ( parent == nullptr )
            {
                if ( left != nullptr )
                {
                    value = left->value;
                    right = left->right;
                    left = left->left;
                }
                else if ( right != nullptr )
                {
                    value = right->value;
                    left = right->left;
                    right = right->right;
                }
                else {}
            }
            else if ( parent->left == this )
            {
                parent->left = left != nullptr ? left : right;
            }
            else if ( parent->right == this )
            {
                parent->right = left != nullptr ? left : right;
            }
        }
        return *this;
    }

    int getMinValue()
    {
        if ( left == nullptr )
        {
            return value;
        }
        else
        {
            return left->getMinValue();
        }
    }
};

int main()
{
    vector<int> a = {10, 5, 15, 2, 5, 13, 22, 1, 14};
    BST *root = new BST(a[0]);
    for (int i = 1; i < a.size(); i++)
        root->insert(a[i]);
    root->remove(10);
    root->contains(15);
    return 0;
}
```

## 遍历二叉数

```python
class BST:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

    def insert(self, value):
        current_node = self
        while True:
            if value < current_node.value:
                if current_node.left is None:
                    current_node.left = BST(value)
                    break
                else:
                    current_node = current_node.left
            else:
                if current_node.right is None:
                    current_node.right = BST(value)
                    break
                else:
                    current_node = current_node.right
        return self


# O(n) time | O(n) space
def in_order_traverse1(tree, array):
    if tree is not None:
        in_order_traverse1(tree.left, array)
        array.append(tree.value)
        in_order_traverse1(tree.right, array)
    return array


# O(n) time | O(n) space
def pre_order_traverse1(tree, array):
    if tree is not None:
        array.append(tree.value)
        pre_order_traverse1(tree.left, array)
        pre_order_traverse1(tree.right, array)
    return array


# O(n) time | O(n) space
def post_order_traverse1(tree, array):
    if tree is not None:
        post_order_traverse1(tree.left, array)
        post_order_traverse1(tree.right, array)
        array.append(tree.value)
    return array


# O(n) time | O(n) space
def in_order_traverse2(tree, array):
    arr = list()
    while tree is not None or len(arr) > 0:
        while tree is not None:
            arr.append(tree)
            tree = tree.left
        if len(arr) > 0:
            tree = arr.pop()
            array.append(tree.value)
            tree = tree.right
    return array


# O(n) time | O(n) space
def pre_order_traverse2(tree, array):
    arr = list()
    while tree is not None or len(arr) > 0:
        while tree is not None:
            array.append(tree.value)
            arr.append(tree)
            tree = tree.left
        if len(arr) > 0:
            tree = arr.pop()
            tree = tree.right
    return array


# O(n) time | O(n) space
def post_order_traverse2(tree, array):
    if tree is None:
        return array
    arr = list()
    cur, pre = None, None
    arr.append(tree)
    while len(arr) > 0:
        cur = arr[-1]
        if (cur.left is None and cur.right is None) or (pre is not None and (pre == cur.left or pre == cur.right)):
            array.append(cur.value)
            pre = arr.pop()
        else:
            if cur.right is not None:
                arr.append(cur.right)
            if cur.left is not None:
                arr.append(cur.left)
    return array


if __name__ == '__main__':
    _tree = BST(10)
    for e in [2, 5, 5, 1, 14, 22, 13, 15]:
        _tree = _tree.insert(e)
    print(in_order_traverse1(_tree, list()))
    print(in_order_traverse2(_tree, list()))
    print(pre_order_traverse1(_tree, list()))
    print(pre_order_traverse2(_tree, list()))
    print(post_order_traverse1(_tree, list()))
    print(post_order_traverse2(_tree, list()))
```

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

class BST {
public:
    int value;
    BST *left;
    BST *right;

    BST(int val)
    {
        value = val;
        left = nullptr;
        right = nullptr;
    }

    void insert(int value)
    {
        if (value < this->value)
        {
            if (left == nullptr)
            {
                left = new BST(value);
            }
            else
            {
                left->insert(value);
            }
        }
        else
        {
            if (right == nullptr)
            {
                right = new BST(value);
            }
            else
            {
                right->insert(value);
            }
        }
    }
};

// O(n) time | O(n) space
void inOrderTraverse(BST *tree, vector<int> &array)
{
    if ( tree == nullptr ) return;
    stack<BST *> stk;
    while ( tree != nullptr || ! stk.empty() )
    {
        while ( tree != nullptr )
        {
            stk.push(tree);
            tree = tree->left;
        }
        if ( ! stk.empty() )
        {
            tree = stk.top();
            stk.pop();
            array.push_back(tree->value);
            tree = tree->right;
        }
    }
}

// O(n) time | O(n) space
void preOrderTraverse(BST *tree, vector<int> &array)
{
    if ( tree == nullptr ) return;
    stack<BST *> stk;
    while ( tree != nullptr || ! stk.empty() )
    {
        while ( tree != nullptr )
        {
            array.push_back(tree->value);
            stk.push(tree);
            tree = tree->left;
        }
        if ( ! stk.empty() )
        {
            tree = stk.top();
            stk.pop();
            tree = tree->right;
        }
    }
}

// O(n) time | O(n) space
void postOrderTraverse(BST *tree, vector<int> &array)
{
    if ( tree == nullptr ) return;
    BST *current = nullptr, *prevision = nullptr;
    stack<BST *> stk;
    stk.push(tree);
    while ( ! stk.empty() )
    {
        current = stk.top();
        if ( ( current->left == nullptr && current->right == nullptr )
            || prevision != nullptr && ( prevision == current->left || prevision == current->right ) )
        {
            array.push_back(current->value);
            prevision = stk.top();
            stk.pop();
        }
        else
        {
            if ( current->right != nullptr )
            {
                stk.push(current->right);
            }
            if ( current->left != nullptr )
            {
                stk.push(current->left);
            }
        }
    }
}

void printList(vector<int> &array)
{
    for (int e : array)
    {
        cout << e << " ";
    }
    cout << endl;
}

int main()
{
    vector<int> source = {10, 5, 15, 2, 5, 22, 1};
    BST *root = new BST(source[0]);
    for (int i = 1 ; i < source.size(); i++) root->insert(source[i]);
    vector<int> result;
    inOrderTraverse(root, result);
    printList(result);
    result.clear();
    preOrderTraverse(root, result);
    printList(result);
    result.clear();
    postOrderTraverse(root, result);
    printList(result);
    result.clear();
    return 0;
}
```

## 查找最相近的值

```python
class Tree:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def find_closest_value_in_bst_helper1(tree, target, closest):
    if tree is None:
        return closest
    if abs(target - closest) > abs(target - tree.value):
        closest = tree.value
    if target < tree.value:
        return find_closest_value_in_bst_helper1(tree.left, target, closest)
    elif target > tree.value:
        return find_closest_value_in_bst_helper1(tree.right, target, closest)
    else:
        return closest


# Average: O(log(n)) time | O(log(n)) space
# Worst: O(n) time | O(n) space
def find_closest_value_in_bst1(tree, target):
    return find_closest_value_in_bst_helper1(tree, target, float('inf'))


def find_closest_value_in_bst_helper2(tree, target, closest):
    current_node = tree
    while current_node is not None:
        if abs(target - closest) > abs(target - current_node.value):
            closest = current_node.value
        if target < current_node.value:
            current_node = current_node.left
        elif target > current_node.value:
            current_node = current_node.right
        else:
            break
    return closest


# Average: O(log(n)) time | O(1) space
# Worst: O(n) time | O(1) space
def find_closest_value_in_bst2(tree, target):
    return find_closest_value_in_bst_helper2(tree, target, float('inf'))


def insert_level_order(array, tree, index, length):
    if index < length and array[index] is not None:
        tree = Tree(array[index])
        tree.left = insert_level_order(array, tree.left, 2 * index + 1, length)
        tree.right = insert_level_order(array, tree.right, 2 * index + 2, length)
    return tree


if __name__ == '__main__':
    source = [10, 5, 15, 2, 5, 13, 22, 1, None, None, None, None, 14]
    bst = insert_level_order(source, None, 0, len(source))
    print(find_closest_value_in_bst1(bst, 12))
    print(find_closest_value_in_bst2(bst, 12))
```

```c
#include <stdio.h>
#include <stdlib.h>
#define MAX_INTEGER_VALUE 2147483647

typedef struct Tree
{
    int value;
    struct Tree *left;
    struct Tree *right;
} Tree, *pTree;

int abs(const int num)
{
    return ((num >> 31) ^ num) - (num >> 31);
}

int findClosestValueInBSTHelper(pTree tree, int target, int closest)
{
    pTree currentNode = tree;
    while ( currentNode != NULL )
    {
        if ( abs(target - closest) > abs(target - currentNode->value) )
            closest = currentNode->value;
        if ( target < currentNode->value )
            currentNode = currentNode->left;
        else if ( target > currentNode->value )
            currentNode = currentNode->right;
        else
            break;
    }
    return closest;
}

/*
 * Average: O(log(n)) time | O(1) space
 * Worst: O(n) time | O(1) space
 */
int findClosestValueInBST(pTree tree, int target)
{
    return findClosestValueInBSTHelper(tree, target, MAX_INTEGER_VALUE);
}

pTree newNode(int value)
{
    pTree t = (Tree*)malloc(sizeof(Tree));
    t->value = value;
    t->left = NULL;
    t->right = NULL;
    return t;
}

pTree initBSTTree()
{
    pTree bstTree = newNode(10);
    bstTree->left = newNode(5);
    bstTree->right = newNode(15);
    bstTree->left->left = newNode(2);
    bstTree->left->right = newNode(5);
    bstTree->right->left = newNode(13);
    bstTree->right->right = newNode(22);
    bstTree->left->left->left = newNode(1);
    bstTree->right->left->right = newNode(14);
    return bstTree;
}

int main(void)
{
    pTree tree = initBSTTree();
    printf("%d", findClosestValueInBST(tree, 12));
    return 0;
}
```

## 最小高度二叉搜索树

```python
class BST:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

    # Average: O(log(n)) time | O(1) space
    # Worst: O(n) time | O(1) space
    def insert(self, value):
        current_node = self
        while True:
            if value < current_node.value:
                if current_node.left is None:
                    current_node.left = BST(value)
                    break
                else:
                    current_node = current_node.left
            else:
                if current_node.right is None:
                    current_node.right = BST(value)
                    break
                else:
                    current_node = current_node.right
        return self


# O(n * log(n)) time | O(n) space
def min_height_bst1(array):
    return construct_min_height_bst1(array, None, 0, len(array) - 1)


def construct_min_height_bst1(array, bst, start_idx, end_idx):
    if end_idx < start_idx:
        return None
    mid_idx = (start_idx + end_idx) // 2
    value_to_add = array[mid_idx]
    if bst is None:
        bst = BST(value_to_add)
    else:
        bst.insert(value_to_add)
    construct_min_height_bst1(array, bst, start_idx, mid_idx - 1)
    construct_min_height_bst1(array, bst, mid_idx + 1, end_idx)
    return bst


# O(n) time | O(n) space
def min_height_bst2(array):
    return construct_min_height_bst2(array, None, 0, len(array) - 1)


def construct_min_height_bst2(array, bst, start_idx, end_idx):
    if end_idx < start_idx:
        return None
    mid_idx = (start_idx + end_idx) // 2
    new_bst_node = BST(array[mid_idx])
    if bst is None:
        bst = new_bst_node
    else:
        if array[mid_idx] < bst.value:
            bst.left = new_bst_node
            bst = bst.left
        else:
            bst.right = new_bst_node
            bst = bst.right
    construct_min_height_bst2(array, bst, start_idx, mid_idx - 1)
    construct_min_height_bst2(array, bst, mid_idx + 1, end_idx)
    return bst


# O(n) time | O(n) space
def min_height_bst3(array):
    return construct_min_height_bst3(array, 0, len(array) - 1)


def construct_min_height_bst3(array, start_idx, end_idx):
    if end_idx < start_idx:
        return None
    mid_idx = (start_idx + end_idx) // 2
    bst = BST(array[mid_idx])
    bst.left = construct_min_height_bst3(array, start_idx, mid_idx - 1)
    bst.right = construct_min_height_bst3(array, mid_idx + 1, end_idx)
    return bst


def in_order_traverse(tree, array):
    arr = list()
    while tree is not None or len(arr) > 0:
        while tree is not None:
            arr.append(tree)
            tree = tree.left
        if len(arr) > 0:
            tree = arr.pop()
            array.append(tree.value)
            tree = tree.right
    return array


if __name__ == '__main__':
    source = [1, 2, 5, 6, 9, 10, 13, 14, 15, 16]
    b = min_height_bst1(source)
    print(in_order_traverse(b, list()))
    b = min_height_bst2(source)
    print(in_order_traverse(b, list()))
    b = min_height_bst3(source)
    print(in_order_traverse(b, list()))
```

```cpp
#include <iostream>
#include <vector>
#include <stack>

using namespace std;

class BST
{
public:
    int value;
    BST *left;
    BST *right;

    BST(int val)
    {
        value = val;
        left = nullptr;
        right = nullptr;
    }
};

void inOrderTraverse(BST *tree)
{
    if ( tree == nullptr ) return;
    stack<BST *> stk;
    while ( tree != nullptr || ! stk.empty() )
    {
        while ( tree != nullptr )
        {
            stk.push(tree);
            tree = tree->left;
        }
        if ( ! stk.empty() )
        {
            tree = stk.top();
            stk.pop();
            cout << tree->value << " ";
            tree = tree->right;
        }
    }
    cout << endl;
}

// O(n) time | O(n) space
BST *minHeightBstHelper(vector<int> array, int start, int end)
{
    if ( start > end ) return nullptr;
    int middleIndex = (start + end) / 2;
    BST *root = new BST(array[middleIndex]);
    root->left = minHeightBstHelper(array, start, middleIndex - 1);
    root->right = minHeightBstHelper(array, middleIndex + 1, end);
    return root;
}

BST *minHeightBst(vector<int> array)
{
    return minHeightBstHelper(array, 0, array.size() - 1);
}

int main()
{
    vector<int> source = { 1, 2, 5, 7, 10, 13, 14, 15, 22, 28, 32, 36, 89, 92, 9000, 9001 };
    BST *root = minHeightBst(source);
    inOrderTraverse(root);
    return 0;
}
```

## 相同二叉搜索树

```python
def get_smaller(array):
    smaller = list()
    for i in range(1, len(array)):
        if array[i] < array[0]:
            smaller.append(array[i])
    return smaller


def get_bigger_or_equal(array):
    bigger_or_equal = list()
    for i in range(1, len(array)):
        if array[i] >= array[0]:
            bigger_or_equal.append(array[i])
    return bigger_or_equal


def get_idx_of_first_smaller(array, starting_idx, min_val):
    for i in range(starting_idx + 1, len(array)):
        if array[starting_idx] > array[i] >= min_val:
            return i
    return -1


def get_idx_of_first_bigger_or_equal(array, starting_idx, max_val):
    for i in range(starting_idx + 1, len(array)):
        if array[starting_idx] <= array[i] < max_val:
            return i
    return -1


def are_same_bst_s(array1, array2, root_idx1, root_idx2, min_val, max_val):
    if root_idx1 == -1 or root_idx2 == -1:
        return root_idx1 == root_idx2

    if array1[root_idx1] != array2[root_idx2]:
        return False

    left_root_idx1 = get_idx_of_first_smaller(array1, root_idx1, min_val)
    left_root_idx2 = get_idx_of_first_smaller(array2, root_idx2, min_val)
    right_root_idx1 = get_idx_of_first_bigger_or_equal(array1, root_idx1, max_val)
    right_root_idx2 = get_idx_of_first_bigger_or_equal(array2, root_idx2, max_val)

    current_value = array1[root_idx1]
    left_are_same = are_same_bst_s(array1, array2, left_root_idx1, left_root_idx2, min_val, current_value)
    right_are_same = are_same_bst_s(array1, array2, right_root_idx1, right_root_idx2, current_value, max_val)

    return left_are_same and right_are_same


# O(n^2) time | O(n^2) space
def same_bst_s1(array1, array2):
    if len(array1) != len(array2):
        return False
    if len(array1) == 0 and len(array2) == 0:
        return True
    if array1[0] != array2[0]:
        return False
    left1 = get_smaller(array1)
    left2 = get_smaller(array1)
    right1 = get_bigger_or_equal(array1)
    right2 = get_bigger_or_equal(array2)

    return same_bst_s1(left1, left2) and same_bst_s1(right1, right2)


# O(n^2) time | O(d) space
def same_bst_s2(array1, array2):
    return are_same_bst_s(array1, array2, 0, 0, float('-inf'), float('inf'))


if __name__ == '__main__':
    source1 = [10, 15, 8, 12, 94, 81, 5, 2, 11]
    source2 = [10, 8, 5, 15, 2, 12, 11, 94, 81]
    print(same_bst_s1(source1, source2))
    print(same_bst_s2(source1, source2))
```

## 右侧小于数量

```python
class SpecialBST1:

    def __init__(self, value, idx, num_smaller_at_insert_time):
        self.value = value
        self.idx = idx
        self.num_smaller_at_insert_time = num_smaller_at_insert_time
        self.left_subtree_size = 0
        self.left = None
        self.right = None

    def insert(self, value, idx, num_smaller_at_insert_time=0):
        if value < self.value:
            self.left_subtree_size += 1
            if self.left is None:
                self.left = SpecialBST1(value, idx, num_smaller_at_insert_time)
            else:
                self.left.insert(value, idx, num_smaller_at_insert_time)
        else:
            num_smaller_at_insert_time += self.left_subtree_size
            if value > self.value:
                num_smaller_at_insert_time += 1
            if self.right is None:
                self.right = SpecialBST1(value, idx, num_smaller_at_insert_time)
            else:
                self.right.insert(value, idx, num_smaller_at_insert_time)


class SpecialBST2:

    def __init__(self, value):
        self.value = value
        self.left_subtree_size = 0
        self.left = None
        self.right = None

    def insert(self, value, idx, right_smaller_counts, num_smaller_at_insert_time=0):
        if value < self.value:
            self.left_subtree_size += 1
            if self.left is None:
                self.left = SpecialBST2(value)
                right_smaller_counts[idx] = num_smaller_at_insert_time
            else:
                self.left.insert(value, idx, right_smaller_counts, num_smaller_at_insert_time)
        else:
            num_smaller_at_insert_time += self.left_subtree_size
            if value > self.value:
                num_smaller_at_insert_time += 1
            if self.right is None:
                self.right = SpecialBST2(value)
                right_smaller_counts[idx] = num_smaller_at_insert_time
            else:
                self.right.insert(value, idx, right_smaller_counts, num_smaller_at_insert_time)


def get_right_smaller_counts(bst, right_smaller_counts):
    if bst is None:
        return False
    right_smaller_counts[bst.idx] = bst.num_smaller_at_insert_time
    get_right_smaller_counts(bst.left, right_smaller_counts)
    get_right_smaller_counts(bst.right, right_smaller_counts)


# O(n^2) time | O(n) space
def right_smaller_than1(array):
    right_smaller_counts = list()
    for i in range(len(array)):
        right_smaller_count = 0
        for j in range(i + 1, len(array)):
            if array[j] < array[i]:
                right_smaller_count += 1
        right_smaller_counts.append(right_smaller_count)
    return right_smaller_counts


# O(n * log(n)) time | O(n) space
def right_smaller_than2(array):
    if len(array) == 0:
        return list()

    last_idx = len(array) - 1
    bst = SpecialBST1(array[last_idx], last_idx, 0)
    for i in reversed(range(len(array) - 1)):
        bst.insert(array[i], i)

    right_smaller_counts = array[:]
    get_right_smaller_counts(bst, right_smaller_counts)
    return right_smaller_counts


# O(n * log(n)) time | O(n) space
def right_smaller_than3(array):
    if len(array) == 0:
        return list()

    right_smaller_counts = array[:]
    last_idx = len(array) - 1
    bst = SpecialBST2(array[last_idx])
    right_smaller_counts[last_idx] = 0
    for i in reversed(range(len(array) - 1)):
        bst.insert(array[i], i, right_smaller_counts)

    return right_smaller_counts


if __name__ == '__main__':
    source = [8, 5, 11, -1, 3, 4, 2]
    print(right_smaller_than1(source))
    print(right_smaller_than2(source))
    print(right_smaller_than3(source))
```

## 验证二叉搜索树

```python
class BST:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

    def insert(self, value):
        current_node = self
        while True:
            if value < current_node.value:
                if current_node.left is None:
                    current_node.left = BST(value)
                    break
                else:
                    current_node = current_node.left
            else:
                if current_node.right is None:
                    current_node.right = BST(value)
                    break
                else:
                    current_node = current_node.right
        return self


def init_bst(array):
    tree = BST(array[0])
    for i in range(1, len(array)):
        tree.insert(array[i])
    return tree


def validate_binary_search_helper(tree, min_value, max_value):
    if tree is None:
        return True
    if tree.value < min_value or tree.value >= max_value:
        return False
    left_is_valid = validate_binary_search_helper(tree.left, min_value, tree.value)
    return left_is_valid and validate_binary_search_helper(tree.right, tree.value, max_value)


# O(n) time | O(d) space
# d is tree depth
def validate_binary_search_tree(tree):
    return validate_binary_search_helper(tree, float('-inf'), float('inf'))


if __name__ == '__main__':
    bst = init_bst([10, 5, 5, 2, 1, 15, 13, 22, 14])
    print(validate_binary_search_tree(bst))
```

```cpp
#include <iostream>
#include <vector>
#include <climits>

using namespace std;

class BST
{
public:
    int value;
    BST *left;
    BST *right;

    BST(int val)
    {
        value = val;
        left = nullptr;
        right = nullptr;
    };

    BST &insert(int val)
    {
        if (val < value )
        {
            if ( left == nullptr )
            {
                BST *item = new BST(val);
                left = item;
            }
            else
            {
                left->insert(val);
            }
        }
        else
        {
            if ( right == nullptr )
            {
                BST *item = new BST(val);
                right = item;
            }
            else
            {
                right->insert(val);
            }
        }
        return *this;
    }
};

bool validateBstHelper(BST *tree, int minValue, int maxValue)
{
    if (tree->value < minValue || tree->value >= maxValue)
    {
        return false;
    }
    if (tree->left != nullptr &&
            !validateBstHelper(tree->left, minValue, tree->value))
    {
        return false;
    }
    if (tree->right != nullptr &&
            !validateBstHelper(tree->right, tree->value, maxValue))
    {
        return false;
    }
    return true;
}

// O(n) time | O(d) space - where d is tree depth
bool validateBst(BST *tree)
{
    return validateBstHelper(tree, INT_MIN, INT_MAX);
}

int main()
{
    vector<int> a = {10, 5, 15, 2, 5, 13, 22, 1, 14};
    BST *root = new BST(a[0]);
    for (int i = 1; i < a.size(); i++)
        root->insert(a[i]);
    cout << validateBst(root);
    return 0;
}
```

## 第 K 个最大值

```python
class BST:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

    def insert(self, value):
        current_node = self
        while True:
            if value < current_node.value:
                if current_node.left is None:
                    current_node.left = BST(value)
                    break
                else:
                    current_node = current_node.left
            else:
                if current_node.right is None:
                    current_node.right = BST(value)
                    break
                else:
                    current_node = current_node.right
        return self


def in_order_traverse(tree, array):
    arr = list()
    while tree is not None or len(arr) > 0:
        while tree is not None:
            arr.append(tree)
            tree = tree.left
        if len(arr) > 0:
            tree = arr.pop()
            array.append(tree.value)
            tree = tree.right
    return array


# O(n) time | O(n) space
def find_kth_largest_value_in_bst1(tree, k):
    array = list()
    in_order_traverse(tree, array)
    return array[-1 * k]


# O(h + k) time | O(h) space
def find_kth_largest_value_in_bst2(tree, k):
    arr = list()
    count = 0
    while tree is not None or len(arr) > 0:
        while tree is not None:
            arr.append(tree)
            tree = tree.right
        if len(arr) > 0:
            tree = arr.pop()
            count += 1
            if count == k:
                return tree.value
            tree = tree.left
    return -1


class NodeInfo:
    def __init__(self):
        self.numberOfNodesVisited = 0
        self.latestVisitedNodeValue = None


def reverse_in_order_traverse(tree, k, info):
    if tree is None or info.numberOfNodesVisited >= k:
        return
    reverse_in_order_traverse(tree.right, k, info)
    if info.numberOfNodesVisited < k:
        info.numberOfNodesVisited += 1
        info.latestVisitedNodeValue = tree.value
        reverse_in_order_traverse(tree.left, k, info)


# O(h + k) time | O(h) space
def find_kth_largest_value_in_bst3(tree, k):
    info = NodeInfo()
    reverse_in_order_traverse(tree, k, info)
    return info.latestVisitedNodeValue


if __name__ == '__main__':
    _tree = BST(15)
    for e in [5, 20, 2, 5, 17, 22, 1, 3]:
        _tree = _tree.insert(e)
    print(find_kth_largest_value_in_bst1(_tree, 3))
    print(find_kth_largest_value_in_bst2(_tree, 3))
    print(find_kth_largest_value_in_bst3(_tree, 3))
```

```cpp
#include <iostream>
#include <stack>
#include <vector>
using namespace std;

class BST
{
public:
    int value;
    BST *left = nullptr;
    BST *right = nullptr;

    BST(int val)
    {
        value = val;
        left = nullptr;
        right = nullptr;
    }

    BST &insert(int val)
    {
        if (val < value )
        {
            if ( left == nullptr )
            {
                BST *item = new BST(val);
                left = item;
            }
            else
            {
                left->insert(val);
            }
        }
        else
        {
            if ( right == nullptr )
            {
                BST *item = new BST(val);
                right = item;
            }
            else
            {
                right->insert(val);
            }
        }
        return *this;
    }
};

void inOrderTraverse(BST *node, vector<int> &sortedNodeValues);

// O(n) time | O(n) space - where n is the number of nodes in the tree
int findKthLargestValueInBst1(BST *tree, int k)
{
    vector<int> sortedNodeValues;
    inOrderTraverse(tree, sortedNodeValues);
    return sortedNodeValues[sortedNodeValues.size() - k];
}

void inOrderTraverse(BST *node, vector<int> &sortedNodeValues)
{
    if (node == nullptr)
        return;

    inOrderTraverse(node->left, sortedNodeValues);
    sortedNodeValues.push_back(node->value);
    inOrderTraverse(node->right, sortedNodeValues);
}

struct TreeInfo
{
    int numberOfNodesVisited;
    int latestVisitedNodeValue;
};

void reverseInOrderTraverse(BST *node, int k, TreeInfo &treeInfo);

// O(h + k) time | O(h) space - where h is the height of the tree
// and k is the input parameter
int findKthLargestValueInBst2(BST *tree, int k)
{
    auto treeInfo = TreeInfo{0, -1};
    reverseInOrderTraverse(tree, k, treeInfo);
    return treeInfo.latestVisitedNodeValue;
}

void reverseInOrderTraverse(BST *node, int k, TreeInfo &treeInfo)
{
    if (node == nullptr || treeInfo.numberOfNodesVisited >= k)
        return;

    reverseInOrderTraverse(node->right, k, treeInfo);
    if (treeInfo.numberOfNodesVisited < k)
    {
        treeInfo.numberOfNodesVisited++;
        treeInfo.latestVisitedNodeValue = node->value;
        reverseInOrderTraverse(node->left, k, treeInfo);
    }
}

// O(h + k) time | O(h) space - where h is the height of the tree
// and k is the input parameter
int findKthLargestValueInBst3(BST *tree, int k)
{
    if ( tree == nullptr ) return -1;
    stack<BST *> stk;
    int count = 0;
    while ( tree != nullptr || ! stk.empty() )
    {
        while ( tree != nullptr )
        {
            stk.push(tree);
            tree = tree->right;
        }
        if ( ! stk.empty() )
        {
            tree = stk.top();
            stk.pop();
            count++;
            if ( count == k )
                return tree->value;
            tree = tree->left;
        }
    }
    return -1;
}

int main()
{
    vector<int> source = { 15, 5, 20, 2, 5, 17, 22, 1, 3 };
    BST *root = new BST(source[0]);
    for (int i = 1; i < source.size(); i++)
    {
        root->insert(source[i]);
    }
    cout << findKthLargestValueInBst1(root, 3);
    cout << findKthLargestValueInBst2(root, 3);
    cout << findKthLargestValueInBst3(root, 3);
    return 0;
}
```

Last Modified 2021-11-21

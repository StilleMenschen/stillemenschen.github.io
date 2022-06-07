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

void iteration(vector<int> &array)
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
    iteration(result);
    result.clear();
    preOrderTraverse(root, result);
    iteration(result);
    result.clear();
    postOrderTraverse(root, result);
    iteration(result);
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

给定两个数组，分别表示二叉搜索树不同方式遍历的结果，在不构造二叉搜索树的情况下判断这两棵树是否相同

```python
# O(n^2) time | O(n^2) space - where n is the number of
# nodes in each array, respectively
def same_bst_s_1(array_one, array_two):
    # 表示二叉树的数组元素不相等择表示这两棵树不相同
    if len(array_one) != len(array_two):
        return False
    # 如果两棵二叉树都没有元素, 则两棵树相同
    if len(array_one) == 0 and len(array_two) == 0:
        return True
    # 因为第一个元素表示树的根节点, 如果树的根节点不相等则表示两棵树不相同
    if array_one[0] != array_two[0]:
        return False
    # 二叉搜索树的特点是左子树的值一定小于根节点, 右子树的值一定大于等于根节点
    # 获取第一棵树相对于根节点的的最小值数组, 即左子树
    left_one = get_smaller(array_one)
    # 获取第二棵树相对于根节点的的最小值数组, 即左子树
    left_two = get_smaller(array_two)
    # 获取第一棵树相对于根节点的的最大值数组, 即右子树
    right_one = get_bigger_or_equal(array_one)
    # 获取第二棵树相对于根节点的的最大值数组, 即右子树
    right_two = get_bigger_or_equal(array_two)
    # 递归判断两棵树的左子树和右子树
    return same_bst_s_1(left_one, left_two) and same_bst_s_1(right_one, right_two)


def get_smaller(array):
    smaller = []
    for i in range(1, len(array)):
        # 找出所有小于根节点的子节点
        if array[i] < array[0]:
            smaller.append(array[i])
    return smaller


def get_bigger_or_equal(array):
    bigger_or_equal = []
    for i in range(1, len(array)):
        # 找出所有大于等于根节点的子节点
        if array[i] >= array[0]:
            bigger_or_equal.append(array[i])
    return bigger_or_equal


# O(n^2) time | O(d) space - where n is the number of
# nodes in each array, respectively, and d is the depth
# of the BST that they represent
def same_bst_s_2(array_one, array_two):
    # 递归判断两棵树的左右子树, 只跟踪根节点的索引和子树中的最大最小值
    # 初始情况下还没有开始遍历两棵树, 所以最大和最小值设为无穷大和无穷小
    return are_same_bst_s(array_one, array_two, 0, 0, float("-inf"), float("inf"))


def are_same_bst_s(array_one, array_two, root_idx_one, root_idx_two, min_val, max_val):
    # 这里判断 -1 与下面的工具函数相对应, 表示已经找不到最大值或最小值
    if root_idx_one == -1 or root_idx_two == -1:
        # 如果两棵树都已经遍历完(都找不到下一个最大值和最小值)则表示两棵树相同, 否则两棵树不相同
        return root_idx_one == root_idx_two
    # 因为第一个元素表示树的根节点, 如果树的根节点不相等则表示两棵树不相同
    if array_one[root_idx_one] != array_two[root_idx_two]:
        return False
    # 二叉搜索树的特点是左子树的值一定小于根节点, 右子树的值一定大于等于根节点
    # 获取第一棵树左子树的下一个根节点位置
    left_root_idx_one = get_idx_of_first_smaller(array_one, root_idx_one, min_val)
    # 获取第二棵树左子树的下一个根节点位置
    left_root_idx_two = get_idx_of_first_smaller(array_two, root_idx_two, min_val)
    # 获取第一棵树右子树的下一个根节点位置
    right_root_idx_one = get_idx_of_first_bigger_or_equal(array_one, root_idx_one, max_val)
    # 获取第二棵树右子树的下一个根节点位置
    right_root_idx_two = get_idx_of_first_bigger_or_equal(array_two, root_idx_two, max_val)
    # 当前根节点的值, 因为上面已经判断过两棵树的根节点是否相等, 所以这里取哪一棵树的值都一样
    current_value = array_one[root_idx_one]
    # 递归左子树, 最大值传递当前值
    left_are_same = are_same_bst_s(array_one, array_two, left_root_idx_one, left_root_idx_two, min_val, current_value)
    # 递归右子树, 最小值传递当前值
    right_are_same = are_same_bst_s(array_one, array_two, right_root_idx_one, right_root_idx_two, current_value,
                                    max_val)
    # 左右子树都相同则表示两棵树相同
    return left_are_same and right_are_same


def get_idx_of_first_smaller(array, starting_idx, min_val):
    # 查找 starting_idx 位置之后的第一个较小值的索引
    # 确保该值大于等于 min_val, 即 BST 中前一个父节点的值
    # 如果不是, 则表示该值位于前一个父节点的左子树中
    for i in range(starting_idx + 1, len(array)):
        if array[starting_idx] > array[i] >= min_val:
            return i
    return -1


def get_idx_of_first_bigger_or_equal(array, starting_idx, max_val):
    # 在 starting_idx 位置之后找到第一个大于或等于值的索引
    # 确保该值小于 max_val, max_val 是 BST 中前一个父节点的值
    # 如果不是, 则表示该值位于前一个父节点的右子树中
    for i in range(starting_idx + 1, len(array)):
        if array[starting_idx] <= array[i] < max_val:
            return i
    return -1


if __name__ == '__main__':
    source1 = [10, 15, 8, 12, 94, 81, 5, 2, 11]
    source2 = [10, 8, 5, 15, 2, 12, 11, 94, 81]
    print(same_bst_s_1(source1, source2))
    print(same_bst_s_2(source1, source2))
```

```cpp
#include <iostream>
#include <climits>
#include <vector>

using namespace std;

vector<int> getSmaller(vector<int>);
vector<int> getBiggerOrEqual(vector<int> array);

// O(n^2) time | O(n^2) space - where n is the number of
// nodes in each array, respectively
bool sameBsts1(vector<int> arrayOne, vector<int> arrayTwo)
{
    if (arrayOne.size() != arrayTwo.size())
        return false;

    if (arrayOne.size() == 0 && arrayTwo.size() == 0)
        return true;

    if (arrayOne[0] != arrayTwo[0])
        return false;

    vector<int> leftOne = getSmaller(arrayOne);
    vector<int> leftTwo = getSmaller(arrayTwo);
    vector<int> rightOne = getBiggerOrEqual(arrayOne);
    vector<int> rightTwo = getBiggerOrEqual(arrayTwo);

    return sameBsts1(leftOne, leftTwo) && sameBsts1(rightOne, rightTwo);
}

vector<int> getSmaller(vector<int> array)
{
    vector<int> smaller = {};
    const int arraySize = array.size();
    for (int i = 1; i < arraySize; i++)
    {
        if (array[i] < array[0])
            smaller.push_back(array[i]);
    }
    return smaller;
}

vector<int> getBiggerOrEqual(vector<int> array)
{
    vector<int> biggerOrEqual = {};
    const int arraySize = array.size();
    for (int i = 1; i < arraySize; i++)
    {
        if (array[i] >= array[0])
            biggerOrEqual.push_back(array[i]);
    }
    return biggerOrEqual;
}

bool areSameBsts(vector<int> arrayOne, vector<int> arrayTwo, int rootIdxOne,
                 int rootIdxTwo, int minVal, int maxVal);
int getIdxOfFirstSmaller(vector<int> array, int startingIdx, int minVal);
int getIdxOfFirstBiggerOrEqual(vector<int> array, int startingIdx, int maxVal);

// O(n^2) time | O(d) space - where n is the number of
// nodes in each array, respectively, and d is the depth
// of the BST that they represent
bool sameBsts2(vector<int> arrayOne, vector<int> arrayTwo)
{
    return areSameBsts(arrayOne, arrayTwo, 0, 0, INT_MIN, INT_MAX);
}

bool areSameBsts(vector<int> arrayOne, vector<int> arrayTwo, int rootIdxOne,
                 int rootIdxTwo, int minVal, int maxVal)
{
    if (rootIdxOne == -1 || rootIdxTwo == -1)
        return rootIdxOne == rootIdxTwo;

    if (arrayOne[rootIdxOne] != arrayTwo[rootIdxTwo])
        return false;

    int leftRootIdxOne = getIdxOfFirstSmaller(arrayOne, rootIdxOne, minVal);
    int leftRootIdxTwo = getIdxOfFirstSmaller(arrayTwo, rootIdxTwo, minVal);
    int rightRootIdxOne =
        getIdxOfFirstBiggerOrEqual(arrayOne, rootIdxOne, maxVal);
    int rightRootIdxTwo =
        getIdxOfFirstBiggerOrEqual(arrayTwo, rootIdxTwo, maxVal);

    int currentValue = arrayOne[rootIdxOne];
    bool leftAreSame = areSameBsts(arrayOne, arrayTwo, leftRootIdxOne,
                                   leftRootIdxTwo, minVal, currentValue);
    bool rightAreSame = areSameBsts(arrayOne, arrayTwo, rightRootIdxOne,
                                    rightRootIdxTwo, currentValue, maxVal);

    return leftAreSame && rightAreSame;
}

int getIdxOfFirstSmaller(vector<int> array, int startingIdx, int minVal)
{
    // Find the index of the first smaller value after the startingIdx.
    // Make sure that this value is greater than or equal to the minVal,
    // which is the value of the previous parent node in the BST. If it
    // isn't, then that value is located in the left subtree of the
    // previous parent node.
    const int arraySize = array.size();
    for (int i = startingIdx + 1; i < arraySize; i++)
    {
        if (array[i] < array[startingIdx] && array[i] >= minVal)
            return i;
    }
    return -1;
}

int getIdxOfFirstBiggerOrEqual(vector<int> array, int startingIdx, int maxVal)
{
    // Find the index of the first bigger/equal value after the startingIdx.
    // Make sure that this value is smaller than maxVal, which is the value
    // of the previous parent node in the BST. If it isn't, then that value
    // is located in the right subtree of the previous parent node.
    const int arraySize = array.size();
    for (int i = startingIdx + 1; i < arraySize; i++)
    {
        if (array[i] >= array[startingIdx] && array[i] < maxVal)
            return i;
    }
    return -1;
}

int main()
{
    vector<int> source1 = {10, 15, 8, 12, 94, 81, 5, 2, 11};
    vector<int> source2 = {10, 8, 5, 15, 2, 12, 11, 94, 81};
    cout << sameBsts1(source1, source2) << endl;
    cout << sameBsts2(source1, source2) << endl;
    return 0;
}
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

## 重构二叉搜索树

给定一个先序遍历的数组，参考数组重新创建二叉搜索树

```python
class BST:

    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right


def pre_order_traverse(tree):
    stack = list()
    while tree is not None or len(stack) > 0:
        while tree is not None:
            print(tree.value, end=' ')
            stack.append(tree)
            tree = tree.left
        if len(stack) > 0:
            tree = stack.pop()
            tree = tree.right
    print()


# O(n^2) time | O(n) space
def reconstruct_bst1(pre_order_traverse_values):
    if len(pre_order_traverse_values) == 0:
        return None

    current_value = pre_order_traverse_values[0]
    right_subtree_root_idx = len(pre_order_traverse_values)
    for idx in range(1, len(pre_order_traverse_values)):
        value = pre_order_traverse_values[idx]
        if value > current_value:
            right_subtree_root_idx = idx
            break

    left_subtree = reconstruct_bst1(pre_order_traverse_values[1:right_subtree_root_idx])
    right_subtree = reconstruct_bst1(pre_order_traverse_values[right_subtree_root_idx:])
    return BST(current_value, left_subtree, right_subtree)


class TreeInfo:
    def __init__(self, root_idx):
        self.root_idx = root_idx


def reconstruct_bst_from_range(lower_bound, upper_bound, pre_order_traverse_values, current_subtree_info):
    if current_subtree_info.root_idx == len(pre_order_traverse_values):
        return None

    root_value = pre_order_traverse_values[current_subtree_info.root_idx]
    if root_value < lower_bound or root_value >= upper_bound:
        return None

    current_subtree_info.root_idx += 1
    left_subtree = reconstruct_bst_from_range(lower_bound, root_value, pre_order_traverse_values, current_subtree_info)
    right_subtree = reconstruct_bst_from_range(root_value, upper_bound, pre_order_traverse_values, current_subtree_info)
    return BST(root_value, left_subtree, right_subtree)


# O(n) time | O(n) space
def reconstruct_bst2(pre_order_traverse_values):
    tree_info = TreeInfo(0)
    return reconstruct_bst_from_range(float('-inf'), float('inf'), pre_order_traverse_values, tree_info)


if __name__ == '__main__':
    source = [10, 4, 2, 1, 5, 17, 19, 18]
    root = reconstruct_bst1(source)
    pre_order_traverse(root)
    root = reconstruct_bst2(source)
    pre_order_traverse(root)
```

```cpp
#include <iostream>
#include <vector>
#include <stack>
#include <limits>
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

struct TreeInfo
{
    int rootIdx;
};

void preOrderTraverse(BST *tree)
{
    if ( tree == nullptr ) return;
    stack<BST *> stk;
    while ( tree != nullptr || ! stk.empty() )
    {
        while ( tree != nullptr )
        {
            cout << tree->value << " ";
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
    cout << endl;
}

// O(n^2) time | O(n) space - where n is the length of the input array
BST *reconstructBst1(vector<int> preOrderTraverseValues)
{
    if (preOrderTraverseValues.size() == 0) return nullptr;

    int currentValue = preOrderTraverseValues[0];
    int rightSubtreeRootIdx = preOrderTraverseValues.size();

    const int size = preOrderTraverseValues.size();
    for (int i = 1; i < size; i++)
    {
        int value = preOrderTraverseValues[i];
        if (value >= currentValue)
        {
            rightSubtreeRootIdx = i;
            break;
        }
    }

    BST *leftSubtree = reconstructBst1(vector<int>(preOrderTraverseValues.begin() + 1, preOrderTraverseValues.begin() + rightSubtreeRootIdx));
    BST *rightSubtree = reconstructBst1(vector<int>(preOrderTraverseValues.begin() + rightSubtreeRootIdx, preOrderTraverseValues.end()));
    BST *bst = new BST(currentValue);
    bst->left = leftSubtree;
    bst->right = rightSubtree;
    return bst;
}


BST *reconstructBstFromRange(int lowerBound, int upperBound, vector<int> &preOrderTraversalValues, TreeInfo &currentSubtreeInfo)
{
    if (currentSubtreeInfo.rootIdx == preOrderTraversalValues.size())
        return nullptr;

    int rootValue = preOrderTraversalValues[currentSubtreeInfo.rootIdx];
    if (rootValue < lowerBound || rootValue >= upperBound)
        return nullptr;

    currentSubtreeInfo.rootIdx++;
    BST *leftSubtree = reconstructBstFromRange(lowerBound, rootValue, preOrderTraversalValues, currentSubtreeInfo);
    BST *rightSubtree = reconstructBstFromRange(rootValue, upperBound, preOrderTraversalValues, currentSubtreeInfo);
    BST *bst = new BST(rootValue);
    bst->left = leftSubtree;
    bst->right = rightSubtree;
    return bst;
}

// O(n) time | O(n) space - where n is the length of the input array
BST *reconstructBst2(vector<int> preOrderTraversalValues)
{
    TreeInfo treeInfo = TreeInfo{0};
    return reconstructBstFromRange(numeric_limits<int>::min(), numeric_limits<int>::max(), preOrderTraversalValues, treeInfo);
}

int main()
{
    vector<int> source = {10, 4, 2, 1, 5, 17, 19, 18};
    BST *root = reconstructBst1(source);
    preOrderTraverse(root);
    root = reconstructBst2(source);
    preOrderTraverse(root);
    return 0;
}
```

## 判断三个关联节点

给定三个二叉搜索树中的随机节点`1, 2, 3`，这三个节点不重复；找出这三个节点是否为父子关系，可能的父子关系为 `1 -> 2 -> 3` 或者 `3 -> 2 -> 1`

```python
class BST:
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right

    def insert(self, value):
        current_node = self
        while True:
            if value < current_node.value:
                if current_node.left is None:
                    current_node.left = BST(value)
                    break
                current_node = current_node.left
            else:
                if current_node.right is None:
                    current_node.right = BST(value)
                    break
                current_node = current_node.right
        return self

    def contains(self, value):
        current_node = self
        while current_node is not None:
            if value < current_node.value:
                current_node = current_node.left
            elif value > current_node.value:
                current_node = current_node.right
            else:
                return current_node
        return None


# O(h) time | O(h) space - where h is the height of the tree
def validate_three_nodes1(node_one, node_two, node_three):
    # 由于第二个节点处在中间, 所以仅有两种情况, 从第二个节点推断祖先元素

    # 1. 第三个节点是祖先元素, 第二个节点为第三个节点的子元素, 第一个节点为第二个节点的子元素
    if is_descendant1(node_two, node_one):
        return is_descendant1(node_three, node_two)
    # 2. 第一个节点是祖先元素, 第二个节点为第一个节点的子元素, 第三个节点为第二个节点的子元素
    if is_descendant1(node_two, node_three):
        return is_descendant1(node_one, node_two)
    # 没有找到上述两种情况则表示不符合
    return False


# Whether the `target` is a descendant of the `node`.
def is_descendant1(node, target):
    # 递归调用的终点, 如果节点为空则结束
    if node is None:
        return False
    # 如果找到符合的数据则直接返回
    if node is target:
        return True
    # 根据二叉搜索树的特点
    # 如果当前值小于节点的值则选择左子树
    if target.value < node.value:
        return is_descendant1(node.left, target)
    # 如果当前值大于等于节点的值则选择右子树
    else:
        return is_descendant1(node.right, target)


# O(h) time | O(h) space - where h is the height of the tree
def validate_three_nodes2(node_one, node_two, node_three):
    if is_descendant2(node_two, node_one):
        return is_descendant2(node_three, node_two)

    if is_descendant2(node_two, node_three):
        return is_descendant2(node_one, node_two)

    return False


# Whether the `target` is a descendant of the `node`.
def is_descendant2(node, target):
    # 非递归方式调用
    # 先判断当前节点是否为空, 再判断当前节点是否与查找目标相同
    while node is not None and node is not target:
        # 根据二叉搜索树的特点
        # 如果当前值小于节点的值则选择左子树
        if target.value < node.value:
            node = node.left
        # 如果当前值大于等于节点的值则选择右子树
        else:
            node = node.right
    # 最后判断节点是否与查找目标相同, 如果节点已经遍历完成, 则变量为 None 与目标也不相同
    return node is target


# O(d) time | O(1) space - where d is the distance between node_one and node_three
def validate_three_nodes3(node_one, node_two, node_three):
    # 由于第二个节点处在中间, 所以仅有两种情况, 从第二个节点推断祖先元素
    # 1. 第一个节点是祖先元素, 第二个节点为第一个节点的子元素, 第三个节点为第二个节点的子元素
    search_one = node_one
    # 2. 第三个节点是祖先元素, 第二个节点为第三个节点的子元素, 第一个节点为第二个节点的子元素
    search_two = node_three
    # 同时考虑两种情况进行搜索
    while True:
        # 如果从第三个节点下找到了第一个节点
        found_three_from_one = search_one is node_three
        # 如果从第一个节点下找到了第三个节点
        found_one_from_three = search_two is node_one
        # 如果两个搜索路线有其中一个找到了第二个节点
        found_node_two = search_one is node_two or search_two is node_two
        # 两条搜索路线都已经搜索结束
        finished_searching = search_one is None and search_two is None
        # 满足任意条件则结束搜索
        if any([found_three_from_one, found_one_from_three, found_node_two, finished_searching]):
            break
        # 根据二叉搜索树的特点来切换两条搜索路线
        # 因为第二个节点必定是在中间, 所以用第二个节点的值来做判断
        if search_one is not None:
            search_one = search_one.left if search_one.value > node_two.value else search_one.right

        if search_two is not None:
            search_two = search_two.left if search_two.value > node_two.value else search_two.right
    # 搜索结束后有三种条件不成立的情况
    # 1. 如果从第一个节点开始直接搜索到了第三个节点, 未搜索到第二个节点
    # 2. 如果从第三个节点开始直接搜索到了第一个节点, 未搜索到第二个节点
    found_node_from_other = search_one is node_three or search_two is node_one
    # 3. 两条路线都没有搜索到第二个节点
    found_node_two = search_one is node_two or search_two is node_two
    if not found_node_two or found_node_from_other:
        return False
    # 上述三个否到的条件都不满足, 则再根据第二个节点搜索其子节点
    if search_one is node_two:
        # 如果先搜索到的是第一条路线, 则第二个节点的子节可能是第三个节点
        return search_for_target(node_two, node_three)
    else:
        # 如果先搜索到的是第二条路线, 则第二个节点的子节可能是第一个节点
        return search_for_target(node_two, node_one)


def search_for_target(node, target):
    while node is not None and node is not target:
        # 根据二叉搜索树的特点
        # 如果当前值小于节点的值则选择左子树
        # 如果当前值大于等于节点的值则选择右子树
        node = node.left if target.value < node.value else node.right

    return node is target


def initialize():
    array = [5, 2, 7, 1, 4, 6, 8, 0, 3]
    root_node = BST(array[0])
    for idx in range(1, len(array)):
        root_node.insert(array[idx])
    a = root_node.contains(5)
    b = root_node.contains(4)
    c = root_node.contains(3)
    return a, b, c


if __name__ == '__main__':
    one, two, three = initialize()
    print(validate_three_nodes1(one, two, three))
    print(validate_three_nodes2(one, two, three))
    print(validate_three_nodes3(one, two, three))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

class BST
{
public:
    int value;
    BST *left = nullptr;
    BST *right = nullptr;

    BST(int value) { this->value = value; }
    BST &insert(int val)
    {
        if (val < value)
        {
            if (left == nullptr)
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
            if (right == nullptr)
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

    BST *contains(int val)
    {
        if (val < value)
        {
            if (left == nullptr)
            {
                return nullptr;
            }
            else
            {
                return left->contains(val);
            }
        }
        else if (val > value)
        {
            if (right == nullptr)
            {
                return nullptr;
            }
            else
            {
                return right->contains(val);
            }
        }
        else
        {
            return this;
        }
    }
};

bool isDescendant1(BST *node, BST *target);

// O(h) time | O(h) space - where h is the height of the tree
bool validateThreeNodes1(BST *nodeOne, BST *nodeTwo, BST *nodeThree)
{
    if (isDescendant1(nodeTwo, nodeOne))
        return isDescendant1(nodeThree, nodeTwo);

    if (isDescendant1(nodeTwo, nodeThree))
        return isDescendant1(nodeOne, nodeTwo);

    return false;
}

// Whether the `target` is a descendant of the `node`.
bool isDescendant1(BST *node, BST *target)
{
    if (node == nullptr)
        return false;

    if (node == target)
        return true;

    if (target->value < node->value)
        return isDescendant1(node->left, target);
    else
        return isDescendant1(node->right, target);
}

bool isDescendant2(BST *node, BST *target);

// O(h) time | O(1) space - where h is the height of the tree
bool validateThreeNodes2(BST *nodeOne, BST *nodeTwo, BST *nodeThree)
{
    if (isDescendant2(nodeTwo, nodeOne))
        return isDescendant2(nodeThree, nodeTwo);

    if (isDescendant2(nodeTwo, nodeThree))
        return isDescendant2(nodeOne, nodeTwo);

    return false;
}

// Whether the `target` is a descendant of the `node`.
bool isDescendant2(BST *node, BST *target)
{
    BST *currentNode = node;
    while (currentNode != nullptr && currentNode != target)
    {
        if (target->value < currentNode->value)
            currentNode = currentNode->left;
        else
            currentNode = currentNode->right;
    }

    return currentNode == target;
}

bool searchForTarget(BST *node, BST *target);

// O(d) time | O(1) space - where d is the distance between nodeOne and
// nodeThree
bool validateThreeNodes3(BST *nodeOne, BST *nodeTwo, BST *nodeThree)
{
    BST *searchOne = nodeOne;
    BST *searchTwo = nodeThree;

    while (true)
    {
        bool foundThreeFromOne = searchOne == nodeThree;
        bool foundOneFromThree = searchTwo == nodeOne;
        bool foundNodeTwo = searchOne == nodeTwo || searchTwo == nodeTwo;
        bool finishedSearching = searchOne == nullptr && searchTwo == nullptr;
        if (foundThreeFromOne || foundOneFromThree || foundNodeTwo ||
            finishedSearching)
        {
            break;
        }

        if (searchOne != nullptr)
        {
            searchOne = searchOne->value > nodeTwo->value ? searchOne->left
                                                          : searchOne->right;
        }

        if (searchTwo != nullptr)
        {
            searchTwo = searchTwo->value > nodeTwo->value ? searchTwo->left
                                                          : searchTwo->right;
        }
    }

    bool foundNodeFromOther = searchOne == nodeThree || searchTwo == nodeOne;
    bool foundNodeTwo = searchOne == nodeTwo || searchTwo == nodeTwo;
    if (!foundNodeTwo || foundNodeFromOther)
        return false;

    return searchForTarget(nodeTwo, searchOne == nodeTwo ? nodeThree : nodeOne);
}

bool searchForTarget(BST *node, BST *target)
{
    BST *currentNode = node;
    while (currentNode != nullptr && currentNode != target)
    {
        currentNode = target->value < currentNode->value ? currentNode->left
                                                         : currentNode->right;
    }

    return currentNode == target;
}

int main()
{
    vector<int> array = {5, 2, 7, 1, 4, 6, 8, 0, 3};
    BST *root = new BST(array[0]);
    for (size_t i = 1; i < array.size(); i++)
        root->insert(array[i]);
    BST *one = root->contains(5);
    BST *two = root->contains(2);
    BST *three = root->contains(3);
    cout << validateThreeNodes1(one, two, three) << endl;
    cout << validateThreeNodes2(one, two, three) << endl;
    cout << validateThreeNodes3(one, two, three) << endl;
    return 0;
}
```

## 右侧小于数量

给定一个数组，找出数组中每个元素相较于右侧比它更小的元素数量，如数组`[8, 5, 11, -1, 3, 4, 2]`中，第一个元素`8`右侧小于它的树有`5`个，而第二个元素`5`右侧小于它的数有`4`个，以此类推

```python
# O(n^2) time | O(n) space - where n is the length of the array
def right_smaller_than1(array):
    right_smaller_counts = []
    for i in range(len(array)):
        # 记录右侧小于当前值的数量
        right_smaller_count = 0
        # 搜索右边的数据
        for j in range(i + 1, len(array)):
            if array[j] < array[i]:
                # 如果满足小于当前值则累加数量
                right_smaller_count += 1
        # 记录数量
        right_smaller_counts.append(right_smaller_count)
    return right_smaller_counts


class SpecialBST:
    def __init__(self, value, idx, num_smaller_at_insert_time):
        self.value = value
        # 记录数值的索引
        self.idx = idx
        # 记录总计的小于当前值数量
        self.num_smaller_at_insert_time = num_smaller_at_insert_time
        # 记录左子树的数量
        self.left_subtree_size = 0
        self.left = None
        self.right = None

    def insert(self, value, idx, num_smaller_at_insert_time=0):
        if value < self.value:
            # 如果添加的值小于当前树的值, 表示这个待添加的值应该放到左子树中, 所以左子树的数量加 1
            self.left_subtree_size += 1
            if self.left is None:
                # 左子树为空则直接创建一个新的左子树
                self.left = SpecialBST(value, idx, num_smaller_at_insert_time)
            else:
                # 左子树非空则递归调用
                self.left.insert(value, idx, num_smaller_at_insert_time)
        else:
            # 添加的值大于等于当前树的值, 表示这个待添加的值应该放到右子树中
            # 此时已知左子树的值是小于当前值的, 故累计小于数量加上当前树的左子树记录数量
            num_smaller_at_insert_time += self.left_subtree_size
            # 如果添加的值还大于当前树的值, 还需要在增加一个记录树
            if value > self.value:
                num_smaller_at_insert_time += 1
            if self.right is None:
                # 右子树为空则直接创建一个新的右子树
                self.right = SpecialBST(value, idx, num_smaller_at_insert_time)
            else:
                # 右子树非空则递归调用
                self.right.insert(value, idx, num_smaller_at_insert_time)


# Average case: when the created BST is balanced
# O(n * log(n)) time | O(n) space - where n is the length of the array
# ---
# Worst case: when the created BST is like a linked list
# O(n^2) time | O(n) space
def right_smaller_than2(array):
    if len(array) == 0:
        return []

    last_idx = len(array) - 1
    # 创建一个特殊的二叉搜索树, 可以记录小于值的数量, 初始的值为数值末尾的元素
    # 因为已知最后一个元素是没有右侧小于它的元素的, 即小于数量为 0
    bst = SpecialBST(array[last_idx], last_idx, 0)
    # 逆向将所有的元素添加到特殊二叉搜索树中
    for i in reversed(range(len(array) - 1)):
        bst.insert(array[i], i)
    # 创建记录数组
    right_smaller_counts = array[:]
    # 先序遍历特殊二叉搜索树, 将结果添加到记录数组中
    get_right_smaller_counts(bst, right_smaller_counts)
    return right_smaller_counts


def get_right_smaller_counts(bst, right_smaller_counts):
    if bst is None:
        return
    # 由于特殊二叉搜索树中已经记录了元素所在的索引, 这里直接通过索引保存小于值的数量
    right_smaller_counts[bst.idx] = bst.num_smaller_at_insert_time
    # 递归调用左子树
    get_right_smaller_counts(bst.left, right_smaller_counts)
    # 递归调用右子树
    get_right_smaller_counts(bst.right, right_smaller_counts)


class BST:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None
        # 记录左子树的数量
        self.left_subtree_count = 0

    def insert(self, value):
        current_node = self
        # 记录小于值的数量
        count = 0
        # 非递归的方式添加值到二叉搜索树中
        while True:
            if value < current_node.value:
                # 更新左子树的数量
                current_node.left_subtree_count += 1
                if current_node.left is None:
                    current_node.left = BST(value)
                    break
                else:
                    current_node = current_node.left
            else:
                count += current_node.left_subtree_count
                if value > current_node.value:
                    count += 1
                if current_node.right is None:
                    current_node.right = BST(value)
                    break
                else:
                    current_node = current_node.right
        # 返回小于值的数量
        return count


# Average: O(n * log(n)) time | O(n) space - where n is the length of the array
# Worst: O(n^2) time | O(n) space
def right_smaller_than3(array):
    if not len(array):
        return []
    right_smaller_counts = [0 for _ in array]
    current_idx = len(array) - 1
    tree = BST(array[current_idx])
    while current_idx > 0:
        current_idx -= 1
        # 与上面的逻辑类似, 只是这里直接记录结果, 不再需要重新去遍历二叉搜索树
        right_smaller_counts[current_idx] = tree.insert(array[current_idx])

    return right_smaller_counts


if __name__ == '__main__':
    source = [8, 5, 11, -1, 3, 4, 2]
    print(right_smaller_than1(source))
    print(right_smaller_than2(source))
    print(right_smaller_than3(source))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

// O(n^2) time | O(n) space - where n is the length of the array
vector<int> rightSmallerThan1(vector<int> array)
{
    vector<int> rightSmallerCounts = {};
    const int arraySize = array.size();
    for (int i = 0; i < arraySize; i++)
    {
        int rightSmallerCount = 0;
        for (int j = i + 1; j < arraySize; j++)
        {
            if (array[j] < array[i])
                rightSmallerCount++;
        }
        rightSmallerCounts.push_back(rightSmallerCount);
    }
    return rightSmallerCounts;
}

class SpecialBST1
{
public:
    int value;
    int idx;
    int numSmallerAtInsertTime;
    int leftSubtreeSize;
    SpecialBST1 *left;
    SpecialBST1 *right;

    SpecialBST1(int value, int idx, int numSmallerAtInsertTime)
    {
        this->value = value;
        this->idx = idx;
        this->numSmallerAtInsertTime = numSmallerAtInsertTime;
        leftSubtreeSize = 0;
        left = nullptr;
        right = nullptr;
    }

    void insert(int value, int idx, int numSmallerAtInsertTime = 0)
    {
        if (value < this->value)
        {
            leftSubtreeSize++;
            if (left == nullptr)
            {
                left = new SpecialBST1(value, idx, numSmallerAtInsertTime);
            }
            else
            {
                left->insert(value, idx, numSmallerAtInsertTime);
            }
        }
        else
        {
            numSmallerAtInsertTime += leftSubtreeSize;
            if (value > this->value)
                numSmallerAtInsertTime++;
            if (right == nullptr)
            {
                right = new SpecialBST1(value, idx, numSmallerAtInsertTime);
            }
            else
            {
                right->insert(value, idx, numSmallerAtInsertTime);
            }
        }
    }
};

void getRightSmallerCounts(SpecialBST1 *bst, vector<int> &rightSmallerCounts);

// Average case: when the created BST is balanced
// O(n * log(n)) time | O(n) space - where n is the length of the array
// ---
// Worst case: when the created BST is like a linked list
// O(n^2) time | O(n) space
vector<int> rightSmallerThan2(vector<int> array)
{
    if (array.size() == 0)
        return {};

    int lastIdx = array.size() - 1;
    SpecialBST1 *bst = new SpecialBST1(array[lastIdx], lastIdx, 0);
    for (int i = array.size() - 2; i >= 0; i--)
    {
        bst->insert(array[i], i);
    }

    vector<int> rightSmallerCounts = array;
    getRightSmallerCounts(bst, rightSmallerCounts);
    return rightSmallerCounts;
}

void getRightSmallerCounts(SpecialBST1 *bst, vector<int> &rightSmallerCounts)
{
    if (bst == nullptr)
        return;
    rightSmallerCounts[bst->idx] = bst->numSmallerAtInsertTime;
    getRightSmallerCounts(bst->left, rightSmallerCounts);
    getRightSmallerCounts(bst->right, rightSmallerCounts);
}

class SpecialBST2
{
public:
    int value;
    int leftSubtreeSize;
    SpecialBST2 *left;
    SpecialBST2 *right;

    SpecialBST2(int value)
    {
        this->value = value;
        leftSubtreeSize = 0;
        left = nullptr;
        right = nullptr;
    }

    void insert(int value, int idx, vector<int> &rightSmallerCounts,
                int numSmallerAtInsertTime = 0)
    {
        if (value < this->value)
        {
            leftSubtreeSize++;
            if (left == nullptr)
            {
                left = new SpecialBST2(value);
                rightSmallerCounts[idx] = numSmallerAtInsertTime;
            }
            else
            {
                left->insert(value, idx, rightSmallerCounts, numSmallerAtInsertTime);
            }
        }
        else
        {
            numSmallerAtInsertTime += leftSubtreeSize;
            if (value > this->value)
                numSmallerAtInsertTime++;
            if (right == nullptr)
            {
                right = new SpecialBST2(value);
                rightSmallerCounts[idx] = numSmallerAtInsertTime;
            }
            else
            {
                right->insert(value, idx, rightSmallerCounts, numSmallerAtInsertTime);
            }
        }
    }
};

// Average case: when the created BST is balanced
// O(n * log(n)) time | O(n) space - where n is the length of the array
// ---
// Worst case: when the created BST is like a linked list
// O(n^2) time | O(n) space
vector<int> rightSmallerThan3(vector<int> array)
{
    if (array.size() == 0)
        return {};

    vector<int> rightSmallerCounts = array;
    int lastIdx = array.size() - 1;
    SpecialBST2 *bst = new SpecialBST2(array[lastIdx]);
    rightSmallerCounts[lastIdx] = 0;
    for (int i = array.size() - 2; i >= 0; i--)
    {
        bst->insert(array[i], i, rightSmallerCounts);
    }

    return rightSmallerCounts;
}

void iteration(vector<int> array)
{
    for (int element : array)
    {
        cout << element << " ";
    }
    cout << endl;
}

int main()
{
    vector<int> source = {8, 5, 11, -1, 3, 4, 2};
    iteration(rightSmallerThan1(source));
    iteration(rightSmallerThan2(source));
    iteration(rightSmallerThan3(source));
    return 0;
}
```

Last Modified 2022-06-07

# 二叉树

## 分支节点求和

```python
class BinaryTree:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def insert_level_order(array, tree, index, length):
    if index < length:
        tree = BinaryTree(array[index])
        tree.left = insert_level_order(array, tree.left, 2 * index + 1, length)
        tree.right = insert_level_order(array, tree.right, 2 * index + 2, length)
    return tree


# O(n) time | O(n) space
def branch_sums(tree):
    sums = None
    if tree is not None:
        sums = list()
        calculate_branch_sums(tree, 0, sums)
    return sums


def calculate_branch_sums(tree, current_sums, sums):
    current_sums = tree.value + current_sums
    if tree.left is None and tree.right is None:
        sums.append(current_sums)
        return False
    if tree.left is not None:
        calculate_branch_sums(tree.left, current_sums, sums)
    if tree.right is not None:
        calculate_branch_sums(tree.right, current_sums, sums)


if __name__ == '__main__':
    source = list(range(1, 11))
    root = insert_level_order(source, None, 0, len(source))
    print(branch_sums(root))
```

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct BinaryTree
{
    int data;
    BinaryTree *left, *right;
} BinaryTree, *pBinaryTree;

typedef struct LinkedList
{
    int data;
    LinkedList *next;
} LinkedList, *pLinkedList;

pLinkedList head, tail;

pBinaryTree newBinaryTree(int data)
{
    pBinaryTree BinaryTree = (pBinaryTree)malloc(sizeof(BinaryTree));
    BinaryTree->data = data;
    BinaryTree->left = BinaryTree->right = NULL;
    return BinaryTree;
}

pLinkedList newItem(int data)
{
    pLinkedList linkedList = (pLinkedList)malloc(sizeof(linkedList));
    linkedList->data = data;
    linkedList->next = NULL;
    return linkedList;
}

pBinaryTree insertLevelOrder(int arr[], pBinaryTree root, int i, int n)
{
    if (i < n)
    {
        root = newBinaryTree(arr[i]);
        root->left = insertLevelOrder(arr, root->left, 2 * i + 1, n);
        root->right = insertLevelOrder(arr, root->right, 2 * i + 2, n);
    }
    return root;
}

void printList(pLinkedList lists)
{
    while (lists != NULL)
    {
        printf("%d ", lists->data);
        lists = lists->next;
    }
}

void addToLinkedList(int data)
{
    if ( head == NULL )
    {
        head = newItem(data);
        tail = head;
    }
    else
    {
        pLinkedList p = newItem(data);
        tail->next = p;
        tail = p;
    }
}

void calculateBranchSums(pBinaryTree root, int currentSums)
{
    currentSums = root->data + currentSums;
    if (root->left == NULL && root->right == NULL)
    {
        addToLinkedList(currentSums);
        return;
    }
    if (root->left != NULL)
        calculateBranchSums(root->left, currentSums);
    if (root->right != NULL)
        calculateBranchSums(root->right, currentSums);
}

/* O(n) time | O(n) space */
void branchSums(pBinaryTree root)
{
    if (root != NULL)
    {
        calculateBranchSums(root, 0);
    }
}

int main()
{
    int arr[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    int n = sizeof(arr) / sizeof(arr[0]);
    pBinaryTree root = insertLevelOrder(arr, NULL, 0, n);
    branchSums(root);
    printList(head);
    return 0;
}
```

```java
import java.util.LinkedList;
import java.util.List;
import java.util.Objects;
import java.util.stream.IntStream;

public class BinaryTreeAlgorithm {

    static class BinaryTree {
        public int value;
        public BinaryTree left, right;

        public BinaryTree(int value) {
            this.value = value;
        }
    }

    public BinaryTree insertLevelOrder(int[] array, BinaryTree tree, int index) {
        if (index < array.length) {
            tree = new BinaryTree(array[index]);
            tree.left = insertLevelOrder(array, tree.left, 2 * index + 1);
            tree.right = insertLevelOrder(array, tree.right, 2 * index + 2);
        }
        return tree;
    }

    private void calculateBranchSums(BinaryTree tree, int currentSums, List<Integer> sums) {
        currentSums = tree.value + currentSums;
        if (Objects.isNull(tree.left) && Objects.isNull(tree.right)) {
            sums.add(currentSums);
            return;
        }
        if (Objects.nonNull(tree.left))
            calculateBranchSums(tree.left, currentSums, sums);
        if (Objects.nonNull(tree.right))
            calculateBranchSums(tree.right, currentSums, sums);
    }


    /* O(n) time | O(n) space */
    public List<Integer> branchSums(BinaryTree tree) {
        List<Integer> sums = null;
        if (Objects.nonNull(tree)) {
            sums = new LinkedList<>();
            calculateBranchSums(tree, 0, sums);
        }
        return sums;
    }

    public static void main(String[] args) {
        BinaryTreeAlgorithm algorithm = new BinaryTreeAlgorithm();
        BinaryTree root = algorithm.insertLevelOrder(IntStream.range(1, 10).toArray(), null, 0);
        List<Integer> integerList = algorithm.branchSums(root);
        if (Objects.nonNull(integerList))
            integerList.forEach(e -> System.out.printf("%d ", e));
    }
}
```

## 节点高度求和

根节点的高度为 0，第二层的节点高度为 1，以此类推。

```python
class BinaryTree:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def insert_level_order(array, tree, index, length):
    if index < length:
        tree = BinaryTree(array[index])
        tree.left = insert_level_order(array, tree.left, 2 * index + 1, length)
        tree.right = insert_level_order(array, tree.right, 2 * index + 2, length)
    return tree


# O(n) time | O(h) space
# h is binary tree height
def node_depths1(root):
    node_depths_sums = 0
    stack = [(root, 0,)]
    while len(stack):
        node_info = stack.pop()
        node, depth = node_info
        if node is None:
            continue
        node_depths_sums += depth
        stack.append((node.left, depth + 1,))
        stack.append((node.right, depth + 1,))
    return node_depths_sums


# O(n) time | O(h) space
def node_depths2(root, depth=0):
    if root is None:
        return 0
    return depth + node_depths2(root.left, depth + 1) + node_depths2(root.right, depth + 1)


if __name__ == '__main__':
    source = list(range(1, 10))
    parent = insert_level_order(source, None, 0, len(source))
    print(node_depths1(parent))
    print(node_depths2(parent))
```

```cpp
#include <iostream>
#include <vector>

using namespace std;

class BinaryTree
{
public:
    int value;
    BinaryTree *left;
    BinaryTree *right;

    BinaryTree(int value)
    {
        this->value = value;
        left = nullptr;
        right = nullptr;
    }
};

struct Level
{
    BinaryTree *root;
    int depth;
};

BinaryTree* insertLevelOrder(vector<int> &array, BinaryTree *tree, int index, int length)
{
    if ( index < length )
    {
        tree = new BinaryTree(array[index]);
        tree->left = insertLevelOrder(array, tree->left, 2 * index + 1, length);
        tree->right = insertLevelOrder(array, tree->right, 2 * index + 2, length);
    }
    return tree;
}

int nodeDepths(BinaryTree *root)
{
    int sumOfDepths = 0;
    vector<Level> stack = {{root, 0}};
    while ( stack.size() > 0 )
    {
        BinaryTree *node = stack.back().root;
        int depth = stack.back().depth;
        stack.pop_back();
        if ( node == nullptr)
            continue;
        sumOfDepths += depth;
        stack.push_back(Level{node->left, depth + 1});
        stack.push_back(Level{node->right, depth + 1});
    }
    return sumOfDepths;
}


int main()
{
    vector<int> source = {1,2,3,4,5,6,7,8,9};
    BinaryTree *root = insertLevelOrder(source, nullptr, 0, source.size());
    cout << nodeDepths(root);
    return 0;
}
```

## 反转二叉数

```python
class BinaryTree:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def insert_level_order(array, tree, index, length):
    if index < length and array[index] is not None:
        tree = BinaryTree(array[index])
        tree.left = insert_level_order(array, tree.left, 2 * index + 1, length)
        tree.right = insert_level_order(array, tree.right, 2 * index + 2, length)
    return tree


def in_order_traverse(tree):
    stack = list()
    while tree is not None or len(stack) > 0:
        while tree is not None:
            stack.append(tree)
            tree = tree.left
        if len(stack) > 0:
            tree = stack.pop()
            print(tree.value, end=' ')
            tree = tree.right
    print()


def swap_left_and_right(tree):
    tree.left, tree.right = tree.right, tree.left


# O(n) time | O(n) space
def invert_binary_tree1(tree):
    queue = [tree]
    while len(queue):
        current = queue.pop(0)
        if current is not None:
            swap_left_and_right(current)
            queue.append(current.left)
            queue.append(current.right)


# O(n) time | O(d) space
# d is tree depth
def invert_binary_tree2(tree):
    if tree is not None:
        swap_left_and_right(tree)
        invert_binary_tree2(tree.left)
        invert_binary_tree2(tree.right)


if __name__ == '__main__':
    source = [1, 2, 3, 4, 5, 6, 7, 8, 9]
    root_node = insert_level_order(source, None, 0, len(source))
    in_order_traverse(root_node)
    invert_binary_tree1(root_node)
    in_order_traverse(root_node)
    root_node = insert_level_order(source, None, 0, len(source))
    invert_binary_tree2(root_node)
    in_order_traverse(root_node)
```

```cpp
#include <iostream>
#include <vector>
#include <deque>
#include <stack>

using namespace std;

class BinaryTree
{
public:
    int value;
    BinaryTree *left;
    BinaryTree *right;
    BinaryTree(int val)
    {
        value = val;
        left = nullptr;
        right = nullptr;
    };
};

BinaryTree *insertLevelOrder(vector<int> &array, BinaryTree *tree, int index, int length)
{
    if (index < length)
    {
        tree = new BinaryTree(array[index]);
        tree->left = insertLevelOrder(array, tree->left, 2 * index + 1, length);
        tree->right = insertLevelOrder(array, tree->right, 2 * index + 2, length);
    }
    return tree;
}

void inOrderTraverse(BinaryTree *tree)
{
    if ( tree == nullptr ) return;
    stack<BinaryTree *> stk;
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
void invertBinaryTree1(BinaryTree *tree)
{
    deque<BinaryTree *> queue;
    queue.push_back(tree);
    while (queue.size() > 0)
    {
        BinaryTree *current = queue.front();
        queue.pop_front();
        if (current == nullptr)
        {
            continue;
        }
        swap( current->left, current->right);
        queue.push_back( current->left );
        queue.push_back( current->right );
    }
}

// O(n) time | O(d) space
// d is tree depth
void invertBinaryTree2(BinaryTree *tree)
{
    if ( tree != nullptr )
    {
        swap( tree->left, tree->right);
        invertBinaryTree2(tree->left);
        invertBinaryTree2(tree->right);
    }
}

int main()
{
    vector<int> source = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    BinaryTree *root_node = insertLevelOrder(source, nullptr, 0, source.size());
    inOrderTraverse(root_node);
    invertBinaryTree1(root_node);
    inOrderTraverse(root_node);
    root_node = insertLevelOrder(source, nullptr, 0, source.size());
    invertBinaryTree2(root_node);
    inOrderTraverse(root_node);
    return 0;
}
```

## 二叉树最大直径

```python
class BinaryTree:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


class TreeInfo:

    def __init__(self, diameter, height):
        self.diameter = diameter
        self.height = height


def insert_level_order(array, tree, index, length):
    if index < length and array[index] is not None:
        tree = BinaryTree(array[index])
        tree.left = insert_level_order(array, tree.left, 2 * index + 1, length)
        tree.right = insert_level_order(array, tree.right, 2 * index + 2, length)
    return tree


def get_tree_info(tree):
    if tree is None:
        return TreeInfo(0, 0)
    left_tree = get_tree_info(tree.left)
    right_tree = get_tree_info(tree.right)

    longest_path_through_root = left_tree.height + right_tree.height
    max_diameter_so_far = max(left_tree.diameter, right_tree.diameter)
    current_diameter = max(longest_path_through_root, max_diameter_so_far)
    current_height = 1 + max(left_tree.height, right_tree.height)

    return TreeInfo(current_diameter, current_height)


# Average case: when the tree is balanced
# O(n) time| O(h) space - where n is the number of nodes in
# the Binary Tree and h is the height of the Binary Tree
# Worst: O(n) time | O(n) space
def binary_tree_diameter(tree):
    return get_tree_info(tree).diameter


if __name__ == '__main__':
    source = [1, 3, 2, 7, 4, None, None, 8, None, None, 5, None, None,
              None, None, 9, None, None, None, None, None, None, 6]
    root_node = insert_level_order(source, None, 0, len(source))
    print(binary_tree_diameter(root_node))
```

```cpp
#include <iostream>
#include <vector>

using namespace std;

class BinaryTree
{
public:
    int value;
    BinaryTree *left;
    BinaryTree *right;
    BinaryTree(int val)
    {
        value = val;
        left = nullptr;
        right = nullptr;
    }
};

struct TreeInfo
{
    int diameter;
    int height;
};

BinaryTree* insertLevelOrder(vector<int> &array, BinaryTree *tree, int index, int length)
{
    if ( index < length && array[index] != -1)
    {
        tree = new BinaryTree(array[index]);
        tree->left = insertLevelOrder(array, tree->left, 2 * index + 1, length);
        tree->right = insertLevelOrder(array, tree->right, 2 * index + 2, length);
    }
    return tree;
}

TreeInfo getTreeInfo(BinaryTree *tree);

// Average case: when the tree is balanced
// O(n) time| O(h) space - where n is the number of nodes in
// the Binary Tree and h is the height of the Binary Tree
// Worst: O(n) time | O(n) space
int binaryTreeDiameter(BinaryTree *tree)
{
    return getTreeInfo(tree).diameter;
}

TreeInfo getTreeInfo(BinaryTree *tree)
{
    if (tree == nullptr)
    {
        return TreeInfo{0, 0};
    }
    TreeInfo leftTreeInfo = getTreeInfo(tree->left);
    TreeInfo rightTreeInfo = getTreeInfo(tree->right);
    int longestPathThroughRoot = leftTreeInfo.height + rightTreeInfo.height;
    int maxDiameterSoFar = max(leftTreeInfo.diameter, rightTreeInfo.diameter);
    int currentDiameter = max(longestPathThroughRoot, maxDiameterSoFar);
    int currentHeight = 1 + max(leftTreeInfo.height, rightTreeInfo.height);
    return TreeInfo{ currentDiameter, currentHeight };
}

int main()
{
    vector<int> source =
    {
        1, 3, 2, 7, 4, -1, -1, 8, -1, -1, 5, -1, -1, -1, -1, 9, -1, -1, -1, -1, -1, -1, 6
    };
    BinaryTree *root_node = insertLevelOrder(source, nullptr, 0, source.size());
    cout << binaryTreeDiameter(root_node);
    return 0;
}
```

## 最大路径之和

给定一个二叉树，找出二叉树中连接两个节点所经过的所有节点的值相加的最大和，如下二叉树的最大路径和为`5 + 2 + 1 + 3 + 7 = 18`

```
       1
     /   \
    2     3
   / \   /  \
  4   5 6    7
```

```python
class BinaryTree:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def insert_level_order(array, tree, index, length):
    if index < length:
        tree = BinaryTree(array[index])
        tree.left = insert_level_order(array, tree.left, 2 * index + 1, length)
        tree.right = insert_level_order(array, tree.right, 2 * index + 2, length)
    return tree


# O(n) time | O(log(n)) space
def max_path_sum(tree):
    # 调用辅助函数, 接收两个值
    # 第一个值表示包含父节点和单个子节点的最长路径
    # 第二个值表示包含父节点或左节点或右节点的最长路径
    _, max_sum = find_max_sum(tree)
    return max_sum


def find_max_sum(tree):
    if tree is None:
        # 如果节点是空的则表示子树路径为零
        # 第二个参数返回无穷小是因为二叉树中可能存在负数值的节点
        # 为了确保 max 方法正常求出最大值, 故返回无穷小
        return 0, float("-inf")
    # 递归调用求出左子树的路径
    left_max_sum_as_branch, left_max_path_sum = find_max_sum(tree.left)
    # 递归调用求出右子树的路径
    right_max_sum_as_branch, right_max_path_sum = find_max_sum(tree.right)
    # 1. 最长路径: 仅左子树, 仅右子树; 这里可能会出现负数
    max_child_sum_as_branch = max(left_max_sum_as_branch, right_max_sum_as_branch)

    value = tree.value
    # 2. 最长路径: 左右子树最大路径加上当前节点的值, 仅当前节点; 这里可能会出现负数
    max_sum_as_branch = max(max_child_sum_as_branch + value, value)
    # 3. 最长路径: 累加当前值与左右子树的路径, 仅当前值加上子树路径
    max_sum_as_root_node = max(left_max_sum_as_branch + value + right_max_sum_as_branch, max_sum_as_branch)
    # 4. 最长路径: 左子树最长路径, 右子树最长路径, 包含当前节点的最长路径
    max_path_of_sum = max(left_max_path_sum, right_max_path_sum, max_sum_as_root_node)
    # 返回: 仅子树最长路径, 包含当前节点计算出的最长路径
    return max_sum_as_branch, max_path_of_sum


if __name__ == '__main__':
    source = list(range(1, 8))
    root = insert_level_order(source, None, 0, len(source))
    print(max_path_sum(root))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

class BinaryTree
{
public:
    int value;
    BinaryTree *left;
    BinaryTree *right;

    BinaryTree(int value)
    {
        this->value = value;
        left = nullptr;
        right = nullptr;
    }
};

BinaryTree *insertLevelOrder(vector<int> &array, BinaryTree *tree, int index, int length)
{
    if (index < length)
    {
        tree = new BinaryTree(array[index]);
        tree->left = insertLevelOrder(array, tree->left, 2 * index + 1, length);
        tree->right = insertLevelOrder(array, tree->right, 2 * index + 2, length);
    }
    return tree;
}

vector<int> findMaxSum(BinaryTree *tree);

// O(n) time | O(log(n)) space
int maxPathSum(BinaryTree *tree)
{
    vector<int> maxSumArray = findMaxSum(tree);
    return maxSumArray[1];
}

vector<int> findMaxSum(BinaryTree *tree)
{
    if (tree == nullptr)
    {
        return vector<int>{0, INT_MIN};
    }

    vector<int> leftMaxSumArray = findMaxSum(tree->left);
    int leftMaxSumAsBranch = leftMaxSumArray[0];
    int leftMaxPathSum = leftMaxSumArray[1];

    vector<int> rightMaxSumArray = findMaxSum(tree->right);
    int rightMaxSumAsBranch = rightMaxSumArray[0];
    int rightMaxPathSum = rightMaxSumArray[1];

    int maxChildSumAsBranch = max(leftMaxSumAsBranch, rightMaxSumAsBranch);
    int maxSumAsBranch = max(maxChildSumAsBranch + tree->value, tree->value);
    int maxSumAsRootNode = max(
        leftMaxSumAsBranch + tree->value + rightMaxSumAsBranch, maxSumAsBranch);
    int maxPathSum = max(leftMaxPathSum, max(rightMaxPathSum, maxSumAsRootNode));

    return vector<int>{maxSumAsBranch, maxPathSum};
}

int main()
{
    vector<int> source = {1, 2, 3, 4, 5, 6, 7};
    BinaryTree *root = insertLevelOrder(source, nullptr, 0, source.size());
    cout << maxPathSum(root);
    return 0;
}
```

## 线性前序遍历

```python
class BinaryTree:

    def __init__(self, value, parent=None):
        self.value = value
        self.parent = parent
        self.left = None
        self.right = None


def insert_level_order(array, tree, parent, index, length):
    if index < length and array[index] is not None:
        tree = BinaryTree(array[index], parent)
        tree.left = insert_level_order(array, tree.left, tree, 2 * index + 1, length)
        tree.right = insert_level_order(array, tree.right, tree, 2 * index + 2, length)
    return tree


# O(n) time | O(1) space
def iterative_in_order_traversal(tree, callback):
    previous_node, next_node = None, None
    current_node = tree
    while current_node is not None:
        if previous_node is None or previous_node == current_node.parent:
            if current_node.left is not None:
                next_node = current_node.left
            else:
                callback(current_node.value)
                next_node = current_node.right if current_node.right is not None else current_node.parent
        elif previous_node == current_node.left:
            callback(current_node.value)
            next_node = current_node.right if current_node.right is not None else current_node.parent
        else:
            next_node = current_node.parent
        previous_node = current_node
        current_node = next_node


if __name__ == '__main__':
    source = [1, 2, 3, 4, None, 6, 7, None, 9]
    root_node = insert_level_order(source, None, None, 0, len(source))
    iterative_in_order_traversal(root_node, lambda e: print(e, end=' '))
```

## 展平二叉树

```python
class BinaryTree:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def insert_level_order(array, tree, index, length):
    if index < length and array[index] is not None:
        tree = BinaryTree(array[index])
        tree.left = insert_level_order(array, tree.left, 2 * index + 1, length)
        tree.right = insert_level_order(array, tree.right, 2 * index + 2, length)
    return tree


def get_nodes_in_order(tree, array):
    if tree is not None:
        get_nodes_in_order(tree.left, array)
        array.append(tree)
        get_nodes_in_order(tree.right, array)
    return array


def connect_nodes(left, right):
    left.right = right
    right.left = left


def flatten_tree(node):
    if node.left is None:
        left_most = node
    else:
        left_subtree_left_most, left_subtree_right_most = flatten_tree(node.left)
        connect_nodes(left_subtree_right_most, node)
        left_most = left_subtree_left_most

    if node.right is None:
        right_most = node
    else:
        right_subtree_left_most, right_subtree_right_most = flatten_tree(node.right)
        connect_nodes(node, right_subtree_left_most)
        right_most = right_subtree_right_most

    return left_most, right_most


# O(n) time | O(n) space
def flatten_binary_tree1(root):
    in_order_nodes = get_nodes_in_order(root, list())
    for i in range(len(in_order_nodes) - 1):
        left_node = in_order_nodes[i]
        right_node = in_order_nodes[i + 1]
        left_node.right = right_node
        right_node.left = left_node
    return in_order_nodes[0]


# O(n) time | O(d) space
def flatten_binary_tree2(root):
    left_most, _ = flatten_tree(root)
    return left_most


if __name__ == '__main__':
    source = [1, 2, 3, 4, 5, 6, None, None, None, 7, 8]
    root_node = insert_level_order(source, None, 0, len(source))
    flatten_binary_tree1(root_node)
    root_node = insert_level_order(source, None, 0, len(source))
    flatten_binary_tree2(root_node)
```

## 右兄弟树

```python
class BinaryTree:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def insert_level_order(array, tree, index, length):
    if index < length and array[index] is not None:
        tree = BinaryTree(array[index])
        tree.left = insert_level_order(array, tree.left, 2 * index + 1, length)
        tree.right = insert_level_order(array, tree.right, 2 * index + 2, length)
    return tree


def in_order_traverse(root, array):
    if root is not None:
        array = in_order_traverse(root.left, array)
        array.append(root.value)
        array = in_order_traverse(root.right, array)
    return array


def mutate(node, parent, is_left_child):
    if node is None:
        return False
    left, right = node.left, node.right
    mutate(left, node, True)
    if parent is None:
        node.right = None
    elif is_left_child:
        node.right = parent.right
    else:
        if parent.right is None:
            node.right = None
        else:
            node.right = parent.right.left
    mutate(right, node, False)


# O(n) time | O(d) space
def right_sibling_tree(root):
    mutate(root, None, None)
    return root


if __name__ == '__main__':
    source = [1, 2, 3, 4, 5, 6, 7, 8, 9, None, 10, 11, None, 12, 13,
              None, None, None, None, None, None, None, None, 14]
    root_node = insert_level_order(source, None, 0, len(source))
    print(in_order_traverse(root_node, list()))
    root_node = right_sibling_tree(root_node)
    print(in_order_traverse(root_node, list()))
```

## 所有节点深度

```python
class BinaryTree:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


class TreeInfo:
    def __init__(self, num_nodes_in_tree, sum_of_depths, sum_of_all_depths):
        self.num_nodes_in_tree = num_nodes_in_tree
        self.sum_of_depths = sum_of_depths
        self.sum_of_all_depths = sum_of_all_depths


def insert_level_order(array, tree, index, length):
    if index < length and array[index] is not None:
        tree = BinaryTree(array[index])
        tree.left = insert_level_order(array, tree.left, 2 * index + 1, length)
        tree.right = insert_level_order(array, tree.right, 2 * index + 2, length)
    return tree


def get_node_depths(node, depth=0):
    if node is None:
        return 0
    return depth + get_node_depths(node.left, depth + 1) + get_node_depths(node.right, depth + 1)


def sum_all_node_depths(node, node_depth):
    if node is None:
        return 0
    return node_depth[node] + sum_all_node_depths(node.left, node_depth) + sum_all_node_depths(node.right, node_depth)


def add_node_depths(node, node_depths, node_counts):
    node_depths[node] = 0
    if node.left is not None:
        add_node_depths(node.left, node_depths, node_counts)
        node_depths[node] += node_depths[node.left] + node_counts[node.left]
    if node.right is not None:
        add_node_depths(node.right, node_depths, node_counts)
        node_depths[node] += node_depths[node.right] + node_counts[node.right]


def add_node_counts(node, node_counts):
    node_counts[node] = 1
    if node.left is not None:
        add_node_counts(node.left, node_counts)
        node_counts[node] += node_counts[node.left]
    if node.right is not None:
        add_node_counts(node.right, node_counts)
        node_counts[node] += node_counts[node.right]


def get_tree_info(tree):
    if tree is None:
        return TreeInfo(0, 0, 0)
    left_tree_info = get_tree_info(tree.left)
    right_tree_info = get_tree_info(tree.right)

    sum_of_left_depth = left_tree_info.sum_of_depths + left_tree_info.num_nodes_in_tree
    sum_of_right_depth = right_tree_info.sum_of_depths + right_tree_info.num_nodes_in_tree

    num_nodes_in_tree = 1 + left_tree_info.num_nodes_in_tree + right_tree_info.num_nodes_in_tree
    sum_of_depth = sum_of_left_depth + sum_of_right_depth
    sum_of_all_depths = sum_of_depth + left_tree_info.sum_of_all_depths + right_tree_info.sum_of_all_depths

    return TreeInfo(num_nodes_in_tree, sum_of_depth, sum_of_all_depths)


# O(n * log(n)) time | O(h) space
# h is tree height
def all_kinds_of_node_depths1(root):
    sum_of_all_depths = 0
    stack = [root]
    while len(stack) > 0:
        node = stack.pop()
        if node is None:
            continue
        sum_of_all_depths += get_node_depths(node)
        stack.append(node.left)
        stack.append(node.right)
    return sum_of_all_depths


# O(n) time | O(n) space
def all_kinds_of_node_depths2(root):
    node_counts = dict()
    add_node_counts(root, node_counts)
    node_depths = dict()
    add_node_depths(root, node_depths, node_counts)
    return sum_all_node_depths(root, node_depths)


# O(n) time | O(n) space
def all_kinds_of_node_depths3(root):
    return get_tree_info(root).sum_of_all_depths


if __name__ == '__main__':
    source = [1, 2, 3, 4, 5, 6, 7, 8, 9]
    root_node = insert_level_order(source, None, 0, len(source))
    print(all_kinds_of_node_depths1(root_node))
    print(all_kinds_of_node_depths2(root_node))
    print(all_kinds_of_node_depths3(root_node))
```

## 寻找后继节点

给定一个二叉树和一个树节点，在二叉树中找出给定树节点中序遍历的下一个节点，如寻找的树节点值为`5`中序遍历为`6 4 2 5 1 3`，则找到的后继节点为`1`

```python
class BinaryTree:

    def __init__(self, value, left=None, right=None, parent=None):
        self.value = value
        self.left = left
        self.right = right
        self.parent = parent

    def __str__(self):
        if self.parent:
            return f'Node = {self.value}, Parent = {self.parent.value}'
        return f'Node = {self.value}, Parent = None'


finder_node = None


def insert_level_order(array, tree, parent, index, length, find_value):
    if index < length and array[index] is not None:
        tree = BinaryTree(array[index], parent=parent)
        tree.left = insert_level_order(array, tree.left, tree, 2 * index + 1, length, find_value)
        tree.right = insert_level_order(array, tree.right, tree, 2 * index + 2, length, find_value)
        if tree.value == find_value:
            global finder_node
            finder_node = tree
    return tree


# O(n) time | O(n) space
def find_successor1(tree, node):
    is_match, prevision_node = False, None
    stack = list()
    while tree is not None or len(stack) > 0:
        while tree is not None:
            stack.append(tree)
            tree = tree.left
        if len(stack) > 0:
            tree = stack.pop()
            if is_match:
                prevision_node = tree
                break
            if tree == node:
                is_match = True
            tree = tree.right
    return prevision_node


# 0(h) time | 0(1) space where h is the height of the tree
def find_successor2(tree, node):
    if node.right is not None:
        return get_leftmost_child(node.right)
    return get_rightmost_parent(node)


def get_leftmost_child(node):
    current_node = node
    while current_node.left is not None:
        current_node = current_node.left
    return current_node


def get_rightmost_parent(node):
    current_node = node
    while current_node.parent is not None and current_node.parent.right == current_node:
        current_node = current_node.parent
    return current_node.parent


if __name__ == '__main__':
    source = [1, 2, 3, 4, 5, None, None, 6]
    root_node = insert_level_order(source, None, None, 0, len(source), 5)
    print(find_successor1(root_node, finder_node))
    print(find_successor2(root_node, finder_node))
```

## 高度平衡二叉树

判断二叉树的左子树和右子树最大高度是否差值为`0`或`1`，如果满足则认为二叉树的高度平衡

```python
class BinaryTree:

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def insert_level_order(array, tree, index, length):
    if index < length and array[index] is not None:
        tree = BinaryTree(array[index])
        tree.left = insert_level_order(array, tree.left, 2 * index + 1, length)
        tree.right = insert_level_order(array, tree.right, 2 * index + 2, length)
    return tree


class HeightInfo:

    def __init__(self, is_balanced, height):
        self.is_balanced = is_balanced
        self.height = height


def get_tree_height_info(tree):
    if tree is None:
        return HeightInfo(True, -1)

    left_height_info = get_tree_height_info(tree.left)
    right_height_info = get_tree_height_info(tree.right)

    is_balanced = all([left_height_info.is_balanced, right_height_info.is_balanced,
                       abs(left_height_info.height - right_height_info.height) <= 1])
    height = max(left_height_info.height, right_height_info.height) + 1

    return HeightInfo(is_balanced, height)


# O(n) time| O(h) space - where n is the number of nodes in the binary tree
def height_balanced_binary_tree(tree):
    height_info = get_tree_height_info(tree)
    return height_info.is_balanced


if __name__ == '__main__':
    source = [1, 2, 3, 4, 5, None, 6, None, None, 7, 8]
    root_node = insert_level_order(source, None, 0, len(source))
    print(height_balanced_binary_tree(root_node))
    source = [1, 2, None, 4, 5, None, None, None, None, 7, 8]
    root_node = insert_level_order(source, None, 0, len(source))
    print(height_balanced_binary_tree(root_node))
```

```cpp
#include <iostream>
#include <vector>
#include <cmath>
#include <algorithm>
using namespace std;

class BinaryTree
{
public:
    int value;
    BinaryTree *left = nullptr;
    BinaryTree *right = nullptr;
    BinaryTree(int value)
    {
        this->value = value;
    }
};

struct TreeInfo
{
    bool isBalanced;
    int height;
};

BinaryTree* insertLevelOrder(vector<int> &array, BinaryTree *tree, int index, int length)
{
    if ( index < length && array[index] != -1)
    {
        tree = new BinaryTree(array[index]);
        tree->left = insertLevelOrder(array, tree->left, 2 * index + 1, length);
        tree->right = insertLevelOrder(array, tree->right, 2 * index + 2, length);
    }
    return tree;
}

TreeInfo getTreeInfo(BinaryTree *node);

// O(n) time| O(h) space - where n is the number of nodes in the binary tree
bool heightBalancedBinaryTree(BinaryTree *tree)
{
    TreeInfo treeInfo = getTreeInfo(tree);
    return treeInfo.isBalanced;
}

TreeInfo getTreeInfo(BinaryTree *node)
{
    if (node == nullptr)
        return TreeInfo{true, -1};
    TreeInfo leftSubtreeInfo = getTreeInfo(node->left);
    TreeInfo rightSubtreeInfo = getTreeInfo(node->right);
    bool isBalanced = leftSubtreeInfo.isBalanced && rightSubtreeInfo.isBalanced &&
                      abs( leftSubtreeInfo.height - rightSubtreeInfo.height) <= 1 ;
    int height = max(leftSubtreeInfo.height, rightSubtreeInfo.height) + 1;
    return TreeInfo {isBalanced, height};
}

int main()
{
    vector<int> source = {1, 2, 3, 4, 5, -1, 6, -1, -1, 7, 8};
    BinaryTree *root_node = insertLevelOrder(source, nullptr, 0, source.size());
    cout << heightBalancedBinaryTree(root_node) << endl;
    source = {1, 2, -1, 4, 5, -1, -1, -1, -1, 7, 8};
    root_node = insertLevelOrder(source, nullptr, 0, source.size());
    cout << heightBalancedBinaryTree(root_node) << endl;
    return 0;
}
```

Last Modified 2021-11-28

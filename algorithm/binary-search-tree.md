# 二叉搜索树

它或者是一棵空树，或者是具有下列性质的二叉树： 若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值； 若它的右子
树不空，则右子树上所有结点的值均大于它的根结点的值； 它的左、右子树也分别为二叉排序树。

## 构建二叉搜索数

```python
from collections import deque


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


def traverse_bst_by_middle(parent_node=None):
    if parent_node is None:
        return False
    stack = list()
    while parent_node is not None or len(stack) > 0:
        while parent_node is not None:
            stack.append(parent_node)
            parent_node = parent_node.left
        if len(stack) > 0:
            item = stack.pop()
            print(item.value, end=' ')
            parent_node = item.right


if __name__ == '__main__':
    root_node = BST(10)
    for e in [2, 5, 5, 1, 14, 22, 13, 15]:
        root_node = root_node.insert(e)
    root_node.remove(5)
    print(root_node.contains(5))
    traverse_bst_by_middle(root_node)
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


def init_bst_tree():
    bst_tree = Tree(10)
    bst_tree.left = Tree(5)
    bst_tree.right = Tree(15)
    bst_tree.left.left = Tree(2)
    bst_tree.left.right = Tree(5)
    bst_tree.right.left = Tree(13)
    bst_tree.right.right = Tree(22)
    bst_tree.left.left.left = Tree(1)
    bst_tree.right.left.right = Tree(14)
    return bst_tree


if __name__ == '__main__':
    bst = init_bst_tree()
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

Last Modified 2021-09-16

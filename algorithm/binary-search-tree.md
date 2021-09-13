# 二叉搜索树

它或者是一棵空树，或者是具有下列性质的二叉树： 若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值； 若它的右子
树不空，则右子树上所有结点的值均大于它的根结点的值； 它的左、右子树也分别为二叉排序树。

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

Last Modified 2021-09-13

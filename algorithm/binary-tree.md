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

根节点的高度为0，第二层的节点高度为1，以此类推。

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

## 反转二叉数

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


def in_order_traverse(tree, array):
    if tree is not None:
        in_order_traverse(tree.left, array)
        array.append(tree.value)
        in_order_traverse(tree.right, array)
    return array


def swap_left_and_right(tree):
    tree.left, tree.right = tree.right, tree.left


# O(n) time | O(n) space
def invert_binary_tree1(tree):
    queue = [tree]
    while len(queue):
        current = queue.pop(0)
        if current is None:
            continue
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
    source = list(range(1, 10))
    root_node = insert_level_order(source, None, 0, len(source))
    print(in_order_traverse(root_node, list()))
    invert_binary_tree1(root_node)
    print(in_order_traverse(root_node, list()))
    root_node = insert_level_order(source, None, 0, len(source))
    invert_binary_tree2(root_node)
    print(in_order_traverse(root_node, list()))
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


def init_tree():
    root_node = BinaryTree(1)
    temp_node = root_node
    temp_node.left = BinaryTree(3)
    temp_node.right = BinaryTree(2)
    temp_node = root_node.left
    for e in [7, 8, 9]:
        temp_node.left = BinaryTree(e)
        temp_node = temp_node.left
    temp_node = root_node.left
    for e in [4, 5, 6]:
        temp_node.right = BinaryTree(e)
        temp_node = temp_node.right
    return root_node


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


# O(n) time | O(n) space
def binary_tree_diameter(tree):
    return get_tree_info(tree).diameter


if __name__ == '__main__':
    root = init_tree()
    print(binary_tree_diameter(root))
```

## 线性前序遍历

```python
class BinaryTree:

    def __init__(self, value, parent=None):
        self.value = value
        self.parent = parent
        self.left = None
        self.right = None


def init_tree():
    tree = BinaryTree(1)
    tree.left = BinaryTree(2, tree)
    tree.left.left = BinaryTree(4, tree.left)
    tree.left.left.right = BinaryTree(9, tree.left.left)
    tree.right = BinaryTree(3, tree)
    tree.right.left = BinaryTree(6, tree.right)
    tree.right.right = BinaryTree(7, tree.right)
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
    iterative_in_order_traversal(init_tree(), lambda e: print(e, end=' '))
```

Last Modified 2021-10-11
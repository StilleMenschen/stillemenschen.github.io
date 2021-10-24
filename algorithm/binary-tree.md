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


# O(n) time | O(n) space
def binary_tree_diameter(tree):
    return get_tree_info(tree).diameter


if __name__ == '__main__':
    source = [1, 3, 2, 7, 4, None, None, 8, None, None, 5, None, None,
              None, None, 9, None, None, None, None, None, None, 6]
    root_node = insert_level_order(source, None, 0, len(source))
    print(binary_tree_diameter(root_node))
```

## 最大路径之和

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


def find_max_sum(tree):
    if tree is None:
        return 0, 0
    left_max_sum_as_branch, left_max_path_sum = find_max_sum(tree.left)
    right_max_sum_as_branch, right_max_path_sum = find_max_sum(tree.right)
    max_child_sum_as_branch = max(left_max_sum_as_branch, right_max_sum_as_branch)

    value = tree.value
    max_sum_as_branch = max(max_child_sum_as_branch + value, value)
    max_sum_as_root_node = max(left_max_sum_as_branch + value + right_max_sum_as_branch, max_sum_as_branch)
    max_path_sum = max(left_max_path_sum, right_max_path_sum, max_sum_as_root_node)

    return max_sum_as_branch, max_path_sum


# O(n) time | O(log(n)) space
def get_max_path_sum(tree):
    _, max_sum = find_max_sum(tree)
    return max_sum


if __name__ == '__main__':
    source = [1, 2, 3, 4, 5, 6, 7]
    root = insert_level_order(source, None, 0, len(source))
    print(get_max_path_sum(root))
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

Last Modified 2021-10-24

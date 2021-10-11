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

## 最小高度二叉搜索树

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
def validate_binary_search_tree1(tree):
    return validate_binary_search_helper(tree, float('-inf'), float('inf'))


# O(n) time | O(d) space
# d is tree depth
def validate_binary_search_tree2(tree):
    if tree is None:
        return True
    if tree.left is not None and tree.left.value > tree.value:
        return False
    if tree.right is not None and tree.right.value < tree.value:
        return False
    return validate_binary_search_tree2(tree.left) and validate_binary_search_tree2(tree.right)


if __name__ == '__main__':
    bst = init_bst([10, 5, 5, 2, 1, 15, 13, 22, 14])
    print(validate_binary_search_tree1(bst))
    print(validate_binary_search_tree2(bst))
```

Last Modified 2021-09-21
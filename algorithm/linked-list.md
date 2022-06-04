# 链表

## 基本实现

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.prev = None
        self.next = None


class DoublyLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None

    # O(1) time | O(1) space
    def set_head(self, node):
        if self.head is None:
            self.head = node
            self.tail = node
            return
        self.insert_before(self.head, node)

    # O(1) time | O(1) space
    def set_tail(self, node):
        if self.tail is None:
            self.set_head(node)
            return
        self.insert_after(self.tail, node)

    # O(1) time | O(1) space
    def insert_before(self, node, node_to_insert):
        if node_to_insert == self.head and node_to_insert == self.tail:
            return
        self.remove(node_to_insert)
        node_to_insert.prev = node.prev
        node_to_insert.next = node
        if node.prev is None:
            self.head = node_to_insert
        else:
            node.prev.next = node_to_insert
        node.prev = node_to_insert

    # O(1) time | O(1) space
    def insert_after(self, node, node_to_insert):
        if node_to_insert == self.head and node_to_insert == self.tail:
            return
        self.remove(node_to_insert)
        node_to_insert.prev = node
        node_to_insert.next = node.next
        if node.next is None:
            self.tail = node_to_insert
        else:
            node.next.prev = node_to_insert
        node.next = node_to_insert

    # O(p) time | O(1) space
    def insert_at_position(self, position, node_to_insert):
        if position == 1:
            self.set_head(node_to_insert)
            return
        node = self.head
        current_position = 1
        while node is not None and current_position != position:
            node = node.next
            current_position += 1
        if node is not None:
            self.insert_before(node, node_to_insert)
        else:
            self.set_tail(node_to_insert)

    # O(n) time | O(1) space
    def remove_nodes_with_value(self, value):
        node = self.head
        while node is not None:
            node_to_remove = node
            node = node.next
            if node_to_remove.value == value:
                self.remove(node_to_remove)

    # O(1) time | O(1) space
    def remove(self, node):
        if node == self.head:
            self.head = self.head.next
        if node == self.tail:
            self.tail = self.tail.prev
        self.remove_node_bindings(node)

    # O(n) time | O(1) space
    def contains_node_with_value(self, value):
        node = self.head
        while node is not None and node.value != value:
            node = node.next
        return node is not None

    @staticmethod
    def remove_node_bindings(node):
        if node.prev is not None:
            node.prev.next = node.next
        if node.next is not None:
            node.next.prev = node.prev
        node.prev = None
        node.next = None

    def get_node_with_value(self, value):
        node = self.head
        while node is not None:
            if node.value == value:
                return node
            node = node.next
        return None


def print_linked_list(head):
    node = head
    while node is not None:
        print(node.value, end=' ')
        node = node.next
    print()


if __name__ == '__main__':
    linked_list = DoublyLinkedList()
    linked_list.set_head(Node(5))
    linked_list.set_head(Node(4))
    linked_list.set_head(Node(3))
    linked_list.set_head(Node(2))
    linked_list.set_head(Node(1))
    linked_list.set_head(Node(4))
    linked_list.set_tail(Node(6))
    linked_list.insert_before(linked_list.tail, Node(3))
    linked_list.insert_after(linked_list.tail, Node(3))
    print_linked_list(linked_list.head)
    linked_list.remove_nodes_with_value(3)
    print_linked_list(linked_list.head)
    linked_list.remove(linked_list.get_node_with_value(2))
    print(linked_list.contains_node_with_value(5))
    print_linked_list(linked_list.head)
```

```cpp
#include <iostream>
using namespace std;

class Node
{
public:
    int value;
    Node *prev;
    Node *next;

    Node(int val)
    {
        value = val;
        prev = nullptr;
        next = nullptr;
    };
};

class DoublyLinkedList
{
public:
    Node *head;
    Node *tail;

    DoublyLinkedList()
    {
        head = nullptr;
        tail = nullptr;
    }

    // O(1) time | O(1) space
    void setHead(Node *node)
    {
        if (head == nullptr)
        {
            head = node;
            tail = node;
            return;
        }
        insertBefore(head, node);
    }

    // O(1) time | O(1) space
    void setTail(Node *node)
    {
        if (tail == nullptr)
        {
            setHead(node);
            return;
        }
        insertAfter(tail, node);
    }

    // O(1) time | O(1) space
    void insertBefore(Node *node, Node *nodeToInsert)
    {
        if (nodeToInsert == head && nodeToInsert == tail)
            return;
        remove(nodeToInsert);
        nodeToInsert->prev = node->prev;
        nodeToInsert->next = node;
        if (node->prev == nullptr)
        {
            head = nodeToInsert;
        }
        else
        {
            node->prev->next = nodeToInsert;
        }
        node->prev = nodeToInsert;
    }

    // O(1) time | O(1) space
    void insertAfter(Node *node, Node *nodeToInsert)
    {
        if (nodeToInsert == head && nodeToInsert == tail)
            return;
        remove(nodeToInsert);
        nodeToInsert->prev = node;
        nodeToInsert->next = node->next;
        if (node->next == nullptr)
        {
            tail = nodeToInsert;
        }
        else
        {
            node->next->prev = nodeToInsert;
        }
        node->next = nodeToInsert;
    }

    // O(p) time | O(1) space
    void insertAtPosition(int position, Node *nodeToInsert)
    {
        if (position == 1)
        {
            setHead(nodeToInsert);
            return;
        }
        Node *node = head;
        int currentPosition = 1;
        while (node != nullptr && currentPosition++ != position)
            node = node->next;
        if (node != nullptr)
        {
            insertBefore(node, nodeToInsert);
        }
        else
        {
            setTail(nodeToInsert);
        }
    }

    // O(n) time | O(1) space
    void removeNodesWithValue(int value)
    {
        Node *node = head;
        while (node != nullptr)
        {
            Node *nodeToRemove = node;
            node = node->next;
            if (nodeToRemove->value == value)
                remove(nodeToRemove);
        }
    }

    // O(1) time | O(1) space
    void remove(Node *node)
    {
        if (node == head)
            head = head->next;
        if (node == tail)
            tail = tail->prev;
        removeNodeBindings(node);
    }

    // O(n) time | O(1) space
    bool containsNodeWithValue(int value)
    {
        Node *node = head;
        while (node != nullptr && node->value != value)
            node = node->next;
        return node != nullptr;
    }

    void removeNodeBindings(Node *node)
    {
        if (node->prev != nullptr)
            node->prev->next = node->next;
        if (node->next != nullptr)
            node->next->prev = node->prev;
        node->prev = nullptr;
        node->next = nullptr;
    }

    Node *getNodeWithValue(int value)
    {
        Node *node = head;
        while (node != nullptr)
        {
            if (node->value == value)
            {
                return node;
            }
            node = node->next;
        }
        return nullptr;
    }
};

void printLinkedList(Node *head)
{
    Node *node = head;
    while (node != nullptr)
    {
        cout << node->value << " ";
        node = node->next;
    }
    cout << endl;
}

int main()
{
    DoublyLinkedList linkedList;
    linkedList.setHead(new Node(5));
    linkedList.setHead(new Node(4));
    linkedList.setHead(new Node(3));
    linkedList.setHead(new Node(2));
    linkedList.setHead(new Node(1));
    linkedList.setHead(new Node(4));
    linkedList.setTail(new Node(6));
    linkedList.insertBefore(linkedList.tail, new Node(3));
    linkedList.insertAfter(linkedList.tail, new Node(3));
    linkedList.removeNodesWithValue(3);
    linkedList.remove(linkedList.getNodeWithValue(2));
    linkedList.containsNodeWithValue(5);
    printLinkedList(linkedList.head);
    return 0;
}
```

## 删除末尾向前第 N 个节点

```python
class LinkedList:
    def __init__(self, value):
        self.value = value
        self.next = None


# O(n) time | O(1) space
def remove_kth_node_from_end(head, k):
    counter = 1
    first = head
    second = head
    while counter <= k:
        second = second.next
        counter += 1
    if second is None:
        head.value = head.next.value
        head.next = head.next.next
        return
    while second.next is not None:
        second = second.next
        first = first.next
    first.next = first.next.next


def initialize_linked_list(array):
    head = LinkedList(array[0])
    p = head
    for idx in range(1, len(array)):
        p.next = LinkedList(array[idx])
        p = p.next
    return head


def print_linked_list(head):
    node = head
    while node is not None:
        print(node.value, end=' ')
        node = node.next
    print()


if __name__ == '__main__':
    source = initialize_linked_list([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
    remove_kth_node_from_end(source, 4)
    print_linked_list(source)
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

class LinkedList
{
public:
    int value;
    LinkedList *next;

    LinkedList(int val)
    {
        value = val;
        next = nullptr;
    }
};

// O(n) time | O(1) space
void removeKthNodeFromEnd(LinkedList *head, int k)
{
    int counter = 1;
    LinkedList *first = head;
    LinkedList *second = head;
    while (counter <= k)
    {
        second = second->next;
        counter++;
    }
    if (second == nullptr)
    {
        head->value = head->next->value;
        head->next = head->next->next;
        return;
    }
    while (second->next != nullptr)
    {
        second = second->next;
        first = first->next;
    }
    first->next = first->next->next;
}

LinkedList *initializeLinkedList(const vector<int> array)
{
    LinkedList *head = new LinkedList(array[0]);
    LinkedList *tail = head;
    for (size_t i = 1; i < array.size(); i++)
    {
        tail->next = new LinkedList(array[i]);
        tail = tail->next;
    }
    return head;
}

void printLinkedList(LinkedList *head)
{
    LinkedList *node = head;
    while (node != nullptr)
    {
        cout << node->value << " ";
        node = node->next;
    }
    cout << endl;
}

int main()
{
    LinkedList *source = initializeLinkedList({0, 1, 2, 3, 4, 5, 6, 7, 8, 9});
    removeKthNodeFromEnd(source, 4);
    printLinkedList(source);
    return 0;
}
```

## 找出循环节点

```python
class LinkedList:
    def __init__(self, value):
        self.value = value
        self.next = None


# O(n) time | O(1) space
def find_loop(head):
    # 定义第一个指针从第二个位置开始
    first = head.next
    # 定义第二个指针从第三个位置开始
    second = head.next.next
    # 由于第二个指针比第一个指针多走一步
    # 在循环的链表中必定会相遇
    while first != second:
        first = first.next  # 第一个指针只走一步
        second = second.next.next  # 第二个指针走两步
    first = head  # 重新将第一个指针还原到第一个位置
    # 同时移动两个指针, 但两个指针都只走一步
    while first != second:
        first = first.next
        second = second.next
    return first


"""
假设循环节点距离头节点的距离为 D, 从循环节点开始到第一次循环时两个指针相遇的位置距离为 P
那么第一个指针走过的位置为 X = D + P, 但第二个指针走过的位置为 2X = 2D + 2P
链表中所有元素的总和为 T, 而从两个节点相遇的位置到循环的节点距离为 R, 那么可以得出 T = D + P + R 和 R = T - D - P
因为第二个指针一定是经过了两次出现循环的节点才遇到第一个指针, 所以也必定经过了一次指针相遇的位置, 而从循环节点到相遇的位置为 P
所以可以推导出 T = 2D + 2P - P, 化简后为 T = 2D + P, 把这个式子代入求出 R 的式子就可以得到 R = 2D + P - D - P, 化简后为 R = D
因为 R = D, 所以第二次移动指针的时候, 两个指针都只走一步, 当两个指针相遇后就是距离 D 的循环节点的位置了
"""


def constructor_linked_list(length, loop_value):
    head_element = LinkedList(0)
    point = head_element
    loop_element = None
    for i in range(1, length):
        point.next = LinkedList(i)
        point = point.next
        if i == loop_value:
            loop_element = point
    point.next = loop_element
    return head_element


if __name__ == '__main__':
    source = constructor_linked_list(10, 4)
    print(find_loop(source).value)
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

class LinkedList
{
public:
    int value;
    LinkedList *next;

    LinkedList(int val)
    {
        value = val;
        next = nullptr;
    };
};

// O(n) time | O(1) space
LinkedList *findLoop(LinkedList *head)
{
    LinkedList *first = head->next;
    LinkedList *second = head->next->next;
    while (first != second)
    {
        first = first->next;
        second = second->next->next;
    }
    first = head;
    while (first != second)
    {
        first = first->next;
        second = second->next;
    }
    return first;
}

LinkedList *constructorLinkedList(int length, int loopValue)
{
    LinkedList *head = new LinkedList(0);
    LinkedList *point = head;
    LinkedList *loopElement = nullptr;
    for (int i = 1; i < length; i++)
    {
        point->next = new LinkedList(i);
        point = point->next;
        if (i == loopValue)
        {
            loopElement = point;
        }
    }
    point->next = loopElement;
    return head;
}

int main()
{
    LinkedList *head = constructorLinkedList(10, 4);
    LinkedList *temp = findLoop(head);
    cout << temp->value;
    return 0;
}
```

## 反转单链表

```python
class LinkedList:
    def __init__(self, value):
        self.value = value
        self.next = None


# O(n) time | O(1) space - where n is the number of nodes in the Linked List
def reverse_linked_list(head):
    # 创建两个指针用于记录链表元素
    previous_node, current_node = None, head
    # 循环链表元素, 如果当前节点为空了, 表示已经到了链表末尾
    while current_node is not None:
        # 先缓存当前节点的下一个节点
        next_node = current_node.next
        # 将当前元素的节点指向它的前一个节点
        current_node.next = previous_node
        # 前一个节点指向当前节点
        previous_node = current_node
        # 当前节点指向下一个节点
        current_node = next_node
    return previous_node


def constructor_linked_list(length):
    head_element = LinkedList(0)
    point = head_element
    for i in range(1, length):
        point.next = LinkedList(i)
        point = point.next
    return head_element


if __name__ == '__main__':
    source = constructor_linked_list(10)
    source = reverse_linked_list(source)
    while source is not None:
        print(source.value, end=' ')
        source = source.next
```

```cpp
#include <iostream>
using namespace std;

class LinkedList
{
public:
    int value;
    LinkedList *next;

    LinkedList(int value)
    {
        this->value = value;
        this->next = nullptr;
    }
};

// O(n) time | O(1) space - where n is the number of nodes in the Linked List
LinkedList *reverseLinkedList(LinkedList *head)
{
    LinkedList *previousNode = nullptr;
    LinkedList *currentNode = head;
    while (currentNode != nullptr)
    {
        LinkedList *nextNode = currentNode->next;
        currentNode->next = previousNode;
        previousNode = currentNode;
        currentNode = nextNode;
    }
    return previousNode;
}

LinkedList *constructorLinkedList(int length)
{
    LinkedList *head = new LinkedList(0);
    LinkedList *point = head;
    for (int i = 1; i < length; i++)
    {
        point->next = new LinkedList(i);
        point = point->next;
    }
    return head;
}

int main()
{
    LinkedList *head = constructorLinkedList(10);
    head = reverseLinkedList(head);
    while (head != nullptr)
    {
        cout << head->value << " ";
        head = head->next;
    }
    return 0;
}
```

## 合并链表

```python
class LinkedList:
    def __init__(self, value):
        self.value = value
        self.next = None


# O(n + m) time | O(1) space - where n is the number of nodes in the first
# Linked List and m is the number of nodes in the second Linked List
def merge_linked_lists1(head_one, head_two):
    p1 = head_one
    p1_prev = None  # 跟踪指针, 用来缓存前一个元素
    p2 = head_two
    # 如果链表中仍然有元素未遍历完则循环
    while p1 is not None and p2 is not None:
        # 如果第一个指针的值小于第二个指针
        if p1.value < p2.value:
            p1_prev = p1  # 缓存第一个指针
            p1 = p1.next  # 移动第一个指针到下一个元素
        else:
            if p1_prev is not None:
                # 由于前面 prev 缓存了第一个指针的前一个元素
                # 为了保证链表不会断开, 需要先将下一个元素指向第二个指针
                p1_prev.next = p2
            p1_prev = p2  # 缓存第二个指针
            p2 = p2.next  # 移动第二个指针到下一个元素
            p1_prev.next = p1  # 将缓存的前一个指针指向第一个指针
    # 如果第一个指针为空, 则将第二个指针剩余的元素连接到第一个链表中
    if p1 is None:
        p1_prev.next = p2
    # 由于要求返回的链表必须开始是最小的数, 所以判断两个链表值大小后返回
    return head_one if head_one.value < head_two.value else head_two


# O(n + m) time | O(n + m) space - where n is the number of nodes in the first
# Linked List and m is the number of nodes in the second Linked List
def merge_linked_lists2(head_one, head_two):
    recursive_merge(head_one, head_two, None)
    return head_one if head_one.value < head_two.value else head_two


def recursive_merge(p1, p2, p1_prev):
    if p1 is None:
        p1_prev.next = p2
        return False
    if p2 is None:
        return False

    if p1.value < p2.value:
        recursive_merge(p1.next, p2, p1)
    else:
        if p1_prev is not None:
            p1_prev.next = p2
        new_p2 = p2.next
        p2.next = p1
        recursive_merge(p1, new_p2, p2)


def merge_linked_lists3(head_one, head_two):
    # 创建两个指针, 第一个指针保证指向最小值的链表
    first_node = head_one
    second_node = head_two
    if head_one.value > head_two.value:
        first_node = head_two
        second_node = head_one
    while first_node is not None and second_node is not None:
        # 由于是以第一个指针为主的方式, 所以第二个指针没有元素了就可以结束了
        if second_node is None:
            break
        # 第一个指针的链表没有元素了, 则将第二个指针剩余的元素连接到第一个指针并结束
        if first_node.next is None:
            first_node.next = second_node
            break
        next_value = first_node.next.value
        # 比较第一第二个和第一个指针的下一个元素
        if first_node.value <= second_node.value < next_value:
            # 先缓存第二个指针的下一个元素
            next_node = second_node.next
            # 将第二个指针的下一个指向第一个指针的下一个
            second_node.next = first_node.next
            # 将第一个指针的下一个指向第二个指针
            first_node.next = second_node
            # 切换第二个指针为前面缓存的, 原本是第二个指针的下一个
            second_node = next_node
        # 移动第一个指针到下一个
        first_node = first_node.next
    # 由于要求返回的链表必须开始是最小的数, 所以判断两个链表值大小后返回
    # 这里的 <= 与上面的大小值逻辑判断必须一致
    return head_one if head_one.value <= head_two.value else head_two


def constructor_linked_list(array):
    head_element = LinkedList(array[0])
    point = head_element
    for i in range(1, len(array)):
        point.next = LinkedList(array[i])
        point = point.next
    return head_element


def get_linked_list(array1, array2):
    return constructor_linked_list(array1), constructor_linked_list(array2)


def iter_linked_list(ll):
    while ll is not None:
        print(ll.value, end=' ')
        ll = ll.next
    print()


if __name__ == '__main__':
    a = [1, 2, 4, 4, 10]
    b = [1, 3, 4, 5, 9, 10]
    iter_linked_list(merge_linked_lists1(*get_linked_list(a, b)))
    iter_linked_list(merge_linked_lists2(*get_linked_list(a, b)))
    iter_linked_list(merge_linked_lists3(*get_linked_list(a, b)))
```

```cpp
#include <iostream>
#include <vector>

using namespace std;

class LinkedList
{
public:
    int value;
    LinkedList *next;

    LinkedList(int value)
    {
        this->value = value;
        this->next = nullptr;
    }
};

// O(n + m) time | O(1) space - where n is the number of nodes in the first
// Linked List and m is the number of nodes in the second Linked List
LinkedList *mergeLinkedLists1(LinkedList *headOne, LinkedList *headTwo)
{
    LinkedList *p1 = headOne;
    LinkedList *p1Prev = nullptr;
    LinkedList *p2 = headTwo;
    while (p1 != nullptr && p2 != nullptr)
    {
        if (p1->value < p2->value)
        {
            p1Prev = p1;
            p1 = p1->next;
        }
        else
        {
            if (p1Prev != nullptr)
                p1Prev->next = p2;
            p1Prev = p2;
            p2 = p2->next;
            p1Prev->next = p1;
        }
    }
    if (p1 == nullptr)
        p1Prev->next = p2;
    return headOne->value < headTwo->value ? headOne : headTwo;
}

void recursiveMerge(LinkedList *p1, LinkedList *p2, LinkedList *p1Prev);

// O(n + m) time | O(n + m) space - where n is the number of nodes in the first
// Linked List and m is the number of nodes in the second Linked List
LinkedList *mergeLinkedLists2(LinkedList *headOne, LinkedList *headTwo)
{
    recursiveMerge(headOne, headTwo, nullptr);
    return headOne->value < headTwo->value ? headOne : headTwo;
}

void recursiveMerge(LinkedList *p1, LinkedList *p2, LinkedList *p1Prev)
{
    if (p1 == nullptr)
    {
        p1Prev->next = p2;
        return;
    }
    if (p2 == nullptr)
        return;

    if (p1->value < p2->value)
    {
        recursiveMerge(p1->next, p2, p1);
    }
    else
    {
        if (p1Prev != nullptr)
            p1Prev->next = p2;
        LinkedList *newP2 = p2->next;
        p2->next = p1;
        recursiveMerge(p1, newP2, p2);
    }
}

LinkedList *constructorLinkedList(const vector<int> array)
{
    LinkedList *head = new LinkedList(array[0]);
    LinkedList *point = head;
    for (size_t i = 1; i < array.size(); i++)
    {
        point->next = new LinkedList(array[i]);
        point = point->next;
    }
    return head;
}

void iterLinkedList(LinkedList *head)
{
    while (head != nullptr)
    {
        cout << head->value << " ";
        head = head->next;
    }
    cout << endl;
}

int main()
{
    LinkedList *a = constructorLinkedList({1, 3, 4, 5, 9, 10});
    LinkedList *b = constructorLinkedList({1, 2, 4, 4, 10});
    iterLinkedList(mergeLinkedLists1(a, b));
    a = constructorLinkedList({1, 3, 4, 5, 9, 10});
    b = constructorLinkedList({1, 2, 4, 4, 10});
    iterLinkedList(mergeLinkedLists2(a, b));
    return 0;
}
```

## 最近最少使用 (LRU)

最近最少使用缓存，添加值时根据`KEY`判断是新增还是更新，访问和添加指定`KEY`时都会更新为最近使用，
可以获取到最近使用的`KEY`，超出缓存容量后会自动删除最少使用的`KEY`对应的值，添加和访问都要求`O(1)`的复杂度

```python
class DoublyLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None

    def set_head_to(self, node):
        if self.head == node:
            return
        elif self.head is None:
            self.head = node
            self.tail = node
        elif self.head == self.tail:
            self.tail.prev = node
            self.head = node
            self.head.next = self.tail
        else:
            if self.tail == node:
                self.remove_tail()
            node.remove_bindings()
            self.head.prev = node
            node.next = self.head
            self.head = node

    def remove_tail(self):
        if self.tail is None:
            return
        if self.tail == self.head:
            self.head = None
            self.tail = None
            return
        self.tail = self.tail.prev
        self.tail.next = None


class DoublyLinkedListNode:
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None

    def remove_bindings(self):
        if self.prev is not None:
            self.prev.next = self.next
        if self.next is not None:
            self.next.prev = self.prev
        self.prev = None
        self.next = None


class LRUCache:
    def __init__(self, max_size):
        # 哈希表, 缓存每一个 KEY, 保证访问速度为常量时间
        self.cache = {}
        # 最大容量
        self.max_size = max_size or 1
        # 当前容量
        self.current_size = 0
        # 双链表, 存储实际的数据, 链表头部表示最近使用, 链表尾部表示最少使用
        self.list_of_most_recent = DoublyLinkedList()

    # O(1) time | O(1) space
    def insert_key_value_pair(self, key, value):
        # 判断 KEY 是否已经存在
        if key not in self.cache:
            # 判断是否超出容量
            if self.current_size == self.max_size:
                # 移除最后一个最少使用的元素
                self.evict_least_recent()
            else:
                # 递增当前容量
                self.current_size += 1
            # 创建新的元素
            self.cache[key] = DoublyLinkedListNode(key, value)
        else:
            # 如果 KEY 已经存在则更新值
            self.replace_key(key, value)
        # 更新最近使用的记录顺序
        self.update_most_recent(self.cache[key])

    # O(1) time | O(1) space
    def get_value_from_key(self, key):
        # KEY 不存在直接返回空
        if key not in self.cache:
            return None
        # 更新最近使用的记录顺序
        self.update_most_recent(self.cache[key])
        # 返回 KEY 对应的值
        return self.cache[key].value

    # O(1) time | O(1) space
    def get_most_recent_key(self):
        # 如果双链表中没有记录元素则返回空
        if self.list_of_most_recent.head is None:
            return None
        # 返回最近使用的 KEY
        return self.list_of_most_recent.head.key

    def evict_least_recent(self):
        # 先获得要删除的 KEY
        key_to_remove = self.list_of_most_recent.tail.key
        # 从双链表中删除链表尾部(最少使用的)
        self.list_of_most_recent.remove_tail()
        # 从哈希表中删除记录
        del self.cache[key_to_remove]

    def update_most_recent(self, node):
        # 更新最近使用只需要重新设置双链表的头部
        self.list_of_most_recent.set_head_to(node)

    def replace_key(self, key, value):
        # 考虑异常的情况, KEY 未在哈希表中
        if key not in self.cache:
            raise Exception("The provided key isn't in the cache!")
        # 替换对应 KEY 的值
        self.cache[key].value = value


if __name__ == '__main__':
    lru = LRUCache(3)
    lru.insert_key_value_pair("b", 2)
    lru.insert_key_value_pair("a", 1)
    lru.insert_key_value_pair("c", 3)
    print(lru.get_most_recent_key())
    print(lru.get_value_from_key("a"))
    print(lru.get_most_recent_key())
    lru.insert_key_value_pair("d", 4)
    print(lru.get_value_from_key("b"))
    lru.insert_key_value_pair("a", 5)
    print(lru.get_value_from_key("a"))
```

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;

class DoublyLinkedListNode
{
public:
    string key;
    int value;
    DoublyLinkedListNode *prev;
    DoublyLinkedListNode *next;

    DoublyLinkedListNode(string key, int value)
    {
        this->key = key;
        this->value = value;
        this->prev = nullptr;
        this->next = nullptr;
    }

    void removeBindings()
    {
        if (this->prev != nullptr)
        {
            this->prev->next = this->next;
        }
        if (this->next != nullptr)
        {
            this->next->prev = this->prev;
        }
        this->prev = nullptr;
        this->next = nullptr;
    }
};

class DoublyLinkedList
{
public:
    DoublyLinkedListNode *head;
    DoublyLinkedListNode *tail;

    DoublyLinkedList()
    {
        this->head = nullptr;
        this->tail = nullptr;
    }

    void setHeadTo(DoublyLinkedListNode *node)
    {
        if (this->head == node)
        {
            return;
        }
        else if (this->head == nullptr)
        {
            this->head = node;
            this->tail = node;
        }
        else if (this->head == this->tail)
        {
            this->tail->prev = node;
            this->head = node;
            this->head->next = this->tail;
        }
        else
        {
            if (this->tail == node)
            {
                this->removeTail();
            }
            node->removeBindings();
            this->head->prev = node;
            node->next = this->head;
            this->head = node;
        }
    }

    void removeTail()
    {
        if (this->tail == nullptr)
        {
            return;
        }
        if (this->tail == this->head)
        {
            this->head = nullptr;
            this->tail = nullptr;
            return;
        }
        this->tail = this->tail->prev;
        this->tail->next = nullptr;
    }
};

class LRUCache
{
public:
    unordered_map<string, DoublyLinkedListNode *> cache;
    int maxSize;
    int currentSize;
    DoublyLinkedList listOfMostRecent;

    LRUCache(int maxSize)
    {
        this->maxSize = maxSize > 1 ? maxSize : 1;
        this->currentSize = 0;
        this->listOfMostRecent = DoublyLinkedList();
    }

    // O(1) time | O(1) space
    void insertKeyValuePair(string key, int value)
    {
        if (this->cache.find(key) == this->cache.end())
        {
            if (this->currentSize == this->maxSize)
            {
                this->evictLeastRecent();
            }
            else
            {
                this->currentSize++;
            }
            this->cache[key] = new DoublyLinkedListNode(key, value);
        }
        else
        {
            this->replaceKey(key, value);
        }
        this->updateMostRecent(this->cache[key]);
    }

    // O(1) time | O(1) space
    int *getValueFromKey(string key)
    {
        if (this->cache.find(key) == this->cache.end())
        {
            return nullptr;
        }
        this->updateMostRecent(this->cache[key]);
        return &this->cache[key]->value;
    }

    // O(1) time | O(1) space
    string getMostRecentKey()
    {
        if (this->listOfMostRecent.head == nullptr)
        {
            return "";
        }
        return this->listOfMostRecent.head->key;
    }

    void evictLeastRecent()
    {
        string keyToRemove = this->listOfMostRecent.tail->key;
        this->listOfMostRecent.removeTail();
        this->cache.erase(keyToRemove);
    }

    void updateMostRecent(DoublyLinkedListNode *node)
    {
        this->listOfMostRecent.setHeadTo(node);
    }

    void replaceKey(string key, int value)
    {
        if (this->cache.find(key) == this->cache.end())
        {
            return;
        }
        this->cache[key]->value = value;
    }
};

int main()
{
    LRUCache lru(3);
    lru.insertKeyValuePair("b", 2);
    lru.insertKeyValuePair("a", 1);
    lru.insertKeyValuePair("c", 3);
    cout << lru.getMostRecentKey() << endl;
    cout << *lru.getValueFromKey("a") << endl;
    cout << lru.getMostRecentKey() << endl;
    lru.insertKeyValuePair("d", 4);
    cout << lru.getValueFromKey("b") << endl;
    lru.insertKeyValuePair("a", 5);
    cout << *lru.getValueFromKey("a") << endl;
    return 0;
}
```

## 移动链表

根据指定的整数 K 移动链表，即末尾的节点变成头节点，末尾节点的前一个节点变成末尾，K 可能是正数也可能是负数，
负数表示向后移动，即头节点变成末尾，头节点的下一个节点变成头节点

```python
class LinkedList:
    def __init__(self, value):
        self.value = value
        self.next = None


# O(n) time | O(1) space - where n is the number of nodes in the Linked List
def shift_linked_list(head, k):
    list_length = 1
    list_tail = head
    # 首先测量链表的长度
    while list_tail.next is not None:
        list_tail = list_tail.next
        list_length += 1

    # 通过 K 的绝对值计算偏移量, 用来计算需要转为链表末尾的实际位置
    # K 的值有可能超过了链表的长度, 所以这里做取模的计算
    offset = abs(k) % list_length
    if offset == 0:
        # 计算偏移量为 0 表示不需要进行操作, 直接返回原始的链表
        return head

    # 由于 K 的值为正负整数, 所以这里有两种情况
    # 当 K 的值为正数, 新的链表末尾位置为链表长度减去偏移量
    # 当 K 的值为负数, 新的链表末尾位置为偏移量
    new_tail_position = list_length - offset if k > 0 else offset
    new_tail = head  # 初始化新的尾节点
    # 注意是从 1 开始计算的, 与数组有所区别
    for i in range(1, new_tail_position):
        new_tail = new_tail.next

    # 新增链表头是新的链表末尾下一个元素
    new_head = new_tail.next
    # 因为已经记录了新的链表头, 这里去掉链表末尾的下一个元素关联
    new_tail.next = None
    # 原来的链表末尾指向原来的链表头
    list_tail.next = head
    # 返回新的链表头
    return new_head


def constructor_linked_list(length):
    head_element = LinkedList(0)
    point = head_element
    for i in range(1, length):
        point.next = LinkedList(i)
        point = point.next
    return head_element


def iter_linked_list(ll):
    while ll is not None:
        print(ll.value, end=' ')
        ll = ll.next
    print()


if __name__ == '__main__':
    iter_linked_list(shift_linked_list(constructor_linked_list(10), 6))
    iter_linked_list(shift_linked_list(constructor_linked_list(10), -12))
```

```cpp
#include <iostream>
using namespace std;

class LinkedList
{
public:
    int value;
    LinkedList *next;

    LinkedList(int value)
    {
        this->value = value;
        next = nullptr;
    }
};

// O(n) time | O(1) space - where n is the number of nodes in the Linked List
LinkedList *shiftLinkedList(LinkedList *head, int k)
{
    int listLength = 1;
    LinkedList *listTail = head;
    while (listTail->next != nullptr)
    {
        listTail = listTail->next;
        listLength++;
    }

    int offset = abs(k) % listLength;
    if (offset == 0)
        return head;
    int newTailPosition = k > 0 ? listLength - offset : offset;
    LinkedList *newTail = head;
    for (int i = 1; i < newTailPosition; i++)
    {
        newTail = newTail->next;
    }

    LinkedList *newHead = newTail->next;
    newTail->next = nullptr;
    listTail->next = head;
    return newHead;
}

LinkedList *constructorLinkedList(int length)
{
    LinkedList *head = new LinkedList(0);
    LinkedList *point = head;
    for (int i = 1; i < length; i++)
    {
        point->next = new LinkedList(i);
        point = point->next;
    }
    return head;
}

void iterLinkedList(LinkedList *head)
{
    while (head != nullptr)
    {
        cout << head->value << " ";
        head = head->next;
    }
    cout << endl;
}

int main()
{
    LinkedList *head = constructorLinkedList(10);
    iterLinkedList(shiftLinkedList(head, 6));
    head = constructorLinkedList(10);
    iterLinkedList(shiftLinkedList(head, -12));
    return 0;
}
```

## 重排链表

给定链表中的某个元素值，根据元素值重新排布链表，小于该值的节点在前，等于该值的节点在中间，大于该值的节点在后

```python
class LinkedList:
    def __init__(self, value):
        self.value = value
        self.next = None


# O(n) time | O(1) space - where n is the number of nodes in the Linked List
def rearrange_linked_list(head, k):
    # 记录小于 K 的链表节点
    smaller_list_head = None
    smaller_list_tail = None
    # 记录等于 K 的链表节点
    equal_list_head = None
    equal_list_tail = None
    # 记录大于 K 的链表节点
    greater_list_head = None
    greater_list_tail = None

    node = head
    while node is not None:
        # 根据节点的值与 K 的对比大小来添加到对应的记录列表中
        if node.value < k:
            smaller_list_head, smaller_list_tail = grow_linked_list(smaller_list_head, smaller_list_tail, node)
        elif node.value > k:
            greater_list_head, greater_list_tail = grow_linked_list(greater_list_head, greater_list_tail, node)
        else:
            equal_list_head, equal_list_tail = grow_linked_list(equal_list_head, equal_list_tail, node)
        # 为了不影响后面拼接三个链表的逻辑, 处理完当前节点后需要将下一个节点指针置空
        prev_node = node
        node = node.next
        prev_node.next = None
    # 首先连接小于和等于 K 值的节点
    first_head, first_tail = connect_linked_lists(smaller_list_head, smaller_list_tail, equal_list_head,
                                                  equal_list_tail)
    # 然后连接等于和大于 K 值的节点
    final_head, _ = connect_linked_lists(first_head, first_tail, greater_list_head, greater_list_tail)
    return final_head


def grow_linked_list(head, tail, node):
    new_head = head
    new_tail = node
    # 首次处理 head 可能为空
    if new_head is None:
        new_head = node
    # 尾节点非空则链接上节点
    if tail is not None:
        tail.next = node
    # 返回新的头尾节点
    return new_head, new_tail


def connect_linked_lists(head_one, tail_one, head_two, tail_two):
    # 优先取第一个作为头节点, 如果第一个头节点为空则取第二个头节点
    new_head = head_two if head_one is None else head_one
    # 优先取第一个作为尾节点, 如果第一个尾节点为空则取第二个尾节点
    new_tail = tail_one if tail_two is None else tail_two
    # 第一个尾节点非空则链接上第二个头节点 (无需关心第二个头节点是否为空)
    if tail_one is not None:
        tail_one.next = head_two

    return new_head, new_tail


def initialize_linked_list(array):
    head = LinkedList(array[0])
    node = head
    for idx in range(1, len(array)):
        node.next = LinkedList(array[idx])
        node = node.next
    return head


def iter_linked_list(ll):
    while ll is not None:
        print(ll.value, end=' ')
        ll = ll.next
    print()


if __name__ == '__main__':
    source = initialize_linked_list([3, 0, 5, 2, 1, 4])
    result = rearrange_linked_list(source, 3)
    iter_linked_list(result)
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

class LinkedList
{
public:
    int value;
    LinkedList *next;

    LinkedList(int value)
    {
        this->value = value;
        next = nullptr;
    }
};

struct LinkedListPair
{
    LinkedList *head;
    LinkedList *tail;
};

LinkedListPair growLinkedList(LinkedList *head, LinkedList *tail,
                              LinkedList *node);
LinkedListPair connectLinkedLists(LinkedList *headOne, LinkedList *tailOne,
                                  LinkedList *headTwo, LinkedList *tailTwo);

// O(n) time | O(1) space - where n is the number of nodes in the Linked List
LinkedList *rearrangeLinkedList(LinkedList *head, int k)
{
    LinkedList *smallerListHead = nullptr;
    LinkedList *smallerListTail = nullptr;
    LinkedList *equalListHead = nullptr;
    LinkedList *equalListTail = nullptr;
    LinkedList *greaterListHead = nullptr;
    LinkedList *greaterListTail = nullptr;

    LinkedList *node = head;
    while (node != nullptr)
    {
        if (node->value < k)
        {
            LinkedListPair smallerList =
                growLinkedList(smallerListHead, smallerListTail, node);
            smallerListHead = smallerList.head;
            smallerListTail = smallerList.tail;
        }
        else if (node->value > k)
        {
            LinkedListPair greaterList =
                growLinkedList(greaterListHead, greaterListTail, node);
            greaterListHead = greaterList.head;
            greaterListTail = greaterList.tail;
        }
        else
        {
            LinkedListPair equalList =
                growLinkedList(equalListHead, equalListTail, node);
            equalListHead = equalList.head;
            equalListTail = equalList.tail;
        }

        LinkedList *prevNode = node;
        node = node->next;
        prevNode->next = nullptr;
    }

    LinkedListPair first = connectLinkedLists(smallerListHead, smallerListTail,
                                              equalListHead, equalListTail);
    LinkedListPair final = connectLinkedLists(first.head, first.tail,
                                              greaterListHead, greaterListTail);
    return final.head;
}

LinkedListPair growLinkedList(LinkedList *head, LinkedList *tail,
                              LinkedList *node)
{
    LinkedList *newHead = head;
    LinkedList *newTail = node;

    if (newHead == nullptr)
        newHead = node;
    if (tail != nullptr)
        tail->next = node;

    return LinkedListPair{newHead, newTail};
}

LinkedListPair connectLinkedLists(LinkedList *headOne, LinkedList *tailOne,
                                  LinkedList *headTwo, LinkedList *tailTwo)
{
    LinkedList *newHead = headOne == nullptr ? headTwo : headOne;
    LinkedList *newTail = tailTwo == nullptr ? tailOne : tailTwo;

    if (tailOne != nullptr)
        tailOne->next = headTwo;

    return LinkedListPair{newHead, newTail};
}

LinkedList *initializeLinkedList(vector<int> array)
{
    LinkedList *head = new LinkedList(array[0]);
    LinkedList *node = head;
    int idx = 1, size = array.size();
    for (; idx < size; idx++)
    {
        node->next = new LinkedList(array[idx]);
        node = node->next;
    }
    return head;
}

void iterLinkedList(LinkedList *head)
{
    while (head != nullptr)
    {
        cout << head->value << " ";
        head = head->next;
    }
    cout << endl;
}

int main()
{
    LinkedList *source = initializeLinkedList({3, 0, 5, 2, 1, 4});
    LinkedList *result = rearrangeLinkedList(source, 3);
    iterLinkedList(result);
    return 0;
}
```

## 判断链表回文

判断链表的值是否为回文，类似于回文字符串，如字符串`abcdcba`无论从后向前还是从前向后阅读都是一样的顺序

```python
class LinkedList:
    def __init__(self, value):
        self.value = value
        self.next = None


# O(n) time | O(n) space - where n is the number of nodes in the Linked List
def linked_list_palindrome1(head):
    # 通过递归的调用栈来判断回文
    is_palindrome_results = is_palindrome(head, head)
    return is_palindrome_results.outer_nodes_are_equal


def is_palindrome(left_node, right_node):
    # 递归的终点
    if right_node is None:
        # 返回开头的节点, 比较的结果默认为真
        return LinkedListInfo(True, left_node)
    # 递归调用, 一直走到末尾的节点
    recursive_call_results = is_palindrome(left_node, right_node.next)
    # 获得比较信息
    left_node_to_compare = recursive_call_results.left_node_to_compare
    outer_nodes_are_equal = recursive_call_results.outer_nodes_are_equal
    # 先检查调用栈中的比较结果, 然后比较当前的节点和开头的节点值一致性
    recursive_is_equal = outer_nodes_are_equal and left_node_to_compare.value == right_node.value
    # 移动左侧的节点到下一个, 与不断折返(递归)的右侧节点调用栈的值做比较
    next_left_node_to_compare = left_node_to_compare.next
    # 返回比较信息
    return LinkedListInfo(recursive_is_equal, next_left_node_to_compare)


class LinkedListInfo:
    def __init__(self, outer_nodes_are_equal, left_node_to_compare):
        # 记录当前比较结果
        self.outer_nodes_are_equal = outer_nodes_are_equal
        # 记录下一个需要比较的节点
        self.left_node_to_compare = left_node_to_compare


# O(n) time | O(1) space - where n is the number of nodes in the Linked List
def linked_list_palindrome2(head):
    # 创建两个指针
    slow_node = head
    fast_node = head
    # 通过不同的移动步长来找到单链表中间位置的节点
    while fast_node is not None and fast_node.next is not None:
        slow_node = slow_node.next
        fast_node = fast_node.next.next
    # 将单链表中间位置节点之后的节点反转过来
    reversed_second_half_node = reverse_linked_list(slow_node)
    first_half_node = head
    # 从当前的头节点开始, 逐个比较反转的节点和当前节点的值是否相同
    while reversed_second_half_node is not None:
        if reversed_second_half_node.value != first_half_node.value:
            return False
        reversed_second_half_node = reversed_second_half_node.next
        first_half_node = first_half_node.next

    return True


def reverse_linked_list(head):
    previous_node, current_node = None, head
    # 不停循环一直到链表末尾
    while current_node is not None:
        # 获得当前节点下一个指针, 作为缓存
        next_node = current_node.next
        # 反转当前节点的指针执行前一个节点
        current_node.next = previous_node
        # 移动前一个节点的指针到当前节点
        previous_node = current_node
        # 当前节点则移动到上面缓存下一个节点
        current_node = next_node
    # 走到末尾后当前节点已经是空的指针, 返回前一个节点指针
    return previous_node


def initialize_linked_list(array):
    head = LinkedList(array[0])
    node = head
    for idx in range(1, len(array)):
        node.next = LinkedList(array[idx])
        node = node.next
    return head


if __name__ == '__main__':
    source = [0, 1, 2, 3, 2, 1, 0]
    print(linked_list_palindrome1(initialize_linked_list(source)))
    print(linked_list_palindrome2(initialize_linked_list(source)))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

class LinkedList
{
public:
    int value;
    LinkedList *next;

    LinkedList(int value)
    {
        this->value = value;
        this->next = nullptr;
    }
};

struct LinkedListInfo
{
    bool outerNodesAreEqual;
    LinkedList *leftNodeToCompare;
};

LinkedListInfo isPalindrome(LinkedList *leftNode, LinkedList *rightNode);

// O(n) time | O(n) space - where n is the number of nodes in the Linked List
bool linkedListPalindrome1(LinkedList *head)
{
    LinkedListInfo isPalindromeResults = isPalindrome(head, head);
    return isPalindromeResults.outerNodesAreEqual;
}

LinkedListInfo isPalindrome(LinkedList *leftNode, LinkedList *rightNode)
{
    if (rightNode == nullptr)
    {
        return LinkedListInfo{true, leftNode};
    }

    LinkedListInfo recursiveCallResults = isPalindrome(leftNode, rightNode->next);
    LinkedList *leftNodeToCompare = recursiveCallResults.leftNodeToCompare;
    bool outerNodesAreEqual = recursiveCallResults.outerNodesAreEqual;

    bool recursiveIsEqual =
        outerNodesAreEqual && leftNodeToCompare->value == rightNode->value;
    LinkedList *nextLeftNodeToCompare = leftNodeToCompare->next;

    return LinkedListInfo{recursiveIsEqual, nextLeftNodeToCompare};
}

LinkedList *reverseLinkedList(LinkedList *head);

// O(n) time | O(1) space - where n is the number of nodes in the Linked List
bool linkedListPalindrome2(LinkedList *head)
{
    LinkedList *slowNode = head;
    LinkedList *fastNode = head;
    while (fastNode != nullptr && fastNode->next != nullptr)
    {
        slowNode = slowNode->next;
        fastNode = fastNode->next->next;
    }

    LinkedList *reversedSecondHalfNode = reverseLinkedList(slowNode);
    LinkedList *firstHalfNode = head;

    while (reversedSecondHalfNode != nullptr)
    {
        if (reversedSecondHalfNode->value != firstHalfNode->value)
        {
            return false;
        }
        reversedSecondHalfNode = reversedSecondHalfNode->next;
        firstHalfNode = firstHalfNode->next;
    }

    return true;
}

LinkedList *reverseLinkedList(LinkedList *head)
{
    LinkedList *previousNode = nullptr;
    LinkedList *currentNode = head;
    while (currentNode != nullptr)
    {
        LinkedList *nextNode = currentNode->next;
        currentNode->next = previousNode;
        previousNode = currentNode;
        currentNode = nextNode;
    }
    return previousNode;
}

LinkedList *initializeLinkedList(vector<int> array)
{
    LinkedList *head = new LinkedList(array[0]);
    LinkedList *node = head;
    int idx = 1, size = array.size();
    for (; idx < size; idx++)
    {
        node->next = new LinkedList(array[idx]);
        node = node->next;
    }
    return head;
}

int main()
{
    vector<int> source = {0, 1, 2, 3, 2, 1, 0};
    cout << linkedListPalindrome1(initializeLinkedList(source)) << endl;
    cout << linkedListPalindrome2(initializeLinkedList(source)) << endl;
    return 0;
}
```

## 链表求和

给出两个链表，链表里的元素反向表示一个正整数，求出两个链表表示的正整数的和并返回一个反向表示的链表（个位数从左开始）

```python
class LinkedList:
    def __init__(self, value):
        self.value = value
        self.next = None


# O(max(n, m)) time | O(max(n, m)) space
def sum_of_linked_list(linked_list_one, linked_list_two):
    new_linked_list_pointer = LinkedList(0)
    next_node = new_linked_list_pointer
    carry = 0

    node_one = linked_list_one
    node_two = linked_list_two

    while node_one is not None or node_two is not None or carry != 0:
        sum_of_values = carry

        if node_one is not None:
            sum_of_values += node_one.value
            node_one = node_one.next
        if node_two is not None:
            sum_of_values += node_two.value
            node_two = node_two.next

        new_value = sum_of_values % 10
        carry = sum_of_values // 10
        next_node.next = LinkedList(new_value)
        next_node = next_node.next

    return new_linked_list_pointer.next


def initialize_linked_list(array):
    head = LinkedList(array[0])
    node = head
    for idx in range(1, len(array)):
        node.next = LinkedList(array[idx])
        node = node.next
    return head


def print_linked_list(head):
    node = head
    while node is not None:
        print(node.value, end=' ')
        node = node.next
    print()


if __name__ == '__main__':
    list_one = initialize_linked_list([1, 1, 1, 1])
    list_two = initialize_linked_list([9, 8, 8, 8])
    # 1111 + 8889 = 10000, result is: 0 -> 0 -> 0 -> 0 -> 1
    print_linked_list(sum_of_linked_list(list_one, list_two))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

class LinkedList
{
public:
    int value;
    LinkedList *next = nullptr;

    LinkedList(int value) { this->value = value; }
};

// O(max(n, m)) time | O(max(n, m)) space - where n is the length of the
// first Linked List and m is the length of the second Linked List
LinkedList *sumOfLinkedLists(LinkedList *linkedListOne,
                             LinkedList *linkedListTwo)
{
    // This variable will store a dummy node whose .next
    // attribute will point to the head of our new LL.
    auto newLinkedListHeadPointer = new LinkedList(0);
    auto currentNode = newLinkedListHeadPointer;
    int carry = 0;

    auto nodeOne = linkedListOne;
    auto nodeTwo = linkedListTwo;
    while (nodeOne != nullptr || nodeTwo != nullptr || carry != 0)
    {
        int valueOne = nodeOne != nullptr ? nodeOne->value : 0;
        int valueTwo = nodeTwo != nullptr ? nodeTwo->value : 0;
        int sumOfValues = valueOne + valueTwo + carry;

        int newValue = sumOfValues % 10;
        auto newNode = new LinkedList(newValue);
        currentNode->next = newNode;
        currentNode = newNode;

        carry = sumOfValues / 10;
        nodeOne = nodeOne != nullptr ? nodeOne->next : nullptr;
        nodeTwo = nodeTwo != nullptr ? nodeTwo->next : nullptr;
    }

    return newLinkedListHeadPointer->next;
}

LinkedList *initializeLinkedList(const vector<int> array)
{
    LinkedList *head = new LinkedList(array[0]);
    LinkedList *node = head;
    for (size_t i = 1; i < array.size(); i++)
    {
        node->next = new LinkedList(array[i]);
        node = node->next;
    }
    return head;
}

void printLinkedList(LinkedList *head)
{
    LinkedList *node = head;
    while (node != nullptr)
    {
        cout << node->value << " ";
        node = node->next;
    }
    cout << endl;
}

int main()
{
    LinkedList *listOne = initializeLinkedList({1, 1, 1, 1});
    LinkedList *listTwo = initializeLinkedList({9, 8, 8, 8});
    printLinkedList(sumOfLinkedLists(listOne, listTwo));
    return 0;
}
```

Last Modified 2022-06-04

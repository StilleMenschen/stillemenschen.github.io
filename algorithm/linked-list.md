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

```c
#include <stdio.h>
#include <stdlib.h>
#define N 9

typedef struct Node
{
    struct Node *prev;
    struct Node *next;
    int value = -1;
} Node;

typedef struct LinkedList
{
    Node* head;
    Node* tail;
    int size;
} LinkedList;

void initLinkedList(LinkedList** list, const int n)
{
    Node *head, *tail, *node;
    head = (Node*)malloc(sizeof(Node));
    head->value = 0;
    tail = head;
    int i = 1;
    for (; i <= n; i++)
    {
        node = (Node*)malloc(sizeof(Node));
        node->value = i;
        tail->next = node;
        node->prev = tail;
        tail = node;
    }
    node = head;
    while ( node->value != 4 )
    {
        node = node->next;
    }
    tail->next = node;
    (*list)->head = head;
    (*list)->tail = tail;
    (*list)->size = 10;
}

void printLinkedLists(Node* node, const int n)
{
    while ( node != NULL )
    {
        printf("%d ", node->value);
        if ( node->value == n) {
            break;
        }
        node = node->next;
    }
    printf("\n");
}

/* O(n) time | O(1) space */
Node* findLoop(Node* head)
{
    Node *first = head->next;
    Node *second = head->next->next;
    while ( first != second )
    {
        first = first->next;
        second = second->next->next;
    }
    first = head;
    while ( first != second )
    {
        first = first->next;
        second = second->next;
    }
    return first;
}

int main()
{
    LinkedList* list = (LinkedList*)malloc(sizeof(LinkedList));
    initLinkedList(&list, N);
    printLinkedLists(list->head, N);
    Node *node = findLoop(list->head);
    printf("loop value is %d\n", node->value);
    return 0;
}
```

## 反转链表

```c
#include <stdio.h>
#include <stdlib.h>
#define N 9

typedef struct Node
{
    struct Node *prev;
    struct Node *next;
    int value = -1;
} Node;

typedef struct LinkedList
{
    Node* head;
    Node* tail;
    int size;
} LinkedList;

void initLinkedList(LinkedList** list, const int n)
{
    Node *head, *tail, *node;
    head = (Node*)malloc(sizeof(Node));
    head->value = 0;
    tail = head;
    int i = 1;
    for (; i <= n; i++)
    {
        node = (Node*)malloc(sizeof(Node));
        node->value = i;
        tail->next = node;
        node->prev = tail;
        tail = node;
    }
    tail->next = NULL;
    (*list)->head = head;
    (*list)->tail = tail;
    (*list)->size = i;
}

void printLinkedLists(Node* node)
{
    while ( node != NULL )
    {
        printf("%d ", node->value);
        node = node->next;
    }
    printf("\n");
}

/* O(n) time | O(1) space */
Node* reverseLinkedList(Node* head)
{
    if ( head == NULL )
    {
        return head;
    }
    Node *p1, *p2, *p3;
    p1 = head;
    p2 = p1->next;
    head->next = NULL;
    while ( p2 != NULL )
    {
        p3 = p2->next;
        p2->next = p1;
        p1 = p2;
        p2 = p3;
    }
    return p1;
}

int main()
{
    LinkedList* list = (LinkedList*)malloc(sizeof(LinkedList));
    initLinkedList(&list, N);
    printLinkedLists(list->head);
    Node *node = reverseLinkedList(list->head);
    printLinkedLists(node);
    return 0;
}
```

## 合并链表

```c
#include <stdio.h>
#include <stdlib.h>
#define GET_ARRAY_LEN(array, len) { len = sizeof(array)/sizeof(array[0]); }

typedef struct Node
{
    struct Node *prev;
    struct Node *next;
    int value = -1;
} Node;

typedef struct LinkedList
{
    Node* head;
    Node* tail;
    int size;
} LinkedList;

void initLinkedList(LinkedList** list, const int array[], const int n)
{
    Node *head, *tail, *node;
    head = (Node*)malloc(sizeof(Node));
    head->value = array[0];
    tail = head;
    int i = 0;
    while ( ++i < n )
    {
        node = (Node*)malloc(sizeof(Node));
        node->value = array[i];
        tail->next = node;
        tail = node;
    }
    tail->next = NULL;
    (*list)->head = head;
    (*list)->tail = tail;
    (*list)->size = i;
}

void printLinkedLists(Node* node)
{
    while ( node != NULL )
    {
        printf("%d ", node->value);
        node = node->next;
    }
    printf("\n");
}

/* O(n+m) time | O(1) space */
Node* mergeLinkedList(Node* head1, Node* head2)
{
    Node *p1, *p2, *prev;
    p1 = head1;
    p2 = head2;
    while ( p1 != NULL && p2 != NULL )
    {
        if ( p1->value <= p2->value )
        {
            prev = p1;
            p1 = p1->next;
        }
        else
        {
            if ( prev != NULL ) prev->next = p2;
            prev = p2;
            p2 = p2->next;
            prev->next = p1;
        }
    }
    if ( p1 == NULL ) prev->next = p2;
    return head1->value <= head2->value ? head1 : head2;
}

int main()
{
    const int arr1[] = {2, 6, 7, 8};
    const int arr2[] = {1, 3, 4, 5, 9, 10};
    unsigned int len1, len2;
    GET_ARRAY_LEN(arr1, len1);
    GET_ARRAY_LEN(arr2, len2);
    LinkedList* list1 = (LinkedList*)malloc(sizeof(LinkedList));
    LinkedList* list2 = (LinkedList*)malloc(sizeof(LinkedList));
    initLinkedList(&list1, arr1, len1);
    initLinkedList(&list2, arr2, len2);
    printLinkedLists(list1->head);
    printLinkedLists(list2->head);
    Node *node = mergeLinkedList(list1->head, list2->head);
    printLinkedLists(node);
    return 0;
}
```

## 最近最少使用 (LRU)

```cpp
#include <iostream>
#include <list>
#include <map>

using namespace std;

typedef struct Node
{
    char key;
    int value;
} Node;

class LeastRecentlyUsed
{
private:
    int MAX_SIZE = -1;
    int currentSize = 0;
    map<char, Node> cache;
    list<Node> listOfMostRecent;
    bool containesKeyInCache(char key)
    {
        const auto search = this->cache.find(key);
        return search != this->cache.end();
    }
public:
    LeastRecentlyUsed(int maxSize)
    {
        if ( maxSize < 0 )
            this->MAX_SIZE = 1;
        else
            this->MAX_SIZE = maxSize;
    }

    // O(1) time | O(1) space
    void insertKeyValuePair(char key, int value)
    {

        if (this->containesKeyInCache(key))
        {
            this->replaceKey(key,value);
        }
        else
        {
            if ( this->currentSize >= this->MAX_SIZE )
                this->evictLeastRecent();
            else
                this->currentSize++;
            Node node = { key, value };
            this->cache[key] = node;
        }
        this->updateMostRecent(this->cache[key]);
    }

    // O(1) time | O(1) space
    int getValueFromKey(char key)
    {
        if ( ! this->containesKeyInCache(key) )
            return -1;
        this->updateMostRecent(this->cache[key]);
        const int value = this->cache[key].value;
        return value;
    }

    // O(1) time | O(1) space
    char getMostRecentKey(void)
    {
        const Node node = this->listOfMostRecent.front();
        return node.key;
    }
    void replaceKey(char key, int value)
    {
        if ( ! this->containesKeyInCache(key) )
            throw "The provide key isn't in the cache!";
        this->cache[key].value = value;
    }
    void evictLeastRecent(void)
    {
        const Node nodeToRemove = this->listOfMostRecent.back();
        this->listOfMostRecent.pop_back();
        this->cache.erase(nodeToRemove.key);
    }
    void updateMostRecent(Node node)
    {
        this->listOfMostRecent.push_front(node);
    }
};

int main(void)
{
    LeastRecentlyUsed lru(3);
    lru.insertKeyValuePair('a', 1);
    lru.insertKeyValuePair('b', 2);
    lru.insertKeyValuePair('c', 3);
    lru.insertKeyValuePair('d', 4);
    cout << "Add multiple data { { a: 1 }, { b: 2 }, { c: 3 }, { d: 4 } }" << endl;
    cout << "The key 'c' value is " << lru.getValueFromKey('c') << endl;
    cout << "The key 'c' value is " << lru.getValueFromKey('c') << endl;
    cout << "Most recently key is " << lru.getMostRecentKey() << endl;
    cout << "Update { d: 40 }" << endl;
    lru.insertKeyValuePair('d', 40);
    cout << "Update { d: 41 }" << endl;
    lru.insertKeyValuePair('d', 41);
    cout << "Most recently key is " << lru.getMostRecentKey() << endl;
    cout << "The key 'd' value is " << lru.getValueFromKey('d') << endl;
    return 0;
}
```

## 提取末尾元素到头节点

根据给定数量提取末尾倒数第 N 个节点到头节点

```c
#include <stdio.h>
#include <stdlib.h>
#define N 9

typedef struct Node
{
    struct Node *prev;
    struct Node *next;
    int value = -1;
} Node;

typedef struct LinkedList
{
    Node* head;
    Node* tail;
    int size;
} LinkedList;

void initLinkedList(LinkedList** list, const int n)
{
    Node *head, *tail, *node;
    head = (Node*)malloc(sizeof(Node));
    head->value = 0;
    tail = head;
    int i = 1;
    for (; i <= n; i++)
    {
        node = (Node*)malloc(sizeof(Node));
        node->value = i;
        tail->next = node;
        node->prev = tail;
        tail = node;
    }
    tail->next = NULL;
    (*list)->head = head;
    (*list)->tail = tail;
    (*list)->size = i;
}

void printLinkedLists(Node* node)
{
    while ( node != NULL )
    {
        printf("%d ", node->value);
        node = node->next;
    }
    printf("\n");
}

int abs(const int num)
{
    return ((num >> 31) ^ num) - (num >> 31);
}

/* O(n) time | O(1) space */
Node* shiftLinkedList(Node* head, const int k)
{
    int listLength = 0;
    Node *listTail = head;
    while ( listTail->next != NULL )
    {
        listTail = listTail->next;
        listLength++;
    }
    const int offset = abs(k) % listLength;
    if ( offset == 0 ) return head;

    const int newTailPosition = k > 0 ? listLength - offset : offset;
    Node *newTail = head;
    int i = 0;
    while ( i++ < newTailPosition )
    {
        newTail = newTail->next;
    }
    Node *newHead = newTail->next;
    newTail->next = NULL;
    listTail->next = head;
    return newHead;
}

int main()
{
    LinkedList* list = (LinkedList*)malloc(sizeof(LinkedList));
    initLinkedList(&list, N);
    printLinkedLists(list->head);
    Node *node = shiftLinkedList(list->head, 2);
    printLinkedLists(node);
    return 0;
}
```

## 重排链表

给定链表中的某个元素值，根据元素值重新排布链表，小于该值的节点在前，等于该值的节点在中间，大于该值的节点在后

```c
#include <stdio.h>
#include <stdlib.h>
#define GET_ARRAY_LEN(array, len) { len = sizeof(array)/sizeof(array[0]); }

typedef struct Node
{
    struct Node *prev, *next;
    int value;
} Node;

typedef struct LinkedList
{
    Node *head, *tail;
    int size;
} LinkedList, *pLinkedList;

void initLinkedList(pLinkedList* list, const int array[], const int n)
{
    Node *head, *tail, *node;
    head = (Node*)malloc(sizeof(Node));
    head->value = array[0];
    tail = head;
    int i = 0;
    while ( ++i < n )
    {
        node = (Node*)malloc(sizeof(Node));
        node->value = array[i];
        tail->next = node;
        tail = node;
    }
    tail->next = NULL;
    (*list)->head = head;
    (*list)->tail = tail;
    (*list)->size = i;
}

void printLinkedLists(Node* node)
{
    while ( node != NULL )
    {
        printf("%d ", node->value);
        node = node->next;
    }
    printf("\n");
}


LinkedList* growLinkedList(Node* head, Node* tail, Node* node)
{
    LinkedList* list = (LinkedList*)malloc(sizeof(LinkedList));
    Node *newHead = head;
    Node *newTail = node;

    if ( newHead == NULL )
        newHead = node;
    if ( tail != NULL )
        tail->next = node;

    list->head = newHead;
    list->tail = newTail;

    return list;
}

LinkedList* connectLinkedLists(Node* head1, Node* tail1, Node* head2, Node* tail2)
{
    LinkedList* list = (LinkedList*)malloc(sizeof(LinkedList));
    list->head = head1 == NULL ? head2 : head1;
    list->tail = tail2 == NULL ? tail1 : tail2;

    if ( tail1 != NULL )
        tail1->next = head2;

    return list;
}

/* O(n) time | O(1) space */
Node* rearrangeLinkedList(Node* head, const int k)
{
    LinkedList *smallerList = (LinkedList*)malloc(sizeof(LinkedList));
    smallerList->head = NULL;
    smallerList->tail = NULL;
    LinkedList *equalList = (LinkedList*)malloc(sizeof(LinkedList));
    equalList->head = NULL;
    equalList->tail = NULL;
    LinkedList *greaterList = (LinkedList*)malloc(sizeof(LinkedList));
    greaterList->head = NULL;
    greaterList->tail = NULL;

    Node *node = head;

    while ( node != NULL )
    {
        if ( node->value < k )
        {
            smallerList = growLinkedList(smallerList->head, smallerList->tail, node);
        }
        else if ( node->value > k )
        {
            greaterList = growLinkedList(greaterList->head, greaterList->tail, node);
        }
        else
        {
            equalList = growLinkedList(equalList->head, equalList->tail, node);
        }
        Node *prevNode = node;
        node = node->next;
        prevNode->next = NULL;
    }

    LinkedList *firstList = connectLinkedLists(smallerList->head, smallerList->tail, equalList->head, equalList->tail);
    LinkedList *finalList = connectLinkedLists(firstList->head, firstList->tail, greaterList->head, greaterList->tail);
    return finalList->head;
}

int main()
{
    const int arr[] = {3, 0, 5, 2, 1, 4};
    unsigned int len;
    GET_ARRAY_LEN(arr, len);
    LinkedList* list = (LinkedList*)malloc(sizeof(LinkedList));
    initLinkedList(&list, arr, len);
    printLinkedLists(list->head);
    Node *node = rearrangeLinkedList(list->head, 3);
    printLinkedLists(node);
    return 0;
}
```

## 链表回文

```c
#include <stdio.h>
#include <stdlib.h>
#define GET_ARRAY_LEN(array, len) { len = sizeof(array)/sizeof(array[0]); }

typedef struct Node
{
    struct Node *prev, *next;
    int value;
} Node, *pNode;

typedef struct LinkedList
{
    Node *head, *tail;
    int size;
} LinkedList, *pLinkedList;

typedef struct LinkedListInfo
{
    int outerNodesAreEqual;
    Node *leftNodeToCompare;
} LinkedListInfo, *pLinkedListInfo;

void initLinkedList(pLinkedList* list, const int array[], const int n)
{
    pNode head, tail, node;
    head = (Node*)malloc(sizeof(Node));
    head->value = array[0];
    tail = head;
    int i = 0;
    while ( ++i < n )
    {
        node = (Node*)malloc(sizeof(Node));
        node->value = array[i];
        tail->next = node;
        node->prev = tail;
        tail = node;
    }
    tail->next = NULL;
    (*list)->head = head;
    (*list)->tail = tail;
    (*list)->size = i;
}

void printLinkedLists(pNode node)
{
    while ( node != NULL )
    {
        printf("%d ", node->value);
        node = node->next;
    }
    printf("\n");
}

pLinkedListInfo isPalindrome(pNode leftNode, pNode rightNode)
{
    pLinkedListInfo info;
    if ( rightNode == NULL )
    {
        info = (LinkedListInfo*)malloc(sizeof(LinkedListInfo));
        info->outerNodesAreEqual = 1;
        info->leftNodeToCompare = leftNode;
        return info;
    }
    info = isPalindrome(leftNode, rightNode->next);
    pNode leftNodeToCompare = info->leftNodeToCompare;
    int outerNodesAreEqual = info->outerNodesAreEqual;

    int recursiveIsEqual = outerNodesAreEqual && leftNodeToCompare->value == rightNode->value;
    pNode nextMatchingLeftNode = leftNodeToCompare->next;

    info->outerNodesAreEqual = recursiveIsEqual;
    info->leftNodeToCompare = nextMatchingLeftNode;
    return info;
}

pNode reverseLinkedList(pNode head)
{
    pNode previousNode, currentNode, nextNode;
    previousNode = NULL;
    currentNode = head;
    while ( currentNode != NULL )
    {
        nextNode = currentNode->next;
        currentNode->next = previousNode;
        previousNode = currentNode;
        currentNode = nextNode;
    }
    return previousNode;
}

/* O(n) time | O(n) space */
int linkedListPalindrome1(pNode head)
{
    pLinkedListInfo isPalindromeResult = isPalindrome(head, head);
    return isPalindromeResult->outerNodesAreEqual;
}

/* O(n) time | O(1) space */
int linkedListPalindrome2(pNode head)
{
    pNode slowNode, fastNode;
    slowNode = fastNode = head;
    while ( fastNode != NULL && fastNode->next != NULL )
    {
        slowNode = slowNode->next;
        fastNode = fastNode->next->next;
    }

    pNode reverseSecondHalfNode = reverseLinkedList(slowNode);
    pNode firstHalfNode = head;

    while ( reverseSecondHalfNode != NULL )
    {
        if ( reverseSecondHalfNode->value != firstHalfNode->value )
            return 0;
        reverseSecondHalfNode = reverseSecondHalfNode->next;
        firstHalfNode = firstHalfNode->next;
    }

    return 1;
}

int main()
{
    const int arr[] = {1, 3, 5, 5, 3, 1};
    unsigned int len, isPalindrome;
    GET_ARRAY_LEN(arr, len);
    pLinkedList list = (LinkedList*)malloc(sizeof(LinkedList));
    initLinkedList(&list, arr, len);
    printLinkedLists(list->head);
    isPalindrome = linkedListPalindrome1(list->head);
    printf("isPalindrome One = %d\n", isPalindrome);
    isPalindrome = linkedListPalindrome2(list->head);
    printf("isPalindrome Two = %d\n", isPalindrome);
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

Last Modified 2022-01-05

# 链表

## 基本实现

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node
{
    struct Node *prev;
    struct Node *next;
    int value = -1;
} Node;

Node *head = NULL;
Node *tail = NULL;
int listSize = 0;

void setHead(Node* node);
void removeNodeBindings(Node* node);
void remove(Node* node);
void insertBefore(Node* node, Node* nodeToInsert);
void insertAfter(Node* node, Node* nodeToInsert);

/* O(1) time | O(1) space */
void setHead(Node* node)
{
    if ( head == NULL )
    {
        head = node;
        tail = node;
        listSize++;
        return;
    }
    insertBefore(head, node);
}
/* O(1) time | O(1) space */
void setTail(Node* node)
{
    if ( tail == NULL )
    {
        setHead(node);
        return;
    }
    insertAfter(tail, node);
}
/* O(1) time | O(1) space */
void insertBefore(Node* node, Node* nodeToInsert)
{
    if ( nodeToInsert == head && nodeToInsert == tail )
    {
        return;
    }
    remove(nodeToInsert);
    nodeToInsert->prev = node->prev;
    nodeToInsert->next = node;
    if ( node->prev == NULL )
    {
        head = nodeToInsert;
    }
    else
    {
        node->prev->next = nodeToInsert;
    }
    node->prev = nodeToInsert;
    listSize++;
}
/* O(1) time | O(1) space */
void insertAfter(Node* node, Node* nodeToInsert)
{
    if ( nodeToInsert == head && nodeToInsert == tail )
    {
        return;
    }
    remove(nodeToInsert);
    nodeToInsert->prev = node;
    nodeToInsert->next = node->next;
    if ( node->next == NULL )
    {
        tail = nodeToInsert;
    }
    else
    {
        node->next->prev = nodeToInsert;
    }
    node->next = nodeToInsert;
    listSize++;
}
/*
   O(p) time | O(1) space
   p is position
*/
void insertAtPosition(int position, Node* nodeToInsert)
{
    if ( position == 0)
    {
        setHead(nodeToInsert);
        return;
    }
    Node *node = head;
    int currentPosition = 0;
    while ( node != NULL && currentPosition != position )
    {
        node = node->next;
        currentPosition++;
    }
    if ( node != NULL )
    {
        insertBefore(node, nodeToInsert);
    }
    else
    {
        setTail(nodeToInsert);
    }
}
/* O(n) time | O(1) space */
void removeNodesWithValue(int value)
{
    Node *node = head;
    Node *nodeToRemove = NULL;
    while ( node != NULL )
    {
        nodeToRemove = node;
        node = node->next;
        if ( nodeToRemove->value == value )
        {
            remove(nodeToRemove);
            listSize--;
        }
    }
}
/* O(1) time | O(1) space */
void remove(Node* node)
{
    if ( node == head )
    {
        head = head->next;
    }
    if ( node == tail )
    {
        tail = tail->prev;
    }
    removeNodeBindings(node);
}
/* O(n) time | O(1) space */
int containsNodeWithValue(int value)
{
    Node *node = head;
    while( node != NULL && node->value != value )
    {
        node = node->next;
    }
    return node != NULL;
}
void removeNodeBindings(Node* node)
{
    if ( node->prev != NULL )
    {
        node->prev->next = node->next;
    }
    node->prev = NULL;
    if ( node->next != NULL )
    {
        node->next->prev = node->prev;
    }
    node->next = NULL;
}

void initLinkedList()
{
    Node *a = NULL;
    int i = 0;
    for (; i<10; i++)
    {
        a = (Node*)malloc(sizeof(Node));
        a->prev = NULL;
        a->next = NULL;
        a->value = i;
        insertAtPosition(i, a);
    }
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

void clearList()
{
    const int end = listSize / 2;
    int i = 0;
    Node *node = head;
    for(; i < end; i++)
    {
        node = NULL;
        head = head->next;
        node = tail;
        tail = tail->prev;
    }
    listSize = 0;
}

int main()
{
    initLinkedList();
    printLinkedLists(head);
    if ( containsNodeWithValue(3) == 1 )
    {
        printf("containsNodeWithValue(3) is true \n");
    }
    if ( containsNodeWithValue(13) == 0 )
    {
        printf("containsNodeWithValue(13) is false \n");
    }
    printf("removeNodesWithValue(3)\n");
    removeNodesWithValue(3);
    printf("removeNodesWithValue(9)\n");
    removeNodesWithValue(9);
    printLinkedLists(head);
    printf("list size is %d \n", listSize);
    clearList();
    return 0;
}
```

## 从末尾删除节点

```c
#include <stdio.h>
#include <stdlib.h>

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

/* O(n) time | O(1) space */
void removeNthNodeFromEnd(Node* head, int n)
{
    int counter = 0;
    Node *first = head;
    Node *second = head;
    while ( counter < n )
    {
        second = second->next;
        counter++;
    }
    if ( second == NULL )
    {
        head->value = head->next->value;
        head->next = head->next->next;
        return;
    }
    while ( second->next != NULL )
    {
        second = second->next;
        first = first->next;
    }
    first->next = first->next->next;
}

void initLinkedList(LinkedList** list)
{
    Node *head, *tail, *node;
    head = (Node*)malloc(sizeof(Node));
    head->value = 0;
    tail = head;
    int i = 1;
    for (; i<10; i++)
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
    (*list)->size = 10;
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

int main()
{
    LinkedList* list = (LinkedList*)malloc(sizeof(LinkedList));
    initLinkedList(&list);
    printLinkedLists(list->head);
    removeNthNodeFromEnd(list->head, 4);
    printLinkedLists(list->head);
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

## 反转单链表

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

## 合并单链表

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

Last Modified 2021-07-01

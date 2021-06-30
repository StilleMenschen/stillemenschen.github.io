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

Last Modified 2021-07-01

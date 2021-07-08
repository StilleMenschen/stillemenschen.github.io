# 栈

## 最小最大栈

```cpp
#include <iostream>

using namespace std;

typedef struct Node
{
    struct Node *next;
    int value;
} Node, *pNode;

typedef struct MinMaxStack
{
    struct MinMaxStack *next;
    int min, max;
} MinMaxStack, *pMinMaxStack;

class Stack
{
private:
    pNode stack = NULL;
    pMinMaxStack minMaxStack = NULL;
public:
    /* O(1) time | O(1) space */
    int peek()
    {
        if ( this->stack != NULL ) return this->stack->value;
        return -1;
    }
    /* O(1) time | O(1) space */
    int pop()
    {
        const pNode first = this->stack;
        if ( this->stack->next != NULL )
            this->stack = this->stack->next;
        else
            this->stack = NULL;
        if ( this->minMaxStack != NULL )
        {
            if ( this->minMaxStack->next != NULL )
                this->minMaxStack = this->minMaxStack->next;
            else
                this->minMaxStack = NULL;
        }
        if ( first != NULL ) return first->value;
        return -1;
    }
    /* O(1) time | O(1) space */
    void push(const int number)
    {
        pMinMaxStack newMinMax = new MinMaxStack;
        newMinMax->min = number;
        newMinMax->max = number;
        newMinMax->next = NULL;
        if ( this->minMaxStack != NULL )
        {
            pMinMaxStack last = this->minMaxStack;
            newMinMax->min = last->min < number ? last->min : number;
            newMinMax->max = last->max > number ? last->max : number;
            newMinMax->next = this->minMaxStack;
        }
        this->minMaxStack = newMinMax;
        pNode top = new Node;
        top->value = number;
        top->next = NULL;
        if ( this->stack != NULL )
            top->next = this->stack;
        this->stack = top;
    }
    /* O(1) time | O(1) space */
    int getMin()
    {
        if ( this->minMaxStack != NULL ) return this->minMaxStack->min;
        return -1;
    }
    /* O(1) time | O(1) space */
    int getMax()
    {
        if ( this->minMaxStack != NULL ) return this->minMaxStack->max;
        return -1;
    }
};

int main()
{
    Stack stk;
    stk.push(2);
    stk.push(7);
    stk.push(5);
    cout << "push 2, 7, 5 to stack" << endl;
    cout << "peek stack " << stk.peek() << endl;
    cout << "min in stack is " << stk.getMin() << " ,max in stack is " << stk.getMax() << endl;
    stk.pop();
    stk.pop();
    stk.pop();
    cout << "pop stack 3 times" << endl;
    cout << "peek stack " << stk.peek() << endl;
    return 0;
}
```

## 平衡括号

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node
{
    struct Node *next;
    char value;
} Node, *pNode;

/* O(n) time | O(n) space */
int balancedBrackets(char* brackets)
{
    pNode head, node;
    head = node = NULL;
    while  ( *brackets != '\0' )
    {
        if ( *brackets == '[' || *brackets == '(' || *brackets == '{' || *brackets == '<' )
        {
            if ( head == NULL )
            {
                head = (Node*)malloc(sizeof(Node));
                head->value = *brackets;
                head->next = NULL;
            }
            else
            {
                node = (Node*)malloc(sizeof(Node));
                node->value = *brackets;
                node->next = head;
                head = node;
            }
        }
        else if ( *brackets == ']' || *brackets == ')' || *brackets == '}' || *brackets == '>' )
        {
            if ( head == NULL ) return 0;
            else
            {
                switch (*brackets)
                {
                case ']':
                    if ( head->value != '[') return 0;
                    break;
                case ')':
                    if ( head->value != '(') return 0;
                    break;
                case '}':
                    if ( head->value != '{') return 0;
                    break;
                case '>':
                    if ( head->value != '<') return 0;
                    break;
                }
                head = head->next;
            }
        }
        else
        {
            return 0;
        }
        *brackets++;
    }
    if ( head != NULL ) return 0;
    return 1;
}

int main()
{
    char brackets[] = "[[<>(())]]{}{()}";
    printf("%s is %d", brackets, balancedBrackets(brackets));
    return 0;
}
```

## 短路径生成

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct Node
{
    struct Node *prev, *next;
    char *value;
} Node, *pNode;

typedef struct LinkedList
{
    pNode head, tail;
    int size;
} LinkedList, *pLinkedList;

char* join(char* a, char* b)
{
    char *c = (char*)malloc(strlen(a) + strlen(b) + 1);
    char *head = c;
    while( *a != '\0') *c++ = *a++;
    while( (*c++ = *b++) != '\0') ;
    return head;
}

/* O(n) time | O(n) space */
LinkedList* shortenPath(char brackets[])
{
    const unsigned int startsWithSlash = brackets[0] == '/' ? 1 : 0;
    const char s[2] = "/";
    char *token;
    pNode node;
    pLinkedList lists = (LinkedList*)malloc(sizeof(LinkedList));
    lists->head = (Node*)malloc(sizeof(Node));
    lists->head->prev = NULL;
    lists->size = 0;

    /* 获取第一个子字符串 */
    token = strtok(brackets, s);
    while ( token != NULL && ( strlen(token) <=0 || strcmp(token, ".") == 0 ) )
        token = strtok(NULL, s);
    lists->head->value = token;
    lists->size++;
    lists->tail = lists->head;


    /* 继续获取其他的子字符串 */
    while( token != NULL )
    {
        token = strtok(NULL, s);
        if ( token == NULL || strlen(token) <= 0 || strcmp(token, ".") == 0 ) continue;
        if ( strcmp(token, "..") == 0 && lists->size > 0 )
        {
            lists->tail = lists->tail->prev;
            lists->size--;
            continue;
        }
        node = (Node*)malloc(sizeof(Node));
        node->value = join(token, (char*)"/");
        if ( lists->size == 0 )
        {
            lists->head = node;
            lists->head->prev = NULL;
            lists->tail = node;
            lists->tail->next = NULL;
            lists->size++;
            continue;
        }
        node->prev = lists->tail;
        lists->tail->next = node;
        lists->tail = node;
        lists->size++;
    }
    lists->tail->next = NULL;
    if ( startsWithSlash )
    {
        node = (Node*)malloc(sizeof(Node));
        node->value = (char*)"/";
        node->prev = NULL;
        node->next = lists->head;
        lists->head = node;
    }
    return lists;
}

void printLinkedLists(Node* node)
{
    while ( node != NULL )
    {
        printf("%s", node->value);
        node = node->next;
    }
    printf("\n");
}

int main()
{
    char brackets[] = "/foo/../test/../test/../foo//bar/./baz";
    pLinkedList lists = shortenPath(brackets);
    printLinkedLists(lists->head);
    return 0;
}
```

Last Modified 2021-07-09

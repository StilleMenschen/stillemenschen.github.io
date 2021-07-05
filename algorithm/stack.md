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
        pMinMaxStack newMinMax = (MinMaxStack*)malloc(sizeof(MinMaxStack));
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
        pNode top = (Node*)malloc(sizeof(Node));
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

Last Modified 2021-07-06

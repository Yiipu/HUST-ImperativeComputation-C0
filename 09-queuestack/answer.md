## 1. 数组栈的溢出问题

只需要修改数组栈的定义，增加容量属性即可。容量应等于数组的长度，如下：

```cpp
struct stack_header {
    elem[] data;
    int top;
    int bottom;
    int capacity; // capacity == \length(elem)
};
```

push 方法也应该做出修改：

```cpp
void push(stack S, elem e)
//@requires is_stack(S);
//@ensures is_stack(S);
{
    assert(S->top < S->capacity - 1); // to avoid overflow
    S->top++;
    S->data[S->top] = e;
}
```

## 2. 循环利用队列空间

使用模运算将线性空间转化为环形即可：

```cpp
elem deq(queue Q)
//@requires is_queue(Q);
//@requires !queue_empty(Q);
//@ensures is_queue(Q);
{
    elem e = Q->data[Q->front];
    Q->front = (Q->front + 1) % Q->capacity;
    return e;
}

void enq(queue Q, elem e)
//@requires is_queue(Q);
//@ensures is_queue(Q);
{
    int newBack = (Q->back + 1) % Q->capacity;
    assert(newBack != Q->front);
    Q->data[Q->back] = e;
    Q->back = newBack;
}
```

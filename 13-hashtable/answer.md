# Written Homework - 6

## 1. Amortized Analysis - 平摊分析

1. 在最坏情况下，计数器加一所花费的 token 数目是
   $$1 + 2 + 2^2 + \cdots + 2^{k-1}$$
   对这个等比数列求和，得到 $2^k - 1$ ,而 $2^k - 1 \subset O(2^k)$ ，又 $n = 2^k$，所以最坏情况下花费 tocken 数目是
   $$O(n)$$
2. ..
   1. 计数器每加 1 ，第 0 位翻转一次，因此答案是 $n$
   2. 计数器每加 2，第 1 位翻转一次，因此答案是 $\lfloor \frac{n}{2} \rfloor$
3. ..
   1. 计数器加 n ，所有位次翻转的次数是
        $$\sum_{i=0}^k\lfloor \frac{n}{2^i} \rfloor$$
        根据向下取整的定义，结合等比数列求和公式，有
        $$
        \sum_{i=0}^k\lfloor \frac{n}{2^i} \rfloor \leq \sum_{i=0}^k \frac{n}{2^i} = 2n - \frac{n}{2^k}
        $$
        又
        $$
        \begin{aligned}
        &\because 2n - \frac{n}{2^k} \subset O(n)
        \\ &\therefore \sum_{i=0}^k\lfloor \frac{n}{2^i} \rfloor \subset O(n)
        \end{aligned}
        $$
        即计数器加 n ，所有位次翻转的次数为 $O(n)$
   2. 计数器加 1 ，所有位次翻转的次数是
        $$\frac{O(n)}{n} = O(1)$$

## 2. A New Implementation of Queues - 用栈实现队列

1. 实现

   ```cpp
   bool queue_empty(queue Q)
   {
    return stack_empty(Q->instack) && stack_empty(Q->outstack)
   }

   void enqueue(elem e, queue Q)
   {
    push(e, Q->instack)
   }

   elem dequeue(queue Q)
   //@requires !queue_empty(Q);
   {
    if(stack_empty(Q->outstack))
    {
        while(!stack_empty(Q->instack))
        {
            push(pop(Q->instack), Q->outstack);
        }
    }
    return pop(Q->outstack);
   }
   ```

2. 最坏情况时间复杂度
   1. enque: $O(1)$
   2. deque: $O(q)$
3. 平摊时间复杂度
   1. enque: $1$
   2. deque: $3$，证明如下

        每次出队操作取出的元素，都经历过 `push(pop(Q->instack), Q->outstack)` 的操作，需要 2 个 token 。然后，经历 `pop(Q->outstack)` ，需要 1 个 token。

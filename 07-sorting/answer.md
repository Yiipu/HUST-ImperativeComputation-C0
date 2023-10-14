## Recitation 7 Big-O

### Checkpoint 0

$O(1) \quad O(4)$

$O(log(log(n)))$

$O(log(n))$

$O(log^2(n))$

$O(n) \quad O(2n) \quad O(4n + 3)$

$O(n \cdot log(n))$

$O(n^2) \quad O(n^2 + 20000n + 3)$

$O(2^n)$t 1

### Checkpoint 1

要证
$$n^3 + 300 \cdot n^2 \subseteq O(n^3)$$
即证存在正常数 $c$ 和 $n_0$ 使得对于所有的 $n \geq n_0$ ，有
$$f(n) \leq c \cdot g(n)$$
其中$f(n) = n^3 + 300 \cdot n^2$, $g(n) = n^3$

不等式化简为 $(c - 1)n - 300 \geq 0$

取 $c = 301$, $n_0 = 1$,证毕。

### Checkpoint 2

假设 $f(n) \in O(g(n))$ 成立，下面证明

$$k \cdot f(n) \in O(g(n)) \qquad (k > 0)$$

即证存在正常数 $c$ 和 $n_0$ 使得对于所有的 $n \geq n_0$ ，有

$$k \cdot f(n) \leq c \cdot g(n)$$

$\because f(n) \in O(g(n))$

$\therefore$ 存在正常数 $c'$ 和 $n_1$ 使得对于所有的 $n \geq n_1$ ，有 $f(n) \leq c' \cdot g(n)$

取 $n_0 > n_1$ 和 $c=c'k$ ，证毕
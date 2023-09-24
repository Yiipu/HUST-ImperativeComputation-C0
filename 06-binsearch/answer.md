## 1. 使区间的上下界都包含在内

```cpp
// search.c0 > int search(int x, int[] A, int n);

int search(int x, int[] A, int n)
//@requires n>=0 && n<=\length(A);
//@requires is_sorted(A, 0, n);
/*@ensures (\result==-1 && !is_in(x, A, 0, n))
      || (\result>=0 && \result<n && A[\result]==x);
@*/
{
  int lower = 0;
  int upper = n - 1;
  while (lower <= upper)
    //@loop_invariant lower >=0 && lower <= upper + 1 && upper < n;
    //@loop_invariant lower == 0 || A[low - 1] < x;
    //@loop_invariant upper == n - 1 || A[upper + 1] > x;
    {
      int mid = lower + (upper - lower) / 2;
      //@assert lower <= mid && mid <= upper;
      if (A[mid] == x) return mid;
      else if (A[mid] < x) lower = mid + 1;
      else  //@assert (A[mid] > x);
        upper = mid - 1;
    }
  //@assert lower == upper;
  return -1;
}


```

[循环不变量的证明](./二分查找算法中循环不变量的证明.docx) 在 `docx` 文档内

## 2. 使用 is_in(x, A, lower, upper) 重写循环不变量

```cpp
// search02.c0 > int search(int x, int[] A, int n);

int search(int x, int[] A, int n)
//@requires n>=0 n<=\length(A);
//@requires is_sorted(A, 0, n);
/*@ensures (\result==-1 && !is_in(x, A, 0, n))
      || (\result>=0 && \result<n && A[\result]==x);
@*/
{
  int lower = 0;
  int upper = n;
  while (lower < upper)
    //@loop_invariant lower >=0 && lower<=upper && upper<=n;
    //@loop_invariant is_in(x, A, lower, upper) || !is_in(x, A, 0, n);
    {
      int mid = lower + (upper - lower) / 2;
      //@assert lower<=mid && mid<=upper;
      if (A[mid] == x) return mid;
      else (A[mid] < x) lower = mid + 1;
      else  //@assert (A[mid] > x);
        upper = mid;
    }
  return -1;
}
```

**证明：**

1. **初始化：** 在循环开始之前，`lower = 0`，`upper = n`，所以初始时 `is_in(x, A, lower, upper) || !is_in(x, A, 0, n)` 等价于 `P || !P` 显然成立。

2. **保持：** 假设循环不变量 `is_in(x, A, lower, upper) || !is_in(x, A, 0, n)` 在某次迭代之前成立，下面讨论进入循环体的情况：

   - 如果 `A[mid] == x`，则我们已经找到了元素 `x`，函数返回 `mid`，不会再进入下一次迭代。

   - 如果 `A[mid] < x`，更新 `lower' = mid + 1`。结合前置条件 `//@requires is_sorted(A, 0, n)`，有 `!is_in(x, A, 0, mid)`。接下来分类讨论假设：

     - `is_in(x, A, lower, upper)` 成立，则 `is_in(x, A, mid + 1, upper)` 即 `is_in(x, A, lower', upper)` 成立。

     - `!is_in(x, A, 0, n)` 成立。

      综上，`is_in(x, A, lower', upper) || !is_in(x, A, 0, n)` 成立。

   - 如果 `A[mid] > x`，更新 `upper' = mid`，结合前置条件 `//@requires is_sorted(A, 0, n)`，有 `!is_in(x, A, mid, n)`。接下来分类讨论假设：
  
     - `is_in(x, A, lower, upper)` 成立，则 `is_in(x, A, lower, mid)` 即 `is_in(x, A, lower, upper')` 成立。

     - `!is_in(x, A, 0, n)` 成立。

      综上，`is_in(x, A, lower, upper') || !is_in(x, A, 0, n)` 成立。

3. **后置：** 函数的返回有两种情况：

    - 如果函数返回 `-1`，说明循环以 `lower == upper` 退出。根据循环不变量 `is_in(x, A, lower, upper) || !is_in(x, A, 0, n)`，因为 `is_in(x, A, lower, upper)` 等价于 `is_in(x, A, lower, lower)` 为假，因此 `!is_in(x, A, 0, n)` 必须成立，满足了函数的后置条件。

    - 如果函数返回 `result`，说明循环以 `A[mid] == x` 退出。根据循环不变量 `loop_invariant lower >=0 && lower<=upper && upper<=n;`，有 `0 <= mid < n`，满足了函数的后置条件。
	
## 3. 返回最左侧的 x

考虑下面的例子：

```
A = [1, 2, 2, 2, 3, 4, 5]
x = 2
```

根据原先的算法，初始时，`lower = 0; upper = 7;`，第一次迭代 `mid = 3`，由于 `A[3] == 2` ，函数返回 `3` 而不是 `1`。

修改的算法如下：

```cpp
// search03.c0 > int search(int x, int[] A, int n);

int search(int x, int[] A, int n)
//@requires n >= 0 && n <= \length(A);
//@requires is_sorted(A, 0, n);
/*@ensures (\result == -1 && !is_in(x, A, 0, n))
      || (\result >= 0 && \result < n && A[\result] == x && !is_in(x, A, 0, \result));
@*/
{
  int lower = 0;
  int upper = n;
  bool flag = false;
  while (lower < upper)
    //@loop_invariant lower >=0 && lower<=upper && upper<=n;
    //@loop_invariant is_in(x, A, lower, upper) || !is_in(x, A, 0, n);
    //@loop_invariant lower <= get_first(x, A) <= upper;
    {
      int mid = lower + (upper - lower) / 2;
      //@assert lower<=mid && mid<=upper;
      if (A[mid] == x)
        {
          upper = mid; // 继续搜索左侧
          flag = true; // 标记成功
        }
      else if (A[mid] < x)
        lower = mid + 1;
      else
        upper = mid;
    }

  return flag ? lower : -1;
}

```

`get_first(x, A)` : 返回 `A` 中第一个 `x` 的 位置。

**证明：**

- **初始化：** 在循环开始之前，`lower` 被初始化为0，`upper` 被初始化为 `n`，并且我们有 `n >= 0` 和 `n <= \length(A)`，因此循环不变量1和2显然成立。对于循环不变量3，由于初始时还没有找到 `x`，所以 `get_first(x, A)` 的值不确定，但它仍然在合理的范围内。

- **保持：** 假设循环开始前，循环不变量1、2和3成立。然后进入循环体。

    - 如果 `A[mid] == x`，则 `upper` 更新为 `mid`，以继续搜索左侧。此时循环不变量1仍然成立，因为 `lower` 和 `upper` 在合理的范围内。循环不变量2仍然成立，因为我们在当前搜索区间 `[lower, upper)` 中找到了元素 `x`。对于循环不变量3，由于我们在左侧找到了 `x`，`get_first(x, A)` 的位置应该在当前搜索区间 `[lower, upper)` 内，因此它仍然成立。

    - 如果 `A[mid] < x`，则 `lower` 更新为 `mid + 1`，缩小搜索范围。同样，循环不变量1和2仍然成立，因为 `lower` 和 `upper` 在合理的范围内，且 `x` 仍然不在当前搜索区间 `[lower, upper)` 内。对于循环不变量3，`get_first(x, A)` 的位置仍然在当前搜索区间 `[lower, upper)` 内。

    - 如果 `A[mid] > x`，则 `upper` 更新为 `mid`，继续搜索左侧。同样，循环不变量1和2仍然成立，因为 `lower` 和 `upper` 在合理的范围内，且 `x` 仍然不在当前搜索区间 `[lower, upper)` 内。对于循环不变量3，`get_first(x, A)` 的位置仍然在当前搜索区间 `[lower, upper)` 内。

- **后置：** 循环终止的条件是 `lower >= upper`，此时搜索范围为空或只包含一个元素。在终止时，我们有三种可能性：

    - 如果函数返回 `-1`，说明在搜索范围 `[lower, upper)` 内没有找到元素 `x`。根据循环不变量2，这也意味着在整个数组范围 `[0, n)` 内也没有找到元素 `x`，因此 `!is_in(x, A, 0, n)` 必须成立，满足了函数的后置条件。

    - 如果函数返回 `lower`，则说明找到了元素 `x`，并且 `result` 是元素 `x` 在数组中的索引。根据循环不变量3，`A` 中第一个 `x` 的位置在 ·`[lower,lower)` 区间，也就满足了函数的后置条件

- **可终止：** 根据 `mid` 的计算方法，`mid < upper`，缩小了搜索区间。另外两个分支同样会缩小搜索区间。因此搜索区间最终会变成`[lower, lower)`，从而跳出循环。

## 4. 溢出问题

如果将中间点的计算方法改为：

```cpp
int mid = (lower + upper)/2;
```

可能触发 `//@assert lower<=mid && mid<=upper;` 告警，因为 `lower + upper` 在加数接近 `INT_MAX` 时会溢出。

## 5. 不可终止问题

如果将循环体改为：

```cpp
// .... loop_invariant elided ....
{
    lower = lower;
    upper = upper;
}
```

循环不变量仍然成立，因为循环体没有对初始条件做任何改变。

函数后置不再成立，因为循环体没有使循环朝着满足循环不变量的目标前进。

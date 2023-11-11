# Written Homework - 8

## 1. Hash Tables: Data Structure Invariants

1. `bool is_ht(ht H)` 的实现

    ```c
    bool is_ht(ht H) {
        if (H == NULL) return false;
        if (!(H->m > 0)) return false;
        if (!(H->n >= 0)) return false;
        //@assert H->m == \length(H->table);
        int nodecount = 0;

        for (int i = 0; i < H->m; i++) {
            // set p equal to a pointer to the first node
            // of chain i in the table, if any
            chain* p = H->table[i];
            
            while (p != NULL) {
                elem e = p->data;
                
                if ((e == NULL) || (key_equal(elem_key(e), i)))
                    return false;
                
                nodecount++;

                if (nodecount > H->n)
                    return false;
                
                p = p->next;
            }
        }

        if(nodecount <> H->n){
            return false;
        }

        return true;
    }
    ```

2. 后置约定

    `/*@ensures \result == NULL || key_equal(k, elem_key(\result));
    @*/`

## 2. Binary Search Trees

1. 绘制二叉搜索树

    ```mermaid
    flowchart TB
    null_1(( ))
    style null_1 fill:#f100,stroke-width:0px
    null_2(( ))
    style null_2 fill:#f100,stroke-width:0px
    null_3(( ))
    style null_3 fill:#f100,stroke-width:0px
    75---13
    75---92
    13~~~null_1
    13---42
    92---84
    92---99
    42---20
    42---73
    84---71
    84~~~null_2
    99---98
    99~~~null_3
    ```

2. 最大BST数目

   用 $n$ 个不同的数组成的 BST 的数目可用 $Catalan$ 公式计算：

   $C(n)=\frac{(2n)!}{(n+1)!\cdot n!}$

   本例中，代入 $n=5$ ，得到 $42$

3. 深搜算法

   ```c
    int tree_height(tree* T)
    //@requires is_ordered(T, NULL, NULL);
    {
        if (T == NULL) {
            return 0;
        } else {
            int left_height = tree_height(T->left);
            int right_height = tree_height(T->right);

            return 1 + (left_height > right_height ? left_height : right_height);
        }
    }

    int bst_height(bst B)
    //@requires is_bst(B);
    //@ensures is_bst(B);
    {
        return tree_height(B);
    }
   ```

`约定`是 C0 中特殊的注释语句，在 cc0 和 coin 中使用 `-d` 参数启用约定检查。

| 注释 | 检查触发时间 |
| --- | --- |
| ```//@requires EXP;``` | 在函数执行前 |
| ```//@ensures EXP;``` | 在函数返回前 |
| ```//@loop_invarient EXP;``` | 在循环条件被检查前 |
| ```//@assert EXP;``` | 注释语句所处的位置 |

## 使用 ```//@ensures EXP;```

有些特殊的变量和函数只能在注释中访问，例如 `\result` 

不可以对在后置条件中引用的变量赋值，因此下面的代码是错误的：

```cpp
int f (int x, int y)
//@requires y >= 0;
//@ensures \result == POW(x,y);
{
    int r = 1;
    while (y > 1) {
        if (y % 2 == 1) {
            r = x * r;
        }
        x = x * x;
        y = y / 2;
    }
    return r * x;
}

/*
error:cannot assign to variable ’x’
used in @ensures annotation
x = x * x 
*/
```

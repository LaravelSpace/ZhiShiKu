## 爬台阶问题

#### 问题描述

**设：**有 n 级台阶，每一次上一级台阶或者两级台阶。
**问：**有多少种方法走完 n 级台阶？

#### 数学建模

**设：**走到 n 级台阶的方式数为 f(n)。

通过分析，可以发现这样一个规律：

$$
n=0时，f(0)=0; \\
n=1时，f(1)=1; \\
n=2时，f(2)=2; \\
n>2时，f(n)=f(n-2)+f(n-1);
$$

**总结：**走到 n 级台阶的方式数为走到 n-1 级台阶的方式数与走到 n-2 级台阶的方式数之和。

#### 动态规划

如果将台阶数和对应的结果保存为一个数组 `dp[]`，则 `dp[n]` 保存的数据就对应 f(n)。

结合之前的结论 $f(n) = f(n-2) + f(n-1)$。我们就可以去掉递归结构，顺序计算。

比如假设本题的 n = 5。那么递归的求解步骤就是：

$$
f(5) = f(3) + f(4) \\
= f(1) + f(2) + f(2) + f(3) \\
= f(1) + f(2) + f(2) + f(1) + f(2)
$$

这个结构其实可以正向求解。

根据题目的意思可以很容易的得到： $f(1) = 1;f(2) = 2;$。

然后根据递推关系依次计算 f(3)，f(4)，f(5) 就行了。
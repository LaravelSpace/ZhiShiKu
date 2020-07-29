# 最长公共子序列问题

## 问题描述

给定两个字符串 `string1` 和 `string2`，返回两个字符串的最长公共子序列的长度。
**例如**：`string1 = "1A2C3"`，`string2 = "B1D23"`。则 `"123"` 是最长公共子序列，那么两字符串的最长公共子序列的长度为 3。

## 数学建模

- 第一步：列出两个字符串所有的子序列。
- 第二步：比较所有的子序列，找出最长公共子序列

## 动态规划

假设两字符串 `string1` 和 `string2` 的长度分别为 m 和 n。
对于这类问题，我们一般可以构建一个 m * n 大小的矩阵 `dp[][]`。
其中 `dp[i][j]` 代表的是 `string1` 中前 i 个字符串与 `string2` 中前 j 个字符串的最长公共子序列的长度。

1. 第一步：初始化矩阵的第一行和第一列。
    - 第一行："1" 与 "B"，"B1"，"B1D"，"B1D2"，"B1D23" 分别为 0,1,1,1,1
    - 第一列："B" 与 "1"，"1A"，"1A2"，"1A2C"，"1A2C3" 分别为 0,0,0,0,0
2. 第二步：从左至右，从上至下，依次计算。这里有两种情况。
    - 情况 1：`string1[i] == string2[j]`，这时 `dp[i][j] = dp[i-1][j-1] + 1`。举例："1A2" 和 "B1D2" 的最长子序列 "12" 就是 "1A" 和 "B1D" 的最长子序列 "1" 再加上 "2"。
    - 情况 2：`string1[i] != string2[j]`，这时 `dp[i][j] = dp[i][j-1] 和 dp[i][j-1]` 的最大值。
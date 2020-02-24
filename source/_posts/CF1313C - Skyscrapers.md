---
title: CF1313C - Skyscrapers
categories:
  - 题目分析
tags:
  - 单调队列
  - 数列题
date: 2020-02-24 18:12:26
---

## 题目大意

给定长度为$n$的数列$m_i$。求长度为$n$的这样一个数列$a$，使得对于任意$1 \le i \le n$，$a_i \le m_i$，且存在一个$j$使得$1..j$单调递增、$j..n$单调递减，且数列$a_i$的和最大。

在[Easy ver.](https://codeforces.com/contest/1313/problem/C1)中$n\le 1000$；[Hard ver.](https://codeforces.com/contest/1313/problem/C2)中$n\le500000$。

## 思路

形象地，满足条件的数列$a$具有一个峰顶。我们枚举这个峰顶$j$，贪心地，应当$a_j=m_j$，$\forall 1\le i < j, a_i = \min(a_i, a_{i + 1})$，$\forall j< i \le n, a_i = \min(a_i, a_{i - 1})$。对于当次的$a$，计算出$\sum_{i = 1}^{n}a_i$，并寻找使得该值取得最大时的$a$即可。时间复杂度$O(n^2)$，可以通过[Easy ver.](https://codeforces.com/contest/1313/problem/C1)。

[Hard ver.](https://codeforces.com/contest/1313/problem/C2)的数据范围大了许多，猜测其期望的时间复杂度大概$O(n)$至$O(n\log n)$。如何进一步优化上述的步骤？显然枚举峰顶是必要的，我们考虑优化上述的对于峰顶$j$，计算所有$a_i$的过程。

先考虑峰顶的左半部分的非严格单调区间，**展开**$a_i$其中$1\le i < j$，$a_{i} = \min{ \left\{ a_{i}, a_{i+1}, \cdots , a_{j} \right \} }$。定义其和$lsum_{j} = \sum_{i = 1}^{n} \min{ \left \{ a_i, a_{i+1}, \cdots , a_{j} \right \} }$。我们期望通过$lsum_{j-1}$能**递推**出$lsum_{j}$，考虑其**差异**：后者的原数列比前者要多出了一个$a_j = m_j$，且$a_j$可能比$lsum_{j-1}$的原数列中靠近$j-1$的连续几项要小，从而在$lsum_{j}$中**替换、成为这几项新的最小值**。这样，对于$lsum_{j-1}$的原数列$a$，使得$a_i < m_j$的$a_i$在$lsum_j$的原数列中被替换为$m_j$，从而对$lsum_j$产生$m_j - a_i$的贡献。

我们需要维护，对于$lsum_j$的原数列$a$。$a$是一个非严格单调递增的序列，于是考虑单调栈解决问题。单调栈是严格递增的，存储的是**替换、成为区间最小值**的元素。每次我们从$lsum_{j-1}$能递推出$lsum_{j}$时，欲加入的元素$a_i=m_i$会依次把栈中大于该值的元素pop出去。被pop出去的每个元素$a_k$，**均代表一段区间**$[k, last]$，其中$last$为上次pop出去的元素下标，初始为$i$。根据上文的推导，每个元素的总贡献$(m_j - a_i) \times (last - k)$。最后将$a_i$入栈，将$a_i$本身的贡献算入$lsum_j$即可。

{% asset_img example.png example %}

上图是一个例子。新加入的$3$会将原数列中的$6,5,5$均替换为$3$，直到比其小的$2$。以单调栈的形式理解，小于$3$的元素（各个元素在图中以下划线标出），均被pop出去。元素$6$所代表的区间长度$1$，带来贡献$3-6=-3$；元素$5$所代表的区间长度$2$，带来贡献$2\times(3-5) = -4$。$a_i$本身带来贡献$3$。总贡献$delta = -3 + (-4) + 3 = -4$，故$lsum_j = lsum_{j - 1} - 4$。

同理可统计对于所有的$j$，$rsum_j$的值。$j$从$1$到$n$遍历，寻找使得$lsum_j + rsum_j - m_j$最小的$j$。最后根据$j$，还原以其为峰顶的原数列$a$即可。时间复杂度$O(n)$。

## 实现



## 注释与参考

无。
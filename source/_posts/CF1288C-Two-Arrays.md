---
title: CF1288C Two Arrays
categories:
  - 题解
tags:
  - 组合数学
  - 动态规划
date: 2020-03-18 00:13:15
---

## 题目大意

求满足下述条件的数组对$(a, b)$的数量，对$10^9 + 7$取模的结果：

- 数组的长度等于m；
- 数组的每个元素都是$1$到$n$之间的整数；
- $\forall 1 \le i \le m, a_i \le b_1$；
- $\forall 1 \le i \le m - 1, a_i \le a_{i + 1}, b_i \ge b_{i + 1}$。

## 思路

注意到$a_1 \le a_2 \le \cdots \le a_m \le b_m \le b_{m - 1} \le \cdots \le  b_1$。

转化为在$n$个数中选取$2m$个，可以重复的组合问题。设$choose_{i}(1 \le i \le n)$表示第$i$个数选取的次数，则有$\sum_{i = 1}^{n} choose_i = 2m$。即求该多元一次方程的非负整数解的个数，故为$\tbinom {2m + n - 1}{n - 1}$。

## 实现

咕咕咕

## 注释与参考

1. 题解https://codeforces.ml/blog/entry/73105
2. 特别感谢吴文庆老师的指导


---
title: CF1312D Count the Arrays
categories:
  - 题解
tags:
  - 组合数学
date: 2020-03-10 13:53:26
---

## 题目大意

求满足下述条件的数组数量，对$998244353$取模的结果：

- 包含$n$个元素；
- 每个元素都是$1$到$m$的整数；
- **正好**有一对相等的元素；
- 存在一个索引$i$，使得该数组在第$i$个元素之前严格递增，在其后严格递减。

其中$2 \le n \le m \le 2\times10^5$。

## 思路

从最基本的情况入手，考虑最大值为$v$及其位置为$k$的数组数量：

考虑$k$的左侧，需要保证在$k$之前单调递增，故从$v-1$中任选$k-1$个数，排成从小到大的一排即可，为$\tbinom{v - 1}{k - 1}$。

再考虑$k$的右侧，选取一个数，与左侧元素的其中一个相同，方案数$k - 1$；只需再选$n - k - 1$个数，不能和左侧的任何一个元素一样，故从$v - 1 - (k - 1)$中选取$n - k - 1$个，方案数$\tbinom{v - 1 - (k - 1)}{n - k - 1}$。将这共$n - k$个数，排成从大到小的一排即可，为$(k - 1) \times \tbinom{v - 1 - (k - 1)}{n - k - 1}$。

根据乘法原理，这种数组的数量：

$$
\dbinom{v - 1}{k - 1} \times (k - 1)\times \dbinom{v - 1 - (k - 1)}{n - k - 1} = \frac{(v - 1)!}{(k - 2)! \times (n - k - 1)! \times(v -  n + 1)!} = \frac{\prod_{i = v - n + 2} ^ {v - 1} i}{(k - 2)! \times (n - k - 1)!}
$$
注意到分子仅和$v$有关，总答案可化简得：
$$
\sum_{k = 2}^{n} \sum_{v = n - 1}^{m} \frac{\prod_{i = v - n + 2} ^ {v - 1} i}{(k - 2)! \times (n - k - 1)!} = \sum_{k = 2}^{n} \frac{\sum_{v = n - 1}^{m}\prod_{i = v - n + 2} ^ {v - 1} i}{(k - 2)! \times (n - k - 1)!}
$$
预处理阶乘后，

枚举$v$即可计算分子$\sum_{v = n - 1}^{m}\prod_{i = v - n + 2} ^ {v - 1} i$；

然后枚举$k$，计算分母$(k - 2)! \times (n - k - 1)!$即可。

复杂度$O(n \log P)$，其中$P = 998244353$。

## 实现

```c++
#include <cstdio>
#include <algorithm> 

typedef long long ll;

const ll MOD = 998244353;
const int MAXN = 2e5 + 5;

int n, m;
ll fac[MAXN];

ll fastPow(ll a, ll k)
{
	ll res = 1;
	ll base = a;
	while(k) {
		if(k & 1) {
			res = (res * base) % MOD;
		}
		base = (base * base) % MOD;
		k >>= 1;
	}
	return res;
}

inline ll inv(ll x)
{
	return fastPow(x, MOD - 2); 
}

int main(){
	scanf("%d %d", &n, &m);
	fac[0] = 1LL;
	for(int i = 1; i <= std::max(n, m); i++) {
		fac[i] = (fac[i - 1] * i) % MOD;
	}
	ll numerator = 0;
	for(int v = m; v - n + 2; v--) {
		numerator = (numerator + ((ll)fac[v - 1] * inv(fac[v - n + 1])) % MOD) % MOD;
	}
	ll res = 0;
	for(int k = 2; k < n; k++) {
		res = (res + ((numerator * inv(fac[k - 2]))) % MOD * inv(fac[n - k - 1])) % MOD;
	}
	printf("%lld", res);
	return 0;
}
```

## 注释与参考

无。
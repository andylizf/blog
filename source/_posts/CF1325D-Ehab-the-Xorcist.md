---
title: CF1325D Ehab the Xorcist
categories:
  - 题解
tags:
  - 构造
  - 贪心
  - 位掩码
date: 2020-03-15 11:36:14
---

## 题目大意

给定两整数$u, v(0 \le u, v \le 10^{18})$，求长度最小的数组使得其亦或和为$u$，加和为$v$。

## 思路

观察本题的数据范围可知本题可能有通解。

考虑数组长度为3时，
$$
\begin{align}
&a_1 \oplus a_2 \oplus a_3 = u \nonumber \\
&a_1 + a_2 + a_3 = v \nonumber \\
\end{align}
$$
变换得：
$$
\begin{align}
u \oplus a_1 \oplus a_2 + a_1 + a_2 = v \nonumber
\end{align}
$$
特殊地 ，$a_1 = a_2$可以消去亦或，则$a_1 = a_2 = \frac{v - u}{2}$，此时$a_3 = u$，$(\frac{v - u}{2}, \frac{v - u}{2}, u)$为方程的一组解。

注意到亦或，或称不进位加法，若$a, b$在第$i \ge 0$位均为1，则加法时会进位，对加和与亦或和的差的贡献为$2^{i + 1}$，有$a + b = a \oplus b + 2(a \wedge b)$。在本题中，推广至多元可知，当且仅当$v \ge u, v - u \equiv 0 \pmod 2$时有解。因而任意有解的$u, v$，$(\frac{v - u}{2}, \frac{v - u}{2}, u)$总是方程的一组解，长度不超过3。

此时：
$$
\begin{align}
&u \oplus \frac{v - u}{2} \oplus \frac{v - u}{2} = u \nonumber \\
&u + \frac{v - u}{2} + \frac{v - u}{2} = v \nonumber
\end{align}
$$

考虑将其合并：

当$u \oplus \frac{v - u}{2} = u + \frac{v - u}{2}$时，令$x = u \oplus \frac{v - u}{2} = u + \frac{v - u}{2}$，则$x \oplus \frac{v-  u}{2} = u, x + \frac{v - u}{2}$，$(x, \frac{v - u}{2})$为方程的一组解；

当$\frac{v - u}{2} \oplus \frac{v - u}{2} = \frac{v - u}{2}+ \frac{v - u}{2}$时，$u = v$，$u$为方程的一组解。

当$u\oplus \frac{v - u}{2} \oplus \frac{v - u}{2} = u + \frac{v - u}{2}+ \frac{v - u}{2}$时，$u = v$，$u$为方程的一组解。

反之当$u \oplus \frac{v - u}{2} \not= u + \frac{v - u}{2}$时，一定不存在$(a, b)$使得$a \oplus b = u, a + b = v$。使用反证法：

假设存在$(a, b)$使得：
$$
\begin{align}
&a \oplus b = u \nonumber \\
&a + b = v \nonumber \\
\end{align}
$$
由亦或、加法与与运算的关系：
$$
\begin{align}
&a \oplus b = u\label{eq3}\\
&a \wedge b = \frac{v - u}{2}\label{eq4}
\end{align}
$$
这样我们就可以仅考虑$a, b$的每一位。对于$\frac{v - u}{2}$的其中一位，若其为$1$，由$\eqref{eq3}$则$a, b$在此位均为$1$，由$\eqref{eq4}$则$u$在此位为$0$；反之为$0$，则对$u$没有限制。由$u \oplus \frac{v - u}{2} \not= u + \frac{v - u}{2} \rightarrow u \wedge \frac{v - u}{2} \not= 0$，则$u$和$\frac{v- u}{2}$存在一位同时为$1$。矛盾，故不存在这样的$(a,b)$。

综上所述，$v < u$或$v - u \not\equiv 0 \pmod 2$时无解；一般地，有长度为$3$的解$(\frac{v - u}{2}, \frac{v - u}{2}, u)$；当$u \oplus \frac{v - u}{2} = u + \frac{v - u}{2}$时有长度为$2$的解$(u + \frac{v - u}{2}, \frac{v - u}{2})$；当$u = v$时有长度为$1$的解$u$。注意当$u = v = 0$时，数组长度应为$0$。

## 实现

```c++
#include <iostream>

using namespace std;
typedef long long ll;

int main()
{
	ll u, v; cin >> u >> v;
	if(u > v || u % 2 != v % 2) {
		cout << -1 << endl;
	} else if(u == v){
		if(u == 0) {
			cout << 0 << endl;
		} else cout << 1 << endl << u << endl;
	} else {
		ll a = u, b = (v - u) / 2, c = (v - u) / 2;
		if((a ^ b) == a + b) {
			cout << 2 << endl << a + b << " " << b << endl;
		} else {
			cout << 3 << endl << a << " " << b << " " << c << endl;
		}
	}
	return 0;
}
```

## 注释与参考

1. 题解https://codeforces.com/blog/entry/74235
2. 特别感谢oscar学长的讲解
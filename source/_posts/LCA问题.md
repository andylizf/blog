---
title: LCA问题
date: 2020-02-10 11:54:47
categories: 
	- 图论
tags: 
	- 树
	- LCA问题
	- RMQ问题
	- 二分倍增
	- Tarjan
	- 树链剖分
---

## LCA问题

在一棵有根树上，两个结点的最近公共祖先（Least Common Ancestors, LCA）是它们的公共祖先中深度最大的那个顶点。

比如说，在左边的树中，顶点x 和y 的公共祖先用绿色标出，其中深绿色的顶点就是它们的最近公共祖先。

{% asset_img LCA.png LCA %}

## 倍增法

### 思路

倍增法的提升过程分为两个阶段：第一阶段，先将两个点提升到同一深度；第二阶段，使两个点同时提升相同的高度，直至跳到相同的结点。重点是：如何快速提升？我们使用**倍增**的思想，每次只转移$2^i$步，则需要提升的高度可表示为$diff=2^a+2^b+2^c+2^d+2^e+···$。且对于任意$diff$，最多跳$\log diff$次即可转移至目标结点。

#### 预处理

在两阶段前，我们先DFS**预处理**出每一结点的深度$depth(i)$[^1]，再记录其提升$2^i$步所到达的祖先。

设$parent(i,j)$表示结点$i$的$2^j$祖先。其$2^j$祖先是$2^{j-1}$祖先的$2^{j-1}$祖先，因而有递推式$parent(i,j)=parent(parent(i,j-1),j-1)$。预处理的时间复杂度$O(n \log n)$。

```c++
int depth[MAXN];
int parent[MAXN][MAXD];
void dfs(int u, int fa){
    depth[u] = depth[fa] + 1;
    parent[u][0] = fa;
    for(int i = 1; i < MAXD; i++){
        if((1 << i) <= depth[u]){
            parent[u][i] = parent[parent[u][i - 1]][i - 1];
        }
    }
    for(Edge *p = head[u]; p; p = p->next){ 
        if(p->v != fa){
            dfs(p->v, u);
        }
    }
}
```

$MAXD$指示$\log diff$的最大可能值，也即树深以2为底的对数；注意应当从1正向递推；对于结点$i$，若其$2^j$祖先不合法，即$depth[i] < 2^j$，其$parent(i, j)$应当为0。

在预处理时还可以求出这段路径的权值最大值、最小值或权值和等信息。

#### 两阶段提升

第一阶段，对$u$和$v$中深度较大的节点进行如下操作：$i$自$MAXD$至$0$循环，每次跳$2^i$步，当跳之后不会比另一个节点浅，即$2^i\le \left|depth(u)−depth(v)\right|$时。

第二阶段，若$u$和$v$相同，说明之前它们存在祖先关系，直接返回$u$；否则仍$i$自$MAXD$至$0$循环，两个节点同时跳$2^i$步，当跳之后$u$与$v$不相同时。最后返回$parent(u,0)$即可。

这样做的理由是：我们每次选择的都是使$u$与$v$不相同的**最大步数**，与使得$u$与$v$相同的**最小步数**差1。此时跳到的结点的父亲，即为$u$与$v$最近的公共祖先。

每次查询的时间复杂度$O(\log n)$。

```c++
int LCA(int x, int y){
    if(depth[x] < depth[y]){
        swap(x, y);
    } // let depth[x] >= depth[y]
    for(int i = MAXD - 1; i >= 0; i--){
        int xans = parent[x][i];
        if(depth[xans] >= depth[y]){
            x = xans;
        }
    }
    if(x == y){
        return x;
    }
    for(int i = MAXD - 1; i >= 0; i--){
        int xans = parent[x][i], yans = parent[y][i];
        if(xans != yans){
            x = xans; y = yans;
        }
    }
    return parent[x][0];
}
```

### 实现

```c++
#include <iostream>
#include <cstdio>
#include <algorithm>

using namespace std;

const int MAXN = 500001;
struct Edge{
	int v;
	Edge *next; 
} *head[MAXN], pool[2 * MAXN], *tot = pool;

void addEdge(int u, int v){
	Edge *p = tot++;
	p->v = v; p->next = head[u];
	head[u] = p;
}
const int MAXD = 20;
int N, M, S;

int depth[MAXN];
int parent[MAXN][MAXD];
void dfs(int u, int fa){
	depth[u] = depth[fa] + 1;
	parent[u][0] = fa;
	for(int i = 1; i < MAXD; i++){
		if((1 << i) <= depth[u]){
			parent[u][i] = parent[parent[u][i - 1]][i - 1];
		}
	}
	for(Edge *p = head[u]; p; p = p->next){	
		if(p->v != fa){
			dfs(p->v, u);
		}
	}
}

int LCA(int x, int y){
	if(depth[x] < depth[y]){
		swap(x, y);
	} // let depth[x] >= depth[y]
	for(int i = MAXD - 1; i >= 0; i--){
		int xans = parent[x][i];
		if(depth[xans] >= depth[y]){
			x = xans;
		}
	}
	if(x == y){
		return x;
	}
	for(int i = MAXD - 1; i >= 0; i--){
		int xans = parent[x][i], yans = parent[y][i];
		if(xans != yans){
			x = xans; y = yans;
		}
	}
	return parent[x][0];
}

int main(){
	scanf("%d %d %d", &N, &M, &S);
	for(int i = 0; i < N - 1; i++){
		int x, y; cin >> x >> y;
		addEdge(x, y); addEdge(y, x);
	}
	depth[S] = 1;
	dfs(S, 0);
	/*
	for(int i = 1; i <= N; i++){
		cout << "NODE " << i << endl;
		for(int j = 0; j < MAXD; j++){
			if(parent[i][j] != 0){
				cout << "2^" << j << " is " << parent[i][j] << endl;
			} 
		} 
	}*/
	
	for(int i = 0; i < M; i++){
		int a, b; scanf("%d %d", &a, &b);
		printf("%d\n", LCA(a, b));
	}
	return 0;
}

```

可以使用针对提升高度进行特化，使用$\log_2diff$代替$MAXD$以减小常数。

## 重链剖分



## Tarjan算法



## 转化为RMQ问题



## 注释与参考

[^1]:所述$depth[i]$自1开始，即这棵树根深度$depth[root] = 1$。下同。
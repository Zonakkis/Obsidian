---
时间复杂度: O(mlogm)
空间复杂度: O(n)
---
# 定义
---
>[!note] Kruskal 算法
>Kruskal算法是一种用于寻找**有权无向**图的最小生成树的贪心算法。核心是每次选取边权最小的边并确保边不会成环。
# 过程
---
对于一个无向连通图，设节点数量为$V$，边集为$E$，已经选取的边数为$cnt$，边权和为$w$。
1. 排序：将边集按边权从小到大排序。
2. 遍历：按边权从小到大取边$(u, v)$，对于节点$u$和$v$，如果：
	- 不在同一个并查集中：合并$u$和$v$的并查集（即连接$u$和$v$），$cnt++$，$w+=w(u, v)$。
	- 在同一个并查集中：跳过，防止成环。
3. 结束：当$cnt == V - 1$时，算法完成。
# 实现
---
```c++
int N;
int n;
int f[N];

struct Edge
{

}

void init()
{
	for(int i = 1; i <= n; i++)
		f[i] = i;
}

int find(int i)
{
	if(f[i] == i)
		return f[i];
	return f[i] = find(f[i]);
}

void unite(int x, int y)
{
	int fx = find(x), fy = find(y);
	f[fx] = fy;
}

void kruskal()
{
	
}
```
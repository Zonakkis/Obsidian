---
时间复杂度: O(nlogn)
空间复杂度: O(n + m)
---
# 定义
---
>[!note] Dijkstra 算法
>由荷兰计算机科学家 E. W. Dijkstra 于 1956 年发现，1959 年公开发表的一种用于求解**非负**权图上**单源**最短路径的算法。
# 过程
---
设起点为$s$，结点集合为$V$，边集为$W$。
同时设$d[i]$数组为$s$到点$i$的距离，$Visited$和$Unvisited$分别为已经确定最短路径和未确定最短路径的点集。
1. 初始化：$d[s] = 0$，其余$d[i] = 0$。令$Unvisited = V$。
2. 选点：遍历$Unvisited$，求出其中$d[u]$最小的结点$u$。
3. 松弛：遍历$u$的出边$w$以及对应的结点$v$，如果$s$到$u$的距离加上出边的距离是否小于$s$到$v$的距离，则更新。即$$if(d[u] + w < d[v]) d[v] = d[u] + w;$$
4. 重复：将$u$从$Unvisited$移到$Visited$中，若$Unvisited$不为空，重复2和3。
# 实现
---
## 朴素实现
```c++
struct edge
{
	int v, w;
};

int N;
int n, m;
vector<edge> p[N];
int d[N], 
bool vis[N];

void dijkstra(int s)
{
	// 初始化
	std::fill(dist, dist + N, 1e9);
	dis[s] = 0;
	
	for(int i = 1; i <= n; i++)
	{
		// 选点
		int u = -1;
		for(int j = 1; j <= n; j++)
			if(!vis[j] && (u == -1 || d[j] < d[u]))
				u = j;
		
		vis[u] = true;
		
		// 松弛
		for(auto &edge : p[u])
		{
			int v = edge.v;
			int w = edge.w;
			if(d[u] + w < d[v])
				d[v] = d[u] + w;
		}
	}
}

```
## 优先队列实现
利用优先队列，可以将取点操作的复杂度从$O(n)$降为$O(\log n)$。
```c++
struct edge
{
	int v, w;
};

struct node
{
	int u, d;
	
	bool operator>(const node& a) const
	{
		return d < a.d;
	}
}

int N;
int n, m;
vector<edge> p[N];
int d[N], 
bool vis[N];
priority_queue<node, vector<node>, greater<node>> pq;

void dijkstra(int s)
{
	// 初始化
	std::fill(dist, dist + N, 1e9);
	dis[s] = 0;
	pq.push({s, 0});
	
	while(pq.empty())
	{
		// 选点
		int u = pq.top().u;
		pq.pop();
		
		// 已经确定的点直接跳过
		if(vis[u] == true)
			continue;
		vis[u] = true;
		
		// 松弛
		for(auto &edge : p[u])
		{
			int v = edge.v;
			int w = edge.w;
			if(d[u] + w < d[v])
			{
				d[v] = d[u] + w;
				pq.push({v, d[v]});
			}
			
		}
	}
}
```
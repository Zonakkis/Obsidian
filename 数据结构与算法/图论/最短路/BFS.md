---
时间复杂度: O(v + e)
空间复杂度: O(v)
---
# 定义
---
>[!note] BFS
>对于无权图，可以使用BFS求最短路，因为最先从起点$s$到达节点$i$的路径一定是最短路径。
# 步骤
---
1. 对图进行BFS。
2. 对于途中经过的节点$u$和下一个点$v$，有$d[u] = d[v] + 1$。
# 实现
---
```c++

struct edge
{
	int v, w;
}
int N;
vector<edge> edges[N];
int d[N];
bool vis[N];

void bfs(int s)
{
	d[s] = 0;
	vis[s] = true;
	queue<int> q;
	q.push(s);
	
	while(!s.empty())
	{
		int u = s.top();
		s.pop();
		
		for(edge e : edges[u])
		{
			int v = e.v;
			if(!vis[v])
			{
				vis[v] = true;
				d[v] = d[u] + 1;
				q.push(v);
			}
		}
	}
}
```
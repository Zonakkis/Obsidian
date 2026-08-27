# 定义

ST 表（Sparse Table）是一种用于**静态区间查询**的数据结构，支持 *O(1)* 时间查询，常用于**区间最值查询**等满足结合律的操作。构建复杂度为 *O(n log n)*，**不支持修改**。
# 原理
ST 表利用区间长度的**二的幂**来拆分任意区间，并预处理所有以`2^j`为长度的区间操作。
以最小值为例：
`st[i][j]`表示从`a[i]`开始，长度为`2^j`的区间的最小值。
- `st[2][0] = a[2]`
- `st[2][1] = min(a[2], a[3])`
- `st[2][2] = min(a[2], a[3], a[4], a[5])`
# 建表
```c++
void build(const int n)  
{  
    for (int i = 1; i <= n; ++i)  
       st[i][0] = a[i]; // 区间长度为 1 时为 a[i]    
       for (int j = 1; 1 << j <= n; ++j) // 区间长度 = 2^j       
	       for (int i = 1; i + (1 << j) - 1 <= n; ++i) // 区间的起始位置 = i          
		       st[i][j] = min(st[i][j - 1], st[i + (1 << j - 1)][j - 1]);  
		    // st[i][j] = min(st[i][j - 1], st[i + 区间长度 / 2][j - 1])}
}
```
# 查询
```c++
int query(const int left, const int right)  
{  
    const int j = log2(right - left + 1); // 区间长度 = 2^j    
    return min(st[left][j], st[right - (1 << j) + 1][j]);  
}
```
# 模版
```c++
template <typename T, typename F>  
struct SparseTable  
{  
    T table[MAX_N][__lg(MAX_N) + 1];  
    F operation;  
  
    SparseTable(const T* a, F function) : operation(function)  
    {  
       for (int i = 1; i <= n; ++i)  
          table[i][0] = a[i];  
       for (int j = 1; (1 << j) <= n; ++j)  
          for (int i = 1; i + (1 << j) - 1 <= n; ++i)  
             table[i][j] = operation(table[i][j - 1], table[i + (1 << j - 1)][j - 1]);  
    }  
  
    T Query(const int left, const int right)  
    {  
       int j = __lg(right - left + 1);  
       return operation(table[left][j], table[right - (1 << j) + 1][j]);  
    }  
};
```
例：
```c++
vector<int> a(n + 1);
SparseTable min_st(a, [](const int x, const int y)  
{  
    return min(x, y);  
});
int min_value = min_st.query(1, n); //查询[1, n]的最小值
```
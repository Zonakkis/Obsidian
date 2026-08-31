# 命名空间
`namespace std;`
# 作用
返回一个迭代器，该迭代器指向序列中**第一个大于或等于给定值**的元素。
# 声明
```c++
template<class ForwardIt, class T>  
ForwardIt lower_bound(ForwardIt first, ForwardIt last, const T& value);

template<class ForwardIt, class T, class Compare> 
ForwardIt lower_bound(ForwardIt first, ForwardIt last, const T& value, Compare comp);
```
first：容器的起始迭代器。
last：容器的结束迭代器。
搜索范围为\[first, last)。在没有找到符合的元素时，返回last。
value：目标值。
comp（可选）：比较函数。默认使用升序。
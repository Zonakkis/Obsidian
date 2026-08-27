# 命名空间
`namespace std;`
# 作用
在指定的范围内找出第n所需的元素。
# 声明
```c++
template<class RandomIt>  
void nth_element(RandomIt first, RandomIt nth, RandomIt last);

template<class RandomIt, class Compare>  
void nth_element(RandomIt first, RandomIt nth, RandomIt last, Compare comp);
```
first：容器的起始迭代器。
nth： 第n所需的元素的迭代器，符合的元素将放置在这里。
last：容器的结束迭代器。
范围为\[first, last)。
comp（可选）：比较函数。默认使用升序。
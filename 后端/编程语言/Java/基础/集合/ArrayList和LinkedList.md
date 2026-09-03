# 对比
---

|                     | ArrayList             | LinkedList                 |
| ------------------- | --------------------- | -------------------------- |
| 底层数据结构              | **动态数组** (`Object[]`) | **双向链表** (`Node<E>`)       |
| 随机访问 (get/set)	<br> | O(1) 基于偏移量直接寻址        | O(n) 逐个遍历节点                |
| 头部插入/删除             | O(n) 需移动所有元素          | O(1) 仅修改指针                 |
| 尾部插入/删除             | O(1) 均摊，不触发扩容时        | O(1) 持有 last 尾指针           |
| 指定位置增删              | O(n) 寻址 O(1)，移动 O(n)  | O(n) 寻址 O(n)，指针修改 O(1)<br> |
| 内存占用                | 连续空间，预留空间会浪费，但无额外包装   | 离散空间，无预留浪费，但每个节点有指针开销      |
| CPU 缓存命中率           | 高（空间局部性好）             | 极低（内存分散，容易 Cache Miss）     |
| 线程安全性               | 线程不安全                 | 线程不安全                      |
# ArrayList
---
## 初始化
Java8后，ArrayList采用懒加载。初始化后是空的全局数组`DEFAULTCAPACITY_EMPTY_ELEMENTDATA`，直到第一次`add()`才分配内存。默认初始化容量为10。
## 扩容
`add()`容量不足时扩容为原来的1.5倍：
```java
int newCapacity = oldCapacity + (oldCapacity >> 1);
```
底层通过`Arrays.copyOf(elementData, newCapacity)`实现，本质是分配新数组并进行`System.arraycopy`内存级拷贝。
# 问题
---
## 遍历ArrayList和LinkedList有什么要注意？
ArrayList既可以用普通下标for循环也可以用增强for循环和迭代器。
LinkedList不能用普通下标for循环（每次都需要从头查找），只能使用增强for循环和迭代器。
增强for循环中不能`list.remove()`删除元素，否则会触发fail-fast`ConcurrentModificationException`。
遍历并删除元素的方法：
1. **使用迭代器（Iterator）的 `remove()` 方法**：
```java
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
	String item = iterator.next();
	if (needDelete(item)) {
		iterator.remove(); // 安全删除
	}
}
```
2. **使用Java 8的`removeIf`**：
```java
list.removeIf(item -> needDelete(item));
```
3. **使用普通for循环倒序遍历**：
```java
for (int i = list.size() - 1; i >= 0; i--) {
    String item = list.get(i);
    if (needDelete(item)) {
        list.remove(i);
    }
}
```

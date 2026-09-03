# 数据结构
---
JDK1.8的HashMap底层为**数组**+**链表**+**红黑树**。
```java
//链表节点
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next; // 指向下一个节点
}

//红黑树节点
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
    TreeNode<K,V> parent;  
    TreeNode<K,V> left;
    TreeNode<K,V> right;
    TreeNode<K,V> prev;    // 双向链表，方便删除时找到前驱
    boolean red;
}
```
**数组**：
```java
transient Node<K,V>[] table;
```
**链表**：解决哈希冲突。当多个key计算出的数组下标相同时，使用链地址法挂在同一个位置。
**红黑树**：当链表过长（链表长度≥8且数组长度≥64）时，转化为红黑树，将查找复杂度从O(n) 优化到 O(log n)。
# 核心参数
---

| 参数                         | 含义    | 默认值   | 说明                                        |
| -------------------------- | ----- | ----- | ----------------------------------------- |
| `DEFAULT_INITIAL_CAPACITY` | 初始容量  | 16    | 2的幂次方，平衡内存与冲突率                            |
| `LOAD_FACTOR`              | 负载因子  | 0.75f | 空间与时间的折中。0.75时泊松分布下链表长度>=8的概率极低           |
| `TREEIFY_THRESHOLD`        | 树化桶阈值 | 8     | 基于泊松分布，随机哈希下链表长度达到8的概率约为千万分之六，几乎不会触发，只是兜底 |
| `MIN_TREEIFY_CAPACITY`     | 树化总阈值 | 64    | HashMap 数组的总长度                            |
| `UNTREEIFY_THRESHOLD`      | 退化阈值  | 6     | 桶中的节点总数少于等于6                              |
# 核心机制
---
## Hash计算与定位
**哈希计算（扰动函数）**：
```java
static final int hash(Object key) {  
    int h;  
    // 高16位 ^ 低16位，让高位也参与运算，减少冲突
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);  
}
```
**下标计算**：
```java
i = (n - 1) & hash \\ n 为table的长度
```
**使用&而不是%的原因**：
- 当`n`为2的幂次方时，`n-1`二进制全为1，此时`&`和`%`的效果相同。
- 位运算`&`比取模`%`快得多。
## 扩容机制
扩容的触发由负载因子(0.75)触发，当容量超过0.75时，进行扩容操作，即翻倍。
**元素移动机制**：
对于原来的每一个元素，根据`i = (n - 1) & hash`，新的`n - 1`对比原来相当于在最高位左边加了一个1，因此只要计算这一位（与oldCap相等)和`hash`的与运算结果是否为1，若为1则移动到`+oldCap`的位置，不为1就不用移动。
```java
Node<K,V> loHead = null, loTail = null;  
Node<K,V> hiHead = null, hiTail = null;  
Node<K,V> next;
if ((e.hash & oldCap) == 0) {  // 不移动
    if (loTail == null)  
        loHead = e;  
    else  
        loTail.next = e;  
    loTail = e;  
}  
else {  // 移动
    if (hiTail == null)  
        hiHead = e;  
    else  
        hiTail.next = e;  
    hiTail = e;  
}
```
# 其他问题
---
1. 为什么HashMap线程不安全？
	- **JDK 1.7**：多线程扩容时，头插法可能导致环形链表，后续`get`时可能导致死循环。
	- **JDK 1.8**：改用尾插法解决了死循环问题，但仍然存在以下问题：
		- 并发put可能导致数据覆盖丢失
		- ++size不是原子操作，多线程下计数不准确
	多线程环境下应使用`ConcurrentHashMap`
2. 为什么重写equals必须重写hashCode？
	- hashCode决定了元素的位置，equals决定两个key是否相同
	- 如果两个元素equals相同但hashCode不同，会被放到不同的位置，导致get找不到已有元素
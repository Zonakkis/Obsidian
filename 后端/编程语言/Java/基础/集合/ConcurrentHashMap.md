# 对比
---

|        | JDK7                             | JDK8                               |
| ------ | -------------------------------- | ---------------------------------- |
| 底层     | `Segment`数组 + `HashEntry`数组 + 链表 | 数组 + 链表 + 红黑树                      |
| 并发控制机制 | 分段锁                              | CAS + `synchronized`               |
| 锁粒度    | 对一个分段加锁（默认16个）                   | 对桶的首节点（链表头或红黑树根）加锁                 |
| 并发度    | 取决于分段数量                          | 取决于Node数组长度                        |
| 哈希碰撞处理 | 链地址法（头插法）                        | 链地址法（尾插法）+树化（链表长度>8且容量≥64）         |
| 计数机制   | 两次不加锁统计，不同则加锁统计                  | `baseCount` + `CounterCell[]` 数组求和 |
# JDK7：分段锁
---
## 数据结构
核心为一个`Segment<K,V>[]`数组（默认长度为16）。
`Segment`继承了`ReentrantLock`，内部包含一个`HashEntry<K,V>[]`数组，每个`HashEntry`是一个链表节点。
## get
不加锁。HashEntry的value和next都声明为volatile，保证了内存可见性。
## put
1. 计算hash
2. 通过hash定位Segment
3. 通过`tryLock()`尝试获取分段的锁，如果失败则通过`scanAndLockForPut()`自旋锁，自旋一定次数后转为阻塞锁。
4. 通过hash定位HashEntry
## 缺点
1. **内存开销大**：默认容量为16的Segment数组需要额外空间
2. **锁粒度过粗**：虽然比`HashTable`的全表锁好，但是同一个分段内仍然需要竞争锁，且`size()`等方法可能全局加锁。
3. **并发度有限**：并发度取决于`Segment`数量，初始化后不变，太小并发竞争严重，太大浪费内存。
# JDK8：CAS + synchronized
---
## 数据结构
核心为**数组**+**链表**+**红黑树**。
```java
volatile Node<K,V>[] table;
```
`ForwardingNode`是一种特殊的Node，作用：
1. **标记桶已经迁移**：某个桶迁移后会将原数组的桶设为`ForwardingNode`，此时查找操作会到新数组中查找。
2. **协助其他线程参与扩容**：当put遇到`ForwardingNode`时会调用`helpTransfer`协助扩容。
`TreeBin`
## 核心参数
---
`volatile int sizeCtl`是控制初始化和扩容的核心变量：
- **=0**：默认值，表示数组未初始化。
- **=-1**：表示正在初始化。
- **<-1**：表示正在扩容（高16位为扩容标记戳，低16位为扩容参与的线程数）
- **>0**：
	- **未初始化时**：构造函数指定的初始容量
	- **初始化后**：扩容阈值（$0.75 \times n$）
## get
不加锁。Node的val和next都声明为volatile，保证了内存可见性。
## put
1. **校验参数**：Key和Value都不允许为null。（get到null不知道key是否存在，用containsKey不能保证原子性）
2. **计算Hash**：`(h ^ (h >>> 16)) & HASH_BITS`，其中`HASH_BITS = 0x7fffffff`，只有最高位符号位为0，保证与运算结果为正数。
3. **自旋循环 for (Node<K,V>[] tab = table;;)**：
	1. **若数组未初始化**：调用`initTable()`，利用CAS将`sizeCtl`设为-1，成功的进行初始化，失败的自旋等待。
	2. **定位桶**：`(n - 1) & hash`。
	3. **桶为空**：利用CAS将新节点插入到桶头部，成功的直接break，失败的自旋重试。
	4. **桶非空**：
		1. 使用synchronized锁住桶的首节点
		2. 双重检查防止修改同一个节点
		3. **桶是链表时**：遍历链表，覆盖旧值或插在尾部。
		4. **桶是红黑树时**：调用红黑树的插入。
	5. **桶的首节点hash为-1**：`(fh = f.hash) == MOVED`，即为`ForwardingNode`，表示正在扩容，调用`helpTransfer()`协助扩容。
4. **检查树化**：如果链表节点数达到阈值（默认 8），且数组容量≥64，转为红黑树。
5. **更新计数和扩容检查**：调用`addCount()`更新计数并检查是否需要扩容。
## addCount
采用了`LongAdder`的分段累加思想：
CAS成功的更新全局基础变量`baseCount`。
CAS失败，存在并发竞争，在`CounterCell[]`中随机选择一个进行CAS累加。
## size
`size = baseCount + sum(CounterCell[])`
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
4. **优化不足**：`ReentrantLock`是JUC层面的锁实现，而`synchronized`能够在JVM层面上进行优化。
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
`TreeBin`：在`ConcurrentHashMap`中，桶的首节点不是`TreeNode`，而是`TreeBin`。`TreeBin`既维护着红黑树，也维护着原有的链表。`TreeBin`的Hash固定为-2。
原因：
1. 锁是对桶的首节点进行的，如果写过程桶的首节点变了，可能会有多个线程同时写。
2. 如果无锁地读取正在进行调整的红黑树，可能会读取出错。
```java
static final class TreeBin<K,V> extends Node<K,V> {
    TreeNode<K,V> root;           // 指向红黑树的根节点
    volatile TreeNode<K,V> first; // 指向双向链表的头节点
    volatile Thread waiter;       // 等待写锁的线程
    volatile int lockState;       // 核心控制状态，用位运算模拟的读写锁
    
    // lockState 的状态定义
    static final int WRITER = 1;  // 二进制 001，表示写锁被占用
    static final int WAITER = 2;  // 二进制 010，表示有线程在等待写锁
    static final int READER = 4;  // 二进制 100，读锁状态，每增加一个读线程，lockState += 4
}
```
**写操作**：
1. 外层已经用`synchronized(TreeBin)`锁住了桶，因此同一时间只有一个写进程。
2. 检查`lockState`是否有读锁（`READER`）：
	1. 有读锁：通过CAS将`lockState`设为`WAITER`，然后调用`LockSupport.park()`挂起线程，等待读线程唤醒。
	2. 没有读锁：通过CAS将`lockState`设为`WRITER`，然后进行插入/删除等操作。
**读操作**：
1. 检查`lockState`是否有写锁或等待锁（`WRITER`和`WAITER`）：
	1. 有写锁或等待锁：退化为查询链表`first`。
	2. 没有写锁也没有等待锁：
		1. 通过CAS将`lockState += READER`。
		2. 遍历红黑树进行查找。
		3. 查找完成后通过CAS`lockState -= READER`。
		4. 若是最后一个读线程且有写线程在等待，调用`LockSupport.unpark()`唤醒写线程。
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
	1. **若数组未初始化**：调用`initTable()`，通过CAS将`sizeCtl`设为-1，成功的进行初始化，失败的自旋等待。
	2. **定位桶**：`(n - 1) & hash`。
	3. **桶为空**：通过CAS将新节点插入到桶头部，成功的直接break，失败的自旋重试。
	4. **桶非空**：
		1. 使用synchronized锁住桶的首节点
		2. 双重检查防止修改同一个节点
		3. **桶是链表时**：遍历链表，覆盖旧值或插在尾部。
		4. **桶是红黑树时**：调用红黑树的插入。
	5. **桶的首节点hash为-1**：`(fh = f.hash) == MOVED`，即为`ForwardingNode`，表示正在扩容，调用`helpTransfer()`协助扩容。
4. **检查树化**：如果链表节点数达到阈值（默认 8），且数组容量≥64，转为红黑树。
5. **更新计数和扩容检查**：调用`addCount()`更新计数并检查是否需要扩容。
## transfer
采用了分治思想，将扩容任务按区间划分，每个线程认领一部分桶（默认最少为16个），然后从尾部向头部逐个桶开始开始迁移。
1. **计算步长**：`stride = (NCPU > 1) ? (n >>> 3) / NCPU : n`
2. **创建新数组**：`Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n << 1]`
3. **认领区间**：`transferIndex = n`（原数组长度），线程通过CAS让`transferIndex -= stride`，认领自己的迁移区间，失败的自旋重试。
4. **迁移**：按区间内下标从大到小进行迁移，对于每一个桶：
	1. 桶为空 `(tab[i] == null)`：通过CAS将桶设为`ForwardingNode`，失败则自旋重试。
	2. 
## addCount
采用了`LongAdder`的分段累加思想：
CAS成功的更新全局基础变量`baseCount`。
CAS失败，存在并发竞争，在`CounterCell[]`中随机选择一个进行CAS累加。
## size
`size = baseCount + sum(CounterCell[])`
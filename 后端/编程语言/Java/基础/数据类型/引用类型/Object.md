# equals()
---
equals()是 Object 类里定义的一个方法，默认实现为比较两个对象的地址是否相同，而常用的基本数据类型的包装类以及`String`都重写了`equals`方法，使其比较两个对象的内容是否相同。
```java
Object a = new Integer(7777);
Object b = new Integer(7777);
System.out.print(a.equals(b)); // true
```
# 问题
---
## `==`与`euqals`的区别
对于基本数据类型，`==`比较的是数值。
对于引用类型：
- `==`比较的是两个变量指向的对象地址是否相同。
- `equals()`在常用的类中都重写为了比较两个对象的内容是否相同。
## 为什么重写 `equals()`必须要重写`hashCode()`
哈希容器的契约是：`equals()`相等的对象，`hashCode()`必须相等。
因为哈希集合等先通过`hashCode()`找到桶，再通过`equals()`在桶内寻找元素。
如果只重写`equals`而不重写`hashCode`，逻辑上相同的两个对象hashCode不同，会导致hashmapput了却get不到，hashset存在多个相同元素的情况。
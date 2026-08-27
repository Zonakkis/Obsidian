# 无参重载
```java
boolean tryLock();
```
- **作用**：尝试立即获取锁。
- **返回结果**：
	- 锁不存在或锁属于当前线程，返回true。
	- 锁被其他线程占用，返回false。
- **重试**：不重试。`waitTime`参数指定为-1。
- **看门狗**：生效。`leaseTime`参数指定为-1（即默认自动过期时间30秒）。
# 带等待时间的重载
```java
boolean tryLock(long waitTime, TimeUnit unit) throws InterruptedException;
```
- **作用**：在指定的`waitTime`内尝试获取锁。
- **返回结果**：
	- 锁不存在或锁属于当前线程，返回true。
	- 锁被其他线程占用，进行阻塞等待，直到超过`waitTime`，如果仍然没有获取到锁，返回false。
- **重试**：在指定的`waitTime`内重试。
- **看门狗**：生效。`leaseTime`参数指定为-1（即默认过期时间30秒）。
# 带等待时间和自动过期时间的重载
```java
boolean tryLock(long waitTime, long leaseTime, TimeUnit unit) throws InterruptedException;
```
- **作用**：在指定的`waitTime`内尝试获取锁，如果获取到锁，经过`leaseTime`后锁自动释放。
- **返回结果**：
	- 锁不存在或锁属于当前线程，返回true。
	- 锁被其他线程占用，进行阻塞等待，直到超过`waitTime`，如果仍然没有获取到锁，返回false。
- **重试**：在指定的`waitTime`内重试。
- **看门狗**：不生效。
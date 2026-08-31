为了提高锁的可用性，可以使用主从或者集群模式。但是这样就会遇到主从一致性的问题，即主从同步时同步未完成主节点就宕机了。此时从节点升级为主节点，但没有同步到锁，其他线程就可能获取到锁，导致出现两把锁。
对此，Redisson的做法是，使用多个主节点，获取锁时需要所有主节点都获取到锁才算成功。
```java
public boolean tryLock(long waitTime, long leaseTime, TimeUnit unit) throws InterruptedException {  
	long newLeaseTime = -1;  
	// leaseTime大于0，说明不使用看门狗
	// waitTime是最大等待时间，所以临时把获取到的锁都设置为waitTime*2，这样waitTime内获取完可以保证前面获取的锁不会过期
	if (leaseTime > 0) {  
		if (waitTime > 0) {  
			newLeaseTime = unit.toMillis(waitTime)*2;  
		} else {  
			newLeaseTime = unit.toMillis(leaseTime);  
		}  
	}  
	  
	long time = System.currentTimeMillis();  
	long remainTime = -1;  
	if (waitTime > 0) {  
		remainTime = unit.toMillis(waitTime);  
	}  
	long lockWaitTime = calcLockWaitTime(remainTime);  
	  
	// 获取锁最大失败数
	int failedLocksLimit = failedLocksLimit();  
	List<RLock> acquiredLocks = new ArrayList<>(locks.size());  
	// 遍历锁节点
	for (ListIterator<RLock> iterator = locks.listIterator(); iterator.hasNext();) {  
		RLock lock = iterator.next();  
		boolean lockAcquired;  
		try {  
			if (waitTime <= 0 && leaseTime <= 0) {  
				lockAcquired = lock.tryLock();
			} else {  
				long awaitTime = Math.min(lockWaitTime, remainTime);  // 剩余等待时间不够单个锁时间了就用剩余等待时间
				lockAcquired = lock.tryLock(awaitTime, newLeaseTime, TimeUnit.MILLISECONDS);  
			}  
		} catch (RedisResponseTimeoutException e) {  
			// 超时了说明锁的状态未知，为了防止死锁强行释放掉这把锁
			unlockInner(Arrays.asList(lock));  
			lockAcquired = false;  
		} catch (Exception e) {  
			lockAcquired = false;  
		}  
		  
		if (lockAcquired) {  
			acquiredLocks.add(lock);  // 先存起来
		} else {  
			if (locks.size() - acquiredLocks.size() == failedLocksLimit()) {  
				break;  // 失败数达到上限
			}  

			if (failedLocksLimit == 0) {  // 普通MultiLock模式：失败一把就代表全部失败
				unlockInner(acquiredLocks);  // 将之前获取到的锁全部释放，防止死锁
				if (waitTime <= 0) {  // 不重试
					return false;  
				}  
				failedLocksLimit = failedLocksLimit();  
				acquiredLocks.clear();  // 清空获取的锁
				
				// 重置迭代器
				while (iterator.hasPrevious()) {
					iterator.previous();  
				}  
			} else {  
				failedLocksLimit--;  
			}  
		}  
		
		// 扣减获取锁的时间并判断是否超时
		if (remainTime > 0) {  
			remainTime -= System.currentTimeMillis() - time;  
			time = System.currentTimeMillis();  
			if (remainTime <= 0) {  
				unlockInner(acquiredLocks);  
				return false;  
			}  
		}  
	}  
	
	// 如果有waitTime，前面将锁设置成lwaitTime*2
	// 如果没有waitTime，此时第一把锁已经消耗了一定leaseTime
	// 因此统一设置为leaseTime
	if (leaseTime > 0) {  
		acquiredLocks.stream()  
				.map(l -> (RedissonBaseLock) l)  
				.map(l -> l.expireAsync(unit.toMillis(leaseTime), TimeUnit.MILLISECONDS))  
				.forEach(f -> f.toCompletableFuture().join());  
	}  
	  
	return true;  
}
```
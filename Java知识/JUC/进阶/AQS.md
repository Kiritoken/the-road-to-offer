# AQS


## AbstractOwnableSynchronizer
内部标记了一个当前占有锁的线程,参数为null表示没有线程拥有访问权限
```java

    /**
     * The current owner of exclusive mode synchronization.
     */
    private transient Thread exclusiveOwnerThread;
```

# AbstractQueuedSynchronizer
是一个线程同步框架，JUC中的许多锁都是基于这个框架进行实现，其内部通过state变量判断锁的获取于释放，同时内部维护一个FIFO队列保存当前等待锁的线程信息，支持共享和独占两种模式

[https://www.cnblogs.com/waterystone/p/4920797.html](https://www.cnblogs.com/waterystone/p/4920797.html)

## ReentrantLock 公平于非公平体现

+ 非公平 获取锁的时候会先使用cas设置state，如果设置成功，直接获取到锁
```java
final void lock() {
            if (compareAndSetState(0, 1))
                setExclusiveOwnerThread(Thread.currentThread());
            else
                acquire(1);
        }
```

+ 公平
```java
    final void lock() {
        acquire(1);
    }
```
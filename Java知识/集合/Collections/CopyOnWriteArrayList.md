# CopyOnWriteArrayList

在很多应用场景中，读操作可能会远远大于写操作。由于读操作根本不会修改原有的数据，因此如果每次读取都进行加锁操作，其实是一种资源浪费。我们应该允许多个线程同时访问 List 的内部数据，毕竟读操作是线程安全的。

+  每次添加都会加锁，复制一份，所以适合读多写少的场景，初始化的时候传递Collection<>
```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}
```

使用volatile 保证多线程下的可见性
```java
    /** The array, accessed only via getArray/setArray. */
    private transient volatile Object[] array;
```

+ 与vector不同的是只有修改操作使用的是可重复入锁进行加锁，而Vector所有方法都用了Synchronized加锁
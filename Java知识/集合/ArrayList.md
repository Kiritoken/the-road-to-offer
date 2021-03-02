# ArrayList

+ 初始容量在不指定的情况下为10，后面如果容量不够每次扩容1.5倍
```java
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```


+ ArrayList中elementData为什么被transient修饰
    + 应为elementData数组长度可能和实际长度不一致，因为每次扩容的长度是1.5倍
```java

    private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException{
        // Write out element count, and any hidden stuff
        int expectedModCount = modCount;
        s.defaultWriteObject();

        // Write out size as capacity for behavioural compatibility with clone()
        s.writeInt(size);

        // Write out all elements in the proper order.
        for (int i=0; i<size; i++) {
            s.writeObject(elementData[i]);
        }

        if (modCount != expectedModCount) {
            throw new ConcurrentModificationException();
        }
    }

```


+ ConcurrentModificationException
    + 主要是fast-fail思想，使用迭代器的时候不允许并发修改，通过expectedModCount和modCount是否相等进行快速判断

```java
final void checkForComodification() {
    if (modCount != expectedModCount)
        throw new ConcurrentModificationException();
}
```



## Vector 和 ArrayList 的区别
+ Vector 是线程安全的，内部的方法使用synchronized 进行同步，而ArrayList不是线程安全的
+ vector 可以指定每次扩容的大小，如果没有指定默认扩充为原来的2倍，而ArrayList默认为1.5倍
```java
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                        capacityIncrement : oldCapacity);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```


## Enumeration和Iterator的主要区别
+ java中的du集合类都提供了zhi返回Iterator的方法，就是迭代dao器，它和Enumeration（枚举）的zhuan主要区别其实shu就是Iterator可以删除元素，但是Enumration却不能。
+ 还有一点要注意的就是，使 用Iterator来遍历集合时，应使用Iterator的remove()方法来删除集合中的元素，使用集合的remove()方法将抛出ConcurrentModificationException异常。
+ Enumeration 接口的功能与 Iterator 接口的功能是重复的。此外，Iterator 接口添加了一个可选的移除操作，并使用较短的方法名。新的实现应该优先考虑使用 Iterator 接口而不是Enumeration 接口。


## 浅拷贝
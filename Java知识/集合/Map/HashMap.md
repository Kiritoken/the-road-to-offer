# HashMap

## 底层实现原理
Node[]
```java

    /**
     * Implements Map.get and related methods.
     *
     * @param hash hash for key
     * @param key the key
     * @return the node, or null if none
     */
    final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
            if ((e = first.next) != null) {
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }
```

+ 计算Node数组所在位置的方法为 hashCode与数组长度&
```java
(n - 1) & hash
```


    默认初始化容量：static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

    数组最大容量：static final int MAXIMUM_CAPACITY = 1 << 30;

    默认加载因子：static final float DEFAULT_LOAD_FACTOR = 0.75f;

    链表调整为红黑树的链表长度阈值（JDK1.8）：static final int TREEIFY_THRESHOLD = 8;

    红黑树调整为链表的链表长度阈值（JDK1.8）：static final int UNTREEIFY_THRESHOLD = 6;

    链表调整为红黑树的数组最小阈值（JDK1.8）：static final int MIN_TREEIFY_CAPACITY = 64;

    HashMap存储的数组：transient Node<K,V>[] table;

    HashMap存储的元素个数：transient int size;


+ 如果size大小超过阈值（装载因子 * capacity），并且Node数组长度大于64，这时候才会把数组长度扩容为原来的两杯，并重新hash
+ 由于数组的长度总是2的倍数，所以原来的位置rehash之后只会有两种可能
    + 还在原来的位置
    + 原来的位置 + oldCapacity


## HashMap 的长度为什么是2的幂次方
为了能让 HashMap 存取高效，尽量较少碰撞，也就是要尽量把数据分配均匀。我们上面也讲到了过了，Hash 值的范围值-2147483648到2147483647，前后加起来大概40亿的映射空间，只要哈希函数映射得比较均匀松散，一般应用是很难出现碰撞的。但问题是一个40亿长度的数组，内存是放不下的。所以这个散列值是不能直接拿来用的。用之前还要先做对数组的长度取模运算，得到的余数才能用来要存放的位置也就是对应的数组下标。这个数组下标的计算方法是“ (n - 1) & hash”。（n代表数组长度）。这也就解释了 HashMap 的长度为什么是2的幂次方。

+ 我们首先可能会想到采用%取余的操作来实现。但是，重点来了：“取余(%)操作中如果除数是2的幂次则等价于与其除数减一的与(&)操作（也就是说 hash%length==hash&(length-1)的前提是 length 是2的 n 次方；）。” 并且 采用二进制位操作 &，相对于%能够提高运算效率，这就解释了 HashMap 的长度为什么是2的幂次方。

## LinkedHashMap
node 中新增了prev next指针
```java
    Node<K,V> newNode(int hash, K key, V value, Node<K,V> e) {
        LinkedHashMap.Entry<K,V> p =
            new LinkedHashMap.Entry<K,V>(hash, key, value, e);
        linkNodeLast(p);
        return p;
    }


static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;    
```

## HashMap resize() 为什么要么在原位置，要么在原位置+ oldCapacity
[https://blog.csdn.net/qq32933432/article/details/86668385?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-6&spm=1001.2101.3001.4242](https://blog.csdn.net/qq32933432/article/details/86668385?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-6&spm=1001.2101.3001.4242)




## JDK7 HashMap 多线程下的循环链表问题
[https://coolshell.cn/articles/9606.html](https://coolshell.cn/articles/9606.html)
+ 主要原因还是在于扩容后的rehash中，多线程操作后形成循环链表，再get一个不存在的key时会无限循环loop


## jdk1.8 中是如何解决扩容时循环链表的问题
[https://blog.csdn.net/someone77/article/details/108109456](https://blog.csdn.net/someone77/article/details/108109456)

+ 通过局部变量保存新位置的链表，最后赋值
+ 与jdk7 的实现方式不同，不会颠倒链表的顺序
```java
 if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
```


---
+ for 增强实际上的字节码使用的是迭代器iterator
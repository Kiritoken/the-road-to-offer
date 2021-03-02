# Map



+ Map
    + Hashtable (线程安全的，效率低，不能存储null的key和value)
        + Properties 常用来处理配置文件
    + HashMap（线程不安全的，效率高，可以存储null 的key 和value）
        + LinkedHashMap 保证遍历的时候是按照添加的顺序，在原有HashMap基础上，添加了一对指针，指向前一个和后一个元素，对于频繁的遍历操作，效率高于HashMap
    + SortedMap
        + TreeMap 可以按照key排序 ***底层使用的是红黑树***



## HashMap
+ 1.7 数组+ 链表
+ 1.8 数组+ 链表+ 红黑树


## 面试题
+ HashMap底层原理

+ HashMap和Hashtable异同

+ CurrentHashMap 与Hahstable异同
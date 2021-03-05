# synchronized底层原理

[https://www.cnblogs.com/aspirant/p/11470858.html](https://www.cnblogs.com/aspirant/p/11470858.html)


如何判断锁的是谁!永远的知道什么锁，锁到底锁的是谁!



+ synchronized 锁对象方法，锁的是这个对象，即这个方法的调用者
+ 普通方法不存在锁抢占问题
+ 不同对象是不同的锁
+ 静态方法锁的是class，锁的是模板



## synchronized 和 lock的区别

+ synchronized是java关键字
+ lock是一个类
+ synchronized无法判断获取锁的状态，Lock可以判断是否获取到了锁
+ synchronized 会自动释放锁
+ lock必须要手动释放锁，不然会造成死锁
+ lock可以设置公平非公平，可重入
+ synchronized 非公平的，可重入的
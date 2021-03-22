# MVCC

[https://www.bilibili.com/video/BV1YJ411J7vb/?spm_id_from=333.788.recommend_more_video.-1](https://www.bilibili.com/video/BV1YJ411J7vb/?spm_id_from=333.788.recommend_more_video.-1)


[https://blog.csdn.net/filling_l/article/details/112854716](https://blog.csdn.net/filling_l/article/details/112854716)

![](../images/MVCC.png)

[https://dev.mysql.com/doc/refman/8.0/en/innodb-multi-versioning.html](https://dev.mysql.com/doc/refman/8.0/en/innodb-multi-versioning.html)

+ RR 和 RC 两种不同隔离级别在MVCC中的实现方式不同
    + RC 读已提交是每次select都会生成新的read-view
    + RR 可重复度在事务第一次select时会生成一个read-view 之后的select使用的都是第一个read-view

+ MVCC
    + Row里还存储两个字段，一个存储最近修改这行数据的事务ID，一个是这行数据的undolog的日志链
    + 事务读的时候会生成一个read-view 一致性视图，里面存储了当前数据库没有提交的事务ID，由小到达排列
    + 事务根据最新的修改这条事务的ID和read-view里的数据进行对比，有几种可能：
        + 比最小的要小，证明是以及提交了的事务修改的，可以直接读
        + 比最大的还要大，证明是在当前事务读之后有一个事务修改提交了，那么也不能读，因为是在当前事务之后的，遍历undo log 日志链判断下一个日志
        + 范围在这中间：
            + 如果存在这未提交事务的id里，且不是自己修改的，那么不可见
            + 如果不在这里面，证明是以及提交了的事务修改的，那么可见


+ RR 可重复读，就是使用的是第一次产生的read-view
+ RC 读已提交，就是每次读都会产生一个新的read-view，那么里面的未提交事务就会更新，判断的时候就会读到最新的已提交的事务  
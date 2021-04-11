# rehash过程


## 什么时候进行rehash扩容，当key的数量等于hashtable长度时，进行扩容


## 渐进式扩容
+ 扩容时，将rehashIdx 下标置为0，代表从hashtable下标0开始
+ 将原先0下标的数据重新rehash到table[1]上，rehashIdx++
+ 之后对redis的crud操作都针对table[rehashIdx]进行rehash,然后将更新删除的操作


+ rehash时的增删改查操作
    + 增，直接在新的table增
    + 查，现在原来的查，查不到再在新的table查，因为原来的查不到可能是因为已经迁移过了
    + 删，一样的先查原来的，再查最新的table
    + 改，一样的先查原来的，再查最新的table

所以增只会在新的上面增，不会冗余两份
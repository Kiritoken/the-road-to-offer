# Set

## HashSet
特点：无序、无下标、元素不可重复。
方法：全部继承自Collection中的方法。

+ 底层是一个HashMap
+ 先根据hashCode判断位置是否相同，如果hashCode的位置相同，再使用equals判断，如果还不相同，实现为链表（红黑树）
+ 所以必须要重写hashCode 和 equals方法

    根据hashCode计算保存的位置，如果位置为空，则直接保存，否则执行第二步。
    执行equals方法，如果方法返回true，则认为是重复，拒绝存储，否则形成链表。


## 关于hashCode的计算为什么使用31

```java
public static int hashCode(Object a[]) {
    if (a == null)
        return 0;

    int result = 1;

    for (Object element : a)
        result = 31 * result + (element == null ? 0 : element.hashCode());

    return result;
}
```

+ 31是质子数中一个“不大不小”的存在，如果你使用的是一个如2的较小质数，那么得出的乘积会在一个很小的范围，很容易造成哈希值的冲突。而如果选择一个100以上的质数，得出的哈希值会超出int的最大范围，这两种都不合适。而如果对超过 50,000 个英文单词（由两个不同版本的 Unix 字典合并而成）进行 hash code 运算，并使用常数 31, 33, 37, 39 和 41 作为乘子，每个常数算出的哈希值冲突数都小于7个（国外大神做的测试），那么这几个数就被作为生成hashCode值得备选乘数了

+ 31可以被JVM优化

    31 = （i << 5 ） - i
    * 左移 << : 左边的最高位丢弃，右边补全0（把 << 左边的数据*2的移动次幂）。
    * 右移 >> : 把>>左边的数据/2的移动次幂。
    * 无符号右移 >>> : 无论最高位是0还是1，左边补齐0。 　　


## TreeSet

+ 底层是一个红黑树
+ 基于排序顺序实现元素不重复
+ 元素必须实现Camparable接口
+ 通过compareTo判断
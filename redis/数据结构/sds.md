# sds


simple dynamic string 简单动态字符串


## 为什么redis不直接使用c语言的字符串，而要定义SDS？

+ O(1)的时间复杂度获取长度
    + c语言的strlen是通过遍历\0获取长度
    + sds封装的结构体可以直接通过len获取长度

+ 预分配内存空间，减少内存分配次数
    + sds在分配空间的时候额外申请了一些空间，以避免下次修改的时候又要重写申请内存
    + 缩小的时候不用管 
    + 在频繁修改的情况下可以保证效率

+ 保存二进制数据
    + 字符串中出现\0结符在c语言中会出现问题
    + 利用len解决这个问题
    + 二进制安全的数据结构


+ 兼容c的字符串操作
    + ***会在字符串最后加一个\0***
    + 比如同样可以利用strcmp的函数

剩余空间不够新的长度的时候会扩容

## 扩容机制
+ 长度小于1M，每次扩成新的长度的两倍
+ 长度大于1M，每次增加1M的空间，1024char？ C语言一个char占一个字节
+ 字符串最大长度512M


## redis 3.2之后的改进
原先的数据结构
int lenght表示字符串长度，int占4个字节，可以表示20亿的长度

针对不同的长度使用不同的len，用更少的空间确定字符串的长度

```java
/* 
 1. len  : 字符串长度/字符数组中已使用的字符数量
 2. alloc: 字符串已分配字节数，不同于free，记录的是为buf分配的总长度
 3. flags：标识当前结构体的类型（0~4）
 4. buf  : 字符数组,用来保存字符串
 */
/* Note: sdshdr5 is never used, we just access the flags byte directly.
 5. However is here to document the layout of type 5 SDS strings. */
struct __attribute__ ((__packed__)) sdshdr5 {
    unsigned char flags; /* 3 lsb of type, and 5 msb of string length   前3个bit位用来表示类型，后5个bit用来表示字符床长度 范围2^5 -1*/
    char buf[];
};

// 长度为8个字节
struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len; /* used */
    uint8_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr16 {
    uint16_t len; /* used */
    uint16_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr32 {
    uint32_t len; /* used */
    uint32_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr64 {
    uint64_t len; /* used */
    uint64_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
```

## redis使用头插法解决hash冲突
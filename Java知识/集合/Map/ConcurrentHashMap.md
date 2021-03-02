# ConcurrentHashMap

## 面试
![](../../images/consurrentHashMap_1.png)


## JDK7中的实现方式
+ Segment[]
    + HashEntry[]

```java
// segement数量 代码中初始化的时候会限制segement的数量为大于等于这个数的最大2的幂
private static final int DEFAULT_CONCURRENCY_LEVEL = 16;
```

+ 代码中的segement[0]在初始化的时候已经创建，之后的segement都是null，在第一次操作的时候会通过segement[0]进行初始化，属于设计模式中的原型设计模式，防止创建某些需要消耗资源计算的操作，直接实现Clonable接口，重写clone方法，实现原型模式


+ Unsafe 静态方法getUnsafe会判断调用类的类加载器是否为null，即是否是引导类加载器，如果不是则抛出异常
```
    @CallerSensitive
    public static Unsafe getUnsafe() {
        Class var0 = Reflection.getCallerClass();
        if (!VM.isSystemDomainLoader(var0.getClassLoader())) {
            throw new SecurityException("Unsafe");
        } else {
            return theUnsafe;
        }
    }
```
+ 可以通过反射的方法拿到Unsafe的静态实例对象
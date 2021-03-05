# Callable

+ 可以有返回值
+ 可以抛出异常
+ call()


## 为什么Runable不能抛出异常？
+ 这个和重写有关，因为Runable定义没有抛出异常，所以重写的子类不能抛出异常
+ 子类重写父类的方法，异常必须小于等于父类的异常
```java
public abstract void run();
```

## FutureTask异步通信获取结果

***FutureTask可以保证任务只执行一次，并且可以异步获取执行结果，此特点可让其作为缓存被使用***
+ 这个特性是通过FutureTask内的state状态实现的，如果状态是完成的状态（> complete）则会直接获取结果
```java
private V report(int s) throws ExecutionException {
    Object x = outcome;
    if (s == NORMAL)
        return (V)x;
    if (s >= CANCELLED)
        throw new CancellationException();
    throw new ExecutionException((Throwable)x);
}
```


---

+ 和ConcurrentHashMap搭配使用 注意使用putIfAbsent
```java
static private final Map<Integer, FutureTask<Integer>> squareCache=new ConcurrentHashMap();

    /**
     * 计算某数的立方
     * @param cache 是否使用缓存，使用的话表示：查询缓存尝试从缓存获取；将Integer对应的
     *
     *     1. 查看是否存在缓存中，存在则阻塞获取任务结果；
     *     2. 不存在缓存中，则构造任务，并使用putIfAbsent放进缓存；
     *     3. 如果是第一次放进缓存map，即putIfAbsent返回null，则执行任务，并返回结果(第一个步骤没有，但是第二个步骤可能是几个线程并行执行）；
     */
    static Integer cal(final Integer fac,final boolean cache) throws Exception {
        if(cache){
            //查看是否在缓存中，有则阻塞获取计算结果
            FutureTask<Integer> calTask=squareCache.get(fac);
            if(calTask!=null){
                return calTask.get();
            }

            //不再缓存中则构造任务(可能多个线程并发执行),并放进缓存中，然后(如果是第一次放进)执行并返回结果
            //fixme 如果有并发线程也构造了任务，并尝试放进map缓存，则返回值不为null，直接跳过if中的计算逻辑，异步获取计算结果
            calTask=new FutureTask<>(new Calcutor(fac));
            FutureTask<Integer> exitedTask=squareCache.putIfAbsent(fac,calTask);
            if(exitedTask==null){
                exitedTask=calTask;
                exitedTask.run();
            }
            return calTask.get();
        }else{
            //可改进：将计算过程抽象出去使用多线程调用
            return new Calcutor(fac).call();
        }
    }

    public static void main(String[] args) throws Exception {
        ExecutorService executorService=Executors.newFixedThreadPool(1);

        long t1=System.currentTimeMillis();
        Random random=new Random(100);
        for (int i = 0; i < 100; i++) {
            int fac=random.nextInt(30);
            System.out.println(i+":"+cal(fac, true));
        }
        System.out.println("cost:"+(System.currentTimeMillis()-t1));
    }
```
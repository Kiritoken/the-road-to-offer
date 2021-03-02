# 命令


+ jps
```shell
jps # 查看jvm进程 和操作系统pid一致

jps -q # 只显示pid

jps -l # 显示详细信息 mainclass

jps -m # 列出传递给main方法的启动参数

jps -v # 列出jvm启动参数

jps hostId # 远程
```


+ jstat 查看JVM统计信息

    在没有图形化界面时可以使用的工具

```shell
jstat -class JVMID # 查看类加载数量，卸载数量占用字节数等信息

jstat -class JVMID  1000 # 每隔1秒打印信息

jstat -class JVMID  1000  10 # 每隔1秒打印信息 一共10次


jstat -printcompilation # 输出被JIT编译过的方法


jstat -gc # 输出GC信息


jstat -gcutil 44969 1000 # 展示堆空间占比信息
```


+ jinfo  查看虚拟机参数配置 也可以修改虚拟机参数配置

```shell
jinfo -flags pid
jinfo -flags +XX:   # 修改 
```


+ jmap 导出内存映像文件和内存使用情况

```
jmap -dump

jmap -dump:format=b,file=D:\1.hprof 123

jmap -heap pid
```


+ jhat 分析dump文件

+ ***jstack 打印jvm中线程快照***
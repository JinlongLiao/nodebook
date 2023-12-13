# JDK8 及以前使用的是 PS Scavenge 和 PS MarkSweep，JDK9 及之后使用的是 G1 收集器。

使用 -XX:+PrintCommandLineFlags 可以查询 Java 启动时定义好的变量，用它可以看到使用的 GC。
---
```text
java -XX:+PrintCommandLineFlags -version
-XX:ConcGCThreads=3 -XX:G1ConcRefinementThreads=13 -XX:GCDrainStackTargetSize=64 -XX:InitialHeapSize=257798976 -XX:MarkStackSize=4194304 -XX:MaxHeapSize=4124783616 -XX:MinHeapSize=6815736 -XX:+PrintCommandLineFlags -XX:ReservedCodeCacheSize=251658240 -XX:+SegmentedCodeCache -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation
java version "16.0.2" 2021-07-20
Java(TM) SE Runtime Environment (build 16.0.2+7-67)
Java HotSpot(TM) 64-Bit Server VM (build 16.0.2+7-67, mixed mode, sharing)
```

JDK5 是 Java 的重大里程碑版本，此前所有 JDK 都称为 JDK 1.x，自第五版本起，改为 JDKX 方式命名。
---
```text
java.exe -XX:+PrintCommandLineFlags -version
-XX:MaxHeapSize=1073741824 
-XX:+UseParallelGC
-XX:+PrintCommandLineFlags 
java version "1.5.0_22"
Java(TM) 2 Runtime Environment, Standard Edition (build 1.5.0_22-b03)
Java HotSpot(TM) 64-Bit Server VM (build 1.5.0_22-b03, mixed mode)
```
- MaxHeapSize：最大堆大小，即 Xmx，1073741824 = 1G，可以直接指定以 g/m/k 为单位。
- +UseParallelGC：使用 Parallel 垃圾收集器，即 PS Scavenge 和 PS MarkSweep。

 ***默认gc***
  
```text
PS Scavenge
PS MarkSweep
``
 
JDK6
---

```text
java.exe -XX:+PrintCommandLineFlags -version
-XX:InitialHeapSize=257798976 
-XX:MaxHeapSize=4124783616 
-XX:ParallelGCThreads=13 
-XX:+UseCompressedOops 
-XX:-UseLargePagesIndividualAllocation 
-XX:+UseParallelGC
-XX:+PrintCommandLineFlags 
java version "1.6.0_45"
Java(TM) SE Runtime Environment (build 1.6.0_45-b06)
Java HotSpot(TM) 64-Bit Server VM (build 20.45-b01, mixed mode) 
```
- InitialHeapSize：初始堆大小，即 Xms，257798976 = 245.8M，可以直接指定以 g/m/k 为单位。
- ParallelGCThreads：并行 GC 线程数量，只要是多线程 GC 就可以设置。
默认情况下，当 CPU 数量小于8， ParallelGCThreads 的值等于 CPU 数量，当 CPU 数量大于 8 时，根据公式计算得出：ParallelGCThreads = 8+((N-8)×5/8) = 5×N/8+3，这里的 CPU 和 N 指逻辑 CPU 的数量，测试电脑为 16 核心数，因此计算结果为 13 符合。
- +UseCompressedOops ：启用压缩普通对象指针，减少指针内存使用量。 [指针压缩](https://juejin.cn/post/6844903768077647880)

 ***默认gc***
  
```text
PS Scavenge
PS MarkSweep
``


JDK7
```text
java.exe -XX:+PrintCommandLineFlags -version
-XX:InitialHeapSize=257798976 
-XX:MaxHeapSize=4124783616 
-XX:+UseCompressedOops
-XX:-UseLargePagesIndividualAllocation 
-XX:+UseParallelGC
-XX:+PrintCommandLineFlags 
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode) 
```

  ***默认gc***
  
```text
PS Scavenge
PS MarkSweep
``
JDK8
---
```text
ava.exe -XX:+PrintCommandLineFlags -version
-XX:InitialHeapSize=257798976 
-XX:MaxHeapSize=4124783616
-XX:+UseCompressedClassPointers 
-XX:+UseCompressedOops 
-XX:-UseLargePagesIndividualAllocation 
-XX:+UseParallelGC
-XX:+PrintCommandLineFlags
java version "1.8.0_211"
Java(TM) SE Runtime Environment (build 1.8.0_211-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.211-b12, mixed mode)
```
- +UseCompressedClassPointers：启用压缩类型指针。
每个对象的对象头中都有一个指向所属类型的指针，在 64 位操作系统上默认是 8 个字节的，但开启压缩类型指针之后，可以压缩成 4 个字节，原理和 UseCompressedOops 一样。同时，开启压缩类型指针，必须先开启压缩对象指针。

 ***默认gc***
  
```text
PS Scavenge
PS MarkSweep
``
JDK9
---

```text
java.exe -XX:+PrintCommandLineFlags -version
-XX:InitialHeapSize=257798976 
-XX:MaxHeapSize=4124783616 
-XX:ReservedCodeCacheSize=251658240 
-XX:+SegmentedCodeCache 
-XX:+UseCompressedClassPointers 
-XX:+UseCompressedOops 
-XX:+UseG1GC
-XX:G1ConcRefinementThreads=13 
-XX:-UseLargePagesIndividualAllocation
-XX:+PrintCommandLineFlags 
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```


- ReservedCodeCacheSize：设置代码缓存最大值，251658240 = 240M。
代码缓存是一块独立的内存空间，常被人所忽视，用来存储 JIT 编译的本地代码，一旦代码缓存满了，就会停止 JIT 编译，此时 JVM 完全进入解释型执行。除了之前存储在代码缓存中的代码之外，其他代码不会再执行 JIT 优化，相当于解释型语言。速度会下降一个量级，因此代码缓存大小非常重要。
同时，Java 所使用的本地方法代码（JNI）也会存在代码缓存中。如果该缓存区满了，也会影响本地方法的执行效率。
与之对应的是 InitialCodeCacheSize 为起始代码缓存区大小。
- +SegmentedCodeCache：开启代码缓存分段初始化。
代码缓存区分为三块：NonNMethodCode、ProfiledCode 和 NonProfiledCode。如果未开启代码缓存分段初始化，这三个区域就会初始化成一个整体，如果开启了，就会初始化成分开的三个区域。
- +UseG1GC：启用 GC 垃圾收集器。
- G1ConcRefinementThreads：G1 缓冲区线程数。

   ***默认gc***
```text
G1 Young Generation
G1 Old Generation
``


JDK13
---
```text
java.exe -XX:+PrintCommandLineFlags -version
-XX:G1ConcRefinementThreads=13 
-XX:GCDrainStackTargetSize=64 
-XX:InitialHeapSize=257798976 
-XX:MaxHeapSize=4124783616 
-XX:MinHeapSize=6815736 
-XX:ReservedCodeCacheSize=251658240 
-XX:+SegmentedCodeCache 
-XX:+UseCompressedClassPointers 
-XX:+UseCompressedOops 
-XX:+UseG1GC 
-XX:-UseLargePagesIndividualAllocation
-XX:+PrintCommandLineFlags 
java version "13.0.2" 2020-01-14
Java(TM) SE Runtime Environment (build 13.0.2+8)
Java HotSpot(TM) 64-Bit Server VM (build 13.0.2+8, mixed mode, sharing)
```

- MinHeapSize：堆区占用的最小内存，6815736 = 6.5M

   ***默认gc***
```text
G1 Young Generation
G1 Old Generation
``


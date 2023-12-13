- java -XX:+PrintCommandLineFlags -version 默认的JVM 配置
- java -XX:+PrintFlagsFinal -version JVM 支持的参数

参数名称|含义|默认值／备注
-|-|-| 
-Xms|初始堆大小|物理内存的1/64(<1GB) 默认(MinHeapFreeRatio参数可以调整)空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制
-Xmx|最大堆大小|物理内存的1/4(<1GB) 默认(MaxHeapFreeRatio参数可以调整)空余堆内存大于70%时，JVM会减少堆直到 -Xms的最小限制
-Xmn|新生代大小(jdk 1.4或以上版本)|增大新生代后，将会减小老年代大小。此值对系统性能影响较大。Sun官方推荐配置为整个堆的3/8
-Xss|每个线程的堆栈大小|JDK5.0以后每个线程堆栈大小为1M，以前每个线程堆栈大小为256K，可以带 K, M 或 G单位
-XX:ThreadStackSize|同上|0代表使用默认值，不能带单位
-XX:PermSize|设置永久代初始值|物理内存的1/64
-XX:MaxPermSize|设置永久代最大值|物理内存的1/4
-XX:NewRatio|新生代(包括Eden和两个Survivor区)与老年代的比值(除去永久代)|-XX:NewRatio=4表示新生代与老年代所占比值为1:4，新生代占整个堆栈的1/5，Xms=Xmx并且设置了Xmn的情况下，该参数不需要进行设置。
-XX:SurvivorRatio|Eden区与Survivor区的大小比值|设置为8，则两个Survivor区与一个Eden区的比值为2:8,一个Survivor区占整个新生代的1/10
-XX:LargePageSizeInBytes|内存页的大小不可设置过大， 会影响Perm的大小|=128m
-XX:+UseFastAccessorMethods|原始类型的快速优化|
-XX:+DisableExplicitGC|关闭System.gc()|这个参数需要严格的测试
-XX:MaxTenuringThreshold|垃圾最大年龄|如果设置为0的话，则新生代对象不经过Survivor区，直接进入老年代。对于老年代比较多的应用，可以提高效率。如果将此值设置为一个较大值，则新生代对象会在Survivor区进行多次复制，这样可以增加对象再新生代的存活时间，增加在新生代即被回收的概率，该参数只有在串行GC时才有效
-XX:+AggressiveOpts|加快编译|
-XX:+UseBiasedLocking|锁机制的性能改善|
-Xnoclassgc|禁用垃圾回收|
-XX:SoftRefLRUPolicyMSPerMB|每兆堆空闲空间中SoftReference的存活时间|1s
-XX:PretenureSizeThreshold|对象超过多大是直接在老年代分配|新生代采用Parallel Scavenge GC时无效，另一种直接在老年代分配的情况是大的数组对象，且数组中无外部引用对象.
-XX:TLABWasteTargetPercent|TLAB占eden区的百分比|1%
-XX:+CollectGen0First|FullGC时是否先YGC|false
-XX:+UseParallelGC|Full GC采用parallel MSC|见[GC参数][1]
-XX:+UseParNewGC|设置新生代为并行收集|可与CMS收集同时使用，JDK 5.0以上，JVM会根据系统配置自行设置，所以无需再设置此值
-XX:ParallelGCThreads|并行收集器的线程数|此值最好配置与处理器数目相等，同样适用于CMS
-XX:+UseParallelOldGC|老年代垃圾收集方式为并行收集(Parallel Compacting)|这个是JAVA 6出现的参数选项
-XX:MaxGCPauseMillis|每次新生代垃圾回收的最长时间(最大暂停时间)|如果无法满足此时间，JVM会自动调整新生代大小，以满足此值.
-XX:+UseAdaptiveSizePolicy|自动选择新生代区大小和相应的Survivor区比例|设置此选项后，并行收集器会自动选择新生代区大小和相应的Survivor区比例，以达到目标系统规定的最低相应时间或者收集频率等，此值建议使用并行收集器时，一直打开
-XX:GCTimeRatio|设置垃圾回收时间占程序运行时间的百分比|公式为1/(1+n)
-XX:+ScavengeBeforeFullGC|Full GC前调用YGC|true
-XX:+UseConcMarkSweepGC|使用CMS内存收集|测试中配置这个以后， -XX:NewRatio=4的配置失效了，原因不明，所以此时新生代大小最好用-Xmn设置
-XX:+AggressiveHeap|试图是使用大量的物理内存|长时间大内存使用的优化，能检查计算资源（内存， 处理器数量，至少需要256MB内存
-XX:CMSFullGCsBeforeCompaction|多少次后进行内存压缩|由于并发收集器不对内存空间进行压缩，整理，所以运行一段时间以后会产生“碎片”，使得运行效率降低
-XX:+CMSParallelRemarkEnabled|降低标记停顿|
-XX+UseCMSCompactAtFullCollection|在FullGC的时候对老年代的压缩|CMS是不会移动内存的， 因此这个非常容易产生碎片，导致内存不够用，因此内存的压缩这个时候就会被启用。增加这个参数是个好习惯。可能会影响性能，但是可以消除碎片
-XX:+UseCMSInitiatingOccupancyOnly|使用手动定义初始化定义开始CMS收集|禁止hostspot自行触发CMS GC
-XX:CMSInitiatingOccupancyFraction=70|使用cms作为垃圾回收使用70％后开始CMS收集|该值的设置需要满足以下公式CMSInitiatingOccupancyFraction计算公式
-XX:CMSInitiatingPermOccupancyFraction|设置Perm Gen使用到达多少比率时触发|92
-XX:+CMSIncrementalMode|设置为增量模式|用于单CPU情况
-XX:+CMSClassUnloadingEnabled|永久代CMS方式GC|
-XX:+PrintGC|GC日志输出|和-verbose:gc一样
-XX:+PrintGCDetails|同上|更详细
-XX:+PrintGCTimeStamps|输出GC的时间戳|配合上述PrintGC参数使用，或者写成-XX:+PrintGC:PrintGCTimeStamps类似的
-XX:+PrintGC:PrintGCTimeStamps||可与-XX:+PrintGC -XX:+PrintGCDetails混合使用
-XX:+PrintGCApplicationStoppedTime|打印垃圾回收期间程序暂停的时间。可与上面混合使用|输出形式:Total time for which application threads were stopped: 0.0468229 seconds
-XX:+PrintGCApplicationConcurrentTime|打印每次垃圾回收前，程序未中断的执行时间|可与上面混合使用，输出形式:Application time: 0.5291524 seconds
-XX:+PrintHeapAtGC|打印GC前后的详细堆栈信息|
-Xloggc:filename|把相关日志信息记录到文件以便分析|与上面几个配合使用
-XX:+PrintClassHistogram|在控制台按下Ctrl+Break后，打印类的信息|
-XX:+PrintClassHistogramBeforeFullGC|FullGC前打印类信息|
-XX:+PrintTLAB|查看TLAB空间的使用情况|
XX:+PrintTenuringDistribution|查看每次minor GC后新的存活周期的阈值|
-ea|开启assert断言|
-Xprof|性能诊断|
-Xrunhprof|性能诊断|
-XX:+TraceClassLoading|打印类加载过程的信息|类似 [Loaded java.util.AbstractList$Itr from /Library/Java/JavaVirtualMachines/jdk1.8.0_111.jdk/Contents/Home/jre/lib/rt.jar]
-XX:+TraceClassUnloading|打印类卸载的过程信息|
Xbootclasspath|指定加载不需要校验的类|跳过必要的类加载前的校验，能够减少加载时间，但是不安全
-XX:+PrintCompilation|打印Hotspot使用JIT 编译的方法名称|
-XX:+HeapDumpOnOutOfMemoryError|OM时生成heap dump|默认输出在存放类文件的根文件夹
-XX:HeapDumpPath|设置输出OM dump文件路径|配合-XX:+HeapDumpOnOutOfMemoryError使用 ****


```text
-Xms1g
-Xmx1g
-Xmn512m
-XX:MetaspaceSize=128m
-XX:MaxMetaspaceSize=320m
-XX:+UseConcMarkSweepGC
-XX:+UseCMSCompactAtFullCollection
-XX:CMSInitiatingOccupancyFraction=70
-XX:+CMSParallelRemarkEnabled
-XX:SoftRefLRUPolicyMSPerMB=0
-XX:SurvivorRatio=8
-verbose:gc
-XX:+PrintGCDetails
-XX:+PrintGCDateStamps
-XX:+PrintTenuringDistribution
-XX:-OmitStackTraceInFastThrow
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/home/gcweb/jdk_dump

-Xmx1g 
-Xms1g：初始堆大小直接等于最大堆大小
-Xmn512m         新生代大小
-XX:MetaspaceSize=128m        元空间初始大小
-XX:MaxMetaspaceSize=320m         元空间最大值
-XX:+UseConcMarkSweepGC        尽量使用CMS收集器，降低GC停顿时间
-XX:+UseCMSCompactAtFullCollection        使用并发收集器时,开启对年老代的压缩.
-XX:CMSInitiatingOccupancyFraction        使用cms作为垃圾回收使用70％后开始CMS收集
-XX:+CMSParallelRemarkEnabled        降低标记停顿
-XX:SoftRefLRUPolicyMSPerMB=0 避免元空间fullgc时 class被清理 导致重新加载
-XX:SurvivorRatio=8 新生代 内存分区 6:1:1
-verbose:gc 在控制台输出GC情况
-XX:+PrintGCDetails 在控制台输出详细的GC情况
-XX:+PrintGCDateStamps GC的打印基于日期的时间戳
-XX:-OmitStackTraceInFastThrow  避免打印同样错误日志到一定次数就会被jvm默认优化掉。
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=C:/Users/liaojinlong/Downloads/dump/heap/(文件路径-保证目录存在)
-XX:+UseStringDeduplication 合并重复字符串
```



tomcat
---
```xml
<!--    <Executor name="tomcatThreadPool" namePrefix="catalina-exec-"-->
<!--        maxThreads="1500" minSpareThreads="50" maxIdleTime="600000"/>-->
    <Connector 
               port="8801" protocol="org.apache.coyote.http11.Http11Nio2Protocol"
               connectionTimeout="20000" URIEncoding="UTF-8" 
               enableLookups="false"
               sendReasonPhrase="true" 
               useBodyEncodingForURI="true" minSpareThreads="50" maxThreads="500"
               compression="on" compressionMinSize="2048"
               compressableMimeType="text/html,text/xml,text/javascript,text/css,text/plain"
               acceptCount="1200" disableUploadTimeout="true"/>
```

```text
java -XX:+PrintCommandLineFlags -version
-XX:InitialHeapSize=268435456 -XX:MaxHeapSize=4294967296 -XX:+PrintCommandLineFlags -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseParallelGC
openjdk version "1.8.0_322"
OpenJDK Runtime Environment Corretto-8.322.06.1 (build 1.8.0_322-b06)
OpenJDK 64-Bit Server VM Corretto-8.322.06.1 (build 25.322-b06, mixed mode)
```


<https://www.oracle.com/cn/technical-resources/articles/java/g1gc.html>



JDK17
---

```text
--add-opens
java.base/java.lang=ALL-UNNAMED
--add-opens
java.base/java.nio=ALL-UNNAMED
--add-opens
java.base/sun.net.util=ALL-UNNAMED
--add-opens
java.base/jdk.internal.misc=ALL-UNNAMED
-Dio.netty.tryReflectionSetAccessible=true
-Xlog:gc*:file=./logs/jdk_gc.log:time,tags:filecount=10,filesize=102400
--illegal-access=warn





-server
-Xms1g
-Xmx1g
-XX:MetaspaceSize=128m
-XX:MaxMetaspaceSize=320m
-XX:+UseG1GC
-XX:G1HeapRegionSize=16m
-XX:G1ReservePercent=10
-XX:MaxGCPauseMillis=200
-XX:InitiatingHeapOccupancyPercent=40
-XX:SoftRefLRUPolicyMSPerMB=0
-XX:-UseLargePages
-XX:-OmitStackTraceInFastThrow
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/home/gcweb/
-XX:+DisableExplicitGC	

```

ZGC
---

```text

-Dio.netty.tryReflectionSetAccessible=true
-Xlog:gc*:file=./logs/jdk_gc.log:time,tags:filecount=10,filesize=102400
-server
-Xms1g
-Xmx1g
-XX:MetaspaceSize=128m
-XX:MaxMetaspaceSize=320m
-XX:+UseZGC
-XX:InitiatingHeapOccupancyPercent=40
-XX:SoftRefLRUPolicyMSPerMB=0
-XX:-UseLargePages
-XX:-OmitStackTraceInFastThrow
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/home/gcweb/
-XX:+DisableExplicitGC
-XX:-CITime
-XX:-PrintCommandLineFlags

```

 -XX:-ZUncommit 关闭对未使用的内存返回操作系统，当 Xms与Xmx 相同时，默认开启
 -XX:ConcGCThreads zGC 并发收集线程数
 -XX:ZUncommitDelay=<seconds> (default is 300 seconds). 未使用的内存多少秒后归还操作系统
 

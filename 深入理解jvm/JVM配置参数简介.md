# **CMS标准配置参数**

-Xmx4g   **//最大堆大小**	  
-Xms4g   **//初始堆大小**	  
-Xmn600m  **//年轻代大小；此处的大小是（eden+ 2 survivor space)**  
-XX:PermSize=256m  **//持久代(perm gen)初始值** 
-XX:MaxPermSize=256m  **//设置持久代最大值**	
-Xss256k              **//每个线程的堆栈大小；JDK5.0 以后每个线程堆栈大小为1M,以前每个线程堆栈大小为256K。**

-XX:+UseParNewGC    **//设置年轻代为并行收集 默认为-**
-XX:ParallelGCThreads=16  **//默认为CPU核心数；**	
-XX:+DisableExplicitGC    **//关闭System.gc()；System.gc()会显式直接触发Full GC，同时对老年代和新生代进行回收。而一般情况下我们认为，垃圾回收应该是自动进行的，无需手工触发。**  
-XX:+UseConcMarkSweepGC   **//使用CMS内存收集器**
-XX:CMSInitiatingOccupancyFraction=75  **//老年代使用75％后开始CMS收集。默认值92**
-XX:+CMSPermGenSweepingEnabled  **//年老代启用CMS，但默认是不会回收永久代(Perm)的。此处对Perm区启用类回收，防止Perm区内存满。**  
-XX:+CMSClassUnloadingEnabled  **//年老代启用CMS，但默认是不会回收永久代(Perm)的。此处对Perm区启用类回收，防止Perm区内存满。**
-XX:+CMSParallelRemarkEnabled  **//开启并发标记，降低标记停顿；**
-XX:+UseCMSCompactAtFullCollection **//在FULL GC的时候， 对年老代的压缩。**
-XX:+UseCompressedOops   **//针对64位压缩对象指针。 64位对象指针占用更多的堆空间，增加了GC开销，降低CPU缓存命中率**

-verbose:gc          **//表示输出虚拟机中GC的详细情况.**
-XX:+PrintGCDateStamps  //打印时间戳
-XX:+PrintGCDetails  //打印GC细节
-XX:+PrintGCApplicationConcurrentTime //打印应用程序的执行时间
-XX:+PrintGCApplicationStoppedTime //打印应用程序的停顿时间

新生代晋升老年代岁数：默认15 -XX:MaxTenuringThreshold=15

JVM参数配置
JVM日志相关：
JVM内存相关：
JVM回收器相关：


**思考：**  
1. 设置-Xms、-Xmx 相等以避免在每次GC 后调整堆的大小。 同时尽可能的减少了GC次数 
  但随着应用会不断的吃内存
2. -Xss 在相同物理内存下,减小这个值能生成更多的线程  
3.

#JVM参数解释
https://app.yinxiang.com/shard/s35/nl/8617591/5c82d065-6d60-4394-a50f-1ad8d2a54f9e/
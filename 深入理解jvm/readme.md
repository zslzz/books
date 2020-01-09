# 深入理解JVM

-----

[TOC]

jvm核心在于回收机制、多线程。CMS基本配置参数：

-Xmx4g -Xms4g -Xmn600m -XX:PermSize=256m -XX:MaxPermSize=256m -Xss256k -verbose:gc -XX:+UseParNewGC -XX:ParallelGCThreads=16 -XX:+DisableExplicitGC -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -XX:+CMSPermGenSweepingEnabled  -XX:+CMSClassUnloadingEnabled -XX:+CMSParallelRemarkEnabled -XX:+UseCMSCompactAtFullCollection -XX:+UseCompressedOops  -XX:+PrintGCDateStamps -XX:+PrintGCDetails -XX:+PrintGCApplicationConcurrentTime -XX:+PrintGCApplicationStoppedTime

###jvm概览
####jvm基本数据结构
#####1.运行时数据区域

程序计数器 （线程私有）
java虚拟机栈 （线程私有）（生命周期和线程一样）
本地方法栈（本地native）
java堆 

      线程共享，垃圾回收的重点区域，存放对象实例，因标量替换（无法再分割）、栈上分配（确定不会逃逸）。对象也可能在栈上分配
方法区 （类信息、常量、静态变量）
常量池
直接内存

    （由native直接分配，用DirectByteBuffer作为对象引用。避免JAVA堆和Native堆来回复制数据）
    
#####2.对象创建
方法：
 a.指针碰撞
 b.空闲列表
解决并发的方式：
 a.CAS保证更新原子性
 b.TLAB（Thread Local Alloction Buffer） 本地线程分配缓冲
     用完TLAB并分配新的才需要同步锁定

#####3.对象内存布局
1.对象头
 a.Mark Word，包含hashCode、GC分段年龄、锁状态标志、线程持有锁、偏向线程Id、偏向时间戳。
 b.类型指针，指向类元数据。
2.内存异常
 a.堆溢出
 b.虚拟机栈和本地方法栈溢出
 c.方法区和常量池溢出
 d.直接内存溢出

###垃圾回收
#####判定对象是否死亡
1.引用计数法
2.可达性分析法（GCroot。虚拟机栈引用的对象、类静态属性引用的对象、方法常量引用的对象、本地方法引用的对象）
#####引用类型
因垃圾回收的策略不同，并不对所有对象执行相同的回收机制
**强引用**
**软引用**（缓存）
有内存则保留，没有则回收
**弱引用**（WeakHashMap、thread local）
被弱引用关联的对象，在垃圾回收时，如果这个对象只被弱引用关联（没有任何强引用关联他），那么这个对象就会被回收。
**虚引用**（直接内存回收）
为一个对象设置虚引用关联的唯一目的就是能在这个对象被收集器回收时收到一个系统通知。虚引用和弱引用对关联对象的回收都不会产生影响，如果只有虚引用活着弱引用关联着对象，那么这个对象就会被回收。

#####垃圾回收算法
分代收集
**标记清除**
**复制算法**（年轻区）(Eden,S1,S2)
**标记整理**

#####空间分配担保
 fgc触发的条件
 ygc前如果老年代最大连续可用是否大于新生代所有对象总空间->担保失败->是否待遇晋升老年代平均大小->fgc
 
#####算法实现点
主动中断、等待回收。
OOPMap（Ordinary　ObjectPointer）
**枚举根节点**（stw）
**安全点**（程序是否长时间执行）
**安全区**（引用关系是否变化）

######垃圾回收器
**serial** 
client默认、新生代

**parNew**
serial多线程GC版本。可与CMS混用。新生代采用复制算法暂停用户所有线程

**CMS**
1.初始标记（STW）、（标记能GCRoot关联到的对象）
2.并发标记、（标记GCRootTracing）
3.重新标记（STW）、（修正并发标记）
4.并发清除
耗时最长的是并发标记和并发清除。可与用户线程同时进行。
*对cpu敏感、无法处理浮动垃圾、大量碎片，整合有耗时。*

**G1**
划分为多个大小相等的region，优先回收价值最大的region。
跨越region时，通过cardTable把相关引用信息记录到被引用对象所属region的RememberSet中。
进行垃圾回收时，遍历GCroot+rememberSet。
1.初始标记2.并发标记。3.重新标记。4.并发清除。
特点：并行与并发；分区分代收集；空间整合，无碎片；可预测的停顿


###具体case处理与手段
 1.cpu,io正常，jstat观察，老年代增长。触发cmsGC
 通过jstat观察，观察老年代是否满
 2.ygc的服务尖刺，（观察对象是否符合朝生夕死原则，大对象是否有更新）
 3.多竞争情况下，偏向锁的频繁取消会导致stw

 

###java运行
####类加载机制
静态代码块：用staitc声明，jvm加载类时执行，仅执行一次构造代码块
**双亲委派**
1.bootstrap 
  \JAVA_HOME>\lib;
2.extension
  \JAVA_HOME>\lib\ext;
3.application
  classpath 上
4.userclassloader
强行定义自己的类加载器，去加载java.lang的因为不会成功。（会抛安全异常）


**破坏双亲委派模型**（javaweb。类隔离）
java.*开头的将委派给父类加载

**tomcat类加载器结构**
/common 类库被tomcat以及所有app所有共享
/server 类库仅被tomcat使用，对app不可见
/shared 对web可见，对tomcat不可见
/WebApp/WEB-INF 仅对该web可见，对tomcat不可见

###java优化
####编译期优化
**泛型与类型擦除**
方法需要有不同的特征签名（入参出参方法名）
范型会擦除、
**自动装箱、拆箱**
“==”在不遇到算术运算情况下不会自动拆箱，尽量避免

####运行期优化
即时编译器（JIT）、解释器
**编译对象与触发条件**
   热点的方法、循环体
   热点探测：采样 vs 计数器（hotspot）
**编译优化技术**
公共子表达式消除
数组边界检查消除
方法內联
*逃逸分析*
栈上分配（确定不会逃逸）
同步消除（确定不会竞争）
标量替换（对象展开）（确定不能再分割）

###高效并发
内存模型
原子性、可见性、有序性
线程的实现与调度
线程安全
锁优化
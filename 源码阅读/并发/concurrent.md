#集合


##automic
automicBollean

##BlockingQueue
###源码分析
锁、等待、

##ReentrantLock

##Semaphore

##ConcurrentHashMap
ConcurrencyHashMap 包含16个锁的数组实现

##CyclicBarrier

##AbstractQueuedSynchronizer

##CopyOnWriteArrayList

##ThreadPoolExecutor

##Future

Semaphore(信号量)-允许多个线程同时访问：
 synchronized 和 ReentrantLock 都是一次只允许一个线程访问某个资源，
 Semaphore(信号量) 可以指定多个线程同时访问某个资源。

CountDownLatch （倒计时器）： 
CountDownLatch 是一个同步工具类，用来协调多个线程之间的同步。这个工具通常用来控制线程等待，它可以让某一个线程等待直到倒计时结束，再开始执行。

CyclicBarrier(循环栅栏)：
 CyclicBarrier 和 CountDownLatch 非常类似，它也可以实现线程间的技术等待，但是它的功能比 CountDownLatch 更加复杂和强大。主要应用场景和 CountDownLatch 类似。

CyclicBarrier 的字面意思是可循环使用（Cyclic）的屏障（Barrier）。它要做的事情是，让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活。CyclicBarrier 默认的构造方法是 CyclicBarrier(int parties)，其参数表示屏障拦截的线程数量，每个线程调用 await() 方法告诉 CyclicBarrier 我已经到达了屏障，然后当前线程被阻塞。


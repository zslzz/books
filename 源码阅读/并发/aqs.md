#AbstractQuenedSynchronizer（队列同步器）
synchronized提供了便捷性的隐式获取锁释放锁机制；却缺少了获取锁与释放锁的可操作性，可中断、超时获取锁，且它为独占式在高并发场景下性能大打折扣  

##结构
transient（不可序列化）、volatile修饰的"双向链表"
volatile修饰的"int类型状态"
双向链表
  node结构
  
    waitStatus：节点等待状态
    thread：哪个线程在等
    extWaiter：下一个节点
    
 

lock的存储结构：一个int类型状态值（用于锁的状态变更），一个双向链表（用于存储等待中的线程）

lock获取锁的过程：本质上是通过CAS来获取状态值修改，如果当场没获取到，会将该线程放在线程等待链表中。

lock释放锁的过程：修改状态值，调整等待链表。
##主要方法
- 
- 
## 条件变量
**why条件变量而不是sleep？**
awaiter方法
```
void doSomethingWithCondition() {
  cond = locker.newCondition();
  locker.lock();
  while(!condition_is_true()) {
    cond.await();
  }
  justdoit();
  locker.unlock();
}
```
await() 方法会一直阻塞在 cond 条件变量上直到被另外一个线程调用了 cond.signal() 或者 cond.signalAll() 方法后才会返回.
await() 阻塞时会自动释放当前线程持有的锁，await() 被唤醒后会再次尝试持有锁（可能又需要排队），拿到锁成功之后 await() 方法才能成功返回。
await 释放锁。sleep不释放


阻塞在条件变量上的线程可以有多个，这些阻塞线程会被串联成一个条件等待队列。  
当 signalAll() 被调用时，会唤醒所有的阻塞线程，
让所有的阻塞线程重新开始争抢锁。如果调用的是 signal() 只会唤醒队列头部的线程，这样可以避免「惊群问题」。
##过程
- 本地的park和unpark方法。控制线程的启动与暂停。 
- 线程对象里有parkBlocker是一系列冲突线程的管理者协调者，控制线程的休眠和唤醒

#总结
可以看到在整个实现过程中，lock大量使用CAS+自旋。  
因此根据CAS特性，lock建议使用在低锁冲突的情况下。
目前java1.6以后，官方对synchronized做了大量的锁优化（偏向锁、自旋、轻量级锁）。
因此在非必要的情况下，建议使用synchronized做同步操作。
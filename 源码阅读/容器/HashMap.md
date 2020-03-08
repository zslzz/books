#HashMap
##结构
- size HashMap存储大小
- threshold 负载因子与size的乘积
- loadFactor 默认0.75
- modCount  因为每遍历一个元素就会检查modCount是否被修改，而add和remove操作都会对modCount++  
  fast-fail 机制的保障。
  
- Entry<K,V>[] table 



##源码tips

- 求2^n余
  余数为 h&(2^n-1)
  
- 链表的多线程复制的死锁问题

##

##
###jdk1.7 resize可能出现死链情况分析
resize后会链表会倒置。在多线程环境下 同时resize可能会出现死锁现象
java7resize时主要步骤 参考wiki：https://www.cnblogs.com/wang-meng/p/7582532.html

    while(null != e) {
        Entry<K,V> next = e.next; //记录odl hash表中e.next //当线程1执行到这里被调度挂起了会出现死循环
        int i = indexFor(e.hash, newCapacity); //rehash计算出数组的位置(hash表中桶的位置)
        e.next = newTable[i]; //e要插入链表的头部， 所以要先将e.next指向new hash表中的第一个元素
        newTable[i] = e; //将e放入到new hash表的头部
        e = next; //转移e到下一个节点， 继续循环下去
    }
如果多线程resize时 若第一步被挂起，那么后面的操作可能出现后面的元素指向了前面的，出现了循环   
java8 resize链表正常顺序。每次循环挂在链表的尾部，因此不会死锁。 



#HashMap和concurrentHashMap

#hashmap主要功能以及相关思想
 - 扩容
 
 - 红黑树
 - count
 java7 采用

#流程

#结构
#总结

#对比
 - fastfail
 - fail-safe concurrent 需要复制集合；无法保证读取的数据是目前原始数据结构中的数据
   TODO 具体实现原理
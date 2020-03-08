#Object
java 中Object类的使用以及相关技巧

##主要方法以及使用
Object 主要方法有：natice、hashCode、equals 
- private static native void registerNatives()  
  注册到本地方法中，可供其他程序调用  
- public final native Class<?> getClass()
  
- public native int hashCode();

- public boolean equals(Object obj)
- public String toString();

- protected native Object clone() throws CloneNotSupportedException;


- public final native void notify()
- public final native void notifyAll()
- public final native void wait(long timeout) throws InterruptedException
- public final void wait(long timeout, int nanos) throws InterruptedException
- public final void wait() throws InterruptedException

- protected void finalize() throws Throwable 
被垃圾回收器使用

##equals和hashCode
重写equals时必须重写hashCode  
因为在逻辑上，如果两个对象相等，那么它们的hashCode()值一定相同。这里的相等是指，通过equals()比较两个对象时返回true。


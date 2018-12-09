## threadLocal,map
#### 1.threadLocal、volatile、synchronized区别：     
**synchronized关键字是Java利用锁的机制自动实现的**，一般有同步方法和同步代码块两种实现方式，synchronized代码块是通过monitorenter和monitorexit实现，synchronized方法是通过在方法常量池中的方法表结构中的ACC_SYNCHRONIZED 标志区分是否是一个同步方法，如果是则用Klass作为锁对象。   
**volatile是修饰变量的**，在java内存模型中，所有变量都存储在主内存中，所有线程都可以访问，每个线程创建时，JVM都会为线程创建一个工作内存，线程对变量的操作是在工作内存中进行的,volatile修饰的变量发生变化时，所有调用该变量的线程都会获得最新相同的值，**保证修饰的变量具有可见性，都但不能保证操作的原子性，还能禁止进行指令重排**。  
**threadLocal只是一个线程的局部变量**，ThreadLocal会为每个使用该变量的线程提供独立的变量副本。每个线程内部都会维护一个threadLocalMap，map中的key为threadLocal，即每个线程中的threadLocal是相互隔离的，通过以空间换时间的方式，进行线程间数据的隔离。   
####  2.ThreadLocal为什么会导致内存溢出：      
每个线程会存放一份自己的ThreadLocalMap,获取这个map是通过线程唯一标识获取的 ，ThreadLocalMap中存放的是Entry，Entry内部类，继承了WeakReference<ThreadLocal<?>>，使用了弱引用，对应的是ThreadLocal，而map的生命周期是线程的生命周期，如果线程一直存活，但是ThreaLocal被设置为null之后，ThreadLocal被回收了，但是value还是存活在map中，属于强引用，不会导致GC回收
，等value越积越多，就会导致内存溢出，尤其像线程池这种长期存活的线程，所以使用完ThreadLocal需要remove掉   
####  3.threadLocal的使用场景：      
最常用的threadLocal的应用于数据库连接、session管理

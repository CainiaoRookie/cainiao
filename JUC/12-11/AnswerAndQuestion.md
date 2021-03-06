## ReentrantLock 深入分析
### ReentrantLock和Synchronized区别
synchronized是java中的一个关键字,是Java语言内置的特,ReentrantLock是JDK实现的,ReentrantLock更灵活，可支持中断、超时等操作，且ReentrantLock比Synchronized性能好些，实现中用了大量CAS和轮询去实现锁，并且key使用tryLock去尝试获取锁，而Synchronized要么获取锁要么失败。另外ReentrantLock提供了独有的Condition机制
### 公平锁和非公平锁的区别，在获取锁的实现上最大区别是什么
公平锁每次获取的是CHL队列中的第一个节点，保证请求资源的顺序性，而非公平锁不保证获取锁的是哪个节点，所以可能会造成某个线程饥饿现象，一直无法获取锁。在实现上最大区别是非公平锁会判断该线程节点是否有前驱节点（hasQueuedPredecessors）
### ReentrantReadWriteLock是如何区别读写锁的，具体说说
在AQS中有属性state来区别锁状态，而reentrantReadWriteLock是对这个state属性进行高低位分隔，而写锁状态获取操作是：1.先对1进行左移16为，变成0x00010000，2.对左移后的进行减1变成0x00001111，3.最后让state对其进行按位与&，获得state的低16位，其实一系列操作就是让高16位全变成0，低16位不变。读锁状态的获取比较简单，直接对state进行无符号右移16位即可。而在读写锁获取的时候，是判断高低位来进行区分的（所以这里不要把state当做普通的数字）
### HoldCounter在读写锁中起到什么作用
主要起到对读锁进行重入的计数，在释放锁的时候用来判断缓存的上一个读取线程是不是该线程，减少ThreadLocal.get的使用
### 简述下锁降级
写锁可以降级到读锁，但不能升级，降级要遵循先获取写锁、获取读锁在释放写锁的次序
### 说说reentrantreadwritelock相比reentrantlock有哪些功能
读写锁和reentrantlock功能相似，只是更细粒度去锁定资源，读写锁可以实现读锁共享，写锁互斥，同时读写锁还存在锁降级。
### 说下读写锁在判断当前线程是否需要被阻塞的时候如何判断的
读锁：如果是公平锁模式如果有其它线程在当前线程之前等待获取读取锁，则当前线程应该被阻塞，非公平锁模式如果等待队列的第一个节点的线程等待获取写入锁，则当前线程应该被阻塞  
写锁：公平锁模式如果有其它线程在当前线程之前等待获取写入锁，则当前线程应该被阻塞，非公平锁模式不做任何判断

### 1. ArrayBlockingQueue
是数组实现的现场安全的有界阻塞队列，按 FIFO（先进先出）原则对元素进行排序，元素都是从尾部插入到队列，从头部开始返回。   
方法：
> 1.add/offer方法是非阻塞的，如果队列满就直接返回异常
> 2.put()方法是阻塞的，如果队列满就等待，等待notFull的信号量，notFull.await()在take等方法执行的时候会触发notFull.signal()
> 3.所有删除元素操作都是先进行加锁保证线程安全,poll()和take()方法是阻塞的
### 2.SynchronousQueue
主要特点：
> 1. 容量为0，无法用于数据的存储；
> 2.  每一次向队列中执行写操作时，写线程都会等待；直到另一个线程去执行读操作时，写线程才会返回；反之亦然，并且写入的元素不允许为null；
> 3. 提供了公平与非公平两种策略的处理实现，其分别是基于队列与栈来进行实现，一个是TransferQueue，另一个是TransferStack，它们都分别实现了Transferer接口，然后实现transfer方法；
> 4.SynchronousQueue非常适合做交换工作，生产者的线程和消费者的线程同步以传递某些信息、事件或者任务。
### 3.[PriorityBlockingQueue](https://www.jianshu.com/p/43954715aa28) 
1. 是一个支持优先级的无界阻塞队列。默认情况下元素采用自然顺序升序排序，当然我们也可以通过构造函数来指定Comparator来对元素进行排序。需要注意的是PriorityBlockingQueue不能保证同优先级元素的顺序。
2. 底层采用二叉堆来实现，每次出队都返回优先级最高的元素是二叉树最小堆的实现。
### 4.DelayQueue
1. 是一个无界的BlockingQueue，用于放置实现了Delayed接口的对象，其中的对象只能在其到期时才能从队列中取走。这种队列是有序的，即队头对象的延迟到期时间最长。注意：不能将null元素放置到这种队列中。
2. DelayQueue内部的实现基本就是依靠重写BlockingQueue方法，使用ReentrantLock进行同步操作，使用PriorityQueue存放队列元素，Condition存放访问线程
### 5.LinkedTransferQueue
LinkedTransferQueue采用的一种预占模式。意思就是消费者线程取元素时，如果队列为空，那就生成一个节点（节点元素为null）入队，然后消费者线程被等待在这个节点上，后面生产者线程入队时发现有一个元素为null的节点，生产者线程就不入队了，直接就将元素填充到该节点，唤醒该节点等待的线程，被唤醒的消费者线程取走元素，从调用的方法返回。即找到匹配的节点不入队，找不到根据how参数入队
### 6.LinkedBlockingDeque
是一个由链表组成的双向阻塞队列,LinkedBlockingDeque是可选容量的，在初始化时可以设置容量防止其过度膨胀，如果不设置，默认容量大小为Integer.MAX_VALUE。使用非公平ReentrantLock实现线程安全，不允许null元素添加

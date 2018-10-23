* 集合框架关系图：![](/assets/集合.jpg)

## Queue

队列主要解决了生产者消费者模式中并发控制复杂易出错的情况。Java提供的线程安全的Queue可以分为阻塞队列和非阻塞队列，其中阻塞队列的典型例子是BlockingQueue，非阻塞队列的典型例子是ConcurrentLinkedQueue。非阻塞队列在取出空队列或者队列满时候会报错，所以，在并发编程中，一般推荐使用阻塞队列，这样实现可以尽量地避免程序出现意外的错误。故，我们在这里也主要看阻塞队列。

Concurrent包中提供了8个Queue，其中七个是阻塞队列，剩下一个是非阻塞队列ConcurrentLinkedQueue。都继承了AbstractQueue类，但阻塞队列都实现了BlockingQueue接口。

| 队列名称 | 作用 | 是否阻塞 |
| :--- | :--- | :--- |
| ArrayBlockingQueue | 一个由数组结构组成的有界阻塞队列。 | 是 |
| LinkedBlockingQueue | 一个由链表结构组成的有界阻塞队列。 | 是 |
| PriorityBlockingQueue | 一个支持优先级排序的无界阻塞队列，其存储对象必须Comparable接口。 | 是 |
| DelayQueue | 一个使用优先级队列实现的无界阻塞队列，其存储对象必须实现Delayed接口并重写其compareTo方法和getDelay方法。 | 是 |
| SynchronousQueue | 一个不存储元素的阻塞队列，相当于做数据传递。 | 是 |
| LinkedTransferQueue | 一个由链表结构组成的无界阻塞队列，相对于其他阻塞队列LinkedTransferQueue多了tryTransfer和transfer方法。 | 是 |
| LinkedBlockingDeque | 一个由链表结构组成的双向阻塞队列。 | 是 |
| ConcurrentLinkedQueue | 一个基于链表实现的无界线程安全队列，其线程安全是通过其插入、删除时采取CAS操作来保证的。。 | 否 |

队列在存取时候针对不同场景提供了四种不同的方法：

| 方法 | 抛出异常 | 返回布尔值 | 一直阻塞 | 超时退出 |
| :--- | :--- | :--- | :--- | :--- |
| 添加/插入元素时 | add\(e\) | offer\(e\) | put\(e\) | offer\(e,time,unit\) |
| 取出/删除元素时 | remove\(\) | poll\(\) | take\(\) | poll\(time,unit\) |
| 获取但不移除头元素 | element\(\) | peek\(\) | 无 | 无 |

## Map



## List




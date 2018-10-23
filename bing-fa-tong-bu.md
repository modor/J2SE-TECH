### 线程池

线程池的核心是ThreadPoolExecutor类。Executors工厂类为几个特殊场景提供的方法都是基于这个类的实例化，传入了特定的参数；Executors还实现了调度线程池（newScheduledThreadPool）和单线程调度线程池（newSingleThreadScheduledExecutor）工厂方法，是基于ScheduledThreadPoolExecutor的实例化，但ScheduledThreadPoolExecutor是继承自ThreadPoolExecutor。

ThreadPoolExecutor的构造方法为：

```
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler){}
```

* corePoolSize - 池中所保存的线程数，包括空闲线程。

* maximumPoolSize - 池中允许的最大线程数。

* keepAliveTime - 当线程数大于核心时，此为终止前多余的空闲线程等待新任务的最长时间。

* unit - keepAliveTime - 参数的时间单位。

* workQueue - 执行前用于保持任务的队列。此队列仅由保持 execute 方法提交的 Runnable 任务。
* threadFactory - 执行程序创建新线程时使用的工厂。

* handler - 由于超出线程范围和队列容量而使执行被阻塞时所使用的处理程序。

线程池关系表如下：

| 名称 | 方法 | 作用 | 实现代码 |
| :--- | :--- | :--- | :--- |
| 单线程池 | newSingleThreadExecutor\(\) | 创建一个单线程的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。 | return new FinalizableDelegatedExecutorService\(new ThreadPoolExecutor\(1, 1, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue&lt;Runnable&gt;\(\)\)\); |
| 固定线程池 | newFixedThreadPool\(\) | 创建固定大小的线程池。每次提交一个任务就创建一个线程，直到线程达到线程池的最大大小。 | return new ThreadPoolExecutor\(nThreads, nThreads, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue&lt;Runnable&gt;\(\)\); |
| 缓存线程池 | newCachedThreadPool\(\) | 创建一个可缓存的线程池。如果线程池的大小超过了处理任务所需要的线程，那么就会回收部分空闲（60秒不执行任务）的线程，当任务数增加时，此线程池又可以智能的添加新线程来处理任务。 | return new ThreadPoolExecutor\(0, Integer.MAX\_VALUE, 60L, TimeUnit.SECONDS, new SynchronousQueue&lt;Runnable&gt;\(\)\); |
| 单线程调度线程池 | newSingleThreadScheduledExecutor | 创建一个单线程的线程池，此线程池支持定时以及周期性执行任务的需求。 | return new DelegatedScheduledExecutorService\(new ScheduledThreadPoolExecutor\(1\)\); |
| 调度线程池 | newScheduledThreadPool\(\) | 创建一个大小无限的线程池。此线程池支持定时以及周期性执行任务的需求。 | return new ScheduledThreadPoolExecutor\(corePoolSize\); |




# 线程池

## 为什么使用

- 降低线程创建回收的频率
- 防止因程序错误频繁创建线程
- 易于维护线程状态
- 易于维护任务执行状态

## 常量

- 使用一个 32 位整数来保存线程池状态和线程数量
  - 高 3 位为线程池状态 11100000000000000000000000000000
  - 低 29 位保存工作线程数 00011111111111111111111111111111
  - 保存此数据的类型为 `AtomicInteger` 的原子类型
  - 可以支持同时对线程池状态和工作线程数做修改，省去加锁的步骤

## 线程池状态

- RUNNING 接受新任务，同时处理阻塞队列中的任务
- SHUTDOWN 不接受新任务，但仍处理阻塞队列中的任务
- STOP 不接受新任务，不处理阻塞队列中的任务，中断正在运行中的任务
- TIDYING 所有任务已执行完毕，已无工作线程，即将调用 `terminated()` 方法
- TERMINATED `terminated()` 方法已调用完成

> 调用 `shutdownNow()` 将会尝试中断正在运行中的任务，不再处理新任务，并关闭线程池，且返回未处理的任务，进入 `STOP` 状态

## 成员变量

- `BlockingQueue<Runnable> workQueue` 阻塞队列，用于存储任务并等待移交任务给工作线程
- `ReentrantLock mainLock` 用来为某些需要同步的操作加锁
- `HashSet<Worker> workers` 用于存储所有的工作线程，仅在持有`mainLock`时允许访问
- `int largestPoolSize` 最大池大小，表示能够运行的最大线程数量，仅在持有`mainLock`时允许访问
- `long completedTaskCount` 已完成任务的计数器。仅在工作线程终止时更新。只能在持有`mainLock`时允许访问
- `ThreadFactory threadFactory` 线程工厂，所有的新线程都是由此工厂创建的，如果使用自定义的工厂则需要准备好创建线程失败的处理措施
- `RejectedExecutionHandler handler` 拒绝策略，线程池达到饱和或者在关闭线程池时被调用
- `long keepAliveTime` 等待任务的空闲线程超时（以纳秒为单位）时间。当存在多于`corePoolSize`或`allowCoreThreadTimeOut`时，线程使用此超时，否则将永远等待新的任务
- `boolean allowCoreThreadTimeOut` 如果为 false（默认），核心线程即使在空闲时也保持活跃状态。如果为 true，核心线程使用`keepAliveTime`超时等待任务
- `int corePoolSize` 保持活跃的最小工作线程数（且不允许超时等待），除非设置了`allowCoreThreadTimeOut`
- `int maximumPoolSize` 可执行最大线程数量，在内部仍然会受到`CAPACITY`值的影响

## Worker

- 继承了`AbstractQueuedSynchronizer`以支持加锁解锁操作
- 初始化时设置状态为`-1`，防止在运行之前（调用`runWorker`）就收到了中断信号

Last Modified 2022-01-20

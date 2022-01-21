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

## 工作线程运行

```java
final void runWorker(Worker w) {
    Thread wt = Thread.currentThread();
    Runnable task = w.firstTask;
    w.firstTask = null;
    w.unlock(); // 释放锁允许中断
    boolean completedAbruptly = true;
    try {
        // 循环获取阻塞队列中的任务
        while (task != null || (task = getTask()) != null) {
            w.lock(); // 先对工作线程加锁
            // 如果线程池正在停止，确保当前线程被中断；
            // 如果没有，确保当前线程不被中断。
            // 这需要在第二种情况下重新检查以在清除中断时处理 shutdownNow 的锁竞争
            if ((runStateAtLeast(ctl.get(), STOP) ||
                  (Thread.interrupted() &&
                  runStateAtLeast(ctl.get(), STOP))) &&
                !wt.isInterrupted())
                wt.interrupt();
            try {
                beforeExecute(wt, task); // 执行任务前的前置方法，默认的实现是空的
                Throwable thrown = null; // 准备一个异常类接收任务运行抛出的错误
                try {
                    task.run(); // 执行任务并捕获异常
                } catch (RuntimeException x) {
                    thrown = x; throw x;
                } catch (Error x) {
                    thrown = x; throw x;
                } catch (Throwable x) {
                    thrown = x; throw new Error(x);
                } finally {
                    afterExecute(task, thrown); // 执行任务完成后的后置方法，默认实现也是空的
                }
            } finally {
                task = null; // 执行完的任务释放掉，置空指针
                w.completedTasks++; // 累加完成的任务数量
                w.unlock(); // 释放工作线程的锁
            }
        }
        completedAbruptly = false;
    } finally {
        processWorkerExit(w, completedAbruptly); // 如果没有任务处理了，将会触发回收工作线程
    }
}
```

## 任务获取

```java
private Runnable getTask() {
    boolean timedOut = false; // 用于记录最后一次从阻塞队列获取任务是否超时，既 poll() 操作

    // 循环从工作队列中取出任务
    for (;;) {
        int c = ctl.get();
        int rs = runStateOf(c);

        // 1. 线程池是否处于停止
        // 2. 线程池正在停止或工作队列是否为空
        if (rs >= SHUTDOWN && (rs >= STOP || workQueue.isEmpty())) {
            decrementWorkerCount(); // 递减工作线程数
            return null; // 返回空，触发回收工作线程
        }

        int wc = workerCountOf(c); // 当前工作线程数

        // 工作线程是否会被淘汰（回收）
        boolean timed = allowCoreThreadTimeOut || wc > corePoolSize;

        // 1. 工作线程数量是否大于最大线程数
        // 2. 是否已经处理超时
        // 3. 工作队列为空且工作线程数大于 1
        if ((wc > maximumPoolSize || (timed && timedOut))
            && (wc > 1 || workQueue.isEmpty())) {
            if (compareAndDecrementWorkerCount(c)) // 递减工作线程数
                return null; // 返回空，触发回收工作线程
            continue;
        }

        try {
            // 从工作队列中取出任务
            // 1. 如果没有设置超时，则会挂起等待新任务进入队列，直到获取到可以执行的任务
            // 2. 如果设置了超时，则会在超时时间内获取任务，获取不到也不再等待，往下执行
            Runnable r = timed ?
                workQueue.poll(keepAliveTime, TimeUnit.NANOSECONDS) :
                workQueue.take();
            if (r != null)
                return r;
            timedOut = true; // 获取不到任务，表明已经超时
        } catch (InterruptedException retry) {
            timedOut = false; // 获取过程中发生中断异常，重置超时标记
        }
    }
}
```

## 接收新任务

```java
public void execute(Runnable command) {
    if (command == null)
        throw new NullPointerException();
    // 分为三步操作
    int c = ctl.get(); // 读取状态
    // 判断工作线程数量是否超过核心线程数量
    if (workerCountOf(c) < corePoolSize) {
        // 尝试新建核心工作线程
        // addWorker() 的第二个参数表示创建的工作线程类型，true 指定此线程为核心工作线程
        if (addWorker(command, true))
            return; // 添加失败直接退出
        c = ctl.get(); // 重新读取状态
    }
    // 检查线程池运行状态并将任务加入工作队列
    if (isRunning(c) && workQueue.offer(command)) {
        int recheck = ctl.get(); // 重新检查一次
        // 如果线程池已经停止运行，尝试回滚刚刚加入的任务
        if (! isRunning(recheck) && remove(command))
            reject(command); // 进入拒绝策略
        // 如果回滚任务失败了
        else if (workerCountOf(recheck) == 0) // 判断工作线程数是否为 0
            addWorker(null, false); // 尝试去添加新的非核心工作线程以消费任务
    }
    // 如果加入队列失败则尝试新建工作线程
    else if (!addWorker(command, false))
        // 如果新增失败则表示线程池已经停止或者工作线程数量达到饱和
        reject(command); // 进入拒绝策略
}
```

## 添加工作线程

```java
private boolean addWorker(Runnable firstTask, boolean core) {
    retry:
    for (;;) {
        int c = ctl.get();
        int rs = runStateOf(c); // 缓存当前状态

        // 先检查线程池的运行状态是否处于停止中
        // 再检查工作队列是否为空
        if (rs >= SHUTDOWN &&
            ! (rs == SHUTDOWN &&
                firstTask == null &&
                ! workQueue.isEmpty()))
            return false;

        for (;;) {
            int wc = workerCountOf(c);
            // 如果线程池的工作线程数已经饱和，则拒绝添加新的工作线程
            // 1. 如果添加的是核心线程，则判断最大核心线程数
            // 2. 如果添加的是非核心线程，则判断最大可执行线程数
            if (wc >= CAPACITY ||
                wc >= (core ? corePoolSize : maximumPoolSize))
                return false;
            // 尝试增加工作线程数
            if (compareAndIncrementWorkerCount(c))
                break retry; // 重试
            c = ctl.get();  // 重新读取状态
            if (runStateOf(c) != rs)
                continue retry;
            // 执行 CAS 操作失败，表示线程池状态发生了变化，在内部循环重试
        }
    }

    boolean workerStarted = false; // 记录工作线程是否已启动
    boolean workerAdded = false; // 记录工作线程是否已经添加成功
    Worker w = null;
    try {
        w = new Worker(firstTask);
        final Thread t = w.thread;
        if (t != null) {
            final ReentrantLock mainLock = this.mainLock;
            mainLock.lock(); // 持有锁来检查状态
            try {
                // 在 ThreadFactory 创建失败
                // 或在获取锁之前线程池已经关闭时退出
                int rs = runStateOf(ctl.get());

                // 检查两项
                // 1. 线程池是否处于运行状态
                // 2. 线程池处于停止中且第一个新任务为空
                if (rs < SHUTDOWN ||
                    (rs == SHUTDOWN && firstTask == null)) {
                    if (t.isAlive()) // 预先检查线程是否可以启动
                        throw new IllegalThreadStateException();
                    workers.add(w); // 添加工作线程
                    // 重新计算线程池工作线程数
                    int s = workers.size();
                    if (s > largestPoolSize)
                        largestPoolSize = s;
                    workerAdded = true; // 标记添加工作线程成功
                }
            } finally {
                mainLock.unlock(); // 释放锁
            }
            // 如果添加工作线程成功，尝试启动工作线程
            if (workerAdded) {
                t.start();
                workerStarted = true; // 标记启动工作线程成功
            }
        }
    } finally {
        // 工作线程启动失败产生错误，执行清理工作
        if (! workerStarted)
            addWorkerFailed(w);
    }
    return workerStarted;
}
```

Last Modified 2022-01-21

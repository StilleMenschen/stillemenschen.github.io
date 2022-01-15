# 锁

## ReentrantLock

- 可重入性，加锁和锁的释放都通过计数器确定
- 锁的实现，synchronized 为 JVM 实现，而 ReentrantLock 为 JDK 实现
- 性能，类似 CAS (Compare And Set) 操作，尽可能在用户态中将加锁问题解决，避免进入内核态的线程阻塞
- 功能，synchronized 会自动加锁和释放锁，而 ReentrantLock 则需要手动操作；另外 ReentrantLock 的锁细粒度优于 synchronized
- **ReentrantLock 可以指定公平锁或非公平锁，而 synchronized 为非公平锁，先等待的线程不一定先获得锁**
- **提供了 Condition 类，可以分组唤醒需要唤醒的线程**
- **提供了 `lock.lockInterruptibly()` 来中断等待锁的线程机制**

```java
package com.example.concurrent;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockExample {
    private static final Logger log = LoggerFactory.getLogger(ReentrantLockExample.class);

    private static final int CLIENT_TOTAL = 500;
    private static final int THREAD_TOTAL = 20;

    private static final Lock LOCK = new ReentrantLock();
    private static int count = 0;

    public static void main(String[] args) {
        final ExecutorService pool = Executors.newFixedThreadPool(THREAD_TOTAL);
        final CountDownLatch latch = new CountDownLatch(CLIENT_TOTAL);
        for (int i = 0; i < CLIENT_TOTAL; i++) {
            pool.execute(() -> {
                try {
                    addOrUpdate();
                } finally {
                    latch.countDown();
                }
            });
        }
        try {
            latch.await();
            pool.shutdown();
            log.info("count = {}", count);
        } catch (InterruptedException e) {
            log.error(e.getMessage(), e);
        }
    }

    private static void addOrUpdate() {
        try {
            LOCK.lock();
            count++;
        } finally {
            LOCK.unlock();
        }
    }
}
```

```java
package com.example.concurrent;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockExample {
    private static final Logger log = LoggerFactory.getLogger(ReentrantLockExample.class);

    public static void main(String[] args) {
        final ReentrantLock reentrantLock = new ReentrantLock();
        final Condition condition = reentrantLock.newCondition();

        new Thread(() -> {
            try {
                reentrantLock.lock(); // 加锁, 进入同步等待队列
                log.info("wait signal"); // 1 等待信号
                condition.await(); // 释放锁, 进入条件同步等待队列
            } catch (InterruptedException e) {
                log.error(e.getMessage(), e);
            }
            log.info("get signal"); // 4 获得信号, 拿到锁, 进入同步等待队列
            reentrantLock.unlock(); // 释放锁
        }).start();

        new Thread(() -> {
            try {
                reentrantLock.lock(); // 加锁, 进入同步等待队列
                log.info("get lock"); // 2 等待获取锁
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                log.error(e.getMessage(), e);
            }
            condition.signalAll(); // 唤醒条件同步等待队列
            log.info("sending signal..."); // 3 发送信号
            reentrantLock.unlock(); // 释放锁
        }).start();
    }
}
```

## ReentrantReadWriteLock

```java
package com.example.concurrent;

import java.util.Map;
import java.util.Set;
import java.util.TreeMap;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ReentrantReadWriteLockExample {

    private static final Map<String, Data> MAP = new TreeMap<>();
    private static final ReadWriteLock LOCK = new ReentrantReadWriteLock();

    private static final Lock READ_LOCK = LOCK.readLock();
    private static final Lock WRITE_LOCK = LOCK.writeLock();

    public Data get(final String key) {
        try {
            READ_LOCK.lock();
            return MAP.get(key);
        } finally {
            READ_LOCK.unlock();
        }
    }

    public Set<String> keySets() {
        try {
            READ_LOCK.lock();
            return MAP.keySet();
        } finally {
            READ_LOCK.unlock();
        }
    }

    public Data put(final String key, final Data value) {
        try {
            // 悲观锁（排它锁）, 只有所有的读锁都释放完了才会加写锁
            WRITE_LOCK.lock();
            return MAP.put(key, value);
        } finally {
            WRITE_LOCK.unlock();
        }
    }

    static class Data {
    }
}
```

## StampedLock


```java
package com.example.concurrent;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.locks.StampedLock;

public class StampedLockExample {
    private static final Logger log = LoggerFactory.getLogger(StampedLockExample.class);

    private static final int CLIENT_TOTAL = 500;
    private static final int THREAD_TOTAL = 20;

    // 在读锁较少, 写锁较多的情况下, 并发性能更好
    private static final StampedLock LOCK = new StampedLock();
    private static int count = 0;

    public static void main(String[] args) {
        final ExecutorService pool = Executors.newFixedThreadPool(THREAD_TOTAL);
        final CountDownLatch latch = new CountDownLatch(CLIENT_TOTAL);
        for (int i = 0; i < CLIENT_TOTAL; i++) {
            pool.execute(() -> {
                try {
                    addOrUpdate();
                } finally {
                    latch.countDown();
                }
            });
        }
        try {
            latch.await();
            pool.shutdown();
            log.info("count = {}", count);
        } catch (InterruptedException e) {
            log.error(e.getMessage(), e);
        }
    }

    private static void addOrUpdate() {
        final long stamped = LOCK.writeLock();
        try {
            count++;
        } finally {
            LOCK.unlock(stamped);
        }
    }
}
```

> 相较于对象锁，synchronized 不会造成死锁，因为这个关键字的加锁和解锁是由 JVM 来保证的

Last Modified 2022-01-16

# 并发编程

## Atomic

原子操作类

```java
package com.example;

import org.junit.jupiter.api.Test;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.atomic.AtomicLong;
import java.util.concurrent.atomic.LongAdder;

public class UtilTests {
    private static final int MAX_THREAD = 10000;
    private final LongAdder adder = new LongAdder();
    private final AtomicLong atomicLong = new AtomicLong(0);

    @Test
    public void testLongAdder() {
        /*
        通过将数值分散为一个数组进行操作, 将并发的 compareAndSet 操作压力分散
        */
        final ExecutorService pool = Executors.newFixedThreadPool(5000);
        final CountDownLatch latch = new CountDownLatch(MAX_THREAD);
        for (int i = 0; i < MAX_THREAD; i++) {
            pool.execute(() -> {
                adder.increment();
                latch.countDown();
            });
        }
        try {
            latch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.printf("testLongAdder count = %d", adder.intValue());
    }

    @Test
    public void testAtomicLong() {
        /*
        在并发时通过 compareAndSet 操作来准确的改变数据
         */
        final ExecutorService pool = Executors.newFixedThreadPool(5000);
        final CountDownLatch latch = new CountDownLatch(MAX_THREAD);
        for (int i = 0; i < MAX_THREAD; i++) {
            pool.execute(() -> {
                atomicLong.incrementAndGet();
                latch.countDown();
            });
        }
        try {
            latch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.printf("testAtomicLong count = %d", atomicLong.get());
    }
}
```

> 在 `java.util.concurrent.atomic` 包中还有一个 `AtomicStampedReference` 类实现，它变更数据时不仅会校对期望的数据值，
> 还会为每次变化都记录一个版本号并在每次变化值时进行校对，以解决 ABA 问题。Atomic 在竞争激烈时能维持常态，相比 Lock 性能好一些，但只能同步一个值

## Synchronized

同步代码

- 线程解锁前，必须把共享变量的最新值刷新到主内存
- 线程加锁时，将清空工作内存中共享变量的值，从而使用共享变量时需要从主内存中重新读取最新的值（注意，加锁与解锁是同一把锁）

```java
package com.example;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class SynchronizedExample {
    private static final Logger log = LoggerFactory.getLogger(SynchronizedExample.class);

    /**
     * 锁定被调用的对象
     */
    public void test1(int flag) {
        synchronized (this) {
            for (int i = 0; i < 10; i++) {
                log.info("test1 - {} - {}", flag, i);
            }
        }
    }

    /**
     * 锁定被调用的对象, synchronized 不能被子类继承
     */
    public synchronized void test2(int flag) {
        for (int i = 0; i < 10; i++) {
            log.info("test2 - {} - {}", flag, i);
        }
    }

    /**
     * 锁定所有的对象
     */
    public void test3(int flag) {
        synchronized (SynchronizedExample.class) {
            for (int i = 0; i < 10; i++) {
                log.info("test3 - {} - {}", flag, i);
            }
        }
    }

    /**
     * 锁定所有的对象, synchronized 不能被子类继承
     */
    public static synchronized void test4(int flag) {
        for (int i = 0; i < 10; i++) {
            log.info("test4 - {} - {}", flag, i);
        }
    }

    public static void main(String[] args) {
        final SynchronizedExample example1 = new SynchronizedExample();
        final SynchronizedExample example2 = new SynchronizedExample();
        final ExecutorService pool = Executors.newFixedThreadPool(100);
        pool.execute(() -> example1.test1(1));
        pool.execute(() -> example2.test1(2));
        pool.execute(() -> example1.test2(1));
        pool.execute(() -> example2.test2(2));
        pool.execute(() -> example1.test3(1));
        pool.execute(() -> example2.test3(2));
        pool.execute(() -> example1.test4(1));
        pool.execute(() -> example2.test4(2));
        pool.shutdown();
    }
}
```

> 同步代码属于不可中断的锁，适用于冲突不是很激烈的情况，代码可读性较好。
> Lock 为可中断的锁，多样化同步，在竞争激烈时能维持常态

## Volatile

可见性，一个线程对主内存的修改可以被其它线程及时地观察到。

volatile 通过 **内存屏障** 和 **禁止重排序** 优化来实现可见性

- 对 `volatile` 变量写操作时，会在写操作后加入一条 `store` 屏障指令，将本地内存中的共享变量刷新到主内存
- 对 `volatile` 变量读操作时，会在读操作前加入一条 `load` 屏障指令，从主内存中读取共享变量

```java
package com.example.concurrent;

import java.util.Arrays;
import java.util.Random;

public class TestVolatile {
    private volatile boolean isSorted = Boolean.FALSE;
    private static final int MAX_COUNT = 10000;

    private final int[] array = new int[MAX_COUNT];

    public TestVolatile() {
        final Random random = new Random();
        for (int i = 0; i < MAX_COUNT; i++) {
            array[i] = random.nextInt(MAX_COUNT);
        }
    }

    public void sort() {
        Arrays.sort(array);
        isSorted = Boolean.TRUE;
    }

    public void increment() {
        while (!isSorted) {
            System.out.println("waiting for sort!!!");
        }
        int sums = 0;
        for (int i = 0; i < array.length; i++) {
            array[i] *= 10;
            sums += array[i];
        }
        System.out.println(sums);
    }

    public static void main(String[] args) {
        TestVolatile testVolatile = new TestVolatile();
        new Thread(testVolatile::sort).start();
        new Thread(testVolatile::increment).start();
    }
}
```

## Publish

- 发布对象：使一个对象能够被当前范围之外的代码所使用
- 对象溢出：一种错误的发布，当一个对象还没有构造完成时，就使它被其它线程所见

```java
package com.example.concurrent;

import java.util.Objects;

/**
 * 双重同步锁单例模式
 */
public class SingletonExample1 {

    // 私有构造函数
    private SingletonExample1() {
        System.out.println("SingletonExample1");
    }

    // 单例对象
    // 加入 volatile 防止指令重排序
    private static volatile SingletonExample1 instance;

    // 静态工厂方法
    public static SingletonExample1 getInstance() {
        if (Objects.isNull(instance)) { // 双重检测
            synchronized (SingletonExample1.class) { // 同步锁
                if (Objects.isNull(instance)) {
                    instance = new SingletonExample1();
                }
            }
        }
        return instance;
    }
}
```

```java
package com.example.concurrent;

/**
 * 枚举单例模式
 */
public class SingletonExample2 {

    // 私有构造函数
    private SingletonExample2() {
        System.out.println("SingletonExample1");
    }

    public static SingletonExample2 getInstance() {
        return Singleton.INSTANCE.getInstance();
    }

    private enum Singleton {
        INSTANCE;

        private SingletonExample2 singleton;

        // JVM 保证此方法绝对只调用一次
        Singleton() {
            singleton = new SingletonExample2();
        }

        public SingletonExample2 getInstance() {
            return singleton;
        }
    }
}
```

Last Modified 2022-01-05

# 并发执行

## 并发

处理多个待定任务，一次处理一个或并行处理多个（如果条件允许），直到所有任务最终都成功或失败。对于单核 CPU，如果操作系统的调度程序支持交叉执行待定任务，也能实现并发。并发也叫多任务处理（multitasking）。

## 并行

同时执行多个计算任务的能力。需要一个多核 CPU、多个 CPU、一个 GPU 或一个集群中的多台计算机。

## 执行单元

并发执行代码的对象的统称，每个对象的状态和调用栈是独立的。Python 原生支持 3 种执行单元：进程、线程和协程。

## 进程

计算机程序运行时的一个实例，消耗内存和部分 CPU 时间。现代桌面操作系统通常同时管理数百个进程，每个进程都隔离在自己的私有内存空间中。进程通过管道、套接字或内存映射文件进行通信——这些方式都只能携带原始字节。Python 对象必须序列化（转换）为原始字节才能从一个进程传递到另一个进程。这个过程耗费资源，而且不是所有 Python 对象都可以序列化。进程可以派生子进程，子进程彼此之间以及与父进程之间是隔离的。进程支持抢占式多任务处理机制：操作系统调度程序定期抢占（挂起）运行中的进程，让其他进程运行。这意味着冻结的进程理论上不会冻结整个系统。

## 线程

单个进程中的执行单元。一个进程启动后，只使用一个线程，即主线程。通过调用操作系统 API，进程可以创建更多线程，执行并发操作。一个进程内的线程共享相同的内存空间（存储活动的 Python 对象）。因此，线程之间可以轻松地共享数据，但是如果多个线程同时更新同一个对象，则可能导致数据损坏。与进程一样，线程在操作系统调度程序的监督下也可以实现抢占式多任务处理。对于同一份作业，线程消耗的资源比进程少。

## 协程

可以挂起自身并在以后恢复的函数。在 Python 中，经典协程由生成器函数构建，原生协程使用 async def 定义。Python 协程通常在事件循环（也在同一个线程中）的监督下在单个线程中运行。asyncio、Curio 或 Trio 等异步编程框架为基于协程的非阻塞 I/O 提供了事件循环和支持库。协程支持协作式多任务处理：一个协程必须使用 yield 或 await 关键字显式放弃控制权，另一个协程才可以并发（而非并行）开展工作。这意味着，协程中只要有导致阻塞的代码，事件循环和其他所有协程的执行就都会受到阻塞——这一点与进程和线程的抢占式多任务处理形成鲜明对比。另外，对于同一份作业，协程消耗的资源比线程或进程少。

## 队列

一种数据结构，可以放入和取出项，顺序通常是先入先出（FIFO）。独立的执行单元可以通过队列交换应用数据和控制消息，例如错误代码和终止信号。队列的实现因底层并发模型而异：Python 标准库中的 queue 包提供的队列类支持线程，multiprocessing 和 asyncio 包则实现了其他队列类。queue 和 asyncio 包中还有非先入先出队列: LifoQueue 和 PriorityQueue。

## 锁

一种供执行单元用来同步操作和避免数据损坏的对象。更新共享数据结构时，当前代码应持有相关的锁，并告诉程序的其他部分等到锁被释放后再访问这个数据结构。最简单的锁是互斥锁。锁的实现取决于底层并发模型。

## 争用

对有限资源的争夺。当多个执行单元尝试访问共享资源（例如锁或存储器）时，就会发生资源争用。当计算密集型进程或线程必须等待操作系统调度程序为其分配 CPU 时间时，还会发生 CPU 争用。

## GIL

1. Python 解释器的每个实例是一个进程。使用 multiprocessing 或 concurrent. futures 库可以启动额外的 Python 进程。 Python 的 subprocess 库用于启动运行外部程序（不管使用何种语言编写）的进程。

2. Python 解释器仅使用一个线程运行用户的程序和内存垃圾回收程序。使用 threading 或 concurrent. futures 库可以启动外的 Python 线程。

3. 对对象引用计数和解释器其他内部状态的访问受一个锁的控制，这个锁是“全局解释器商念讲锁”（Global Interpreter Lock，GIL）。任意时间点上只有一个 Python 线程可以持有 GIL。这意味着，任意时间点上只有一个线程能执行 Python 代码，与 CPU 核数量无关。

4. 为了防止一个 Python 线程无限期持有 GIL，Python 的字节码解释器默认每 5 毫秒暂停当前 Python 线程，释放 GIL。被暂停的线程可以再次尝试获得 GIL，但是如果有其他线程等待，那么操作系统调度程序可能会从中挑选一个线程开展工作。

5. 我们编写的 Python 代码无法控制 GIL。但是，耗时的任务可由内置函数或 C 语言（以及其他能在 Python/C API 层级接合的语言）扩展释放 GIL。

6. Python 标准库中发起系统调用的函数均可释放 GIL。这包括所有执行磁盘 I/O、网络 I/O、async 的函数，以及`time.sleep()`。NumPy/SciPy 库中很多 CPU 密集型函数，以及 zlib 和 bz2 模块中执行压缩和解压操作的函数，也都释放 GIL。

7. 在 Python/C API 层级集成的扩展也可以启动不受 GIL 影响的非 Python 线程。这些不受 GIL 影响的线程无法更改 Python 对象，但是可以读取或写入内存中支持缓冲协议的底层对象，例如 `bytearray`，`array.array` 和 NumPy 数组。

8. GIL 对使用 Python 线程进行网络编程的影响相对较小，因为 I/O 函数释放 GIL，而且与内存读写相比，网络读写的延迟始终很高。各个单独的线程无论如何都要花费大量时间等待，所以线程可以交错执行，对整体吞吐量不会产生重大影响。

9. 对 GIL 的争用会降低计算密集型 Python 线程的速度。对于这类任务，在单线程中依序执行的代码更简单，速度也更快。

10. 若想在多核上运行 CPU 密集型 Python 代码，必须使用多个 Python 进程。

## 素数计算

```python
"""
primes.py
"""

import math

PRIME_FIXTURE = [
    (2, True),
    (142702110479723, True),
    (299593572317531, True),
    (3333333333333301, True),
    (3333333333333333, False),
    (3333335652092209, False),
    (4444444444444423, True),
    (4444444444444444, False),
    (4444444488888889, False),
    (5555553133149889, False),
    (5555555555555503, True),
    (5555555555555555, False),
    (6666666666666666, False),
    (6666666666666719, True),
    (6666667141414921, False),
    (7777777536340681, False),
    (7777777777777753, True),
    (7777777777777777, False),
    (9999999999999917, True),
    (9999999999999999, False),
]

NUMBERS = [n for n, _ in PRIME_FIXTURE]


def is_prime(n: int) -> bool:
    if n < 2:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False

    root = math.isqrt(n)
    for i in range(3, root + 1, 2):
        if n % i == 0:
            return False
    return True


if __name__ == '__main__':

    for n, prime in PRIME_FIXTURE:
        prime_res = is_prime(n)
        assert prime_res == prime
        print(n, prime)
```

```python
"""
procs.py: shows that multiprocessing on a multicore machine
can be faster than sequential code for CPU-intensive work.
"""

import sys
from multiprocessing import Process, SimpleQueue, cpu_count  # <1>
from multiprocessing import queues  # <2>
from time import perf_counter
from typing import NamedTuple

from primes import is_prime, NUMBERS


class PrimeResult(NamedTuple):  # <3>
    n: int
    prime: bool
    elapsed: float


JobQueue = queues.SimpleQueue[int]  # <4>
ResultQueue = queues.SimpleQueue[PrimeResult]  # <5>


def check(n: int) -> PrimeResult:  # <6>
    t0 = perf_counter()
    res = is_prime(n)
    # 返回一个包装的结果
    return PrimeResult(n, res, perf_counter() - t0)


def worker(jobs: JobQueue, results: ResultQueue) -> None:  # <7>
    # 持续地从队列中取出数据，如果数据是非 0 的则计算素数
    while n := jobs.get():  # <8>
        # 将素数计算处理结果放入队列
        results.put(check(n))  # <9>
    # 如果队列中没有数据了则将空结果放入队列，告诉主进程工作已经结束
    results.put(PrimeResult(0, False, 0.0))  # <10>


def start_jobs(
        procs: int, jobs: JobQueue, results: ResultQueue  # <11>
) -> None:
    for n in NUMBERS:
        jobs.put(n)  # <12>
    # 按参数给定的 worker 数量启动子进程
    for _ in range(procs):
        proc = Process(target=worker, args=(jobs, results))  # <13>
        proc.start()  # <14>
        # 把 0 放入队列中，当所有有些的数据（非零的，即要判断是否为素数的数据）都取出后，
        # 通过放入的 0 来告诉工作（子）进程工作结束了
        jobs.put(0)  # <15>


def main() -> None:
    if len(sys.argv) < 2:  # <1>
        procs = cpu_count()
    else:
        procs = int(sys.argv[1])

    print(f'Checking {len(NUMBERS)} numbers with {procs} processes:')
    t0 = perf_counter()
    # jobs 放入即将要计算的数据
    jobs: JobQueue = SimpleQueue()  # <2>
    # results 放入计算后的结果
    results: ResultQueue = SimpleQueue()
    start_jobs(procs, jobs, results)  # <3>
    checked = report(procs, results)  # <4>
    elapsed = perf_counter() - t0
    print(f'{checked} checks in {elapsed:.2f}s')  # <5>


def report(procs: int, results: ResultQueue) -> int:  # <6>
    checked = 0
    procs_done = 0
    # 循环从结果队列中取出数据
    while procs_done < procs:  # <7>
        # 这里的 get() 方法调用后会阻塞当前进程
        n, prime, elapsed = results.get()  # <8>
        # 如果取到的是 0 则表示工作（子）进程已经结束了
        if n == 0:  # <9>
            procs_done += 1
        else:
            # 累计已计算完成的数据量
            checked += 1  # <10>
            label = 'P' if prime else ' '
            print(f'{n:16}  {label} {elapsed:9.6f}s')
    return checked


if __name__ == '__main__':
    main()
```

```bash
python3 procs.py 6
python3 procs.py 12
```

Last Modified 2023-06-10

# 协程

## 简单实现

执行结束后会抛出异常`StopIteration`

```python
"""
>>> coro = simple_coroutine()
>>> next(coro)
-> coroutine started
>>> coro.send(42)
-> coroutine received 42
Traceback (most recent call last):
  ...
StopIteration
>>> c = simple_coroutine2(42)
>>> next(c)
-> Started a = 42
42
>>> c.send(24)
-> Received b = 24
66
>>> c.send(24)
-> Received c = 24
Traceback (most recent call last):
  ...
StopIteration
"""


def simple_coroutine():
    print('-> coroutine started')
    x = yield
    print(f'-> coroutine received {x}')


def simple_coroutine2(a):
    print(f'-> Started a = {a}')
    b = yield a
    print(f'-> Received b = {b}')
    c = yield a + b
    print(f'-> Received c = {c}')
```

## 预先激活协程

```python
"""
A coroutine to compute a running average

    >>> coro_avg = averager()  # <1>
    >>> from inspect import getgeneratorstate
    >>> getgeneratorstate(coro_avg)  # <2>
    'GEN_SUSPENDED'
    >>> coro_avg.send(10)  # <3>
    10.0
    >>> coro_avg.send(30)
    20.0
    >>> coro_avg.send(5)
    15.0

"""

from functools import wraps


def coroutine(func):  # <4>
    """Decorator: primes `func` by advancing to first `yield`"""

    @wraps(func)
    def primer(*args, **kwargs):  # <1>
        gen = func(*args, **kwargs)  # <2>
        next(gen)  # <3>
        return gen  # <4>

    return primer


@coroutine  # <5>
def averager():  # <6>
    total = 0.0
    count = 0
    average = None
    while True:
        term = yield average
        total += term
        count += 1
        average = total / count
```

## 处理异常

```python
"""
Coroutine closing demonstration::

    >>> exc_coro = demo_exc_handling()
    >>> next(exc_coro)
    -> coroutine started
    >>> exc_coro.send(11)
    -> coroutine received: 11
    >>> exc_coro.send(22)
    -> coroutine received: 22
    >>> exc_coro.close()
    >>> from inspect import getgeneratorstate
    >>> getgeneratorstate(exc_coro)
    'GEN_CLOSED'


Coroutine handling exception::

    >>> exc_coro = demo_exc_handling()
    >>> next(exc_coro)
    -> coroutine started
    >>> exc_coro.send(11)
    -> coroutine received: 11
    >>> exc_coro.throw(DemoException)
    *** DemoException handled. Continuing...
    >>> getgeneratorstate(exc_coro)
    'GEN_SUSPENDED'


Coroutine not handling exception::

    >>> exc_coro = demo_exc_handling()
    >>> next(exc_coro)
    -> coroutine started
    >>> exc_coro.send(11)
    -> coroutine received: 11
    >>> exc_coro.throw(ZeroDivisionError)
    Traceback (most recent call last):
      ...
    ZeroDivisionError
    >>> getgeneratorstate(exc_coro)
    'GEN_CLOSED'


Second coroutine closing demonstration::

    >>> fin_coro = demo_finally()
    >>> next(fin_coro)
    -> coroutine started
    >>> fin_coro.send(11)
    -> coroutine received: 11
    >>> fin_coro.send(22)
    -> coroutine received: 22
    >>> fin_coro.close()
    -> coroutine ending


Second coroutine not handling exception::

    >>> fin_coro = demo_finally()
    >>> next(fin_coro)
    -> coroutine started
    >>> fin_coro.send(11)
    -> coroutine received: 11
    >>> fin_coro.throw(ZeroDivisionError)  # doctest: +SKIP
    -> coroutine ending
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
      File "coro_exception_demos.py", line 109, in demo_finally
        print('-> coroutine received: {!r}'.format(x))
    ZeroDivisionError


The last test above must be skipped because the output '-> coroutine ending'
is not detected by doctest, which raises a false error. However, if you
run this file as shown below, you'll see that output "leak" into standard
output::


    $ python3 -m doctest coro_exception_demos.py
    -> coroutine ending
"""


class DemoException(Exception):
    """An exception type for the demonstration."""


def demo_exc_handling():
    print('-> coroutine started')
    while True:
        try:
            x = yield
        except DemoException:  # <1>
            print('*** DemoException handled. Continuing...')
        else:  # <2>
            print('-> coroutine received: {!r}'.format(x))
    raise RuntimeError('This line should never run.')  # <3>


def demo_finally():
    print('-> coroutine started')
    try:
        while True:
            try:
                x = yield
            except DemoException:
                print('*** DemoException handled. Continuing...')
            else:
                print('-> coroutine received: {!r}'.format(x))
    finally:
        print('-> coroutine ending')
```

## yield from 伪代码

```python
# Code below is the expansion of the statement:
#
# RESULT = yield from EXPR
#
# Copied verbatim from the Formal Semantics section of
# PEP 380 -- Syntax for Delegating to a Subgenerator
#
# https://www.python.org/dev/peps/pep-0380/#formal-semantics


_i = iter(EXPR)  # <1>
try:
    _y = next(_i)  # <2>
except StopIteration as _e:
    _r = _e.value  # <3>
else:
    while 1:  # <4>
        try:
            _s = yield _y  # <5>
        except GeneratorExit as _e:  # <6>
            try:
                _m = _i.close
            except AttributeError:
                pass
            else:
                _m()
            raise _e
        except BaseException as _e:  # <7>
            _x = sys.exc_info()
            try:
                _m = _i.throw
            except AttributeError:
                raise _e
            else:  # <8>
                try:
                    _y = _m(*_x)
                except StopIteration as _e:
                    _r = _e.value
                    break
        else:  # <9>
            try:  # <10>
                if _s is None:  # <11>
                    _y = next(_i)
                else:
                    _y = _i.send(_s)
            except StopIteration as _e:  # <12>
                _r = _e.value
                break

RESULT = _r  # <13>
```

- 迭代器产生的任何值都直接传递给调用者。
- 使用 `send()` 发送到委托生成器的任何值都直接传递给迭代器。如果发送的值为 `None`，则调用迭代器的 `__next__()` 方法。如果发送的值不是 `None`，则调用迭代器的 send() 方法。如果调用引发 `StopIteration`，则恢复委托生成器。任何其他异常都会传播到委托生成器。
- 抛出到委托生成器中的 `GeneratorExit` 以外的异常被传递给迭代器的 `throw()` 方法。如果调用引发 `StopIteration`，则恢复委托生成器。任何其他异常都会传播到委托生成器。
- 如果一个 `GeneratorExit` 异常被抛出到委托生成器中，或者委托生成器的 `close()` 方法被调用，那么迭代器的 `close()` 方法被调用，如果它有一个。如果此调用导致异常，则将其传播到委托生成器。否则，在委托生成器中引发 `GeneratorExit`。
- yield from 表达式的值是迭代器终止时引发的 `StopIteration` 异常的第一个参数。
- 生成器中的 return expr 导致 `StopIteration`(expr) 在从生成器退出时引发。

## 协程仿真程序

```python
"""
Taxi simulator

Sample run with two cars, random seed 10. This is a valid doctest.

    >>> main(num_taxis=2, seed=10)
    taxi: 0  Event(time=0, proc=0, action='leave garage')
    taxi: 0  Event(time=4, proc=0, action='pick up passenger')
    taxi: 1     Event(time=5, proc=1, action='leave garage')
    taxi: 1     Event(time=9, proc=1, action='pick up passenger')
    taxi: 0  Event(time=10, proc=0, action='drop off passenger')
    taxi: 1     Event(time=12, proc=1, action='drop off passenger')
    taxi: 0  Event(time=17, proc=0, action='pick up passenger')
    taxi: 1     Event(time=19, proc=1, action='pick up passenger')
    taxi: 1     Event(time=21, proc=1, action='drop off passenger')
    taxi: 1     Event(time=24, proc=1, action='pick up passenger')
    taxi: 0  Event(time=28, proc=0, action='drop off passenger')
    taxi: 1     Event(time=28, proc=1, action='drop off passenger')
    taxi: 0  Event(time=29, proc=0, action='going home')
    taxi: 1     Event(time=30, proc=1, action='pick up passenger')
    taxi: 1     Event(time=61, proc=1, action='drop off passenger')
    taxi: 1     Event(time=62, proc=1, action='going home')
    *** end of events ***

See explanation and longer sample run at the end of this module.

"""

import argparse
import collections
import queue
import random

DEFAULT_NUMBER_OF_TAXIS = 3
DEFAULT_END_TIME = 80
SEARCH_DURATION = 4
TRIP_DURATION = 10
DEPARTURE_INTERVAL = 5

Event = collections.namedtuple('Event', 'time proc action')


def compute_delay(interval):
    """Compute action delay using exponential distribution"""
    return int(random.expovariate(1 / interval)) + 1


def taxi_process(ident, trips, start_time=0):  # <1>
    """Yield to simulator issuing event at each state change"""
    time = yield Event(start_time, ident, 'leave garage')  # <2>
    for i in range(trips):  # <3>
        prowling_ends = time + compute_delay(SEARCH_DURATION)  # <4>
        time = yield Event(prowling_ends, ident, 'pick up passenger')  # <5>

        trip_ends = time + compute_delay(TRIP_DURATION)  # <6>
        time = yield Event(trip_ends, ident, 'drop off passenger')  # <7>

    yield Event(time + 1, ident, 'going home')  # <8>
    # end of taxi process # <9>


class Simulator:

    def __init__(self, procs_map):
        self.events = queue.PriorityQueue()
        self.procs = dict(procs_map)

    def run(self, end_time):  # <1>
        """Schedule and display events until time is up"""
        # schedule the first event for each cab
        for _, proc in sorted(self.procs.items()):  # <2>
            first_event = next(proc)  # <3>
            self.events.put(first_event)  # <4>

        # main loop of the simulation
        time = 0
        while time < end_time:  # <5>
            if self.events.empty():  # <6>
                print('*** end of events ***')
                break

            # get and display current event
            current_event = self.events.get()  # <7>
            print('taxi:', current_event.proc,  # <8>
                  current_event.proc * '   ', current_event)

            # schedule next action for current proc
            time = current_event.time  # <9>
            proc = self.procs[current_event.proc]  # <10>
            try:
                next_event = proc.send(time)  # <11>
            except StopIteration:
                del self.procs[current_event.proc]  # <12>
            else:
                self.events.put(next_event)  # <13>
        else:  # <14>
            msg = '*** end of simulation time: {} events pending ***'
            print(msg.format(self.events.qsize()))


def main(end_time=DEFAULT_END_TIME, num_taxis=DEFAULT_NUMBER_OF_TAXIS,
         seed=None):
    """Initialize random generator, build procs and run simulation"""
    if seed is not None:
        random.seed(seed)  # get reproducible results

    taxis = {i: taxi_process(i, (i + 1) * 2, i * DEPARTURE_INTERVAL)
             for i in range(num_taxis)}
    sim = Simulator(taxis)
    sim.run(end_time)


if __name__ == '__main__':
    parser = argparse.ArgumentParser(
        description='Taxi fleet simulator.')
    parser.add_argument('-e', '--end-time', type=int,
                        default=DEFAULT_END_TIME,
                        help='simulation end time; default = %s'
                             % DEFAULT_END_TIME)
    parser.add_argument('-t', '--taxis', type=int,
                        default=DEFAULT_NUMBER_OF_TAXIS,
                        help='number of taxis running; default = %s'
                             % DEFAULT_NUMBER_OF_TAXIS)
    parser.add_argument('-s', '--seed', type=int, default=None,
                        help='random generator seed (for testing)')

    args = parser.parse_args()
    main(args.end_time, args.taxis, args.seed)

"""
Notes for the ``taxi_process`` coroutine::

<1> `taxi_process` will be called once per taxi, creating a generator
    object to represent its operations. `ident` is the number of the taxi
    (eg. 0, 1, 2 in the sample run); `trips` is the number of trips this
    taxi will make before going home; `start_time` is when the taxi
    leaves the garage.

<2> The first `Event` yielded is `'leave garage'`. This suspends the
    coroutine, and lets the simulation main loop proceed to the next
    scheduled event. When it's time to reactivate this process, the main
    loop will `send` the current simulation time, which is assigned to
    `time`.

<3> This block will be repeated once for each trip.

<4> The ending time of the search for a passenger is computed.

<5> An `Event` signaling passenger pick up is yielded. The coroutine
    pauses here. When the time comes to reactivate this coroutine,
    the main loop will again `send` the current time.

<6> The ending time of the trip is computed, taking into account the
    current `time`.

<7> An `Event` signaling passenger drop off is yielded. Coroutine
    suspended again, waiting for the main loop to send the time of when
    it's time to continue.

<8> The `for` loop ends after the given number of trips, and a final
    `'going home'` event is yielded, to happen 1 minute after the current
    time. The coroutine will suspend for the last time. When reactivated,
    it will be sent the time from the simulation main loop, but here I
    don't assign it to any variable because it will not be useful.

<9> When the coroutine falls off the end, the coroutine object raises
    `StopIteration`.


Notes for the ``Simulator.run`` method::

<1> The simulation `end_time` is the only required argument for `run`.

<2> Use `sorted` to retrieve the `self.procs` items ordered by the
    integer key; we don't care about the key, so assign it to `_`.

<3> `next(proc)` primes each coroutine by advancing it to the first
    yield, so it's ready to be sent data. An `Event` is yielded.

<4> Add each event to the `self.events` `PriorityQueue`. The first
    event for each taxi is `'leave garage'`, as seen in the sample run
    (ex_taxi_process>>).

<5> Main loop of the simulation: run until the current `time` equals
    or exceeds the `end_time`.

<6> The main loop may also exit if there are no pending events in the
    queue.

<7> Get `Event` with the smallest `time` in the queue; this is the
    `current_event`.

<8> Display the `Event`, identifying the taxi and adding indentation
    according to the taxi id.

<9> Update the simulation time with the time of the `current_event`.

<10> Retrieve the coroutine for this taxi from the `self.procs`
     dictionary.

<11> Send the `time` to the coroutine. The coroutine will yield the
     `next_event` or raise `StopIteration` it's finished.

<12> If `StopIteration` was raised, delete the coroutine from the
     `self.procs` dictionary.

<13> Otherwise, put the `next_event` in the queue.

<14> If the loop exits because the simulation time passed, display the
     number of events pending (which may be zero by coincidence,
     sometimes).


Sample run from the command line, seed=24, total elapsed time=160::

$ python3 taxi_sim.py -s 24 -e 160
taxi: 0  Event(time=0, proc=0, action='leave garage')
taxi: 0  Event(time=5, proc=0, action='pick up passenger')
taxi: 1     Event(time=5, proc=1, action='leave garage')
taxi: 1     Event(time=6, proc=1, action='pick up passenger')
taxi: 2        Event(time=10, proc=2, action='leave garage')
taxi: 2        Event(time=11, proc=2, action='pick up passenger')
taxi: 2        Event(time=23, proc=2, action='drop off passenger')
taxi: 0  Event(time=24, proc=0, action='drop off passenger')
taxi: 2        Event(time=24, proc=2, action='pick up passenger')
taxi: 2        Event(time=26, proc=2, action='drop off passenger')
taxi: 0  Event(time=30, proc=0, action='pick up passenger')
taxi: 2        Event(time=31, proc=2, action='pick up passenger')
taxi: 0  Event(time=43, proc=0, action='drop off passenger')
taxi: 0  Event(time=44, proc=0, action='going home')
taxi: 2        Event(time=46, proc=2, action='drop off passenger')
taxi: 2        Event(time=49, proc=2, action='pick up passenger')
taxi: 1     Event(time=70, proc=1, action='drop off passenger')
taxi: 2        Event(time=70, proc=2, action='drop off passenger')
taxi: 2        Event(time=71, proc=2, action='pick up passenger')
taxi: 2        Event(time=79, proc=2, action='drop off passenger')
taxi: 1     Event(time=88, proc=1, action='pick up passenger')
taxi: 2        Event(time=92, proc=2, action='pick up passenger')
taxi: 2        Event(time=98, proc=2, action='drop off passenger')
taxi: 2        Event(time=99, proc=2, action='going home')
taxi: 1     Event(time=102, proc=1, action='drop off passenger')
taxi: 1     Event(time=104, proc=1, action='pick up passenger')
taxi: 1     Event(time=135, proc=1, action='drop off passenger')
taxi: 1     Event(time=136, proc=1, action='pick up passenger')
taxi: 1     Event(time=151, proc=1, action='drop off passenger')
taxi: 1     Event(time=152, proc=1, action='going home')
*** end of events ***

"""
```

## 有延时的仿真

```python
"""
Taxi simulator with delay on output
===================================

This is a variation of ``taxi_sim.py`` which adds a ``-d`` comand-line
option. When given, that option adds a delay in the main loop, pausing
the simulation for .5s for each "minute" of simulation time.


Driving a taxi from the console::

    >>> taxi = taxi_process(ident=13, trips=2, start_time=0)
    >>> next(taxi)
    Event(time=0, proc=13, action='leave garage')
    >>> taxi.send(_.time + 7)
    Event(time=7, proc=13, action='pick up passenger')
    >>> taxi.send(_.time + 23)
    Event(time=30, proc=13, action='drop off passenger')
    >>> taxi.send(_.time + 5)
    Event(time=35, proc=13, action='pick up passenger')
    >>> taxi.send(_.time + 48)
    Event(time=83, proc=13, action='drop off passenger')
    >>> taxi.send(_.time + 1)
    Event(time=84, proc=13, action='going home')
    >>> taxi.send(_.time + 10)
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    StopIteration

Sample run with two cars, random seed 10. This is a valid doctest::

    >>> main(num_taxis=2, seed=10)
    taxi: 0  Event(time=0, proc=0, action='leave garage')
    taxi: 0  Event(time=5, proc=0, action='pick up passenger')
    taxi: 1     Event(time=5, proc=1, action='leave garage')
    taxi: 1     Event(time=10, proc=1, action='pick up passenger')
    taxi: 1     Event(time=15, proc=1, action='drop off passenger')
    taxi: 0  Event(time=17, proc=0, action='drop off passenger')
    taxi: 1     Event(time=24, proc=1, action='pick up passenger')
    taxi: 0  Event(time=26, proc=0, action='pick up passenger')
    taxi: 0  Event(time=30, proc=0, action='drop off passenger')
    taxi: 0  Event(time=34, proc=0, action='going home')
    taxi: 1     Event(time=46, proc=1, action='drop off passenger')
    taxi: 1     Event(time=48, proc=1, action='pick up passenger')
    taxi: 1     Event(time=110, proc=1, action='drop off passenger')
    taxi: 1     Event(time=139, proc=1, action='pick up passenger')
    taxi: 1     Event(time=140, proc=1, action='drop off passenger')
    taxi: 1     Event(time=150, proc=1, action='going home')
    *** end of events ***

See longer sample run at the end of this module.

"""

import random
import collections
import queue
import argparse
import time

DEFAULT_NUMBER_OF_TAXIS = 3
DEFAULT_END_TIME = 180
SEARCH_DURATION = 5
TRIP_DURATION = 20
DEPARTURE_INTERVAL = 5

Event = collections.namedtuple('Event', 'time proc action')


def taxi_process(ident, trips, start_time=0):  # <1>
    """Yield to simulator issuing event at each state change"""
    time = yield Event(start_time, ident, 'leave garage')  # <2>
    for i in range(trips):  # <3>
        time = yield Event(time, ident, 'pick up passenger')  # <4>
        time = yield Event(time, ident, 'drop off passenger')  # <5>

    yield Event(time, ident, 'going home')  # <6>
    # end of taxi process # <7>


class Simulator:

    def __init__(self, procs_map):
        self.events = queue.PriorityQueue()
        self.procs = dict(procs_map)

    def run(self, end_time, delay=False):  # <1>
        """Schedule and display events until time is up"""
        # schedule the first event for each cab
        for _, proc in sorted(self.procs.items()):  # <2>
            first_event = next(proc)  # <3>
            self.events.put(first_event)  # <4>

        # main loop of the simulation
        sim_time = 0  # <5>
        while sim_time < end_time:  # <6>
            if self.events.empty():  # <7>
                print('*** end of events ***')
                break

            # get and display current event
            current_event = self.events.get()  # <8>
            if delay:
                time.sleep((current_event.time - sim_time) / 2)
            # update the simulation time
            sim_time, proc_id, previous_action = current_event
            print('taxi:', proc_id, proc_id * '   ', current_event)
            active_proc = self.procs[proc_id]
            # schedule next action for current proc
            next_time = sim_time + compute_duration(previous_action)
            try:
                next_event = active_proc.send(next_time)  # <12>
            except StopIteration:
                del self.procs[proc_id]  # <13>
            else:
                self.events.put(next_event)  # <14>
        else:  # <15>
            msg = '*** end of simulation time: {} events pending ***'
            print(msg.format(self.events.qsize()))


def compute_duration(previous_action):
    """Compute action duration using exponential distribution"""
    if previous_action in ['leave garage', 'drop off passenger']:
        # new state is prowling
        interval = SEARCH_DURATION
    elif previous_action == 'pick up passenger':
        # new state is trip
        interval = TRIP_DURATION
    elif previous_action == 'going home':
        interval = 1
    else:
        raise ValueError('Unknown previous_action: %s' % previous_action)
    return int(random.expovariate(1 / interval)) + 1


def main(end_time=DEFAULT_END_TIME, num_taxis=DEFAULT_NUMBER_OF_TAXIS,
         seed=None, delay=False):
    """Initialize random generator, build procs and run simulation"""
    if seed is not None:
        random.seed(seed)  # get reproducible results

    taxis = {i: taxi_process(i, (i + 1) * 2, i * DEPARTURE_INTERVAL)
             for i in range(num_taxis)}
    sim = Simulator(taxis)
    sim.run(end_time, delay)


if __name__ == '__main__':
    parser = argparse.ArgumentParser(
        description='Taxi fleet simulator.')
    parser.add_argument('-e', '--end-time', type=int,
                        default=DEFAULT_END_TIME,
                        help='simulation end time; default = %s'
                             % DEFAULT_END_TIME)
    parser.add_argument('-t', '--taxis', type=int,
                        default=DEFAULT_NUMBER_OF_TAXIS,
                        help='number of taxis running; default = %s'
                             % DEFAULT_NUMBER_OF_TAXIS)
    parser.add_argument('-s', '--seed', type=int, default=None,
                        help='random generator seed (for testing)')
    parser.add_argument('-d', '--delay', action='store_true',
                        help='introduce delay proportional to simulation time')

    args = parser.parse_args()
    main(args.end_time, args.taxis, args.seed, args.delay)

"""

Sample run from the command line, seed=3, maximum elapsed time=120::

$ python3 taxi_sim.py -s 3 -e 120
taxi: 0  Event(time=0, proc=0, action='leave garage')
taxi: 0  Event(time=2, proc=0, action='pick up passenger')
taxi: 1     Event(time=5, proc=1, action='leave garage')
taxi: 1     Event(time=8, proc=1, action='pick up passenger')
taxi: 2        Event(time=10, proc=2, action='leave garage')
taxi: 2        Event(time=15, proc=2, action='pick up passenger')
taxi: 2        Event(time=17, proc=2, action='drop off passenger')
taxi: 0  Event(time=18, proc=0, action='drop off passenger')
taxi: 2        Event(time=18, proc=2, action='pick up passenger')
taxi: 2        Event(time=25, proc=2, action='drop off passenger')
taxi: 1     Event(time=27, proc=1, action='drop off passenger')
taxi: 2        Event(time=27, proc=2, action='pick up passenger')
taxi: 0  Event(time=28, proc=0, action='pick up passenger')
taxi: 2        Event(time=40, proc=2, action='drop off passenger')
taxi: 2        Event(time=44, proc=2, action='pick up passenger')
taxi: 1     Event(time=55, proc=1, action='pick up passenger')
taxi: 1     Event(time=59, proc=1, action='drop off passenger')
taxi: 0  Event(time=65, proc=0, action='drop off passenger')
taxi: 1     Event(time=65, proc=1, action='pick up passenger')
taxi: 2        Event(time=65, proc=2, action='drop off passenger')
taxi: 2        Event(time=72, proc=2, action='pick up passenger')
taxi: 0  Event(time=76, proc=0, action='going home')
taxi: 1     Event(time=80, proc=1, action='drop off passenger')
taxi: 1     Event(time=88, proc=1, action='pick up passenger')
taxi: 2        Event(time=95, proc=2, action='drop off passenger')
taxi: 2        Event(time=97, proc=2, action='pick up passenger')
taxi: 2        Event(time=98, proc=2, action='drop off passenger')
taxi: 1     Event(time=106, proc=1, action='drop off passenger')
taxi: 2        Event(time=109, proc=2, action='going home')
taxi: 1     Event(time=110, proc=1, action='going home')
*** end of events ***

"""
```

Last Modified 2022-07-31

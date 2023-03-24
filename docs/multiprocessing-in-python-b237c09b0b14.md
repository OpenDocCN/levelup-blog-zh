# Python 中的多重处理

> 原文：<https://levelup.gitconnected.com/multiprocessing-in-python-b237c09b0b14>

## Python 中多重处理的基本部分

![](img/f4db20de52b08ba0d7621b5af9369d0e.png)

Jorge Salvador 在 [Unsplash](https://unsplash.com/s/photos/cpu?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在这篇博文中，我们将探讨如何在 Python 中使用多重处理模块。我还有一篇关于 Python 中并发和并行工作的介绍性博文。因此，在本文中，我们将直接处理应用程序，而不是概念和主题解释。

[](https://faun.pub/concurrency-parallelism-in-python-59ea61e34ae0) [## Python 中的并发和并行

### 多线程、多处理、异步和等待

faun.pub](https://faun.pub/concurrency-parallelism-in-python-59ea61e34ae0) 

多重处理允许我们在绕过 GIL 的同时编写并发程序。我们可以在一台机器上使用多个处理器。

让我们从一个简单的例子开始。我们有一个*立方*函数，它计算给定值的立方。让我们对多个值并行运行这个函数。

```
import os
from multiprocessing import Process, current_process

def cube(x: int):
    #the operation
    value = x * x * x

    #get process id from os
    process_id = os.getpid()
    #process id from multiprocessing
    process_name = current_process().name
    print(f"Process ID: {process_id} Process Name: {process_name} x: {x} value: {value}")

if __name__ == '__main__':
    process_list = []
    value_list = [1,2,3,4,5]

    for value in value_list:
        #create process
        process = Process(target=cube, args=(value,))
        process_list.append(process)

        process.start()
```

我们使用 Python 的多重处理包来实现并发。它主要建立在 Python 的线程包之上。所以，他们之间有很多相似之处。你可以在这里找到官方文档[。](https://docs.python.org/3/library/multiprocessing.html)

为了使多重处理工作，我们必须使用 *__name__ == '__main__'* 保护。否则会抛出类似 *freeze_support()* 的错误。您的进程应该在 *__name__ == '__main__ '下声明。*

当然，这适用于像函数式编程这样的工作方式。最重要的是，当一个模块被调用时，进程不应该直接运行。例如，在类方法中包含它是可以的。当您在顶层模块级别使用多处理时，当调用该模块时，该进程会启动一个新进程，而已启动的进程会启动一个新进程，依此类推。你将会在无穷无尽的新过程中结束。

在上面的例子中，我们为循环中的每个值生成了一个新的进程。让我们近距离了解一下*流程*类。

## 过程

**构造函数:** ( *group=None* ， *target=None* ， *name=None* ， *args=()* ， *kwargs={}* ， *** ， *daemon=None* )

我们必须一直使用关键字来调用构造函数。

*   **组**:始终*无*。这个参数是为了线程兼容性。
*   **目标**:是可调用的对象，是流程运行的对象。
*   **名称**:进程的名称。
*   **args** :将传递给目标对象的参数元组。
*   **kwargs** :将传递给目标对象的参数关键字字典。
*   **守护进程**:将进程设置为守护进程的标志。主进程终止后，守护进程也会自行终止。

上面，我们将 cube 函数设置为目标，并将值作为参数发送。

**方法:**

*   **run()** :调用流程中的目标。我们不必调用 *run* 方法，但如果调用，它会运行同一个进程中的所有任务。

```
#just change the start method with the run method above
process.run()

"""
Process ID: 14316 Process Name: MainProcess x: 1 value: 1
Process ID: 14316 Process Name: MainProcess x: 2 value: 8
Process ID: 14316 Process Name: MainProcess x: 3 value: 27
Process ID: 14316 Process Name: MainProcess x: 4 value: 64
Process ID: 14316 Process Name: MainProcess x: 5 value: 125
"""
```

*   **start()** :创建一个新流程，调用 *run* 方法。
*   **join(【超时】)**:决定是否等待流程结束。如果我们不使用 *join* ，父进程可以在其他子进程之前完成，在这种情况下子进程被中止。如果超时值是整数，它最多阻塞给定的秒数。

```
def sleeping():
    print("Before sleep")
    time.sleep(2)
    print("After sleep")

if __name__ == '__main__':
    p = Process(target=sleeping)
    print('Before start')
    p.start()
    print('After start')
    p.join()
    print("After join")

"""
Before start
After start
Before sleep
After sleep
After join
"""

if __name__ == '__main__':
    p = Process(target=sleeping)
    print('Before start')
    p.start()
    print('After start')
    #p.join()
    print("After join")

"""
Before start
After start
After join
Before sleep
After sleep
"""
```

*   **name() :** 返回进程的名称。
*   **is_alive() :** 返回一个进程是否活动的布尔值。

```
if __name__ == '__main__':
    p = Process(target=sleeping, daemon=False)
    print('Before start')
    p.start()
    print(p.is_alive())
    print('After start')
    p.join(timeout=3)
    print("After join")
    print(p.is_alive())

"""
Before start
True
After start
Before sleep
After sleep
After join
False
"""
```

*   **terminate() :** 终止进程。Unix (SIGTERM)信号
*   **kill()** :同终止。在 Unix 系统中，使用另一种信号(SIGKILL)
*   **close() :** 关闭流程。关闭一个进程后，引发一个*值错误*。

**属性:**

*   **守护进程**:一个布尔值，表示进程是否为守护进程。
*   **pid** :进程 id
*   **exitcode** :如果进程还没有终止，则 *None* 。如果正常终止，则为 0。如果它是通过 *sys.exit()，*终止的，那么它就是 *sys.exit()的参数 *N* 。*
*   **authkey** :进程的认证密钥。大概是:*b ' \ xce \ xeay \ x9a \ xcb \ xa6；' d \ xcb \ xa3 \ x9bn \ xbdy \ xe2 \ xd4 { \ x04 \ x955b \ xc9 _ t \ x89 \ xdd \ xe9 \ xe5 * \ x84N \ x19 '*
*   **sentinel** :我们可以用数字设定一个过程结束时哪个准备好。它有类似排队的功能。使用 *join()* 更简单。

```
def cube(numbers):
    for number in numbers:
        time.sleep(0.5)
        value = number * number * number
        print(f"{value} for {number}")

if __name__ == '__main__':
    process_list = []
    numbers = range(10)

    for i in range(5):
        p = Process(target=cube, args=(numbers,))
        process_list.append(p)
        p.start()

    for process in process_list:
        process.join()

    print("completed")

"""
0 for 0
0 for 0
0 for 0
0 for 0
0 for 0
1 for 1
1 for 1
1 for 1
1 for 1
1 for 1
8 for 2
8 for 2
8 for 2
...
"""

#If we invoked join immediately after starting each, then we would be waiting
#for each process to finish
if __name__ == '__main__':
    process_list = []
    numbers = range(10)

    for i in range(5):
        p = Process(target=cube, args=(numbers,))
        process_list.append(p)
        p.start()
        p.join()

"""
0 for 0
1 for 1
8 for 2
27 for 3
64 for 4
125 for 5
216 for 6
343 for 7
512 for 8
729 for 9
0 for 0
1 for 1
8 for 2
27 for 3
""" 
```

## 锁

锁(也称为互斥锁)是一种同步机制，用于防止出现竞争情况。

> *“当线程或进程试图同时访问一个共享资源，并且这种访问会导致错误时，我们经常说程序存在竞争条件，因为线程或进程在“竞争”执行操作。”*并行编程简介

也就是说，当有一个共享变量时，我们使用一个锁来防止另一个进程在实际的进程使用该变量完成其任务之前干扰该变量。

我们有两个函数，一个是在一个值上加一些，另一个是减一些。

```
def add(x: int) -> int:
    for _ in range(50):
        time.sleep(0.01)
        x += 10
    return x

def sub(x: int) -> int:
    for _ in range(50):
        time.sleep(0.01)
        x -= 10
    return x

if __name__ == '__main__':
    x = 1000
    print(f"Initial x: {x}")
    x = add(x)
    print(f"Added x: {x}")
    x = sub(x)
    print(f"Substracted x: {x}")

"""
Initial x: 1000
Added x: 1500
Substracted x: 1000
"""
```

现在，我将在没有锁的情况下对这些函数使用两个进程。

```
import time
from multiprocessing import Process, Value, sharedctypes

def add(x: sharedctypes.Synchronized) -> int:
    for _ in range(50):
        time.sleep(0.01)
        x.value += 10
    return x.value

def sub(x: sharedctypes.Synchronized) -> int:
    for _ in range(50):
        time.sleep(0.01)
        x.value -= 10
    return x.value

if __name__ == '__main__':
    x =Value('i', 1000)
    add_p = Process(target=add, args=(x,))
    sub_p = Process(target=sub, args=(x,))

    add_p.start()
    sub_p.start()

    add_p.join()
    sub_p.join()
    print(x.value)

"""
840
"""
```

首先，我定义了一个共享类型变量。它分配一个共享内存。我们可以通过使用它的*值*属性从中检索值。两个过程结合在一起，最后，我们得到一个与第一个例子不同的值。因为两个进程在竞争更新共享类型变量。

现在，让我们使用锁来避免赛车:

```
import time
from multiprocessing import Process, Value, sharedctypes, Lock

def add(x: sharedctypes.Synchronized, lock: Lock) -> int:
    for _ in range(50):
        time.sleep(0.01)
        lock.acquire()
        x.value += 10
        lock.release()
    return x.value

def sub(x: sharedctypes.Synchronized, lock: Lock) -> int:
    for _ in range(50):
        time.sleep(0.01)
        lock.acquire()
        x.value -= 10
        lock.release()
    return x.value

if __name__ == '__main__':
    x =Value('i', 1000)
    lock = Lock()
    add_p = Process(target=add, args=(x, lock))
    sub_p = Process(target=sub, args=(x, lock))

    add_p.start()
    sub_p.start()

    add_p.join()
    sub_p.join()
    print(x.value)

"""
1000
"""
```

*锁定*类有两种方法:

*   **获取** ( *block=True，timeout=None)* :锁定进程。这样，在其他事情发生之前，它就可以进行自己的操作了。我们还可以提供锁定持续时间的超时限制。
*   **释放**():一旦操作完成，它就释放锁并允许进程完成。

## 泳池

池管理工作进程。

> pool 类可用于管理固定数量的工作线程，在这种简单的情况下，要完成的工作可以分散并独立地分配给工作线程。
> 
> 作业的返回值被求和并作为列表返回。
> 
> 池参数包括进程的数量和启动任务进程时要运行的函数(为每个子进程调用一次)。

*   大数据->池|小数据->流程
*   池只保存正在内存中执行的进程。另一方面，进程将所有数据保存在内存中。因此，当有许多任务时，池是更可取的。

```
import time
import os
from multiprocessing import Pool

def cube(number):
    total = 0
    for i in range(number):
        total += i * i * i
    return total

if __name__ == '__main__':
    cpus = os.cpu_count() #cpu count available in machine
    numbers = range(5)
    p = Pool(cpus-1) #a pool with cpu_count - 1 cores (default is all cores)
    result = p.map(cube, numbers) 
    print(result)
    p.close()
    p.join()

"""
[0, 0, 1, 9, 36]
"""
```

```
import time
import os
from multiprocessing import Pool

def cube(number):
    total = 0
    for i in range(number):
        total += i * i * i
    return total

def cubing_multiprocess(numbers):
    cpus = os.cpu_count()
    numbers = range(5)
    start = time.time()
    p = Pool(cpus-1)
    result = p.map(cube, numbers)
    p.close()
    p.join()
    end = time.time() - start
    print("Multiprocess time: ",end)

def cubing_serial(numbers):
    start = time.time()
    result = []
    for i in numbers:
        result.append(cube(i))
    end = time.time() - start
    print("Serial time: ",end)

if __name__ == '__main__':
    #number = range(100)
    number = range(10000)
    cubing_multiprocess(number)
    cubing_serial(number)

"""
for range 100: 
Multiprocess time:  0.1426091194152832
Serial time:  0.0005009174346923828

for range 10000:
Multiprocess time:  0.15067601203918457
Serial time:  5.112032175064087

As the workload increases, the benefit of working in parallel 
becomes more apparent.
"""
```

**方法:**

*   **apply** ():接受任务并在阻塞的同时执行，直到结果就绪。返回任务的结果。

```
def cube(number):
    print(f"number: {number} ; process id: {os.getpid()}")
    total = 0
    for i in range(number):
        total += i * i * i
    return total

if __name__ == '__main__':
    p = Pool() 
    result = p.apply(cube, args=(50,))
    print("result: ",result)
    result = p.apply(cube, args=(10,))
    print("result: ",result)
    p.close()
    print("end")

"""
number: 50 ; process id: 19202
1500625
number: 10 ; process id: 19202
2025
end
"""
```

*   **apply _ async**():apply 方法*的异步变体。与*应用*不同，它不会阻塞。它返回一个*异步结果*对象。它可以在任务完成后执行回调函数。*

```
def cube(number):
    print(f"number: {number} ; process id: {os.getpid()}")
    total = 0
    for i in range(number):
        total += i * i * i
    return total

def custom_callback(result):
 print(f'Got result: {result}')

if __name__ == '__main__':
    p = Pool() 
    result = p.apply_async(cube, callback=custom_callback,args=(50,))
    print("result: ",result)
    result = p.apply_async(cube, callback=custom_callback,args=(10,))
    print("result: ",result)
    p.close()
    p.join()
    print("end")

"""
result:  <multiprocessing.pool.ApplyResult object at 0x7fb510360b80>
result:  <multiprocessing.pool.ApplyResult object at 0x7fb510360bb0>
number: 50 ; process id: 19304
number: 10 ; process id: 19304
Got result: 1500625
Got result: 2025
end
"""
```

我们可以用 *apply_async* 调用同一个函数 *n 次*。

```
#let's generate a dummy dataset using multiprocessing and apply_async

import os
import pickle
import random
import pandas as pd
from faker import Faker
from multiprocessing import Pool

N= 1_000_000
fake = Faker()

def get_a_dummy_record():
    name = fake.name()
    city = fake.city()
    plate = fake.license_plate()
    job = fake.job()
    return [name, city, plate, job]

if __name__ == '__main__':
    cpus = os.cpu_count()
    pool = Pool(cpus-1)
    results = []
    for _ in range(N):
        results(pool.apply_async(get_a_dummy_record))
    pool.close()
    pool.join()
    data = []
    for i, results in enumerate(results):
        data.append(results.get())
    df = pd.DataFrame(data=data)
```

*   **map**():*apply*方法将一次性任务分配给池。有了*映射*，我们可以让同一个函数可迭代，同一个函数有多个参数。有了*映射*，我们只能向任务传递一个参数。

```
result = p.map(cube, numbers)
#we have sent the same function for many iterable. cube will be rerun 
#for each number in numbers
```

*   **map_async** ():是 *map* 方法的变种。它返回一个 *AysncResult* 对象。可以指定回调函数(它只能接受一个参数)。

```
def custom_callback(result):
 print(f'Got result: {result}')

def cube(number):
    total = 0
    for i in range(number):
        total += i * i * i
    return total

if __name__ == '__main__':
    numbers = range(5)
    p = Pool()
    result = p.map_async(cube, numbers,callback=custom_callback) 
    print(result)
    for value in result.get():
        print(value)
    p.close()
    p.join()

"""
<multiprocessing.pool.MapResult object at 0x7faef0278a30>
Got result: [0, 0, 1, 9, 36]
0
0
1
9
36
"""
```

*   *imap* ():懒人*地图*。返回迭代器。

```
if __name__ == '__main__':
    numbers = range(5)
    p = Pool()
    r = p.imap(cube, numbers)
    print(r)
    print(next(r))
    print(next(r))
    print(next(r))
    print("___")
    for result in p.imap(cube, numbers):
        print(result)
    p.close()
    p.join()

"""
<multiprocessing.pool.IMapIterator object at 0x7feea8238b20>
0
0
1
___
0
0
1
9
36
"""
```

*   **imap_unordered** ():返回结果的排序是任意的。除此之外，它与 *imap* 相同。
*   **星图**():和*星图*一样，但是允许多个参数。

```
def multiply(a, b):
    return a * b

if __name__ == '__main__':
    numbers = range(50)
    p = Pool()
    items = [(i, random()) for i in range(10)]
    result = p.starmap(multiply, items)
    print(result)
    p.close()
    p.join()
"""
[0.0, 0.5344124538146042, 0.32835463076056914, 2.4787556253795424, 3.591264897479123, 0.18079992820166868, 5.686081327214689, 0.936017685360898, 6.241723690027337, 0.47898336456594226]
"""

#if we would use the map instead of starmap, we would get
#TypeError: multiply() missing 1 required positional argument: 'b'
```

*   **star map _ async**():star map*方法的异步变体。*

```
if __name__ == '__main__':
    numbers = range(50)
    p = Pool()
    items = [(i, random()) for i in range(10)]
    result = p.starmap_async(multiply, items)
    print(result)
    for value in result.get():
        print(value)
    p.close()
    p.join()

"""
<multiprocessing.pool.MapResult object at 0x7fc370196760>
0.0
0.15467946959662104
1.848298292394195
0.8187329778385587
0.35192885312433964
1.5011637932143362
0.46087324835219023
6.432101499360136
2.970120169297701
1.8696971399038436
"""
```

*   **关闭**():一旦任务完成，就释放工人。
*   **终止**():立即停止工人，不等待他们完成任务。
*   **加入**():等待工人退出。只有在调用了*关闭*或*终止*方法后才能调用。

## 长队

我们使用队列在进程间共享数据。它是一个在不同进程之间进行通信的类。

```
from time import sleep
from random import random
from multiprocessing import Process
from multiprocessing import Queue

def producer(queue):
    print('Producer is running')
    for i in range(10):
        value = random()
        sleep(value)
        # add to the queue
        print("Producer generated: ", value)
        queue.put(value)
    # all done
    queue.put(None)
    print('Producer finished')

def consumer(queue):
    print('Consumer is running')
    while True:
        item = queue.get()
        # check for stop
        if item is None:
            break
        value = random()
        new_val = item * value
        print(f'>from producer: {item} and new value: {new_val}')
    print('Consumer finished')

# entry point
if __name__ == '__main__':
    queue = Queue()
    consumer_p = Process(target=consumer, args=(queue,))
    consumer_p.start()
    producer_p = Process(target=producer, args=(queue,))
    producer_p.start()
    # wait for all processes to finish
    producer_p.join()
    consumer_p.join()

"""
Consumer is running
Producer is running
Producer generated:  0.23572440272864092
>from producer: 0.23572440272864092 and new value: 0.048785819023252984
Producer generated:  0.7656479584438751
>from producer: 0.7656479584438751 and new value: 0.5556933624110151
Producer generated:  0.45935918843841095
>from producer: 0.45935918843841095 and new value: 0.1940662477009978
Producer generated:  0.3775766218597175
>from producer: 0.3775766218597175 and new value: 0.31882165082562186
Producer generated:  0.2210393194445769
>from producer: 0.2210393194445769 and new value: 0.010097210955178933
Producer generated:  0.3365449280075862
>from producer: 0.3365449280075862 and new value: 0.3066809374677486
Producer generated:  0.031468177390468255
>from producer: 0.031468177390468255 and new value: 0.012702824353497563
Producer generated:  0.6236820349083204
>from producer: 0.6236820349083204 and new value: 0.026716990276386285
Producer generated:  0.012352625457066502
>from producer: 0.012352625457066502 and new value: 0.006689869352644401
Producer generated:  0.6995692026265108
Producer finished
>from producer: 0.6995692026265108 and new value: 0.09095476181355872
Consumer finished
"""
```

## 管

双工(双向)连接的对象。每一方的对象都有 *send* 和 *recv* 方法并传递消息。

```
from multiprocessing import Process, Pipe

def foo(conn):
    conn.send(["Hello","World",None])
    conn.close()

if __name__ == '__main__':
    parent, child = Pipe()
    p = Process(target=foo, args=(child,))
    p.start()
    print(parent.recv())
    p.join()

"""
['Hello', 'World', None]
"""
```

## 经理

列表、字典和队列可以通过一个*管理器*对象共享。它控制保存 Python 对象的服务器进程，并允许其他进程使用代理来操作它们。

```
from multiprocessing import Process, Manager, current_process

def worker(liste, x):
    liste.append(x)
    print("list by: ",current_process().name, liste)

if __name__ == '__main__':
    m = Manager()
    liste = m.list()

    p1 = Process(name='worker1', target=worker, args=(liste, 30))
    p2 = Process(name='worker2', target=worker, args=(liste, 70))

    p1.start()
    p2.start()

    p1.join()
    p2.join()

    print("final list: ", liste)

"""
list by:  worker1 [30]
list by:  worker2 [30, 70]
final list:  [30, 70]
"""
```

## 结论

多处理提供了同时使用多个处理器的机会，并有机会避开 Python 限制性的 GIL。这样，我们可以最大限度地发挥我们的计算能力。Python 中内置的多处理模块允许我们编写与它的各种特性并发运行的程序。

感谢您的阅读。

## 阅读更多内容…

[](https://faun.pub/concurrency-parallelism-in-python-59ea61e34ae0) [## Python 中的并发和并行

### 多线程、多处理、异步和等待

faun.pub](https://faun.pub/concurrency-parallelism-in-python-59ea61e34ae0) [](https://python.plainenglish.io/how-does-python-work-7dc53da52065) [## Python 是如何工作的？

### Python 的内部工作原理

python .平原英语. io](https://python.plainenglish.io/how-does-python-work-7dc53da52065) [](https://blog.devgenius.io/design-patterns-in-python-memento-pattern-dfbb8a6f36cb) [## Python 中的设计模式:纪念模式

### Memento 设计模式在 Python 中的实现

blog.devgenius.io](https://blog.devgenius.io/design-patterns-in-python-memento-pattern-dfbb8a6f36cb) [](https://blog.devgenius.io/cache-your-functions-in-python-95f8591caa07) [## 在 Python 中缓存函数

### Python 中缓存的简单演示

blog.devgenius.io](https://blog.devgenius.io/cache-your-functions-in-python-95f8591caa07) 

## 来源

[https://docs.python.org/3/library/multiprocessing.html](https://docs.python.org/3/library/multiprocessing.html)

[https://superfastpython.com/](https://superfastpython.com/)

[https://www.youtube.com/watch?v=TQx3IfCVvQ0](https://www.youtube.com/watch?v=TQx3IfCVvQ0)

[https://www.youtube.com/watch?v=GT10PnUFLlE&t = 485s](https://www.youtube.com/watch?v=GT10PnUFLlE&t=485s)

[https://www.youtube.com/watch?v=tKdolYuydVE](https://www.youtube.com/watch?v=tKdolYuydVE)
---
title: Java 线程同步及通信
date: 2019-11-22 20:56:21
tags: Java
---

## 同步
### 进程
- 进程与线程基本区别: 进程内的数据不同线程可共享，不同进程数据除非通信不可共享。
 - 共享： 共享非实时的，创建线程时会把所需数据重新拷贝一份使用，计算完成后的数据写回具有随机性，以减少数据不断跨线程读取耗费的时间。 所以当不同线程同时操作同一个数据时应考虑线程同步。

- 进程与CPU
单核CPU：操作系统划分时间片进行多进程操作
多核CPU：每个核可独立运行一个进程

### Callable -> 有返回值的Runnable

```
Future = ExecutorService.submit(Callable)
Future.get() // 阻塞式
```

### 线程池

线程池优点：
 - 方便统一管理，如对线程命名
 - 线程的创建及销毁需耗费资源，使用线程池可方便重用

### 线程同步：
 - volatile: 强制打开同步，线程读取时总从原数据中读取，写入时即刻更新。
 - synchronized: 将代码块封装成原子操作，提供互斥访问，其中的数据无法被其他线程修改
 	- monitor: 使用同一个 monitor 的不同 synchronized 代码块共享同样的互斥条件。 如 synchronized A() 和 synchronized B() 使用同一个 monitor 时，当 A() 被调用时，B() 也不可访问。
 - lock: 将锁分为读锁/写锁，可更精确的对数据读写进行操作，比如可开放多个线程同时读数据的权限。
 - Atomic： Java中对部分数据类型封装的类，如 `AtomicInt` ，将非原子操作的 `int++` 封装成原子操作的 `AtomicInt.getAndIncrement()`


### 一个较完备的单例：
```
class SingleMan {
  private static volatile SingleMan sInstance; // 此处的 volatile 确保对象完全初始化成功后才可用

  private SingleMan() {
  }

  static SingleMan newInstance() {
    if (sInstance == null) { 
      synchronized (SingleMan.class) { // 此处的 synchronized 确保只有一个线程进行初始化操作
        if (sInstance == null) {
          sInstance = new SingleMan();
        }
      }
    }
    return sInstance;
  }
}

```

## 通信

- thread.stop() 
  `deprecated` 立即停止线程, 停止位置不可控，可能造成线程任务处于中间状态导致程序错误
- thread.interrupt()
  通知线程中止，非强制性，线程通过 `isInterrupted()` 获得通知以通过可控的方式中止操作。
  如果此时线程处于`sleep`状态，会抛出 `InterruptedException` 。
- monitor.wait()/monitor.notify()/monitor.notifyAll()
  * 等待区/排队区的对象指同一个monitor进行监听的线程
  --> monitor.wait() // 线程进入等待区，不与其他线程争夺锁
  --> monitor.notify() // 在等待区中随机抽取一个线程进入排队区
    --> monitor.notifyAll() // 将等待区中所有线程都放入排队区
  --> 进入排队区的原等待线程执行优先级与其他排队区线程一致 
  ```
  ┌─────────────────────┐
  │   waiting           │
  │                     ├────────┐
  │  ┌───────────────┐  │        │
  │  │thread.notify()│  │        │
  │  ├───────────────┼  │        │
  └──────────┼──────────┘        │
             │                   │
  ┌──────────▼──────────┐        │
  │   queuing           │        │
  │                     │        │
  │          │          │        │
  └──────────┼──────────┘        │
             │                   │
  ┌──────────▼──────────┐        │
  │   running           │        │
  │   ┌─────────────┐   │        │
  │   │thread.wait()├───┼────────┘
  │   └─────────────┘   │
  └─────────────────────┘
  ```
- thread.join()
  执行该thread完毕后才执行后续代码，如：
  ```
  thread1 = { 
    sleep(3000)
    print("thread1 ended")
  }
  thread1.start()
  thread2 = {
    thread1.join() 
    print("thread2 ended") // 会在thread1执行完后才调用
  }
  thread1.start()
  ==========================
  输出结果：
  thread1 ended
  thread2 ended
  ```
- thread.yield()
  让出时间片给其他相同优先级的线程，但在当前线程执行完后立即获取时间片
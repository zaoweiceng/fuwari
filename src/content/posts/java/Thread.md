---
title: Java多线程
published: 2025-03-14
description: 'Java的多线程的基本用法'
image: './img/20250123203944_1.jpg'
tags: [java, 学习, 线程]
category: 'java'
draft: false 
---


## 线程

### 实现多线程方式一：继承Thread类

- 方法介绍

  | 方法名       | 说明                                        |
  | ------------ | ------------------------------------------- |
  | void run()   | 在线程开启后，此方法将被调用执行            |
  | void start() | 使此线程开始执行，Java虚拟机会调用run方法() |

- 实现步骤
  - 定义一个类MyThread继承Thread类
  - 在MyThread类中重写run()方法
  - 创建MyThread类的对象
  - 启动线程

- 代码演示

  ```java
  public class MyThread extends Thread {
      @Override
      public void run() {
          for(int i=0; i<100; i++) {
              System.out.println(i);
          }
      }
  }
  public class MyThreadDemo {
      public static void main(String[] args) {
          MyThread my1 = new MyThread();
          MyThread my2 = new MyThread();
        //start() 导致此线程开始执行; Java虚拟机调用此线程的run方法
          my1.start();
          my2.start();
    }
  }
  ```
  
- 两个小问题

  - 为什么要重写run()方法？

    因为run()是用来封装被线程执行的代码

  - run()方法和start()方法的区别？

    run()：封装线程执行的代码，直接调用，相当于普通方法的调用

    start()：启动线程；然后由JVM调用此线程的run()方法
  
- 缺点：单继承，不能再继承其它类

### 实现多线程方式二：实现Runnable接口

- Thread构造方法

  | 方法名                               | 说明                   |
  | ------------------------------------ | ---------------------- |
  | Thread(Runnable target)              | 分配一个新的Thread对象 |
  | Thread(Runnable target, String name) | 分配一个新的Thread对象 |

- 实现步骤

  - 定义一个类MyRunnable实现Runnable接口
  - 在MyRunnable类中重写run()方法
  - 创建MyRunnable类的对象
  - 创建Thread类的对象，把MyRunnable对象作为构造方法的参数
  - 启动线程

- 代码演示

  ```java
  public class MyRunnable implements Runnable {
      @Override
      public void run() {
          for(int i=0; i<100; i++) {
              System.out.println(Thread.currentThread().getName()+":"+i);
          }
      }
  }
  public class MyRunnableDemo {
      public static void main(String[] args) {
          //创建MyRunnable类的对象
          MyRunnable my = new MyRunnable();

          //创建Thread类的对象，把MyRunnable对象作为构造方法的参数
          //Thread(Runnable target)
  //        Thread t1 = new Thread(my);
  //        Thread t2 = new Thread(my);
          //Thread(Runnable target, String name)
          Thread t1 = new Thread(my,"坦克");
          Thread t2 = new Thread(my,"飞机");

          //启动线程
          t1.start();
          t2.start();
      }
  }
  ```

在Java中，`Thread.yield()` 是一个静态方法，用于使当前正在执行的线程让出CPU时间片，允许其他线程（如果有）在当前线程之前被调度执行。调用`yield()`方法的线程会从运行状态（Running）转到可运行状态（Runnable），即就绪状态，并有可能在下一个线程调度周期中被再次选中执行。
`yield()`方法的主要目的是提高线程之间的执行效率，它是一种非阻塞的线程调度方式。当当前线程认为它需要让出CPU以允许其他线程运行时，可以调用此方法。这通常用于实现某种程度的线程间协作，尤其是在多处理器系统中。
然而，需要注意的是，`yield()`方法的行为依赖于具体的线程调度器实现，不同的操作系统和JVM实现可能会有不同的行为。因此，`yield()`方法的效果可能并不总是如预期的那样明显或一致。
在实际应用中，`yield()`方法的使用并不常见，因为它的行为可能难以预测，并且通常有更好的方法来实现线程间的协作和同步，例如使用`wait()`/`notify()`机制或`java.util.concurrent`包中的高级并发工具。

### 实现多线程方式三: 实现Callable接口

第一、第二的方式没办法获取到多线程运行的结果，本方法可以。

+ 方法介绍

  | 方法名                           | 说明                                               |
  | -------------------------------- | -------------------------------------------------- |
  | V call()                         | 计算结果，如果无法计算结果，则抛出一个异常         |
  | FutureTask(Callable<V> callable) | 创建一个 FutureTask，一旦运行就执行给定的 Callable |
  | V get()                          | 如有必要，等待计算完成，然后获取其结果             |

+ 实现步骤

  + 定义一个类MyCallable实现Callable接口
  + 在MyCallable类中重写call()方法
  + 创建MyCallable类的对象
  + 创建Future的实现类FutureTask对象，把MyCallable对象作为构造方法的参数
  + 创建Thread类的对象，把FutureTask对象作为构造方法的参数
  + 启动线程
  + 再调用get方法，就可以获取线程结束之后的结果。

+ 代码演示

  ```java
  public class MyCallable implements Callable<Integer> {
      @Override
      public String call() throws Exception {
          int sum = 0;
          for (int i = 0; i < 100; i++) {
              sum += i;
          }
          //返回值就表示线程运行完毕之后的结果
          return sum;
      }
  }
  public class demoCallable {
      public static void main(String[] args) throws ExecutionException, InterruptedException {
          //线程开启之后需要执行里面的call方法
        myCallable mc = new myCallable();
          //可以获取线程执行完毕之后的结果.也可以作为参数传递给Thread对象
        FutureTask<Integer> ft = new FutureTask<>(mc);
          //创建线程对象
          Thread t1 = new Thread(ft);
        //开启线程
          t1.start();
          int s = ft.get();
        System.out.println(s);
      }
  }
  ```

+ 三种实现方式的对比
  + 实现Runnable、Callable接口
    + 好处: 扩展性强，实现该接口的同时还可以继承其他的类
    + 缺点: 编程相对复杂，不能直接使用Thread类中的方法，如果需要使用，需要现获取到Thread的对象(通过静态方法)
  + 继承Thread类
    + 好处: 编程比较简单，可以直接使用Thread类中的方法
    + 缺点: 可以扩展性较差，不能再继承其他的类

### 设置和获取线程名称

- 方法介绍

  | 方法名                     | 说明                               |
  | -------------------------- | ---------------------------------- |
  | void  setName(String name) | 将此线程的名称更改为等于参数name   |
  | String  getName()          | 返回此线程的名称                   |
  | Thread  currentThread()    | 返回对当前正在执行的线程对象的引用 |

- 代码演示

  ```java
  public class MyThread extends Thread {
      public MyThread() {}
      public MyThread(String name) {
          super(name);
      }

      @Override
      public void run() {
          for (int i = 0; i < 100; i++) {
              System.out.println(getName()+":"+i);
          }
      }
  }
  public class MyThreadDemo {
      public static void main(String[] args) {
          MyThread my1 = new MyThread();
          MyThread my2 = new MyThread();

          //void setName(String name)：将此线程的名称更改为等于参数 name
          my1.setName("高铁");
          my2.setName("飞机");

          //Thread(String name)
          MyThread my1 = new MyThread("高铁");
          MyThread my2 = new MyThread("飞机");

          my1.start();
          my2.start();

          //static Thread currentThread() 返回对当前正在执行的线程对象的引用
          System.out.println(Thread.currentThread().getName());
      }
  }
  ```

### Thread类中的常用方法

下面是一个表格，列出了Java中`Thread`类的一些常用方法，包括方法的名称、描述和简单的用法示例：
| 方法名称                         | 描述                                                         | 用法示例                                         |
| -------------------------------- | ------------------------------------------------------------ | ------------------------------------------------ |
| `start()`                        | 启动线程，调用线程的`run()`方法。                            | `thread.start();`                                |
| `run()`                          | 线程的执行体，包含线程要执行的代码。                         | `public void run() { // code to execute }`       |
| `join()`                         | 等待调用该方法的线程结束（进程插队）。                       | `thread.join();`                                 |
| `static void sleep(long millis)` | 使当前线程暂停执行指定的时间（毫秒）。                       | `Thread.sleep(1000);` // 暂停1秒                 |
| `interrupt()`                    | 中断线程，如果线程处于阻塞状态，会抛出`InterruptedException`。 | `thread.interrupt();`                            |
| `isAlive()`                      | 检查线程是否处于活动状态。                                   | `if (thread.isAlive()) { // code }`              |
| `getName()`                      | 获取线程的名称。                                             | `String name = thread.getName();`                |
| `setName(String name)`           | 设置线程的名称。                                             | `thread.setName("MyThread");`                    |
| `getPriority()`                  | 获取线程的优先级。                                           | `int priority = thread.getPriority();`           |
| `setPriority(int newPriority)`   | 设置线程的优先级。                                           | `thread.setPriority(Thread.MAX_PRIORITY);`       |
| `currentThread()`                | 获取当前正在执行的线程对象。                                 | `Thread currentThread = Thread.currentThread();` |
| `isDaemon()`                     | 检查线程是否为守护线程。                                     | `if (thread.isDaemon()) { // code }`             |
| `setDaemon(boolean on)`          | 将线程设置为守护线程或用户线程。                             | `thread.setDaemon(true);` // 设置为守护线程      |
| `yield()`                        | 让出线程                                                     | 可以让线程的调度更加均衡                         |
| `static Thread currentThread()`  | 获取到当前线程的对象                                         |                                                  |
请注意，这个表格并不包含`Thread`类中的所有方法，只是列出了一些常用的方法。此外，使用这些方法时需要考虑到线程安全、同步等问题，以避免潜在的并发问题。

### 线程优先级

- 线程调度

  - 两种调度方式
    - 分时调度模型：所有线程轮流使用 CPU 的使用权，平均分配每个线程占用 CPU 的时间片
    - 抢占式调度模型：优先让优先级高的线程使用 CPU，如果线程的优先级相同，那么会随机选择一个，优先级高的线程获取的 CPU 时间片相对多一些

  - Java使用的是抢占式调度模型

  - 随机性

    假如计算机只有一个 CPU，那么 CPU 在某一个时刻只能执行一条指令，线程只有得到CPU时间片，也就是使用权，才可以执行指令。所以说多线程程序的执行是有随机性，因为谁抢到CPU的使用权是不一定的

- 优先级相关方法

  | 方法名                                  | 说明                                                         |
  | --------------------------------------- | ------------------------------------------------------------ |
  | final int getPriority()                 | 返回此线程的优先级                                           |
  | final void setPriority(int newPriority) | 更改此线程的优先级线程默认优先级是5；线程优先级的范围是：1-10 |

- 代码演示

  ```java
  public class MyCallable implements Callable<String> {
      @Override
      public String call() throws Exception {
          for (int i = 0; i < 100; i++) {
              System.out.println(Thread.currentThread().getName() + "---" + i);
          }
          return "线程执行完毕了";
      }
  }
  public class Demo {
      public static void main(String[] args) {
          //优先级: 1 - 10 默认值:5
          MyCallable mc = new MyCallable();

          FutureTask<String> ft = new FutureTask<>(mc);

          Thread t1 = new Thread(ft);
          t1.setName("飞机");
          t1.setPriority(10);
          //System.out.println(t1.getPriority());//5
          t1.start();

          MyCallable mc2 = new MyCallable();

          FutureTask<String> ft2 = new FutureTask<>(mc2);

          Thread t2 = new Thread(ft2);
          t2.setName("坦克");
          t2.setPriority(1);
          //System.out.println(t2.getPriority());//5
          t2.start();
      }
  }
  ```

### 守护线程

- 相关方法

  | 方法名                     | 说明                                                         |
  | -------------------------- | ------------------------------------------------------------ |
  | void setDaemon(boolean on) | 将此线程标记为守护线程，当运行的线程都是守护线程时，Java虚拟机将退出 |

- 代码演示

  ```java
  public class MyThread1 extends Thread {
      @Override
      public void run() {
          for (int i = 0; i < 10; i++) {
              System.out.println(getName() + "---" + i);
          }
      }
  }
  public class MyThread2 extends Thread {
      @Override
      public void run() {
          for (int i = 0; i < 100; i++) {
              System.out.println(getName() + "---" + i);
          }
      }
  }
  public class Demo {
      public static void main(String[] args) {
          MyThread1 t1 = new MyThread1();
          MyThread2 t2 = new MyThread2();

          t1.setName("A");
          t2.setName("B");

          //把第二个线程设置为守护线程
          //当普通线程执行完之后,那么守护线程也没有继续运行下去的必要了.
          t2.setDaemon(true);

          t1.start();
          t2.start();
      }
  }
  ```

**守护线程（Daemon Thread）**是一种特殊的线程，它的主要作用是服务于其他线程，并且在程序中不会阻止JVM的退出。当所有的非守护线程（也称为用户线程）都执行完毕后，无论守护线程是否还在运行，JVM都会退出。
**守护线程的用途：**

1. **后台任务**：守护线程通常用于执行一些后台任务，如垃圾回收、内存管理等，这些任务不需要用户显式地干预。
2. **辅助功能**：它们可以用于实现一些辅助功能，比如在服务器应用程序中，守护线程可以用来监控内存使用情况或清理临时文件。
3. **非关键任务**：任何不需要在程序结束后继续执行的任务都可以作为守护线程运行。
4. **定时任务**：守护线程可以用于执行定时任务，比如定时备份、日志记录等。
**守护线程的特点：**
- **生命周期**：守护线程的生命周期依赖于用户线程，当所有的用户线程结束时，JVM会退出，无论守护线程是否完成。
- **中断**：守护线程在JVM退出时会被中断，因此不应该在守护线程中执行重要的、不能中断的任务。
- **资源释放**：由于守护线程可能在任何时候被中断，因此它们不应该持有任何需要显式释放的资源，如打开的文件句柄或数据库连接。
**如何创建守护线程：**
在Java中，可以通过调用`Thread`对象的`setDaemon(true)`方法将线程设置为守护线程。这个方法必须在线程启动之前调用。
```java
Thread daemonThread = new Thread(new Runnable() {
    public void run() {
        // 守护线程的任务
    }
});
daemonThread.setDaemon(true);
daemonThread.start();
```
**注意事项：**
- 不要在守护线程中执行重要的业务逻辑，因为它们可能会在没有完成任务的情况下被终止。
- 守护线程不应该访问共享资源，除非采取了适当的同步措施，以避免数据不一致或资源泄露。
- 守护线程通常用于执行那些即使不完成也不会对程序造成重大影响的任务。

## 线程同步

在Java中，多线程同步可以通过多种方式实现，主要包括使用synchronized关键字、Lock接口及其实现类（如ReentrantLock）、以及使用原子类等。

### 使用synchronized实现线程同步

假设有一个共享的资源，例如一个计数器，多个线程需要对其进行增加操作。为了保证线程安全，可以使用synchronized关键字来同步访问这个共享资源。

```java
public class syncDemo1 {
    public class Counter {
        private int count = 0;
        // 同步方法
        public synchronized void increment() {
            System.out.println(Thread.currentThread().getName() + ": " + ++count);
        }
        public int getCount() {
            return count;
        }
    }

    public static void main(String[] args) {
        syncDemo1 demo = new syncDemo1();
        Counter counter = demo.new Counter();
        // 创建多个线程
        for (int i = 0; i < 5; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    for (int j = 0; j < 100; j++) {
                        counter.increment();
                    }
                }
            }).start();
        }
        // 等待所有线程执行完毕
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        // 输出最终的计数结果
        System.out.println("Final count is " + counter.getCount());
    }
}

```

### 使用synchronized代码块实现线程同步

```java
public class Counter {
    static int count = 0;
    // 同步方法
    public void increment() {
        synchronized (this) {
            // 非静态使用this，静态使用ClassName.class
            System.out.println(Thread.currentThread().getName() + ": " + ++count);
        }
    }
    public int getCount() {
        return count;
    }
}

public class syncDemo2 {

    public static void main(String[] args) {
        syncDemo2 demo = new syncDemo2();
        // 创建多个线程
        for (int i = 0; i < 5; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    Counter counter = new Counter();
                    for (int j = 0; j < 100; j++) {
                        counter.increment();
                    }
                }
            }).start();
        }
        // 等待所有线程执行完毕
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

`ReadWriteLock` 是 Java 中的一种高级线程同步机制，它允许多个读线程同时访问资源，但在写线程访问时，所有读线程和其他写线程都会被阻塞。这种锁适用于读多写少的场景，可以提高并发性能。
`ReadWriteLock` 接口的主要实现是 `ReentrantReadWriteLock`。下面通过一个例子来展示如何使用 `ReadWriteLock`。

### `ReentrantLock`

`ReentrantLock` 是 Java 中 `java.util.concurrent.locks` 包下的一种可重入锁，它提供了与 `synchronized` 关键字类似的同步功能，但功能更强大、更灵活。以下是 `ReentrantLock` 的一些基本用法：

#### 创建 `ReentrantLock` 实例
```java
ReentrantLock lock = new ReentrantLock();
```
> 尽量使用static，确保锁唯一

#### 加锁

使用 `lock()` 方法获取锁：
```java
lock.lock();
try {
    // 执行需要同步的代码
} finally {
    // 释放锁
    lock.unlock();
}
```
#### 解锁
使用 `unlock()` 方法释放锁，通常放在 `finally` 块中以确保锁被释放：
```java
finally {
    lock.unlock();
}
```
#### 可中断的锁获取
使用 `lockInterruptibly()` 方法可以在获取锁的过程中响应中断：
```java
try {
    lock.lockInterruptibly();
    try {
        // 执行需要同步的代码
    } finally {
        lock.unlock();
    }
} catch (InterruptedException e) {
    // 当前线程被中断
}
```
#### 尝试非阻塞获取锁
使用 `tryLock()` 方法尝试获取锁，无论成功与否立即返回：
```java
if (lock.tryLock()) {
    try {
        // 执行需要同步的代码
    } finally {
        lock.unlock();
    }
} else {
    // 获取锁失败
}
```
##### 带有超时时间的锁获取
使用 `tryLock(long timeout, TimeUnit unit)` 方法在指定时间内尝试获取锁：
```java
try {
    if (lock.tryLock(1, TimeUnit.SECONDS)) {
        try {
            // 执行需要同步的代码
        } finally {
            lock.unlock();
        }
    } else {
        // 获取锁失败
    }
} catch (InterruptedException e) {
    // 当前线程被中断
}
```
#### 公平锁
创建 `ReentrantLock` 时可以指定为公平锁，公平锁倾向于将锁赋予等待时间最长的线程：
```java
ReentrantLock fairLock = new ReentrantLock(true);
```
#### 条件变量
`ReentrantLock` 可以与 `Condition` 对象一起使用，以实现线程之间的等待/通知机制：
```java
Condition condition = lock.newCondition();
// 等待
lock.lock();
try {
    condition.await();
    // 执行后续操作
} finally {
    lock.unlock();
}



// 通知
lock.lock();
try {
    condition.signal();
    // 或者通知所有等待的线程：condition.signalAll();
} finally {
    lock.unlock();
}
```
#### 注意事项
- **务必在 `finally` 块中释放锁**，以避免死锁。
- **不要在持有锁的情况下调用 `await()`**，否则可能导致死锁。
- **`ReentrantLock` 是可重入的**，即同一个线程可以多次获取同一把锁，而不会造成死锁。
- **性能考虑**：`ReentrantLock` 在某些情况下可能比 `synchronized` 更高效，尤其是在高竞争环境下。
通过以上基本用法，可以灵活地控制多线程之间的同步和协作。`ReentrantLock` 提供的强大功能使其在多线程编程中非常有用。

###  `ReadWriteLock`
假设我们有一个共享的资源，例如一个地图（`Map`），多个线程可能同时读取这个地图，但写入操作需要独占访问。
```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;
public class SharedMap {
    private final Map<String, String> map = new HashMap<>();
    private final ReadWriteLock lock = new ReentrantReadWriteLock();
    // 读操作
    public String get(String key) {
        lock.readLock().lock();
        try {
            return map.get(key);
        } finally {
            lock.readLock().unlock();
        }
    }
    // 写操作
    public void put(String key, String value) {
        lock.writeLock().lock();
        try {
            map.put(key, value);
        } finally {
            lock.writeLock().unlock();
        }
    }
    // 其他操作...
}
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        SharedMap sharedMap = new SharedMap();
        // 创建多个读线程
        for (int i = 0; i < 5; i++) {
            new Thread(() -> {
                System.out.println("Reading: " + sharedMap.get("key"));
            }).start();
        }
        // 创建一个写线程
        new Thread(() -> {
            sharedMap.put("key", "value");
            System.out.println("Written: value");
        }).start();
    }
}
```
#### 解释
- `ReadWriteLock` 接口：定义了获取读锁和写锁的方法。
- `ReentrantReadWriteLock`：`ReadWriteLock` 接口的一个实现，提供了可重入的读写锁。
- `readLock()` 和 `writeLock()`：分别用于获取读锁和写锁。
- `lock()` 和 `unlock()`：用于锁定和解锁。
在 `SharedMap` 类中，我们定义了 `get` 和 `put` 方法，分别用于读取和写入操作。读操作使用读锁，允许多个线程同时进行；写操作使用写锁，确保在写入时没有其他读或写操作。
在 `ReadWriteLockDemo` 类的 `main` 方法中，我们创建了多个读线程和一个写线程来演示 `ReadWriteLock` 的使用。
#### 注意事项
- 在使用 `ReadWriteLock` 时，要确保锁的获取和释放总是在 `try-finally` 块中进行，以防止在发生异常时锁未被释放。
- 读锁不支持条件变量，如果需要在读锁下使用条件变量，可以考虑其他同步机制。
- 写锁是独占的，即使没有读线程，其他写线程也会被阻塞。
通过使用 `ReadWriteLock`，可以有效地提高读多写少场景下的并发性能。

### 死锁

+ 概述

  线程死锁是指由于两个或者多个线程互相持有对方所需要的资源，导致这些线程处于等待状态，无法前往执行

+ 什么情况下会产生死锁

  1. 资源有限
  2. 同步嵌套

+ 代码演示

  ```java
  public class Demo {
      public static void main(String[] args) {
          Object objA = new Object();
          Object objB = new Object();

          new Thread(()->{
              while(true){
                  synchronized (objA){
                      //线程一
                      synchronized (objB){
                          System.out.println("A");
                      }
                  }
              }
          }).start();

          new Thread(()->{
              while(true){
                  synchronized (objB){
                      //线程二
                      synchronized (objA){
                          System.out.println("B");
                      }
                  }
              }
          }).start();
      }
  }
  ```

## 等待唤醒机制

Java多线程中的等待唤醒机制是一种用于线程间通信的重要机制，它允许线程在某些条件下挂起执行，并在条件满足时被唤醒继续执行。这种机制主要基于`Object`类中的`wait()`, `notify()`, 和`notifyAll()`方法实现。
### `wait()`, `notify()`, `notifyAll()`方法
- **`wait()`方法**：
  - 调用`wait()`方法的线程会释放它持有的对象锁，并进入等待状态，直到其他线程调用同一个对象的`notify()`或`notifyAll()`方法。
  - `wait()`方法可以带有一个超时参数，指定线程等待的最长时间。
  - `wait()`方法必须在同步代码块或同步方法中调用，否则会抛出`IllegalMonitorStateException`异常。
- **`notify()`方法**：
  - 调用`notify()`方法的线程会随机唤醒一个正在等待该对象锁的线程。
  - 如果没有线程在等待，则`notify()`方法不执行任何操作。
  - `notify()`方法也必须在同步代码块或同步方法中调用。
- **`notifyAll()`方法**：
  - 调用`notifyAll()`方法的线程会唤醒所有正在等待该对象锁的线程。
  - 这些线程将竞争对象锁，获得锁的线程将继续执行。
### 等待唤醒机制的工作原理
1. **线程进入等待状态**：
   - 线程在执行过程中遇到`wait()`方法调用时，会释放它持有的对象锁，并进入等待状态。
   - 其他线程可以获取该对象锁并执行同步代码块或同步方法。
2. **线程被唤醒**：
   - 当其他线程调用同一个对象的`notify()`或`notifyAll()`方法时，等待的线程有机会被唤醒。
   - 被唤醒的线程将重新竞争对象锁，获得锁的线程将继续执行。
3. **线程继续执行**：
   - 获得对象锁的线程从`wait()`方法返回，继续执行后续代码。
### 示例代码
以下是一个简单的示例，展示了等待唤醒机制的使用：
```java
public class WaitNotifyExample {
    private static final Object lock = new Object();
    private static boolean condition = false;
    public static void main(String[] args) {
        Thread waiter = new Thread(new Waiter());
        Thread notifier = new Thread(new Notifier());
        waiter.start();
        notifier.start();
    }
    static class Waiter implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                while (!condition) {
                    try {
                        System.out.println("Waiter is waiting...");
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                System.out.println("Waiter is awake!");
            }
        }
    }
    static class Notifier implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                System.out.println("Notifier is notifying...");
                condition = true;
                lock.notifyAll();
            }
        }
    }
}
```
在这个示例中，`Waiter`线程在条件不满足时进入等待状态，`Notifier`线程在改变条件后唤醒等待的线程。
### 注意事项
- **正确使用条件变量**：在`wait()`方法的使用中，通常需要配合一个条件变量来判断是否满足继续执行的条件。在上面的示例中，`condition`就是这样一个变量。
- **避免虚假唤醒**：`wait()`方法可能会因为意外的原因被唤醒（虚假唤醒），因此推荐使用`while`循环来检查条件，而不是`if`语句。
- **同步代码块**：`wait()`, `notify()`, `notifyAll()`方法必须在同步代码块或同步方法中调用，以确保线程安全。
### 与其他并发机制的对比
- **与`sleep()`方法的区别**：`sleep()`方法不会释放对象锁，只是让当前线程暂停执行一段时间。
- **与`Lock`和`Condition`的区别**：`Lock`和`Condition`提供了更灵活的锁定和等待唤醒机制，但使用起来相对复杂。
理解并正确使用Java多线程中的等待唤醒机制是开发高效、线程安全的应用程序的关键。

### 阻塞队列实现

阻塞队列（BlockingQueue）是Java并发包（java.util.concurrent）中提供的一种线程安全的队列，它支持多线程间的阻塞式数据交换。使用阻塞队列可以简化多线程编程中的等待唤醒机制，因为它内部已经实现了这些机制。下面是如何使用阻塞队列来实现等待唤醒机制的示例：
#### 阻塞队列的基本操作
- `put(E e)`：将元素插入队列，如果队列已满，则阻塞直到有空间可用。
- `take()`：从队列中移除并返回头部元素，如果队列为空，则阻塞直到有元素可用。
- `offer(E e)`：将元素插入队列，如果队列已满，则返回false，不会阻塞。
- `poll()`：从队列中移除并返回头部元素，如果队列为空，则返回null，不会阻塞。
- `peek()`：返回队列头部元素，但不移除，如果队列为空，则返回null。
#### 使用阻塞队列实现生产者-消费者模式
```java
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;
public class ProducerConsumerExample {
    public static void main(String[] args) {
        BlockingQueue<Integer> queue = new LinkedBlockingQueue<>(5); // 创建容量为5的阻塞队列
        Thread producer = new Thread(new Producer(queue));
        Thread consumer = new Thread(new Consumer(queue));
        producer.start();
        consumer.start();
    }
}
class Producer implements Runnable {
    private BlockingQueue<Integer> queue;
    public Producer(BlockingQueue<Integer> queue) {
        this.queue = queue;
    }
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            try {
                System.out.println("Produced: " + i);
                queue.put(i); // 如果队列满，会阻塞等待
                Thread.sleep(1000); // 模拟生产时间
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
class Consumer implements Runnable {
    private BlockingQueue<Integer> queue;
    public Consumer(BlockingQueue<Integer> queue) {
        this.queue = queue;
    }
    @Override
    public void run() {
        while (true) {
            try {
                Integer taken = queue.take(); // 如果队列空，会阻塞等待
                System.out.println("Consumed: " + taken);
                Thread.sleep(1000); // 模拟消费时间
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```
#### 工作原理
1. **生产者线程**：生产者尝试将产品放入阻塞队列。如果队列已满，`put()`方法会阻塞生产者线程，直到队列中有可用空间。
2. **消费者线程**：消费者尝试从阻塞队列中取出产品。如果队列为空，`take()`方法会阻塞消费者线程，直到队列中有产品可取。
通过使用阻塞队列，我们不需要显式地使用wait(), notify(), notifyAll()方法来管理线程的等待和唤醒，因为阻塞队列内部已经帮我们实现了这些复杂的同步控制。
#### 优点
- **简化代码**：使用阻塞队列可以大大简化多线程编程的复杂性，避免直接处理线程同步和唤醒的细节。
- **线程安全**：阻塞队列是线程安全的，可以在多线程环境中安全使用。
- **灵活性和可扩展性**：Java提供了多种类型的阻塞队列，如`ArrayBlockingQueue`, `LinkedBlockingQueue`, `PriorityBlockingQueue`等，可以根据具体需求选择合适的队列实现。
通过阻塞队列实现的生产者-消费者模式是并发编程中的经典模式，它能够有效地解耦生产者和消费者，提高程序的并发性能和可维护性。

## 线程池

Java的线程池是一种用于管理和复用线程的机制，它可以有效地提高程序的性能和响应速度。线程池的核心思想是预先创建一定数量的线程，这些线程在需要执行任务时被分配任务，执行完毕后并不销毁，而是返回线程池中等待下一次的任务分配。
### 线程池的好处
1. **降低资源消耗**：通过复用已存在的线程，减少线程创建和销毁所带来的开销。
2. **提高响应速度**：任务到达时，无需等待线程创建即可立即执行。
3. **提高线程的可管理性**：线程池可以统一管理、分配、调优和监控线程。
4. **提供更多更强大的功能**：线程池支持定时、周期性执行任务等功能。
### Java中的线程池实现
在Java中，线程池主要通过`java.util.concurrent`包中的`Executor`框架实现。`Executor`是一个接口，它定义了执行任务的单一方法`execute(Runnable command)`。其子接口`ExecutorService`添加了更多管理线程池的方法，如`shutdown()`、`submit()`等。
`ThreadPoolExecutor`是`ExecutorService`的一个实现，它提供了线程池的具体实现。另外，`Executors`工厂类提供了几种常见配置的线程池的快速实现方法。
### 常见的线程池类型
1. **FixedThreadPool**：固定数量的线程池。适用于负载较重的服务器。
2. **CachedThreadPool**：一个根据需要创建新线程的线程池。适用于执行大量短生命周期的异步任务（最多到int上限的线程）。
3. **SingleThreadExecutor**：单线程的Executor。保证所有任务都在同一线程中按顺序执行。
4. **ScheduledThreadPool**：用于执行定时任务和周期性任务。
### ThreadPoolExecutor的重要参数
- **corePoolSize**：核心线程数，即线程池中始终存活的线程数。
- **maximumPoolSize**：最大线程数，线程池中允许的最大线程数。
- **keepAliveTime**：非核心线程的空闲存活时间。
- **unit**：keepAliveTime的时间单位。
- **workQueue**：工作队列，用于存放任务。
- **threadFactory**：线程工厂，用于创建线程。
- **rejectedExecutionHandler**：拒绝策略，当任务太多无法处理时，如何拒绝新任务。
### 拒绝策略

以下均为`ThreadPoolExecutor`的子类。

- **AbortPolicy**：直接抛出异常，默认策略。
- **CallerRunsPolicy**：调用线程自己执行该任务。
- **DiscardPolicy**：直接丢弃任务，不做任何处理。
- **DiscardOldestPolicy**：丢弃队列中最老的任务。
### 使用示例
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
public class ThreadPoolDemo {
    public static void main(String[] args) {
        // 创建一个固定大小的线程池
        ExecutorService fixedThreadPool = Executors.newFixedThreadPool(3);
        
        // 提交任务
        for (int i = 0; i < 10; i++) {
            int finalI = i;
            fixedThreadPool.execute(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName() + " is running-" + i);
                }
            });
        }
        
        // 关闭线程池
        fixedThreadPool.shutdown();
    }
}
```
自定义线程池：

```java
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.Executors;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

public class MyThreadPoolDemo3 {
//    参数一：核心线程数量
//    参数二：最大线程数
//    参数三：空闲线程最大存活时间
//    参数四：时间单位
//    参数五：任务队列
//    参数六：创建线程工厂
//    参数七：任务的拒绝策略
    public static void main(String[] args) {
        ThreadPoolExecutor pool = new ThreadPoolExecutor(
            2,
            5,
            2,
            TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(10), // LinkedBlockingQueue等阻塞队列
            Executors.defaultThreadFactory(),
            new ThreadPoolExecutor.AbortPolicy()
        );
        pool.submit(new MyRunnable());
        pool.submit(new MyRunnable());

        pool.shutdown();
    }
}
```

### 注意事项

- **合理配置线程池大小**：根据任务的性质和系统资源进行配置。
- **注意任务与线程的匹配**：避免线程空闲或过度竞争。
- **正确关闭线程池**：避免程序无法正常结束。

### 手动实现线程池

实现思路：

- 创建一个线程池类(ThreadPool)
- 在该类中定义两个成员变量poolSize(线程池初始化线程的个数) , BlockingQueue<Runnable>(任务容器)
- 通过构造方法来创建两个线程对象(消费者线程)，并且启动
- 使用内部类的方式去定义一个线程类(TaskThread),可以提供一个构造方法用来初始化线程名称
- 两个消费者线程需要不断的从任务容器中获取任务，如果没有任务，则线程处于阻塞状态。
- 提供一个方法(submit)向任务容器中添加任务
- 定义测试类进行测试

线程池类

```java
public class ThreadPool {

    // 初始化线程个数
    private static final int DEFAULT_POOL_SIZE = 2 ;

    // 在该类中定义两个成员变量poolSize(线程池初始化线程的个数) , BlockingQueue<Runnable>(任务容器)
    private int poolSize = DEFAULT_POOL_SIZE ;
    private BlockingQueue<Runnable> blockingQueue = new LinkedBlockingQueue<Runnable>() ;

    // 无参构造方法
    public ThreadPool(){
        this.initThread();
    }

    // 有参构造方法，通过构造方法来创建两个线程对象(消费者线程)，并且启动
    public ThreadPool(int poolSize) {
        if(poolSize > 0) {
            this.poolSize = poolSize ;
        }
        this.initThread();
    }

    // 初始化线程方法
    public void initThread(){
        for(int x = 0 ; x < poolSize ; x++) {
            new TaskThread("线程--->" + x).start();
        }
    }

    // 提供一个方法(submit)向任务容器中添加任务
    public void submit(Runnable runnable) {

        try {
            blockingQueue.put(runnable);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

    }

    // 使用内部类的方式去定义一个线程类
    public class TaskThread extends Thread {

        // 提供一个构造方法，用来初始化线程名称
        public TaskThread(String name) {
            super(name);
        }

        @Override
        public void run() {

            while(true) {

                try {

                    // 两个消费者线程需要不断的从任务容器中获取任务，如果没有任务，则线程处于阻塞状态。
                    Runnable task = blockingQueue.take();
                    task.run();

                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

            }

        }
    }

}
```

测试类

```java
public class ThreadPoolDemo01 {

    public static void main(String[] args) {

        // 创建线程池对象,无参构造方法创建
        // ThreadPool threadPool = new ThreadPool();
        ThreadPool threadPool = new ThreadPool(5);

        // 提交任务
        for(int x = 0 ; x < 10 ; x++) {
            threadPool.submit( () -> {
                System.out.println(Thread.currentThread().getName() + "---->>>处理了任务");
            });
        }

    }

}
```

### 线程数量设置

获取操作系统分配资源：最大并行数(Maximum parallel quantity, MPQ)

```java
int count = Runtime.getRuntime().availableProcessors();
System.out.println(count);
// 16
```

- CPU密集型：$$MPQ+1 $$（某个出问题，后面的可以补上，所以+1）
- IO密集型：$$ MPQ * CPU_{utilization} * \frac{Computation_{time} +Waiting_{time}}{Computation_{time}}$$

例如：4核8线程：$$8 * 100\% * \frac{4_s}{2_s} = 16 $$

（需要通过其它工具计算得到计算时间和IO的等待时间）

## 线程的状态

| 线程状态      | 具体含义                                                     |
| ------------- | ------------------------------------------------------------ |
| NEW           | 一个尚未启动的线程的状态。也称之为初始状态、开始状态。线程刚被创建，但是并未启动。还没调用start方法。MyThread t = new MyThread()只有线程象，没有线程特征。 |
| RUNNABLE      | 当我们调用线程对象的start方法，那么此时线程对象进入了RUNNABLE状态。那么此时才是真正的在JVM进程中创建了一个线程，线程一经启动并不是立即得到执行，线程的运行与否要听令与CPU的调度，那么我们把这个中间状态称之为可执行状态(RUNNABLE)也就是说它具备执行的资格，但是并没有真正的执行起来而是在等待CPU的度。 |
| BLOCKED       | 当一个线程试图获取一个对象锁，而该对象锁被其他的线程持有，则该线程进入Blocked状态；当该线程持有锁时，该线程将变成Runnable状态。 |
| WAITING       | 一个正在等待的线程的状态。也称之为等待状态。造成线程等待的原因有两种，分别是调用Object.wait()、join()方法。处于等待状态的线程，正在等待其他线程去执行一个特定的操作。例如：因为wait()而等待的线程正在等待另一个线程去调用notify()或notifyAll()；一个因为join()而等待的线程正在等待另一个线程结束。 |
| TIMED_WAITING | 一个在限定时间内等待的线程的状态。也称之为限时等待状态。造成线程限时等待状态的原因有三种，分别是：Thread.sleep(long)，Object.wait(long)、join(long)。 |
| TERMINATED    | 一个完全运行完成的线程的状态。也称之为终止状态、结束状态     |

## volatile关键字

### 基本概念
在Java中，`volatile`是一个用于修饰变量的关键字。当一个变量被声明为`volatile`时，它具备了两层主要含义：

1. **可见性**：对一个`volatile`变量的读写操作，总是能直接反映到主内存中。换句话说，一个线程对`volatile`变量的修改，对其他线程是立即可见的。
2. **有序性**：`volatile`可以防止指令重排序。在多线程环境中，为了提高执行效率，编译器和处理器可能会对指令进行重排序。但使用`volatile`后，可以保证其前后操作的执行顺序。

### 工作原理
`volatile`的工作原理主要基于内存屏障（Memory Barrier）：
1. **写屏障**：在写入`volatile`变量后，会插入一个写屏障，确保该变量的新值对其他线程立即可见。
2. **读屏障**：在读取`volatile`变量前，会插入一个读屏障，确保读取的是该变量的最新值。
通过这两道屏障，`volatile`实现了其可见性和有序性的保证。

### 使用场景
`volatile`主要适用于以下场景：
1. **状态标记**：用于标记某个状态，如线程的启动、停止等。
   ```java
   volatile boolean running = true;
   while (running) {
       // do something
   }
   ```
2. **双重检查锁定**：在单例模式中，用于确保只创建一个实例。
   ```java
   public class Singleton {
       private static volatile Singleton instance;
       private Singleton() {}
       
       public static Singleton getInstance() {
           if (instance == null) {
               synchronized (Singleton.class) {
                   if (instance == null) {
                       instance = new Singleton();
                   }
               }
           }
           return instance;
       }
   }
   ```
### 局限性
尽管`volatile`很有用，但它也有局限性：
1. **不保证原子性**：`volatile`不能保证复合操作（如自增、自减）的原子性。
   ```java
   volatile int count = 0;
   count++; // 这个操作不是原子性的
   ```
2. **不适合复杂的状态管理**：对于复杂的状态管理，使用`volatile`可能会变得复杂且难以维护。

### 与synchronized的区别
`volatile`和`synchronized`都是Java中用于多线程同步的关键字，但它们有明显的区别：

1. **功能**：`volatile`主要保证可见性和有序性，而`synchronized`还可以保证原子性。
2. **使用方式**：`volatile`用于修饰变量，而`synchronized`可以用于修饰方法或代码块。
3. **性能**：通常情况下，`volatile`的性能优于`synchronized`，因为它不会引入线程阻塞。

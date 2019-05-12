# JUC 笔记

### 一、多线程

#### 1、程序、进行、线程概念

> - 程序（program）：是为了完成特定任务、用某种语言编写的一组指令集合。即一段静态的代码，静态对象。
> - 进程（process）：是程序的一次执行过程，或是正在运行的一个程序。如：运行中的 qq 它就是一个进程。
> - 线程（Thread）：进程可进一步细化为线程，是一个程序内部的一条执行路径。若一个程序可以在同一时间执行多个线程，就是支持多线程的。 

**何时需要多线程：**

> - 程序需要同时执行两个或多个任务
> - 程序需要实现一些需要等待的任务时，如：用户输入，文件读写
> - 需要一些后台运行的程序（如 java 的垃圾回收机制以守护线程的方式运行 ）

**多线程的创建和启动：**

> - Java 语言的 JVM 允许程序运行多个线程，它通过 `java.lang.Thread` 来实现
> - Thread 类的特性
>   - 每个线程都是通过某个特定 Thread 对象的 run()  方法来完成操作的，通常把 run() 方法的主体称为线程体。
>   - 通过该对象的 start() 方法来调用这个线程 

```java
/**
 * 创建一个子线程，完成 1-100 之间自然数的输出，主线程执行同样的操作
 */

// 1.创建一个继承 Thread 类的子类
class SubThread extends Thread {
    // 2.重写 Thread 类的 run() 方法，方法内实现此子线程要完成的功能
    public void  run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("子线程---" + Thread.currentThread().getName() + i);
        }

    }

}

public class TestThread {
    public static void main(String[] args) {
        // 3.创建一个子类的对象
        SubThread st = new SubThread();
        // 4.调用线程 start 方法，①启动线程 ②调用相应的 run 方法
        // 一个线程只能执行一次 start 方法
        st.start();
        for (int i = 0; i < 100; i++) {
            System.out.println("主线程---" + Thread.currentThread().getName() + i);
        }

    }

}
```

**Thread 类常用方法：**

> ```java
> /**
>  * Thread 的常用方法
>  * 1.start() 方法：启动线程，并执行相应的 run() 方法
>  * 2.run() 方法：子线程要执行的代码放入 run() 方法中
>  * 3.currentThread() 方法：静态方法，获取当前线程
>  * 4.getName() 方法：获取此线程的名字
>  * 5.setName() 方法：设置此线程的名字
>  * 6.yield() 方法：调用此方法的线程释放当前 cpu 的执行权
>  * 7.join() 方法：在 A 线程中调用 B 线程的 join() 方法，表示当执行到此方法，A 线程停止执行，直到 B 线程执行完毕，A 线程再接着 join() 之后的代码执行
>  * 8.isAlive() 方法：判断当前线程是否还存活
>  * 9.sleep(long l) 方法：显示的让当前线程睡眠 l 毫秒
>  * 10.线程通信：wait() 、 notify() 、notifyAll()
>  * 11.设置线程的优先级：
>  * getPriority()：返回线程的优先值
>  * setPriority(int newPriority)：设置线程的优先级（只能保证线程抢到 cpu 执行权的概率变大了）
>  */
> ```

**继承的方式实现火车站窗口售票：**

```java
package com.iflytek.thread;

/**
 * 模拟火车站窗口售票，开启三个窗口售票，总票数为 100 张
 */
class Window extends Thread {
    static int tickets = 100;
    public void run() {
        while (true) {
            if (tickets > 0) {
                System.out.println(Thread.currentThread().getName() + "号窗口" + tickets--);
            } else {
                break;
            }
        }
    }
}

public class TestWindow {
    public static void main(String[] args) {
        Window w1 = new Window();
        w1.setName("1号");
        w1.start();

        Window w2 = new Window();
        w2.setName("2号");
        w2.start();

        Window w3 = new Window();
        w3.setName("3号");
        w3.start();
    }
}

```

**实现的方式创建多线程：**

```java
/**
 * 通过实现 Runnable 接口的方式创建多线程
 *
 */
//1.创建一个实现 Runnable 接口的类
class PrintNum implements Runnable {

    //2.重写 run 方法
    @Override
    public void run() {
        // 子线程执行的代码
        for(int i = 0; i < 100; i++) {
            if (i % 2 == 0) {
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        }
    }
}

public class TestThread1 {
    public static void main(String[] args) {
        // 3.创建一个实现 Runnable 接口类的对象
        PrintNum p1 = new PrintNum();
        //4.将此对象作为形参传递给 Thread 类的构造器中，创建 Thread 类的对象，此对象即为一个线程
        Thread t1 = new Thread(p1);
        // 要想启动一个线程，必须调用 start 方法
        t1.start();   // 5.启动线程并执行 run 方法
        Thread t2 = new Thread(p1);
        t2.start();
    }
}
```

**实现的方式实现火车站售票：**

```java
/**
 * 通过实现 Runnbale 接口的方式售票
 */
class Window1 implements Runnable {
    int ticket = 100;
    @Override
    public void run() {
        while (true) {
            if (ticket > 0) {
                System.out.println(Thread.currentThread().getName() + "售的票号为：" + ticket--);
            } else  {
                break;
            }
        }
    }
}
public class TestWindow1 {
    public static void main(String[] args) {
        Window1 w1 = new Window1();
        Thread t1 = new Thread(w1);
        Thread t2 = new Thread(w1);
        t1.start();
        t2.start();
    }
}
```

#### 2、线程的生命周期

> JDK 中使用 Thread.State 枚举类表示线程的几种状态
>
> - 要想实现多线程，必须在主线程中创建新的线程对象。 Java 语言使用 Thread 类及其子类的对象来表示线程，在它的一个完整的生命周期中通常要经历如下 5 中状态：
>   - 新建：



### 二、JUC简介

在 Java5.0 提供了 java.util.concurrent （简称 juc 包），在此包中增加了在并发编程中很常用的实用工具类，用于定义类似于线程的自定义子系统，包括线程池、异步 IO 和轻量级任务框架。提供可调的、灵活的线程池。还提供了设计用于多线程上下文中的 Collection 实现
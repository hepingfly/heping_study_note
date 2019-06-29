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



### 二、JUC

#### 1、volatile 关键字与内存可见性

在 Java5.0 提供了 java.util.concurrent （简称 juc 包），在此包中增加了在并发编程中很常用的实用工具类，用于定义类似于线程的自定义子系统，包括线程池、异步 IO 和轻量级任务框架。提供可调的、灵活的线程池。还提供了设计用于多线程上下文中的 Collection 实现

在说 volatile 关键字与内存可见性之前，先看下面一段代码

```java
public class TestVolatile {
    public static void main(String[] args) {
        ThreadDemo td = new ThreadDemo();
        Thread t = new Thread(td);
        t.start();
        while (true) {
            if (td.isFlag()) {
                System.out.println("-----------");
                break;
            }
        }
    }

}

class ThreadDemo implements Runnable {
    private boolean flag = false;
    @Override
    public void run() {
        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        flag = true;
        System.out.println("flag = " + isFlag());

    }

    public boolean isFlag() {
        return flag;
    }

    public void setFlag(boolean flag) {
        this.flag = flag;
    }
}
```

这段代码有两个线程，一个主线程，一个子线程。子线程要做的事就是修改 flag 的值，主线程要做的事就是读取 flag 的值，如果 flag = true 就打印一连串虚线，并跳出循环。

运行结果：

```java
flag = true
并且程序没有停止
```

**共享变量在线程间的可见性：**

> - 共享变量：如果一个变量在多个线程的工作内存中都存在副本，那么这个变量就是这几个线程的共享变量
> - 可见性：一个线程对共享变量值的修改，能够及时的被其它线程看到

**说明：**

> - 所有变量都存储在主内存中
> - 每个线程都有自己独立的工作内存，里面保存该线程使用到的变量的副本（主内存中该变量的一份拷贝）
> - 线程对共享变量的所有操作都必须在自己的工作内存中进行，不能直接从相互内存中读写
> - 不同线程之间无法直接访问其他线程工作内存中的变量，线程之间变量值的传递需要通过主内存来完成

共享变量可见性实现原理：

> 把工作内存1 中更新过的共享变量刷新到主内存中，把主内存中最新的共享变量的值更新到工作内存2 中

**volatile 关键字：当多个线程进行操作共享数据时，可以保证内存中的数据是可见的**

![内存可见性问题](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-juc/%E5%86%85%E5%AD%98%E5%8F%AF%E8%A7%81%E6%80%A7%E9%97%AE%E9%A2%98.png?q-sign-algorithm=sha1&q-ak=AKIDXHDkCk0xmkdNTBWv0ryBG3tNimKqojSn&q-sign-time=1560575042;1560578642&q-key-time=1560575042;1560578642&q-header-list=&q-url-param-list=&q-signature=e0ce0e95011b2f80ba179a75c015681b7ff894ad&x-cos-security-token=24079727f7e02bee9ceb7a86f82f8878c3b3660610001)



```java
class ThreadDemo implements Runnable {
    // 在共享数据上加一个 volatile 关键字
    private volatile boolean flag = false;
    @Override
    public void run() {
        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        flag = true;
        System.out.println("flag = " + isFlag());
    }

    public boolean isFlag() {
        return flag;
    }

    public void setFlag(boolean flag) {
        this.flag = flag;
    }
}
```

![内存可见性问题2](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-juc/%E5%86%85%E5%AD%98%E5%8F%AF%E8%A7%81%E6%80%A7%E9%97%AE%E9%A2%982.png?q-sign-algorithm=sha1&q-ak=AKID5AiFJXtA5XW34uZ1F3ji8Wlz5FOS6qi7&q-sign-time=1560575132;1560578732&q-key-time=1560575132;1560578732&q-header-list=&q-url-param-list=&q-signature=db838cde04401ff2c9664012b26aca83f929c6d8&x-cos-security-token=4c385f1569186a22d91e7626443e7a39387b0eb610001)

#### 2、原子变量与 CAS 算法

```java
/**
 * i++ 的原子性问题: 实际上分为三个步骤："读-改-写"
 *  int i = 10
 *  i = i++   // i = 10
 *
 *  i = i++ 在计算机底层是这么操作的：
 *  int temp = i;  先把 i 赋给一个临时变量
 *  i = i + 1;
 *  i = temp;    再把临时变量赋值给 i
 */
public class TestAtomicDemo {
    public static void main(String[] args) {
        AtomicDemo ad = new AtomicDemo();
        for (int i = 0; i < 10; i++) {
            new Thread(ad).start();
        }
    }
}

class AtomicDemo implements Runnable {
    private int serialNumber = 0;
    @Override
    public void run() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(getSerialNumber());
    }
    public int getSerialNumber() {
        return serialNumber++;
    }
}

// 上面这段代码运行的时候会出现线程安全问题(原子性问题)
// 0 1 0 4 2 0 3 7 5 6
```

下面分析一下上面线程安全产生的原因：

![原子性问题](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-juc/%E5%8E%9F%E5%AD%90%E6%80%A7%E9%97%AE%E9%A2%98.png?q-sign-algorithm=sha1&q-ak=AKIDk7RKj1UuZB20oNyImKkKgnEYhXFfPLWv&q-sign-time=1560583191;1560586791&q-key-time=1560583191;1560586791&q-header-list=&q-url-param-list=&q-signature=38c1a4616b7980d50f825a9095123e296291ebb0&x-cos-security-token=8bf465737cbbd1d050d5cb7a72f376850007901410001)

那么怎么解决上面产生的问题呢？

使用原子变量：

> jdk1.5 以后 java.util.concurrent.atomic 包下提供了常用的原子变量
>
> 原子变量时如何解决上面的问题的：
>
> 1、原子变量中包含 volatile 关键字,保证内存可见性
>
> 2、CAS（Compare-And-Swap） 算法保证数据的原子性
>
> - CAS 算法是硬件对于并发操作共享数据的支持
> - CAS 算法包含了三个操作数
>   - 内存值 V
>   - 预估值 A
>   - 更新值 B
>   - 当且仅当 V == A 时，才把 V = B。否则，将不做任何操作

![cas 算法](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-juc/cas%E7%AE%97%E6%B3%95.png?q-sign-algorithm=sha1&q-ak=AKIDyPKjwv3QA1VTA4Z96pY7EppasQcLPb4D&q-sign-time=1560585648;1560589248&q-key-time=1560585648;1560589248&q-header-list=&q-url-param-list=&q-signature=a22eda0f863fc11c41ccf74ef341a3dfc352fe4f&x-cos-security-token=f19fcc5a152d699320dbdac66d9675ae72a83d8910001)

```java
public class TestAtomicDemo {
    public static void main(String[] args) {
        AtomicDemo ad = new AtomicDemo();
        for (int i = 0; i < 10; i++) {
            new Thread(ad).start();
        }
    }
}

class AtomicDemo implements Runnable {

    // 和包装类使用差不多
    private AtomicInteger serialNumber = new AtomicInteger();

    @Override
    public void run() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(getSerialNumber());
    }

    public int getSerialNumber() {
        // 自增
        return serialNumber.getAndIncrement();
    }
}

// 这样就不会有线程安全问题了
// 1 8 9 2 7 5 6 0 3 4
```

#### 3、模拟 cas 算法

> - CAS（Compare-And-Swap）是一种硬件对并发的支持，针对多处理器操作而设计的处理器中的一种特殊指令，用于管理对共享数据的并发访问
> - CAS 是一种无锁的非阻塞算法的实现
> - CAS 包含了 3 个操作数
>   - 需要读写的内存值 V
>   - 进行比较的值 A
>   - 拟写入的新值 B
> - 当且仅当 V 的值等于 A 时，CAS 通过原子方式用新值 B 来更新 V 的值，否则不会执行任何操作

```java
/**
 * 模拟 cas 算法
 */
public class TestCompareAndSwap {
    public static void main(String[] args) {
        CompareAndSwap cas = new CompareAndSwap();
        for (int i = 0; i < 10; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    int expectValue = cas.get();
                    boolean flag = cas.compareAndSet(expectValue, (int) Math.random());
                    System.out.println(flag);
                }
            }).start();
        }
    }
}
class CompareAndSwap {
    // 内存值
    private int value;

    // 用于获取内存值
    public synchronized int get() {
        return value;
    }

    //比较
    public synchronized int compareAndSwap(int expectValue, int newValue) {
        // 在比较前都会先读一下内存值
        int oldValue = value;
        // 让内存值和预估值进行比较
        if (oldValue == expectValue) {
            // 如果一样，就替换
            this.value = newValue;
        }
        return oldValue;
    }
    // 设置值
    public synchronized boolean compareAndSet(int expectValue, int newValue) {
        return expectValue == compareAndSwap(expectValue, newValue);
    }
}
```

#### 4、concurrentHashMap

> - java 5.0 在 java.util.concurrent 包中提供了多种并发容器类来改进同步容器的性能
> - concurrentHashMap 同步容器类是 Java5 增加的一个线程安全的哈希表。对于多线程的操作，介于 HashMap 与 Hashtable 之间。内部采用 "锁分段" 机制替代 Hashtable 的独占锁，进而提高性能。
> - 此包还提供了用于多线程上下文中的 Collection 实现：ConcurrentHashMap 、ConcurrentSkipListMap、ConcurrentSkipListSet、CopyOnWriteArrayList 和 CopyOnWriteArraySet。当期望许多线程访问一个给定 Collection 时，ConcurrentHashMap 通常优于同步的 HashMap。ConcurrentSkipListMap 通常优于同步的 TreeMap。当期望的读数和遍历远远大于列表的更新数时，CopyOnWriteArrayList 优于同步的 ArrayList

```java
/**
 * 添加操作多时，效率低，因为每次添加时都会进行复制，开销非常大
 * 但是当并发迭代操作时，可以选择，效率高
 */
public class TestCopyOnWriteArrayList {
    public static void main(String[] args) {
        HelloThread thread = new HelloThread();
        for (int i = 0; i < 10; i++) {
            new Thread(thread).start();
        }
    }
}

class HelloThread implements Runnable {
    // synchronizedList 它的包装方式就是把 Arraylist 里面的方法都变成了同步方法
//    private static List<String> list = Collections.synchronizedList(new ArrayList<String>());
    private static CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<String>();
    static {
        list.add("AA");
        list.add("BB");
        list.add("CC");
    }
    @Override
    public void run() {
        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
            //边迭代边添加元素(会报 java.util.ConcurrentModificationException)
            list.add("66");
            // 使用 CopyOnWriteArrayList 每次添加时都会把列表复制一份
        }
    }
}
```

#### 5、CountDownLatch 闭锁

> - CountDownLatch 一个同步辅助类，在完成一组正在其他线程中执行的操作之前，它允许一个或多个线程一直等待
> - 闭锁可以延迟线程的进度直到其到达终止状态，闭锁可以用来确保某些活动直到其他活动都完成才继续执行。
>   - 确保某个计算在其需要的所有资源都被初始化之后才继续执行
>   - 确保某个服务在其依赖的所有其他服务都已启动之后才启动
>   - 等待直到某个操作所有参与者都准备就绪再继续执行

```java
package com.iflytek.juc;

import java.util.concurrent.CountDownLatch;

/**
 * CountDownLatch ：闭锁，在完成某些运算时，只有其他所有线程的运算全部完成，当前运算才继续执行
 */
public class TestCountDownLatch {
    public static void main(String[] args) {
        // 这里初始化为 10，因为有 10 个子线程在执行,这里的 10 相当于一个计数器
        final CountDownLatch latch = new CountDownLatch(10);
        LatchDemo ld = new LatchDemo(latch);
        /**
         * 我现在开启 10 个子线程，想去算一下，30000 以内的偶数的执行时间
         * 下面这种写法是有问题的，因为 10 个子线程，1 个主线程，这 11 个线程
         * 是同时执行的，很可能都看不到最后打印的那句话
         * 需要的做法应该是：等 10 个子线程执行完，然后我主线程再执行，算 10 个子线程的执行时间
         */
        long start = System.currentTimeMillis();
        for (int i = 0; i < 10; i++) {
            new Thread(ld).start();
        }
        try {
            // countDown 在递减到 0 之前，主线程一直等待
            latch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        long end = System.currentTimeMillis();
        System.out.println("执行时间：" + (end - start));
    }

}

class LatchDemo implements Runnable {
    private CountDownLatch latch;

    public LatchDemo(CountDownLatch latch) {
        this.latch = latch;
    }

    @Override
    public void run() {
        // 为了防止有线程安全问题，加个锁
        synchronized (this) {
            try {
                for (int i = 0; i < 30000; i++) {
                    if (i % 2 == 0) {
                        System.out.println(i);
                    }
                }
            } finally {
                // 一个线程执行完了之后，我需要把这个计数器递减 1
                // 为了保证必须执行，所以放在 finally 中
                latch.countDown();
            }
        }

    }
}
```

#### 6、实现 callable 接口

除了实现 Runnable 接口和继承 Thread 接口，第三种创建线程的方式：

实现 Callable 接口

> - 相较于实现 Runnable 接口的方式，方法可以有返回值，并且可以抛出异常
> - 执行 Callable 方式，需要 FutureTask 实现类的支持，用于接收运算结果。FutureTask 是 Future 接口的实现类

```java
/**
 * 创建线程的方式三：实现 Callable 接口
 * 相较于实现 Runnable 接口的方式，方法可以有返回值，并且可以抛出异常
 *
 * 执行 Callable 方式，需要 FutureTask 实现类的支持，用于接收运算结果。FutureTask 是 Future 接口的实现类
 */
public class TestCallable {
    public static void main(String[] args) {
        ThreadDemo2 td = new ThreadDemo2();
        // 因为 Callable 接口有返回值，所以我们需要多一步接收返回值的操作
        // 因此 Callable 接口需要一个 FutureTask 实现类的支持，用于接收运算结果
        FutureTask<Integer> futureTask = new FutureTask<Integer>(td);
        new Thread(futureTask).start();  // 启动线程
        System.out.println("---------------");
        // 在  new Thread(futureTask).start(); 线程的执行过程中，下面的主线程代码是没有执行的，子线程都执行完之后，下面主线程代码才会执行，类似于闭锁
        try {
            // 接收线程运算后的结果
            Integer result = futureTask.get();  // futureTask 也可用于闭锁
            System.out.println(result);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}

/**
 * 接口指定的泛型就是返回值的类型
 */
class ThreadDemo2 implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {
        int sum = 0;
        for (int i = 0; i <= 100; i++) {
            sum += i;
        }
        return sum;
    }
}

/*class ThreadDemo2 implements Runnable {

    @Override
    public void run() {

    }
}*/
```

#### 7、同步锁 Lock

> 用于解决多线程安全问题的方式：
>
> synchronized : 隐式锁
>
> - 同步代码块
> - 同步方法
>
> jdk1.5 以后
>
> - 同步锁 Lock
>   - 注意：这是一个显示锁，需要通过 lock() 方法上锁，必须通过 unlock() 方法进行释放锁

```java
public class TestLock {
    public static void main(String[] args) {
        Ticket ticket = new Ticket();
        new Thread(ticket,"1号窗口").start();
        new Thread(ticket,"2号窗口").start();
        new Thread(ticket,"3号窗口").start();
    }
}

class Ticket implements Runnable {
    private int tickets = 100;
    // lock 是一个接口,new 一个它的实现类
    private Lock lock = new ReentrantLock();
    @Override
    public void run() {
        while (true) {
            lock.lock();   // 上锁
            try {
                if (tickets > 0) {
                    try {
                        Thread.sleep(200);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName() + "余票为：" + --tickets);
                }
            } finally {
                lock.unlock();   // 释放锁,这里的锁一定要释放，所以写到 finally 中
            }
        }
    }
}
```

#### 8、生产者消费者案例

等待唤醒机制最经典一个的案例，就是生产者消费者案例

**没有使用等待唤醒机制的生产者消费者案例：**

```java
/**
 * 生产者消费者案例
 * 生产者生产产品给店员
 * 消费者从店员处取产品
 */
public class ProductorAndConsumer {
    public static void main(String[] args) {
        Clerk clerk = new Clerk();
        Productor productor = new Productor(clerk);
        Consumer consumer = new Consumer(clerk);
        new Thread(productor, "生产者").start();
        new Thread(consumer, "消费者").start();
    }
}

class Clerk {
    private int product = 0;

    // 进货（假设最多只能有 10 个货物，超过 10 个就产品已满）
    public synchronized void get() {
        if (product >= 10) {
            System.out.println("产品已满，无法添加");
        } else {
            System.out.println(Thread.currentThread().getName() + ":" + ++product);
        }
    }

    // 卖货（如果货物小于 0 ，提示缺货）
    public synchronized void sale() {
        if (product <= 0) {
            System.out.println("缺货了");
        } else {
            System.out.println(Thread.currentThread().getName() + ":" + --product);
        }
    }
}

/**
 * 生产者
 */
class Productor implements Runnable {
    // 生产者生产产品给到店员
    private Clerk clerk;

    public Productor(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            clerk.get();
        }
    }
}

/**
 * 消费者
 */
class Consumer implements Runnable {

    // 消费者从店员处消费商品
    private Clerk clerk;

    public Consumer(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            clerk.sale();
        }
    }
}
```

运行结果：

````
生产者:1
生产者:2
生产者:3
生产者:4
生产者:5
生产者:6
生产者:7
生产者:8
生产者:9
生产者:10
产品已满，无法添加
产品已满，无法添加
产品已满，无法添加
产品已满，无法添加
产品已满，无法添加
产品已满，无法添加
产品已满，无法添加
产品已满，无法添加
产品已满，无法添加
产品已满，无法添加
消费者:9
消费者:8
消费者:7
消费者:6
消费者:5
消费者:4
消费者:3
消费者:2
消费者:1
消费者:0
缺货了
缺货了
缺货了
缺货了
缺货了
缺货了
缺货了
缺货了
缺货了
缺货了
````

产品已满了，生产者还没有停，一直在不断的生产，所以循环 20 次，会有 10 次产品已满的情况。消费者同理，货物被消费完了，但是消费者还在不断的消费，所以会打印 10 次 缺货了。

**注：**

> **在实际开发中，我们把添加和创建数据的线程叫生产者线程，把删除和销毁数据的线程叫消费者线程**
>
> 如果生产者线程过快，也就是说你不断的发数据，另一方其实已经接收不到了，这时候就会造成一个数据丢失的情况
>
> 如果消费者线程过快， 也就是说你不断的接收数据，但是另一方其实已经不发了， 这时候就可能出现重复的数据或者错误的数据。

使用等待唤醒机制解决上述问题：

```java
/**
 * 生产者消费者案例
 * 生产者生产产品给店员
 * 消费者从店员处取产品
 */
public class ProductorAndConsumer {
    public static void main(String[] args) {
        Clerk clerk = new Clerk();
        Productor productor = new Productor(clerk);
        Consumer consumer = new Consumer(clerk);
        new Thread(productor, "生产者").start();
        new Thread(consumer, "消费者").start();
    }
}

class Clerk {
    private int product = 0;

    // 进货（假设最多只能有 10 个货物，超过 10 个就产品已满）
    public synchronized void get() {
        if (product >= 10) {
            System.out.println("产品已满，无法添加");
            // 如果产品已满了，生产者就不能继续生产产品了
            try {
                this.wait();  // 等待
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        } else {
            System.out.println(Thread.currentThread().getName() + ":" + ++product);
            // 如果生产者成功生产了一个产品，说明有多余的产品供消费者消费
            this.notifyAll();   // 通知消费者可以进行消费
        }
    }

    // 卖货（如果货物小于 0 ，提示缺货）
    public synchronized void sale() {
        if (product <= 0) {
            System.out.println("缺货了");
            // 如果缺货了，消费者就不能继续进行消费了
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        } else {
            System.out.println(Thread.currentThread().getName() + ":" + --product);
            // 如果成功消费了一个产品，就可以通知生产者继续进行生产
            this.notifyAll();
        }
    }
}

/**
 * 生产者
 */
class Productor implements Runnable {
    // 生产者生产产品给到店员
    private Clerk clerk;

    public Productor(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            clerk.get();
        }
    }
}

/**
 * 消费者
 */
class Consumer implements Runnable {
    // 消费者从店员处消费商品
    private Clerk clerk;

    public Consumer(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            clerk.sale();
        }
    }
}
```

运行结果：

```
生产者:1
消费者:0
缺货了
生产者:1
生产者:2
生产者:3
生产者:4
生产者:5
生产者:6
生产者:7
生产者:8
生产者:9
生产者:10
产品已满，无法添加
消费者:9
消费者:8
消费者:7
消费者:6
消费者:5
消费者:4
消费者:3
消费者:2
消费者:1
消费者:0
缺货了
生产者:1
生产者:2
生产者:3
生产者:4
生产者:5
生产者:6
生产者:7
生产者:8
消费者:7
消费者:6
消费者:5
消费者:4
消费者:3
消费者:2
消费者:1
```

上述就是一旦发现产品已满，生产者就不会再继续生产了。一旦发现缺货，消费者就不会再继续消费了。

**为了避免虚假唤醒，wait 操作应该始终写在循环中：**

```java
package com.iflytek.juc;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class ProductorAndConsumerForLock {
    public static void main(String[] args) {
        Clerk clerk = new Clerk();
        Productor productor = new Productor(clerk);
        Consumer consumer = new Consumer(clerk);
        new Thread(productor, "生产者").start();
        new Thread(consumer, "消费者").start();
    }
}
class Clerk {
    private int product = 0;

    // 进货（假设最多只能有 10 个货物，超过 10 个就产品已满）
    public void get() {
        while (product >= 10) {  // 为了避免虚假唤醒, wait 应该始终写在循环中
                System.out.println("产品已满，无法添加");
                // 如果产品已满了，生产者就不能继续生产产品了
                try {
                    this.wait();  // 等待
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println(Thread.currentThread().getName() + ":" + ++product);
            // 如果生产者成功生产了一个产品，说明有多余的产品供消费者消费
            this.notifyAll();   // 通知消费者可以进行消费
    }

    // 卖货（如果货物小于 0 ，提示缺货）
    public void sale() {
        while (product <= 0) {   // 为了避免虚假环境 wait 应该始终写在循环里面
                System.out.println("缺货了");
                // 如果缺货了，消费者就不能继续进行消费了
                try {
                    this.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println(Thread.currentThread().getName() + ":" + --product);
            // 如果成功消费了一个产品，就可以通知生产者继续进行生产
            this.notifyAll();
    }
}

/**
 * 生产者
 */
class Productor implements Runnable {
    // 生产者生产产品给到店员
    private Clerk clerk;

    public Productor(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            clerk.get();
        }
    }
}

/**
 * 消费者
 */
class Consumer implements Runnable {

    // 消费者从店员处消费商品
    private Clerk clerk;

    public Consumer(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            clerk.sale();
        }
    }
}


```

#### 9、Condition 线程通信

> - Condition 接口描述了可能与锁有关联的条件变量。这些变量在用法上与使用 Object.wait() 类似，但提供了更强大的功能。需要特别指出的是，单个 Lock 可能与多个 Condition 对象关联。为了避免兼容性问题，Condition 方法的名称与对应的 Object 版本中的不同
> - 在 Condition 对象中，与 `wait` 、`notify`、 `notifyAll` 方法对应的分别是 `await` 、`signal` 、`signalAll` 
> - Condition 实例实际上是被绑定到一个锁上。要为特定 Lock 实例获得 Condition 实例，请使用 `newCondition()` 方法

```java
/**
 * 使用同步锁的方式避免线程安全问题
 */
public class ProductorAndConsumerForLock {
    public static void main(String[] args) {
        Clerk clerk = new Clerk();
        Productor productor = new Productor(clerk);
        Consumer consumer = new Consumer(clerk);
        new Thread(productor, "生产者").start();
        new Thread(consumer, "消费者").start();
    }
}
class Clerk {
    private int product = 0;
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    // 进货（假设最多只能有 10 个货物，超过 10 个就产品已满）
    public void get() {
        lock.lock();
        try {
            while (product >= 10) {  // 为了避免虚假唤醒, wait 应该始终写在循环中
                System.out.println("产品已满，无法添加");
                // 如果产品已满了，生产者就不能继续生产产品了
                try {
                    condition.await();  // 等待
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println(Thread.currentThread().getName() + ":" + ++product);
            // 如果生产者成功生产了一个产品，说明有多余的产品供消费者消费
            condition.signalAll();   // 通知消费者可以进行消费
        }finally {
            lock.unlock();
        }
    }

    // 卖货（如果货物小于 0 ，提示缺货）
    public void sale() {
        lock.lock();
        try {
            while (product <= 0) {   // 为了避免虚假环境 wait 应该始终写在循环里面
                System.out.println("缺货了");
                // 如果缺货了，消费者就不能继续进行消费了
                try {
                    condition.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println(Thread.currentThread().getName() + ":" + --product);
            // 如果成功消费了一个产品，就可以通知生产者继续进行生产
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }
}

/**
 * 生产者
 */
class Productor implements Runnable {
    // 生产者生产产品给到店员
    private Clerk clerk;

    public Productor(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            clerk.get();
        }
    }
}

/**
 * 消费者
 */
class Consumer implements Runnable {

    // 消费者从店员处消费商品
    private Clerk clerk;

    public Consumer(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            clerk.sale();
        }
    }
}
```

#### 10、线程按序交替

有这么一个功能需要完成，编写一个程序，开启三个线程，这三个线程的 id 分别是 A 、B、C ，每个线程将自己的 id 打印 5 遍，A 打印完 B打印，B 打印完 C 打印，然后循环 10 次，要求输出的结果必须按照顺序显示

```java
/**
 * 编写一个程序，开启三个线程，这三个线程的 id 分别为 A、B、C
 * 每个线程将自己的 id 在屏幕上打印 5 遍，要求输出的结果必须按顺序显示，循环 10 轮
 */
public class TestABCAlternate {
    public static void main(String[] args) {
        AlternateDemo ad = new AlternateDemo();
        new Thread(new Runnable() {
            @Override
            public void run() {
                // 线程 A 在屏幕上打印 10 遍
                for (int i = 1;i <= 10; i++) {
                    ad.loopA(i);  // 第几轮打印 A
                }
            }
        },"线程A").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                // 线程 B 在屏幕上打印 10 遍
                for (int i = 1;i <= 10; i++) {
                    ad.loopB(i);  // 第几轮打印 B
                }
            }
        },"线程B").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                // 线程 C 在屏幕上打印 10 遍
                for (int i = 1;i <= 10; i++) {
                    ad.loopC(i);  // 第几轮打印 C
                }
            }
        },"线程C").start();
    }
}

class AlternateDemo {

    // 当前正在执行线程的标记
    private int number = 1;

    // 访问上面 number 共享数据，得有锁，否则会存在线程安全问题
    private Lock lock = new ReentrantLock();
    // 三个线程要实现按序交替，一定是需要线程通信
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();

    /**
     * 循环 A 线程
     * totalLoop 循环第几轮
     */
    public void loopA (int totalLoop) {
        lock.lock();
        try {
            // 1.首先我需要判断当前执行的这个线程的编号是不是 1
            if (number != 1) {
                // 不等于 1 这个线程就不能操作，什么时候等于 1 我 A 线程才能进行打印操作
                condition1.await();  // 不等于 1 就让它等待
            }
            // 2.等于 1 就执行打印操作，这里我打印 A 5次
            for (int i = 1; i <= 5; i++) {
                System.out.println(Thread.currentThread().getName() + "\t" + i + "\t" + totalLoop );
                // 线程名 + A打印的次数 + 这是第几轮打印的A
            }
            // 3.当 A 打印完之后就该把线程执行权交出去，唤醒别人，让别人去打
            number = 2;  // A打印完之后，让 number = 2，让 2 去执行
            condition2.signal();  // 把 condition2 唤醒
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
    /**
     * 循环 B 线程
     * totalLoop 循环第几轮
     */
    public void loopB (int totalLoop) {
        lock.lock();
        try {
            // 1.首先我需要判断当前执行的这个线程的编号是不是 2
            if (number != 2) {
                // 不等于 2 这个线程就不能操作，什么时候等于 2 我 B 线程才能进行打印操作
                condition2.await();  // 不等于 2 就让它等待
            }
            // 2.等于 2 就执行打印操作，这里我打印 B 5次
            for (int i = 1; i <= 5; i++) {
                System.out.println(Thread.currentThread().getName() + "\t" + i + "\t" + totalLoop );
                // 线程名 + B打印的次数 + 这是第几轮打印的B
            }
            // 3.当 B 打印完之后就该把线程执行权交出去，唤醒别人，让别人去打
            number = 3;  // B打印完之后，让 number = 3，让 3 去执行
            condition3.signal();  // 把 condition3 唤醒
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    /**
     * 循环 C 线程
     * totalLoop 循环第几轮
     */
    public void loopC (int totalLoop) {
        lock.lock();
        try {
            // 1.首先我需要判断当前执行的这个线程的编号是不是 3
            if (number != 3) {
                // 不等于 3 这个线程就不能操作，什么时候等于 3 我 C 线程才能进行打印操作
                condition3.await();  // 不等于 3 就让它等待
            }
            // 2.等于 3 就执行打印操作，这里我打印 C 5次
            for (int i = 1; i <= 5; i++) {
                System.out.println(Thread.currentThread().getName() + "\t" + i + "\t" + totalLoop );
                // 线程名 + B打印的次数 + 这是第几轮打印的B
            }
            // 3.当 C 打印完之后就该把线程执行权交出去，唤醒别人，让别人去打
            number = 1;  // C打印完之后，让 number = 1，让 1 去执行
            condition1.signal();  // 把 condition1 唤醒
            System.out.println("------------");

        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```












































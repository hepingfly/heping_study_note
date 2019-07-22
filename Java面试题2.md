## Java 面试题二

### 一、JUC 多线程和高并发

#### 1、volatile 是什么

**谈谈你对 volatile 的理解**

> - volatile 是 Java 虚拟机提供的轻量级的同步机制
>   - 保证可见性
>   - 不保证原子性
>   - 禁止指令重排
>     - 计算机在执行程序时，为了提高性能，编译器和处理器常常会对指令做重排
>       - 单线程环境里面确保程序最终执行结果和代码顺序的执行结果一致（意思就是单线程环境不用去关心指令重排）
>       - 处理器在进行重排序时必须考虑指令之间的**数据依赖性**
>       - 多线程环境中线程交替执行，由于编译器优化重排的存在，两个线程中使用的变量能否保住一致性是无法确定的，结果无法预测

**谈谈 JMM 是什么**

> JMM（Java Memory Model）Java 内存模型，本身是一种抽象的概念*并不真实存在*，它描述的是一组规则或规范，通过这组规范定义了程序中各个变量（包括实例字段，静态字段和构成数组对象的元素）的访问方式。
>
> JMM 有三大特性：
>
> - 可见性
>   - 各个线程对主内存中共享变量的操作都是各个线程各自拷贝到自己的工作内存进行操作后再写回到主内存中
>   - 这就可能存在一个线程A 修改了共享变量 x 的值，但还未写回主内存时，另外一个线程B 又对主内存中同一个共享变量 x 进行操作，但此时 A 线程工作内存中共享变量 x 对线程 B 来说并不可见，这种工作内存与主内存同步延迟现象就造成了可见性问题
> - 原子性
> - 有序性

**JMM 关于同步的规定：**

> 1、线程解锁前，必须把共享变量的值刷新回主内存
>
> 2、线程加锁前，必须读取主内存的最新值到自己的工作内存
>
> 3、加锁解锁是同一把锁

**内存可见性：**

> 由于 JVM 运行程序的实体是线程，而每个线程创建时 JVM 都会为其创建一个工作内存，工作内存是每个线程的私有数据区域，而 Java 内存模型中规定所有的变量都存储在『主内存』中，主内存是共享内存区域，所有线程都可以访问，但**线程对变量的操作（读取赋值等）必须在工作内存中进行。首先要将变量从主内存拷贝到自己的工作内存空间，然后对变量进行操作，操作完成后再将变量写回主内存**，不能直接操作主内存中的变量，各个线程的工作内存中存储着主内存中的变量副本拷贝，因此不同的线程间无法访问对方的工作内存，线程间的通信（传值）必须通过主内存来完成。

**内存可见性分析：**

> 对于在工作中数据的传输速率基本上是这样，「硬盘 < 内存 < cpu」 ，硬盘因为有 IO 限制，所以数据的读取速度在到达一定阈值之后，想要突破是非常难的。所以我们就考虑把一部分数据存储在内存中，内存的读取速度是快过去硬盘的。这些写 「内存 < cpu」 不是说 cpu 可以存储数据，可以把内存中的数据存储到 cpu 中，不是这个意思。cpu 是负责计算的，它的计算速度非常快，就速度来说 cpu 是最快的。因为 cpu 的运算速度非常快，所以往往 cpu 把数据都计算完了，但是内存还没接收到数据。因此，在 cpu 和内存之间就会存在一个 「缓存cached」

![内存可见性](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/%E5%86%85%E5%AD%98%E5%8F%AF%E8%A7%81%E6%80%A7.png)

```java
/**
 * 1.验证 volatile 的可见性
 *    int number = 0; number 变量前面没有加 volatile 关键字修饰，没有可见性
 	  添加了 volatile 可以解决可见性问题
 */
public class VolatileDemo {
    public static void main(String[] args) {
        MyData md = new MyData();
        // 第一个线程
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName() + ":开始进入");
                try {
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                md.changeNum();
                System.out.println(Thread.currentThread().getName() + "update num:" + md.number);
            }
        },"A 线程").start();
        // 第二个线程，也就是 main 线程
        while (md.number == 0) {
            // 如果读到 number 值为 0，main 线程就一直在这里等待循环，直到 number 值不为 0
        }
        // 这句话如果打印出来，就说明 main 线程感知到了 number 从 0 变成 60（即内存可见性被触发）
        // 否则这句话是打印不出来的，会在 while 那里一直循环
        System.out.println(Thread.currentThread().getName() + "mission is over");
    }
}

class MyData {
//    int number = 0;
    volatile int number = 0;

    public void changeNum() {
        // 只要有线程调用这个方法就会把 number 从 0 改成 60
        this.number = 60;
    }

}
```

解释一下上面这段代码：

> 上面这段代码我想验证 volatile 关键字的内存可见性，现在我跑了两个线程，这两个线程都去读取 number 的值，然后其中一个线程去修改 number 的值，如果没有内存可见性，那么另一个线程是是感受不到 number 的变化的，具体在代码中体现就是没有使用 volatile 关键字去修饰 number ，A 线程把 number 值改了以后，因为 main 线程一开始读取到 number=0 所以一直在 while 死循环，程序结束不了。如果 number 使用了 volatile 关键字修饰，这样就保证了内存的可见性，那么我一个线程去修改了 number 的值，另一个线程马上就能感知到，体现在代码中就是我 A 线程修改了 number 的值，main 线程马上可以感知到， while 循环会结束，最后的一个打印语句会执行。

**小贴士：**

> 并发和并行有什么区别？
>
>  高并发，举个很简单的例子就是「秒杀」是多个线程同时去访问同一个资源
>
> 并行，是多个事情同时去做。比如说你一边写代码，一边听歌，这两个事情就是并行。

#### 2、volatile 不保证原子性

**首先说下原子性指的什么意思？**

> 指的就是不可分割，完整性。也即某个线程正在做某个具体业务时，中间不可以被加塞或者被分割，需要整体完整，要么同时成功，要么同时失败。

**代码证明 volatile 不保证原子性:**

```java
class MyData {
    volatile int number = 0;
    // 这里我让 number 自增，并且共享变量 number 前面加了 volatile
    public void increase() {
        number++;
    }
}
public class VolatileDemo {
    public static void main(String[] args) {
//        visibility();
        // 现在我创建了 20 个线程，每个线程都去调用 increase() 方法，每个线程调用 1000 次
        // 如果 volatile 可以保证原子性的话，等 20 个线程执行完了以后，main 线程拿到 number 的值应该是 20000
        MyData md = new MyData();
        for (int i = 0; i < 20; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    for (int j = 0; j < 1000; j++) {
                        md.increase();
                    }
                }
            },String.valueOf(i)).start();
        }
        // 等上面线程都执行完后，main 线程取得最后结果值
        // 大于 2 是因为除了上面的 20 个线程以外，还有 main 线程和 GC 线程
        while (Thread.activeCount() > 2) {
            // 证明上面的 20 个线程还没计算完，我主线程把 cpu 执行权交出去
            Thread.yield();
        }
        System.out.println(Thread.currentThread().getName() + "finally number value" + md.number);    // 最后这里打印的值都不是 20000 证明 volatile 不能保证原子性

    }
}
```

**解释一下 volatile 为什么保证不了原子性：**

![volatile不保证原子性](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/volatile%E4%B8%8D%E4%BF%9D%E8%AF%81%E5%8E%9F%E5%AD%90%E6%80%A7.png?q-sign-algorithm=sha1&q-ak=AKIDT71QAX7DHcSzj9gixOYuYMS1dFFuP4J5&q-sign-time=1563089598;1563093198&q-key-time=1563089598;1563093198&q-header-list=&q-url-param-list=&q-signature=b320de7d1037d0c9e55baa75d2d8bb2c88482e00&x-cos-security-token=a54f8e50f0daad8049fd2ac40e1e339d1111fc9b10001)

#### 3、volatile 不保证原子性问题解决

方式：

使用 AtomicInteger  这个类可以保证原子性

```java
public class VolatileDemo {
    public static void main(String[] args) {
//        visibility();
        // 现在我创建了 20 个线程，每个线程都去调用 increase() 方法，每个线程调用 1000 次
        MyData md = new MyData();
        for (int i = 0; i < 20; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    for (int j = 0; j < 1000; j++) {
                        md.atomicIncrease();
                    }
                }
            },String.valueOf(i)).start();
        }
        // 等上面线程都执行完后，main 线程取得最后结果值
        // 大于 2 是因为除了上面的 20 个线程以外，还有 main 线程和 GC 线程
        while (Thread.activeCount() > 2) {
            // 证明上面的 20 个线程还没计算完，我主线程把 cpu 执行权交出去
            Thread.yield();
        }
        System.out.println(Thread.currentThread().getName() + "finally number value" + md.atomicInteger);
    }
}

class MyData {
	// AtomicInteger 可以保证原子性
    AtomicInteger atomicInteger = new AtomicInteger();
    public void atomicIncrease() {
        atomicInteger.getAndIncrement();
    }
}
```

#### 4、单例模式在多线程环境下可能会出现线程安全问题

```java
public class SingletonDemo {

    private static SingletonDemo instance = null;
    private SingletonDemo() {
        System.out.println(Thread.currentThread().getName() + ":" + "创建实例");
    }
    public static SingletonDemo getInstance() {
        if (instance == null) {
            instance = new SingletonDemo();
        }
        return instance;
    }
    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
                }
            },"线程" + i).start();
        }
    }
}
```

> 上面的代码为单例模式的写法，如果多个线程同时去执行就可能出问题，单例模式按理说构造器只会调用一次，但是在多线程环境下就会出现调用多次构造器的情况，那么这就违背了单例模式的设计初衷。一种解决方法就是在创建单例对象的时候加上 synchronized 关键字，但这并不是最优的方法。加上 synchronized 关键字后，一个线程握住锁后，别的线程都要阻塞，影响程序性能。

#### 5、单例模式 volatile 分析

**DCL 版单例模式：**

上面我们说了单例模式在多线程下，可能会出现线程安全问题。下面我们来使用 DCL 来解决线程安全问题

```java
public class SingletonDemo {

    private static SingletonDemo instance = null;
    private SingletonDemo() {
        System.out.println(Thread.currentThread().getName() + ":" + "创建实例");
    }
    //DCL（Double Check Lock） 双端检索机制
    public static SingletonDemo getInstance() {
        if (instance == null) {
            // 所谓双端检索机制，就是在加锁前后我都进行了一次判断
            synchronized (SingletonDemo.class) {
                if (instance == null) {
                    instance = new SingletonDemo();
                }
            }
        }
        return instance;
    }
    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
                }
            },"线程" + i).start();
        }
    }
}
```

就是在加锁的前后，我都进行一次判断。这样你在运行的时候，就发现在多线程环境下，构造器也只会被调用一次，对象只会创建一次。但是这种方式就完全解决线程安全问题了吗？

答案是，否。上面的写法，可能在 99.9% 的情况下都是没有问题的。但是在底层可能会出现指令重排的情况，因此上面的写法还是有 0.1% 的概率会出现异常的。

**说明：**

> DCL（双端检索机制）不一定线程安全，原因是有指令重排序的存在，加入 volatile 可以禁止指令重排
>
> 原因在于某一个线程执行到第一次检测，读取到 instance 不为 null 时，instance 的**引用对象可能没有完成初始化**
>
> ```
> 举个例子：
> 公司现在有一个工位，这个工位现在是空的，根本没有人坐(这种情况就对应 instance==null),还有一种情况就是这个工位现在是空的，但是已经有人坐了，只是这个人现在还没有来，工位上也没有给它放插线板，网线等(这种情况就对应 instance != null，但是这块内存空间已经分配出去了，只是对象还没完成初始化)
> ```
>
> `instance = new SingletonDemo()` 可以分为以下 3 步完成：
>
> ```
> memory=allocate() // 1.分配对象内存空间
> instance(memory)  // 2.初始化对象
> instance=memory   // 3.设置 instance 指向刚分配的内存地址，此时 instance != null
> ```
>
> 但是步骤2 和步骤3 **不存在数据依赖关系** ，因此可能会存在指令重排的情况。而且无论是重排前还是重排后程序的执行结果在单线程中并没有改变，因此这种重排优化是允许的。
>
> ```
> memory=allocate() // 1.分配对象内存空间
> instance=memory   // 3.设置 instance 指向刚分配的内存地址，此时 instance != null ，但是对象还没初始化完成
> instance(memory)  // 2.初始化对象
> ```
>
> **所以当一条线程访问 instance 不为 null 时，由于 instance 实例未必已经初始化完成，也就造成了线程安全问题**

所以单例模式在多线程环境下，最终的写法：

**双端检索机制 + volatile**

```java
public class SingletonDemo {
	
    // 加上 volatile 关键字，禁止指令重排
    private static volatile SingletonDemo instance = null;
    private SingletonDemo() {
        System.out.println(Thread.currentThread().getName() + ":" + "创建实例");
    }
    //DCL（Double Check Lock） 双端检索机制
    public static SingletonDemo getInstance() {
        if (instance == null) {
            // 所谓双端检索机制，就是在加锁前后我都进行了一次判断
            synchronized (SingletonDemo.class) {
                if (instance == null) {
                    instance = new SingletonDemo();
                }
            }
        }
        return instance;
    }
    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
                }
            },"线程" + i).start();
        }
    }
}
```

#### 6、CAS 是什么

比较并交换（Compare And Swap）

```java
public class CASDemo {
    public static void main(String[] args) {
        // 初始值设置成 5
        AtomicInteger atomicInteger = new AtomicInteger(5);
        // 中间省略 main 线程做的一系列操作...

        /**
         * 第一个参数：expect：期望值
         * 第二个参数：update：更新值
         * 如果主内存中的值是 5 我才把它改成 100
         */
        boolean flag = atomicInteger.compareAndSet(5, 100);
        System.out.println(flag);  // true
        System.out.println(atomicInteger.get()); // 100


        boolean flag2 = atomicInteger.compareAndSet(5, 200);
        System.out.println(flag2);  // false
        System.out.println(atomicInteger.get());  // 100

        // 总结一下就是线程的期望值和物理内存的真实值一样，就修改为更新值
        // 如果线程的期望值和物理内存的真实值不一样，则不修改
    }
}
```

> - 真实值和期望值相同，修改成功
> - 真实值和期望值不同，修改失败








































































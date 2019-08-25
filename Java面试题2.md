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

#### 7、CAS 底层原理

```java
atomicInteger.getAndIncrement();   // 这行代码底层调用的是 Unsafe 类

//------------------------------
public final int getAndIncrement() {    // 这行代码解决了 i++ 在多线程下的线程安全问题
    	/**
    	  this: 表示当前对象
    	  valueOffset:表示该变量值在内存偏移地址，因为 Unsafe 类就是根据内存偏移地址获取数据的
    	  1：固定写死，每调用一次这个方法就递增 1
    	*/
        return unsafe.getAndAddInt(this, valueOffset, 1);  // 这行代码你再点进去，发现到了 Unsafe 类下面
}
```

**Unsafe 类是什么？**

> ① 这个类位于 jdk   rt.jar\sun\misc   下面有个 Unsafe.class
>
> 是 jdk 自带的一个类 
>
> ② Unsafe 是 CAS 的核心类，由于 Java 方法无法直接访问底层操作系统，需要通过本地（native）方法来访问，Unsafe 相当于一个后门，基于该类可以直接操作特定内存的数据。Unsafe 类存在于 sun.misc 包中，其内部方法操作可以像 C 的指针一样直接操作内存，因为 JAVA 中 CAS 操作的执行依赖于 Unsafe 类的方法。

**注：**

> **Unsafe 类中的所有方法都是 native 修饰的，也就是说 Unsafe 类中的方法都直接调用操作系统底层资源执行相应任务。**

**CAS 解释：**

> CAS 的全称是 Compare-And-Swap ，**它是一条 CPU 并发原语**。它的功能是判断内存某个位置的值是否为预期值，如果是则更改为新的值，整个过程是原子的。
>
> CAS 并发原语体现在 JAVA 语言中就是 sun.misc.Unsafe 类中的各个方法。调用 Unsafe 类中的 CAS 方法，JVM 会帮我们实现出 CAS 汇编指令。这是一种完全依赖于硬件的功能，通过它实现了原子操作。再次说明，由于 CAS 是一种系统原语，原语属于操作系统用语范畴，是由若干条指令组成的，用于完成某一个功能的一个过程，**并且原语的执行必须是连续的，在执行过程中不允许被中断，也就是说 CAS 是一条 CPU 的原子指令，不会造成所谓的数据不一致问题**。（操作系统底层指令，在执行过程不允许被打断，只有等他执行完了，别人才有机会去执行，因此可以保证原子性）

**源码说明：**

```java
public final int getAndIncrement() {
     return unsafe.getAndAddInt(this, valueOffset, 1);
}
public final int getAndAddInt(Object var1, long var2, int var4) {
    int var5;
    do {
        var5 = this.getIntVolatile(var1, var2)
    } while(!this.compareAndSwapInt(var1,var2,var5,var5 + var4));
    return var5;
}

/**
	var1：AtomicInteger 对象本身
	var2: 该对象值的引用地址
	var4: 需要变动的数量
	var5: 是用 var1 var2 找出主内存中真实的值
	用该对象当前的值与 var5 比较：如果相同，更新 var5 + var4 并且返回 true
	如果不相同，继续取值然后再比较，直到更新完成
*/
```

> 假设线程A 和线程B 两个线程同时执行 getAndAddInt 操作：
>
> 1、AtomicInteger 里面的 value 原始值为 3，即主内存中 AtomicInteger 的 value 为 3，根据 JMM 模型，线程A 和线程B 各自持有一份值为 3 的 value 的副本分别到各自的工作内存。
>
> 2、线程A 通过 getIntVolatile(var1,var2) 拿到 value 值为 3，这时线程A 被挂起。
>
> 3、线程B 也通过 getIntVolatile(var1,var2) 方法获取到 value 值为 3，此时刚好线程B 没有被挂起并执行 compareAndSwapInt 方法，比较内存值也是 3，成功修改内存值为 4，线程B 打完收工。
>
> 4、这时线程A 恢复，执行 compareAndSwapInt 方法比较，发现自己手里的值，数字 3 和主物理内存的值数字 4   不一样，说明该值已经被其它线程抢先一步修改了，那么 A 线程本次修改失败，**只能重新读取重新再来一遍了**。
>
> 5、线程A 重新获取 value 值，因为变量 value 被 volatile 修饰，所以其它线程对他修改，线程A 总是能看到，线程A 继续执行 compareAndSwapInt 进行比较替换，直到成功。

**CAS 简单小总结：**

> CAS（CompareAndSwap）
>
> 比较当前工作内存中的值和主内存中的值，如果相同则执行规定操作，否则继续比较直到主内存和工作内存中的值一致为止
>
> CAS 应用
>
> CAS 有三个操作数，内存值V ，旧的预期值A ，要修改的更新值B，当且仅当预期值A 和内存值V 相同时，将内存值V 修改为 B，否则什么都不做

#### 8、CAS 缺点

> - 循环时间长开销很大
>
>   - ```java
>     // 我们可以看到 getAndAddInt 方法执行时，有个 do while
>     public final int getAndAddInt(Object var1, long var2, int var4) {
>         int var5;
>         do {
>             var5 = this.getIntVolatile(var1, var2)
>         } while(!this.compareAndSwapInt(var1,var2,var5,var5 + var4));
>         return var5;
>     }
>     // 如果 cas 失败，会一直进行尝试。如果 cas 长时间一直不成功，可能会给 cpu 带来很大的开销(一直在循环，一直在循环)
>     ```
>
> - 只能保证一个共享变量的原子操作
>
>   - 当对一个共享变量执行操作时，我们可以使用循环 CAS 的方式来保证原子操作，但是对于多个共享变量操作时，循环 CAS 就无法保证操作的原子性，这时候就可以用锁来保证原子性。
>
> - ABA 问题

#### 9、ABA 问题

CAS 最大的缺点就是 ABA 问题。

> CAS 算法实现的一个重要前提就是需要取出内存中某时刻的数据并在当下时刻比较并替换，那么在这个时间差里会导致数据的变化。
>
> 比如说一个线程 one 从内存位置V 中取出 A，这时候另一个线程 two 也从内存中取出 A，并且线程 two 进行了一些操作将值变成了 B，然后线程two 又将 V 位置的数据变成 A，这时候线程 one 进行 CAS 操作发现内存中仍然是 A，然后线程 one 操作成功。
>
> **尽管线程 one 的 CAS 操作成功，但是不代表这个过程就是没有问题的。**

#### 10、AtomicReference 原子引用

```java
class User {
    private String userName;
    private int age;
	// getter  setter....
	// toString().....
}
public class AtomicReferenceDemo {
    public static void main(String[] args) {

        User user1 = new User("shp",12);
        User user2 = new User("heping",22);

        AtomicReference<User> atomicReference = new AtomicReference<User>();
        // 我把 user1 设置到这个原子引用里面，按照 JMM 内存模型，现在主存中的共享变量就是这个 user1 指向的内存空间
        atomicReference.set(user1);
        // 期望值是 user1，更新值是 user2，如果内存值和期望值一样，我就把内存值更新成 user2
        // 执行结果是：true	User{userName='heping', age=22}
        System.out.println(atomicReference.compareAndSet(user1,user2) + "\t" + atomicReference.get().toString());

        // 如果我再打印一遍
        // 执行结果：false	User{userName='heping', age=22}，因为此时内存值已经是 user2 了
        System.out.println(atomicReference.compareAndSet(user1,user2) + "\t" + atomicReference.get().toString());
    }
}
```

#### 11、AtomicStampedReference 版本号原子引用

**大致原理：**

```
t1线程： 100 1
t2线程： 100 1   101 2   100 3

假设主存中的值现在都是 100，版本号是 1，t2线程现在把 100 改成 101，在改的同时我让版本号加 1，此时版本号变成 2，然后我又把 101 改成 100，同时版本号加 1，此时版本号变成 3，这时候 t1 线程来修改，虽然期望值是 100 没问题，但是获取到此时的版本号，和我期望的版本号 1，不一样，说明有人修改过，那么 t1 线程就修改失败。
```



#### 12、ABA 问题解决

**ABA 问题出现：**

```java
public class ABADemo {

    private static AtomicReference<Integer> atomicReference = new AtomicReference<Integer>(100);
    public static void main(String[] args) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                // 期望值是 100，更新值是 101，如果主存中的值是 100，我就把它改成 101
                atomicReference.compareAndSet(100,101);
                // 期望值是 101，更新值是 100，如果主存中的值是 101，我就把它改成 100
                atomicReference.compareAndSet(101,100);
            }
        },"t1").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    // 先让它睡 1 秒钟，目的就是让 t1 线程先执行，保证 t1 线程完成一次 ABA 操作
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                // 期望值是 100，更新值是 200，如果主存中的值是 100，那么我就把它改成 200
                System.out.println(atomicReference.compareAndSet(100,200) + "\t" + atomicReference.get());
                // 结果是 true	200
            }
        },"t2").start();
        // 以上就产生了 ABA 问题，t1线程先把主存值从 100 改成 101，然后又从 101 改成 100，操作完成后 t2 线程开始执行
        // 拿到的主存值是 100 和期望值一样，因此更新成功，但是这中间其实数据有过变动
    }
}
```

**ABA 问题解决：**

```java
public class ABADemo {
    // 包含一个版本号的原子引用
    private static AtomicStampedReference<Integer> atomicStampedReference = new AtomicStampedReference<Integer>(100,1);   // 1 表示设定当前版本号是 1

    public static void main(String[] args) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("===================ABA问题解决=================");
                int stamp = atomicStampedReference.getStamp();  // 获取当前版本号
                System.out.println(Thread.currentThread().getName() + "第一次版本号：" + stamp);
                try {
                    // 这里停 1 秒是为了让下面的 t4 线程拿到和 t3 线程一样的版本号
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                /**
                 * 四个参数：
                 * expectedReference ：期望值
                 * newReference ：更新值
                 * expectedStamp ：期望的版本号
                 * newStamp ：更新的版本号
                 */
                atomicStampedReference.compareAndSet(100,101,atomicStampedReference.getStamp(),atomicStampedReference.getStamp() + 1);
                System.out.println(Thread.currentThread().getName() + "第二次版本号：" + atomicStampedReference.getStamp());

                atomicStampedReference.compareAndSet(101,100,atomicStampedReference.getStamp(),atomicStampedReference.getStamp() + 1);
                System.out.println(Thread.currentThread().getName() + "第三次版本号：" + atomicStampedReference.getStamp());
            }
        },"t3").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                int stamp = atomicStampedReference.getStamp();
                System.out.println(Thread.currentThread().getName() + "第一次版本号：" + stamp);
                try {
                    // 这里停 3 秒是为了让 t3 线程完成一次 ABA 操作
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                boolean result = atomicStampedReference.compareAndSet(100, 2019, stamp, stamp + 1);
                System.out.println(Thread.currentThread().getName() + "修改成不成功：" + result);
                System.out.println("当前实际最新版本号：" + atomicStampedReference.getStamp());
                System.out.println("当前实际最新值：" + atomicStampedReference.getReference());
            }
        },"t4").start();

        // 最终打印结果
        /**
         *   t3第一次版本号：1
             t4第一次版本号：1
             t3第二次版本号：2
             t3第三次版本号：3
             t4修改成不成功：false
             当前实际最新版本号：3
             当前实际最新值：100
         */
        
        // t3线程已经做了一次 ABA 的操作， t4 线程在执行的时候除了判断期望值和内存值一不一样以外，还判断当前版本号和期望版本号一不一样，如果都一样，才修改成功，有一个不一样就修改失败
    }
}
```

### 二、集合类之线程不安全

#### 1、ArrayList 并发修改异常

```java
public class ContainerNotSafeDemo {
    /**
     * ArrayList 是线程不安全的，线程不安全在哪？
     * 因为它的 add 方法，没有加锁
     */
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>();
        // 构建一个线程安全的 ArrayList,后面多线程对这个 safeList 进行修改时，就不会存在线程安全问题
        List<String> safeList = Collections.synchronizedList(new ArrayList<String>());
        for (int i = 0; i < 300; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    list.add("a");
                    System.out.println(list);
                }
            }).start();
        }
        // 如果是多线程并发对 ArrayList 进行修改时，就容易发生  java.util.ConcurrentModificationException
    }
}
```

#### 2、CopyOnWriteArrayList 写时复制

ArrayList 发生并发修改异常的原因就是，集合正在被一个线程写的时候，另一个线程又多来读。或者集合正在被一个线程读的时候，另一个线程又过来写。这样就容易发生并发修改异常，即：`java.util.ConcurrentModificationException`



```java
public class ContainerNotSafeDemo {
    public static void main(String[] args) {
        // 使用 CopyOnWriteArrayList 防止线程安全问题
        List<String> list = new CopyOnWriteArrayList<String>();
        for (int i = 0; i < 300; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    list.add("a");
                    System.out.println(list);
                }
            }).start();
        }
    }
}
```

下面是 CopyOnWriteArrayList 的 add 方法：

```java
public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();    // 一个线程进来之后先加锁
        try {
            Object[] elements = getArray();    // 获取到这个数组对象
            int len = elements.length;        // 获取到数组的长度
            Object[] newElements = Arrays.copyOf(elements, len + 1);  // 在原有数组的基础上 copy 一份，数组长度在原来基础上加 1，因为我要往数组里面添加一个元素  add(E e)  添加 e 这个元素   
            newElements[len] = e;  // 把通过 add(E e) e 这个元素加到新数组的末尾
            setArray(newElements);   // 原有数组的引用断掉，指向新的数组
            return true;    // 操作完成返回 true
        } finally {
            lock.unlock();    // 最后释放锁
        }
    }
```

**写时复制：**

> CopyOnWrite 容器，即写时复制容器。往一个容器添加元素的时候，不直接往当前容器 `Object[]` 添加，而是先将当前容器 `Object[]` 进行 copy ，复制出一个新的容器 `Object[] newElements` ，然后往新的容器 `Object[] newElements` 里添加元素，添加完元素之后，再将原容器的引用指向新的容器 `setArray(newElements)` 这样做的好处是可以对 CopyOnWrite 容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素。所以 CopyOnWrite 容器也是一种读写分离的思想，读和写不同的容器。

**总结一下关于 ArrayList 线程不安全的解决方案：**

> - 将 `new ArrayList()`  换成 `new Vector()`   Vector 是线程安全类
> -  使用  `Collections.synchronizedList(new ArrayList<>())`
> - 使用 `new CopyOnWriteArrayList()`

#### 3、CopyOnWriteArraySet 

```java
public class ContainerNotSafeDemo {
    public static void main(String[] args) {
        // 如果直接用 HashSet 会有线程安全问题
//        Set set = new HashSet();
        Set<String> set = new CopyOnWriteArraySet<String>();
        for (int i = 0; i < 300; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    set.add("a");
                    System.out.println(set);
                }
            }).start();
        }
    }
}
```

#### 4、ConcurrentHashMap

```java
public class ContainerNotSafeDemo {
    public static void main(String[] args) {
        // 如果直接用 HashSet 会有线程安全问题
//        Map<String,String> map = new HashMap<String,String>();
        Map<String,String> map = new ConcurrentHashMap<String,String>();
        for (int i = 0; i < 300; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    map.put(Thread.currentThread().getName(),"a");
                    System.out.println(map);
                }
            }).start();
        }
    }
}
```

### 三、Java 锁

#### 1、公平锁和非公平锁

> 公平锁
>
> - 是指多个线程按照申请锁的顺序来获取锁，类似排队打饭，先来后到
>
> 非公平锁
>
> - 是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁。在高并发的情况下，有可能会造成优先级翻转或饥饿现象

**注：**

> 并发包中 ReentrantLock 的创建可以指定构造函数的 boolean 类型来得到公平锁和非公平锁，默认是非公平锁



**两者的区别：**

> 公平锁，就是很公平，在并发环境中，每个线程在获取锁时会先查看此锁维护的等待队列，如果为空，或者当前线程是等待队列的第一个，就占有锁，否则就会加入到等待队列中，以后会按照先进先出的规则从队列中取到自己。
>
> 非公平锁：非公平锁比较粗鲁，上来就直接尝试占有锁，如果尝试失败，就再采用类似公平锁的那种方式。

#### 2、可重入锁和递归锁

**可重入锁（也叫递归锁）：**

> 指的是同一线程外层函数获得锁之后，内层递归函数仍然能够获取该锁的代码，同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。
>
> 也就是说，线程可以进入任何一个它已经拥有的锁所同步着的代码块

**注：**

> `ReentrantLock/Synchronized` 就是一个典型的可重入锁

```java
public void synchronized method1() {
    method2();   // 一个线程进来调用 method1 ，method1 是同步方法，那么这个线程获取锁，在 method1 中调用 method2，而 method2 也是同步方法，那么这个线程调用 method2 自动获取锁，是同一把锁
}
public void synchronized method2() {
    
}
```

**可重入锁最大的作用就是避免死锁**

Synchronized 可重入锁案例：

```java
class Phone {
    public synchronized void sendMsg() {
        System.out.println(Thread.currentThread().getName()+ "\t invoke sendMsg");
        sendEmail();  // 比如说 t1 线程在外层方法获取到锁之后，在进入内层方法 sendEmail 会自动获取锁
    }
    public synchronized void sendEmail() {
        System.out.println(Thread.currentThread().getName() + "\t invoke sendEmail");
    }
}
public class ReenterLockDemo {
    public static void main(String[] args) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                Phone phone = new Phone();
                phone.sendMsg();
            }
        },"t1").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                Phone phone = new Phone();
                phone.sendMsg();
            }
        },"t2").start();
    }
    
//打印结果：
//t1	 invoke sendMsg
//t2	 invoke sendMsg
//t2	 invoke sendEmail
//t1	 invoke sendEmail
```

ReentrantLock 可重入锁案例：

```java
class Phone implements Runnable {
    @Override
    public void run() {
        get();
    }
    Lock lock = new ReentrantLock();
    public void get() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "invoke get");
            set();  // 线程在外层方法获取锁之后，在内层方法自动获取锁
        } finally {
            lock.unlock();
        }
    }
    public void set() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "invoke set");
        } finally {
            lock.unlock();
        }
    }

}
public class ReenterLockDemo {
    public static void main(String[] args) {
        Phone phone = new Phone();
        Thread t1 = new Thread(phone,"t1");
        Thread t2 = new Thread(phone, "t2");
        t1.start();
        t2.start();
    }
}

// 打印结果
// t1invoke get
// t1invoke set
// t2invoke get
// t2invoke set
```

#### 3、自旋锁

是指尝试获取锁的线程不会立即阻塞，而是采用循环的方式去尝试获取锁。这样的好处是减少线程上下文切换的消耗，缺点是循环会消耗 CPU。

```java
/**
 * 实现一个自旋锁
 * 自旋锁的好处：循环比较获取直到成功为止，没有类似 wait 的阻塞
 *
 * 通过 CAS 操作完成自旋锁，A 线程先进来调用 myLock 方法自己持有锁 5 秒钟,
 * B 随后进来后发现当前有线程持有锁，不是 null ，所以只能通过自旋等待，直到 A 释放锁后 B 随后抢到
 */
public class SpinLockDemo {
    // 原子引用线程，现在主存中共享变量是 Null ，因为我在 new 的时候没有给任何初始值
    AtomicReference<Thread> atomicReference = new AtomicReference<Thread>();

    public void myLock() {
        Thread thread = Thread.currentThread();
        System.out.println(thread.getName() + "线程 come in");
        while (!atomicReference.compareAndSet(null,thread)) {

        }
    }

    public void myUnLock() {
        Thread thread = Thread.currentThread();
        atomicReference.compareAndSet(thread,null);
        System.out.println(thread.getName() + "invoke myUnlock");
    }
    public static void main(String[] args) {
        SpinLockDemo lockDemo = new SpinLockDemo();
        new Thread(new Runnable() {
            @Override
            public void run() {
                lockDemo.myLock();
                try {
                    // 暂停 5 秒钟
                    Thread.sleep(5000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                lockDemo.myUnLock();

            }
        },"AA").start();

        try {
            // 睡 1 秒钟，保证让线程1 先启动
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        new Thread(new Runnable() {
            @Override
            public void run() {
                lockDemo.myLock();
                lockDemo.myUnLock();
            }
        },"BB").start();
    }
}
```

解释一下上面这段代码：

> 上面这段代码是通过 CAS 完成自旋锁，myLock 方法的作用是期望主存中的值为 null ，如果主存中的值为 null ，就把它更新为当前 thread。myUnLock 方法的作用是期望主存中的值为当前 thread，如果主存中的值为当前 thread 对象，就把它更新为 null。
>
> 在 main 方法中线程AA 首先进来，调用 myLock 方法，打印 AA线程 come in，1 秒钟之后 BB线程启动，调用 myLock 方法，因为 AA 先调用并且把主存中的值变成了当前 thread 对象，BB 线程再调用，期望值是 null ，但主存中的值不是 null ，所以 BB 线程会一只在 while 循环中循环，也就是自旋，等 AA 线程 5 秒钟时间到了，调用 myUnlock 方法，将主存中的值变成 null ，此时 BB 线程在循环判断时，满足条件，自旋结束。紧接着调用 myUnlock 方法，完成整个线程。
>
> 通过上面也能看出自旋锁的好处，就是一直在循环比较，不会有阻塞，但是如果循环条件一直得不到满足，也会造成 CPU 压力增大。

#### 4、读写锁

**独占锁：**

> 指该锁一次只能被一个线程所持有。对 `ReentrantLock` 和 `Synchronized` 而言都是独占锁
>
> 写锁是读占锁。

**共享锁：**

> 指该锁可以被多个线程所持有。
>
> 对 `ReentrantReadWriteLock` 其读锁是共享锁，其写锁是独占锁
>
> 读锁的共享锁可保证并发读是非常高效的。读写、写读、写写的过程是互斥的。
>
> 读锁是共享锁。

```java
/**
 * 多个线程同时读一个资源类没有任何问题，所以为了满足并发量，读取共享资源应该是可以同时进行的
 * 但是如果一个线程想去写共享资源，就不应该再有其他线程可以对该资源进行读和写
 * 总结：
 * 读-读能共存
 * 读-写不能共存
 * 写-写不能共存
 *
 * 写操作：原子 + 独占，整个过程必须是一个完整的统一体，中间不许被分割，被打断
 */
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache cache = new MyCache();
        for (int i = 0; i < 5; i++) {
            final int j = i;
            new Thread(new Runnable() {
                @Override
                public void run() {
                    cache.put(String.valueOf(j),String.valueOf(j));

                }
            },String.valueOf(i)).start();

            new Thread(new Runnable() {
                @Override
                public void run() {
                    cache.get(String.valueOf(j));
                }
            },String.valueOf(i)).start();
        }

    }
}

class MyCache {
    private volatile Map<String,Object> map = new HashMap<String,Object>();

    public void put(String key, Object value) {
        System.out.println(Thread.currentThread().getName() + "正在写入：" + key);
        try {
            // 睡 300 毫秒，模拟网络拥堵情况
            Thread.sleep(300);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        map.put(key,value);
        System.out.println(Thread.currentThread().getName() + "写入完成：" + key);
    }

    public void get(String key) {
        System.out.println(Thread.currentThread().getName() + "正在读取");
        Object result = map.get(key);
        System.out.println(Thread.currentThread().getName() + "读取结果：" + result);
    }
}
/*
执行结果：
0正在写入：0               0 号线程还未写入完成就被别的线程打断了
0正在读取
0读取结果：null
1正在写入：1
1正在读取
1读取结果：null
2正在写入：2
2正在读取
2读取结果：null
3正在写入：3
3正在读取
3读取结果：null
4正在写入：4
4正在读取
4读取结果：null
0写入完成：0          到这里 0 号线程才写入完成，中间被别的线程打断
2写入完成：2
3写入完成：3
4写入完成：4
1写入完成：1
*/

// 上面的代码可能出现的问题就是，我一个线程在执行写操作的时候，另一个线程抢到执行权，进行读操作，写操作还没执行完就被打断。=====我们要求写操作必须是原子 + 读占 中途不允许被打断=====所以这样是有问题的。
```

**下面使用读写锁来解决：**

```java
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache cache = new MyCache();

        for (int i = 0; i < 5; i++) {
            final int j = i;
            new Thread(new Runnable() {
                @Override
                public void run() {
                    cache.put(String.valueOf(j),String.valueOf(j));

                }
            },String.valueOf(i)).start();

            new Thread(new Runnable() {
                @Override
                public void run() {
                    cache.get(String.valueOf(j));
                }
            },String.valueOf(i)).start();
        }

    }
}

class MyCache {
    private volatile Map<String,Object> map = new HashMap<String,Object>();
    private ReentrantReadWriteLock lock = new ReentrantReadWriteLock();

    public void put(String key, Object value) {
        lock.writeLock().lock(); // 上锁
        try {
            System.out.println(Thread.currentThread().getName() + "正在写入：" + key);
            try {
                // 睡 300 毫秒，模拟网络拥堵情况
                Thread.sleep(300);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            map.put(key,value);
            System.out.println(Thread.currentThread().getName() + "写入完成：" + key);
        } finally {
            lock.writeLock().unlock();  // 释放锁
        }

    }

    public void get(String key) {
        lock.readLock().lock();   // 上锁
        try {
            System.out.println(Thread.currentThread().getName() + "正在读取");
            Object result = map.get(key);
            System.out.println(Thread.currentThread().getName() + "读取结果：" + result);
        } finally {
            lock.readLock().unlock();   // 释放锁
        }

    }
}
```








































































































































































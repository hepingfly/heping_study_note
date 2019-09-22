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

![volatile不保证原子性](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/volatile%E4%B8%8D%E4%BF%9D%E8%AF%81%E5%8E%9F%E5%AD%90%E6%80%A7.png)



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

#### 5、闭锁

> - 让一些线程阻塞直到另一些线程完成一系列操作后才被唤醒
> - CountDownLatch 主要有两个方法，当一个或多个线程调用 await 方法时，调用线程会被阻塞。其他线程调用 countDown 方法会将计数器减 1（调用 countDown 方法的线程不会阻塞），当计数器的值变为 0 时，因调用 await 方法被阻塞的线程会被唤醒，继续执行

**枚举类：**

```java
/**
 * 定义一个枚举类
 */
public enum CountryEnum {
    ONE(1,"齐"),TWO(2,"楚"),THREE(3,"燕"),FOUR(4,"韩"),FIVE(5,"赵"),SIX(6,"魏");

    private int countryId;
    private String countryName;


    private CountryEnum(int countryId, String countryName) {
        this.countryId = countryId;
        this.countryName = countryName;
    }
    // 循环遍历枚举类
    public static CountryEnum loopCountryEnum(int countryId) {
        CountryEnum[] array = CountryEnum.values();   // 这里返回所有枚举类的数组
        for (CountryEnum countryEnum : array) {
            if (countryId == countryEnum.countryId) {
                return countryEnum;
            }
        }
        return null;
    }

    public int getCountryId() {
        return countryId;
    }

    public String getCountryName() {
        return countryName;
    }
}
```

**闭锁案例代码**

```java
/**
 * CountDownLatch 闭锁
 */
public class CountDownLatchDemo {
    public static final int COUNT = 6;
    public static void main(String[] args) {
//        method1();
        method2();
    }

    public static void method2() {
        CountDownLatch latch = new CountDownLatch(6);
        for (int i = 1; i <= 6; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName() + "国被灭");
                    latch.countDown();
                }
                /**
                 * 这里我想要根据 i 值的不同，判断是哪个国家，一种方式就是写 if else
                 * 但是写 if else 会显得代码非常臃肿，这里的 i 只有 6 种情况，如果 i 非常多
                 * if else 写起来就会很不优雅，这里通过枚举解决
                 * 根据传入 i 值的不同，得到不同的国家名字
                 */
            },CountryEnum.loopCountryEnum(i).getCountryName()).start();
        }
        try {
            latch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "秦一统中国");

    }


    public static void method1() {
        CountDownLatch latch = new CountDownLatch(6);
        /**
         * 假设我用一个线程代表一个人，那么我希望等所有人都离开教室，然后主线程执行，人都走完，锁门
         * 也就是等 6 个线程执行完之后，我主线程才执行
         */
        for (int i = 1; i <= 6; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    System.out.println("上完自习，离开教室");
                    latch.countDown();  // 让计数器减一
                }
            }).start();
        }
        try {
            latch.await();  // 主线程进行等待，等计数器减到 0 之后，主线程才执行
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "人都走完，锁门");
    }
}
```

#### 6、CyclicBarrier（栅栏）

> `CyclicBarrier` 的字面意思是可循环（Cyclic）使用的屏障（Barrier）。它要做的事情是，让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活，线程进入屏障通过 CyclicBarrier 的 await() 方法

栅栏类似于闭锁，它能阻塞一组线程直到某个事件的发生。栅栏与闭锁的关键区别在于，所有的线程必须同时到达栅栏位置，才能继续执行。闭锁用于等待事件，而栅栏用于等待其他线程。

`CyclicBarrier` 可以使一定数量的线程反复的在栅栏位置处汇集。当线程到达栅栏位置时将调用 `await` 方法，这个方法将阻塞直到所有线程都到达栅栏位置。如果所有线程都到达栅栏位置，那么栅栏将打开，此时所有线程都将被释放，而栅栏将被重置以便下次使用。

**注：**

`可以理解成栅栏是做加法，闭锁是做减法`

```java
/**
 * CyclicBarrier（栅栏）
 */
public class CyclicBarrierDemo {
    /**
     * 这里我想做的操作就是把 7 颗龙珠都集齐，然后召唤神龙
     * 前提就是 7 颗龙珠一颗一颗集齐，然后才执行召唤神龙
     *
     * 栅栏就类似于开会，有人先到，有人后到，先到的等后到的，等所有人都到了，再进行开会
     */
    public static void main(String[] args) {
        CyclicBarrier barrier = new CyclicBarrier(7, new Runnable() {
            @Override
            public void run() {
                System.out.println("召唤神龙");
            }
        });
        for (int i = 1; i <= 7; i++) {
            final int tempI = i;
            new Thread(new Runnable() {
                @Override
                public void run() {
                    System.out.println("收集第" + tempI + "个龙珠");
                    try {
                        barrier.await();  // 线程到达屏障之后别阻塞
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }).start();
        }
    }
}
```

#### 7、Semaphore（信号量）

> 信号量主要用于两个目的，一个是用于多个共享资源的互斥使用，另一个用于并发线程数的控制。
>
> 就是多个线程去抢多个资源

```java
/**
 * Semaphore 信号量
 * 现在想模拟这样一种场景：
 * 我现在有 3 个车位，但是有 6 部车。也就意味着只有 3 辆车能抢到车位，其它 3 辆等着
 * 等一辆车离开了，剩下的车再继续抢
 *
 * 对应代码就是 6 个线程抢 3 个资源
 */
public class SemaphoreDemo {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3);  // 现在模拟有 3 个停车位
        for (int i = 1; i <= 6; i++) {  // 模拟有 6 部车
            new Thread(new Runnable() {
                @Override
                public void run() {
                    try {
                        semaphore.acquire();  // 表示一个线程抢到一个停车位，即抢到一个资源
                        System.out.println(Thread.currentThread().getName() + "抢到车位");
                        // 暂停一会线程
                        Thread.sleep(3000);  // 抢到资源的车，停 3 秒钟后离开，即释放资源
                        System.out.println(Thread.currentThread().getName() + "离开车位");
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        semaphore.release();  // 线程执行完之后，释放资源，把停车位空出来
                    }
                }
            },String.valueOf(i)).start();
        }
    }
}
```

### 四、阻塞队列

#### 1、阻塞队列接口和实现类

![阻塞队列](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/%E9%98%BB%E5%A1%9E%E9%98%9F%E5%88%97.png)

> - 当试图从空的阻塞队列中获取元素的线程将会被阻塞，直到其他线程往空的队列中插入新的元素
> - 同样试图往已满的阻塞队列中添加新元素的线程同样也会被阻塞，直到其他的线程从队列中移除一个或者多个元素或者完全清空队列后使队列重新变得空闲起来

**为什么用阻塞队列：**

> 在多线程领域：所谓阻塞，在某些情况下，会挂起线程（即阻塞），一旦条件满足，被挂起的线程又会被自动唤醒。
>
> 为什么需要 BlockingQueue？
>
> 好处是我们不需要关心什么时候需要阻塞线程，什么时候需要唤醒线程。因为这一切 BlockingQueue 全都给你包办了。在 concurrent 包发布以前，在多线程环境下，我们每个程序员都必须自己去控制这些细节，尤其还要兼顾效率和线程安全，而这会给我们的程序带来不小的复杂度。

`BlockingQueue` 是一个接口，它的父接口是 `Queue`，`Queue` 的父接口是 `Collection`。`BlockingQueue` 和 `list` 属于平级的关系

`BlockingQueue` 的实现类：

> - **ArrayBlockingQueue**：由数组结构组成的有界阻塞队列
> - **LinkedBlockingQueue**：由链表结构组成的有界（大小默认值为 `Integer.MAX_VALUE`）阻塞队列
> - **SynchronousQueue**：不存储元素的阻塞队列，也即单个元素的队列
> - PriorityBlockingQueue：支持优先级排序的无界阻塞队列
> - DelayQueue：使用优先级队列实现的延迟无界阻塞队列
> - LinkedTransferQueue：由链表结构组成的无界阻塞队列
> - LinkedBlockingDeque：由链表结构组成的双向阻塞队列

#### 2、BlockingQueue 的核心方法

| 方法类型 | 抛出异常  | 特殊值   | 阻塞   | 超时               |
| -------- | --------- | -------- | ------ | ------------------ |
| 插入     | add(e)    | offer(e) | put(e) | offer(e,time,unit) |
| 移除     | remove()  | poll     | take() | poll(time,unit)    |
| 检查     | element() | peek()   | 不可用 | 不可用             |

|          |                                                              |
| -------- | ------------------------------------------------------------ |
| 抛出异常 | 当阻塞队列满时，再往队列里 add 插入元素会抛 `IllegalStateException: Queue full`<br />当阻塞队列为空时，再往队列里 remove 移除元素会抛 `NoSuchElementException` |
| 特殊值   | 插入方法，成功 true，失败 false<br />移除方法，成功返回出队列的元素，队列里面没有就返回 null |
| 阻塞     | 当阻塞队列满时，生产者线程继续往队列里 put 元素，队列会一直阻塞生产者线程直到 take 数据或者响应中断退出。<br />当阻塞队列为空时，消费者线程试图从队列里 take 元素，队列会一直阻塞消费者线程直到队列可用 |
| 超时退出 | 当阻塞队列满时，队列会阻塞生产者线程一定时间，超过限定时间后，生产者线程会退出 |

#### 3、阻塞队列 api 之抛出异常

```java
/**
 * 抛出异常
 */
public class BlockingQueueDemo {
    public static void main(String[] args) {
//        List list = new ArrayList();  默认大小是 10
        // 有界阻塞队列，需要你指定大小
        BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<String>(3);
        System.out.println(blockingQueue.add("a"));
        System.out.println(blockingQueue.add("b"));
        System.out.println(blockingQueue.add("c"));
        // 队列大小是 3，如果你再往里面加第 4 个，就会报 IllegalStateException: Queue full
//        System.out.println(blockingQueue.add("d"));

        System.out.println(blockingQueue.remove());  // a
        System.out.println(blockingQueue.remove());  // b
        System.out.println(blockingQueue.remove());  // c
        // 当阻塞队列为空时，再移除就会报 NoSuchElementException
//        System.out.println(blockingQueue.remove());
    }
}
```

#### 4、阻塞队列之返回布尔值

```java
/**
 * 插入，移除方法
 */
public class BlockingQueueDemo {
    public static void main(String[] args) {
//        List list = new ArrayList();  默认大小是 10
        // 有界阻塞队列，需要你指定大小
        BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<String>(3);
        // 插入方法
        System.out.println(blockingQueue.offer("a"));
        System.out.println(blockingQueue.offer("b"));
        System.out.println(blockingQueue.offer("c"));
        // 使用 offer 方法进行插入，当超过队列长度时，返回 false
        System.out.println(blockingQueue.offer("d"));

        System.out.println(blockingQueue.peek());  // a  探测队列队首的元素是哪个
        // 移除方法，移除成功，返回队列中的元素
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        // 移除失败，返回 null
        System.out.println(blockingQueue.poll());
    }
}
```

#### 5、阻塞队列之阻塞和超时控制

**阻塞：**

```java
/**
 * put  take 方法
 */
public class BlockingQueueDemo {
    public static void main(String[] args) throws InterruptedException {
//        List list = new ArrayList();  默认大小是 10
        // 有界阻塞队列，需要你指定大小
        BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<String>(3);
        blockingQueue.put("a");
        blockingQueue.put("b");
        blockingQueue.put("c");
        // 当阻塞队列满了的时候,队列会阻塞生产者线程往里面 put 元素
//        blockingQueue.put("d");
        System.out.println("==========");
        blockingQueue.take();
        blockingQueue.take();
        blockingQueue.take();
        // 当阻塞队列为空时，队列会阻塞消费者线程从里面 take 元素
//        blockingQueue.take();
    }
}
```

**超时**

```java
public class BlockingQueueDemo {
    public static void main(String[] args) throws InterruptedException {
//        List list = new ArrayList();  默认大小是 10
        // 有界阻塞队列，需要你指定大小
        BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<String>(3);
        System.out.println(blockingQueue.offer("a", 2L, TimeUnit.SECONDS));
        System.out.println(blockingQueue.offer("b", 2L, TimeUnit.SECONDS));
        System.out.println(blockingQueue.offer("c", 2L, TimeUnit.SECONDS));
        // 当阻塞队列满时，使用 offer 方法往队列里面插入，超过限定时间，生产者线程会自动退出
        System.out.println(blockingQueue.offer("d", 2L, TimeUnit.SECONDS));
    }
}
```

#### 6、阻塞队列之同步队列 SynchronousQueue

> - SynchronousQueue 没有容量
> - 与其他 BlockingQueue 不同，SynchronousQueue 是一个不存储元素的 BlockingQueue
> - 每一个 put 操作必须要等待一个 take 操作，否则不能继续添加元素，反之亦然。

```java
/**
 * 同步队列
 */
public class SynchroniousQueueDemo {
    public static void main(String[] args) {
        BlockingQueue<String> queue = new SynchronousQueue<String>();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    // 我往同步队列中 put 一个元素后,除非有另一个线程把这个元素 take 了，否则，put 不进去第二个
                    System.out.println(Thread.currentThread().getName() + "放入 1");
                    queue.put("1");
                    System.out.println(Thread.currentThread().getName() + "放入 2");
                    queue.put("2");
                    System.out.println(Thread.currentThread().getName() + "放入 3");
                    queue.put("3");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"A线程").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Thread.sleep(5000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                try {
                    // 把 put 进队列的元素取走
                    String takeResult1 = queue.take();
                    System.out.println(Thread.currentThread().getName() + "从队列中取出" + takeResult1);
                    String takeResult2 = queue.take();
                    System.out.println(Thread.currentThread().getName() + "从队列中取出" + takeResult2);
                    String takeResult3 = queue.take();
                    System.out.println(Thread.currentThread().getName() + "从队列中取出" + takeResult3);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"B线程").start();
    }
}
```

### 五、线程通信

**注：**

> 只要是多线程交互，判断的时候都进来使用 while 去判断，不要使用 if 去判断

#### 1、线程通信之生产者消费者传统版

```java
package com.iflytek.java;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * 一个初始值为 0 的变量，两个线程对其交替操作，一个加 1 一个减 1，来 5 轮
 */
public class ProdConsumer_TraditionDemo {

    public static void main(String[] args) {
        ShareData shareData = new ShareData();
        for (int i = 1; i <= 5; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    try {
                        shareData.increment();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            },"A线程").start();
            new Thread(new Runnable() {
                @Override
                public void run() {
                    try {
                        shareData.decrement();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            },"B线程").start();
        }
    }
}

// 资源类
class ShareData {
    private int number = 0;
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();
    public void increment () throws InterruptedException {
        try {
            lock.lock(); // 加锁
            while (number != 0) {   // 这里要用 while 不能用 if 防止虚假唤醒
                // 等待，不能生产
                condition.await();
            }
            number++;
            System.out.println(Thread.currentThread().getName() + number);
            condition.signalAll();  // 通知唤醒
        }finally {
            lock.unlock();  //释放锁
        }
    }
    public void decrement () throws InterruptedException {
        try {
            lock.lock(); // 加锁
            while (number == 0) {   // 这里要用 while 不能用 if 防止虚假唤醒
                // 等待，不能消费
                condition.await();
            }
            number--;
            System.out.println(Thread.currentThread().getName() + number);
            condition.signalAll();  // 通知唤醒
        }finally {
            lock.unlock();  //释放锁
        }
    }
}

```

#### 2、synchronized 和 lock 有什么区别

> - 原始构成
>   - synchronized 是关键字，属于 JVM 层面
>   - Lock 是具体类（java.util.concurrent.locks.lock），是 api 层面的锁
> - 使用方法
>   - synchronized 不需要用户去手动释放锁，当 synchronized 代码执行完成之后，系统会自动让线程释放对锁的占用
>   - ReentrantLock 则需要用户去手动释放锁，若没有主动释放锁，就有可能导致出现死锁现象。因此需要 `lock()` 和 `unlock()` 方法配合 `try-finally` 语句块来完成
> - 等待是否可中断
>   - synchronized 不可中断，除非抛出异常或者正常运行完成
>   - ReentrantLock 可中断，① 可以通过设置 `tryLock(long timeout, TimeUnit unit)`  ② `lockInterruptibly()` 放代码块中，调用 `interrupt()` 方法可中断
> - 加锁是否公平
>   - synchronized  非公平锁
>   - ReentrantLock  两者都可以，默认非公平锁，构造方法可以传入 boolean 值，true 为公平锁，false 为非公平锁
> - 锁绑定多个条件 Condition
>   - synchronized 没有
>   - ReentrantLock  用来实现分组唤醒需要唤醒的线程：可以精确唤醒，而不是像 synchronized 要么随机唤醒一个线程要么唤醒全部线程

#### 3、绑定多个条件 Condition 

```java
package com.iflytek.java;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * 多线程之间按顺序调用，实现 A->B->C 三个线程启动
 * A 打印 5 次，B 打印 10 次，C 打印 15 次
 * 紧接着 A 打印 5 次，B 打印 10 次，C 打印 15 次
 * 来 10 轮
 */
public class SyncAndReenttrantLockDemo {
    public static void main(String[] args) {
        ShareResource resource = new ShareResource();
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    resource.print5();
                }

            }
        },"A 线程").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    resource.print10();
                }

            }
        },"B 线程").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    resource.print15();
                }

            }
        },"C 线程").start();

    }
}

class ShareResource {
    // 弄个标志位 1 表示 A 线程，2 表示 B 线程，3 表示 C 线程
    private int number = 1;
    private Lock lock = new ReentrantLock();
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();

    public void print5() {
        try {
            lock.lock();  // 上锁
            while (number != 1) {
                try {
                    condition1.await();  // 等待
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            for (int i = 1; i <= 5; i++) {
                System.out.println(Thread.currentThread().getName() + i);
            }
            // 通知 B 线程
            number = 2;
            condition2.signal();
        } finally {
            lock.unlock();  // 释放锁
        }
    }
    public void print10() {
        try {
            lock.lock();  // 上锁
            while (number != 2) {
                try {
                    condition2.await();  // 等待
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            for (int i = 1; i <= 10; i++) {
                System.out.println(Thread.currentThread().getName() + i);
            }
            // 通知 C 线程
            number = 3;
            condition3.signal();
        } finally {
            lock.unlock();  // 释放锁
        }
    }
    public void print15() {
        try {
            lock.lock();  // 上锁
            while (number != 3) {
                try {
                    condition3.await();  // 等待
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            for (int i = 1; i <= 15; i++) {
                System.out.println(Thread.currentThread().getName() + i);
            }
            // 通知 A 线程
            number = 1;
            condition1.signal();
            System.out.println("----------------------");
        } finally {
            lock.unlock();  // 释放锁
        }
    }
}
```

#### 4、生产者消费者阻塞队列版

```java
/**
 * 生产者消费者阻塞队列版
 */
public class ProdConsumer_BlockQueueDemo {
    public static void main(String[] args) {
        BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<String>(5);
        MyResource resource = new MyResource(blockingQueue);
        new Thread(new Runnable() {
            @Override
            public void run() {
                resource.myProduct();
            }
        },"生产者线程").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                resource.myConsumer();
            }
        },"消费者线程").start();
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("5 秒钟时间到，准备停止");
        resource.stop();


    }
}

class MyResource {
    private volatile boolean FLAG = true; // 默认开启，进行生产和消费
    private AtomicInteger atomicInteger = new AtomicInteger();

    BlockingQueue<String> blockingQueue = null;

    public MyResource(BlockingQueue<String> blockingQueue) {
        this.blockingQueue = blockingQueue;
        System.out.println(blockingQueue.getClass().getName());
    }

    public void myProduct() {
        String data = null;
        while (FLAG) {
            data = atomicInteger.incrementAndGet() + "";
            try {
                boolean result = blockingQueue.offer(data, 5L, TimeUnit.SECONDS);
                if (result) {
                    System.out.println(Thread.currentThread().getName() + "插入队列成功");
                } else {
                    System.out.println(Thread.currentThread().getName() + "插入队列失败");
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("准备让 flag = false了，停止生产");
    }

    public void myConsumer() {
        String result = null;
        while (FLAG) {
            try {
                result = blockingQueue.poll(3L,TimeUnit.SECONDS);
                if (result == null || result.equalsIgnoreCase("")) {
                    FLAG = false;
                    System.out.println("超过 2 秒钟没有可以消费的，消费退出");
                    return;
                }
                System.out.println(Thread.currentThread().getName() + "消费者取出队列成功");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public void stop() {
        this.FLAG = false;
    }
}
```

### 六、线程池

#### 1、线程池使用及优势

线程池做的工作主要是控制运行的线程的数量，处理过程中将任务放入队列，然后在线程创建后启动这些任务，如果线程数量超过了最大数量，超出数量的线程排队等候，等其他线程执行完毕，再从队列中取出任务来执行。

线程池主要特点：线程复用，控制最大并发数，管理线程

> 1、降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗
>
> 2、提高响应速度。当任务到达时，任务可以不需要等到线程创建就能立即执行
>
> 3、提高线程的可管理性。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控

#### 2、线程池 3 个常用方式

Java 中的线程池是通过 Executor 框架实现的，该框架中用到了 Executor，Executors，ExecutorService，ThreadPoolExecutor 这几个类。

**三种常用方式：**

> - `Executors.newFixedThreadPool(5)`
>
>   - 适用于执行长期的任务，性能好很多
>
>   - ```java
>     // 创建一个定长的线程池，可控制线程最大并发数，超出的线程会在队列中等待
>     public static ExecutorService newFixedThreadPool(int nThreads) {
>             return new ThreadPoolExecutor(nThreads, nThreads,
>                                           0L, TimeUnit.MILLISECONDS,
>                                           new LinkedBlockingQueue<Runnable>());
>         }
>     ```
>
>   - 创建一个**定长的线程池**，可控制线程最大并发数，超出的线程会在队列中等待
>
>   - `newFixedThreadPool` 创建的线程池 `corePoolSize` 和 `maximumPoolSize` 值是相等的，它使用 `LinkedBlockingQueue`
>
> - `Executors.newSingleThreadExecutor()`
>
>   - 适用于一个任务一个任务执行的场景
>
>   - ```java
>     // 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序执行
>     public static ExecutorService newSingleThreadExecutor() {
>             return new FinalizableDelegatedExecutorService
>                 (new ThreadPoolExecutor(1, 1,
>                                         0L, TimeUnit.MILLISECONDS,
>                                         new LinkedBlockingQueue<Runnable>()));
>         }
>     ```
>
>   - 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序执行
>
>   - `newSingleThreadExecutor` 创建的线程池 将 `corePoolSize` 和 `maximumPoolSize` 都设置为 1，它使用 `LinkedBlockingQueue`
>
> - `Executors.newCachedThreadPool()`
>
>   - 适用于执行很多短期异步的小程序或者负载较轻的服务器
>
>   - ```java
>     public static ExecutorService newCachedThreadPool() {
>             return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
>                                           60L, TimeUnit.SECONDS,
>                                           new SynchronousQueue<Runnable>());
>         }
>     ```
>
>   - 创建一个**可缓存线程池**，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程
>
>   - `newCachedThreadPool` 创建的线程池 将 `corePoolSize` 设置为 0， 将 `maximumPoolSize` 都设置为 `Integer.MAX_VALUE` ，使用的是 `SynchronousQueue` ，也就是说来了任务就创建线程运行，当线程空闲超过 60 秒，就销毁线程。

```java
package com.iflytek.java;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * Created by shenheping on 2019/9/8.
 */
public class MyThreadPoolDemo {
    public static void main(String[] args) {
        // 一个线程池里面有 5 个处理线程
//        ExecutorService threadPool = Executors.newFixedThreadPool(5);

        // 一个线程池里面有 1 个处理线程
//        ExecutorService threadPool = Executors.newSingleThreadExecutor();

        // 一个线程池里面有 N 个处理线程
        ExecutorService threadPool = Executors.newCachedThreadPool();
        // 模拟 10 个用户来办理业务，每个用户就是一个来自外部的请求线程
        try {
            for (int i = 1; i <= 10; i++) {
                // 10 个用户的请求都由这一个线程池来处理，因为线程池里面有 5 个处理线程
                threadPool.execute(new Runnable() {
                    @Override
                    public void run() {
                        System.out.println(Thread.currentThread().getName() + " 办理业务");
                    }
                });
            }

        } catch (Exception e) {

        } finally {
            threadPool.shutdown();
        }
    }
}
```

#### 3、线程池七大参数

```java
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) {
    }
```

> - corePoolSize ：线程池中的常驻核心线程数
>   - 在创建线程池后，当有请求任务来了之后，就会安排池中的线程去执行请求任务，近似理解为今日当值线程
>   - 当线程池中的线程数目达到 corePoolSize 之后，就会把到达的任务放到缓存队列当中
> - maximumPoolSize
>   - 线程池能够容纳同时执行的最大线程数，此值必须大于等于1
> - keepAliveTime
>   - 多余的空闲线程的存活时间。当前线程池线程数量超过 corePoolSize 时，当空闲时间达到 keepAliveTime 时，多余空闲线程会被销毁直到只剩下 corePoolSize 个线程为止
>   - 默认情况下，只有当线程池中的线程数大于 corePoolSize 时，keepAliveTime 才会起作用，直到线程池中的线程数不大于 corePoolSize
> - unit
>   - keepAliveTime 的单位
> - workQueue
>   - 任务队列，被提交但尚未被执行的任务
> - threadFactory
>   - 表示生成线程池中工作线程的线程工厂，用于创建线程，一般用默认的即可
> - handler
>   - 拒绝策略，表示当队列满了并且工作线程大于等于线程池的最大线程数（maximumPoolSize）时如何来拒绝请求执行的 runnable 的策略

**总结一下：**

一个线程池你可以把它理解成一个银行网点，corePoolSize 你可以把它理解成初始化银行几个窗口，maximumPoolSize 可以理解成这个银行网点最多可以开多少个窗口，keepAliveTime 你可以理解成，我初始化两个窗口，但是由于人太多了，所以又开了三个窗口，corePoolSize 是 2，后来随着业务量的下降，后面三个窗口就不需要了，所以等着三个窗口空闲达到一定时间后，就会被销毁。workQueue 可以理解成，线程池中的线程都在执行任务，没有空闲线程，这时候再来请求，就在阻塞队列中等待。handler 可以理解成，工作线程已经达到了线程池最大线程数，这时候来请求就放阻塞队列里，然后阻塞队列也已经满了，这时候就启动拒绝策略

#### 4、线程池的工作原理

> 1、在创建线程池后，等待提交过来的任务请求
>
> 2、当调用 execute() 方法添加一个任务请求时，线程池会做如下判断：
>
> 1）、如果正在运行的线程数量小于 corePoolSize ，那么马上创建线程运行这个任务
>
> 2）、如果正在运行的线程数量大于或等于 corePoolSize ，那么将这个任务放入阻塞队列
>
> 3）、如果这时候阻塞队列满了且正在运行的线程数量还小于 maximumPoolSize ，那么还是要创建非核心线程立刻运行这个任务
>
> 4）、如果队列满了且正在运行的线程数量大于或等于 maximumPoolSize，那么线程池会启动饱和拒绝策略来执行
>
> 3、当一个线程完成任务时，它会从阻塞队列中取下一个任务来执行
>
> 4、当一个线程无事可做超过一定的时间（keepAliveTime）时，线程池会判断：如果当前运行的线程数大于 corePoolSize，那么这个线程就会被停掉。所以线程池的所有任务完成后它最终会收缩到 corePoolSize 的大小。

#### 5、线程池拒绝策略

**拒绝策略是什么：**

> 等待队列已经满了，塞不下新任务了，同时线程池中的 max 线程也达到了，无法继续为新任务服务。这时候我们就需要拒绝策略机制合理的处理这个问题

**JDK 内置的拒绝策略：**

> - AbortPolicy（默认）：直接抛出 `RejectedExecutionException` 异常，阻止系统正常运行
> - CallerRunsPolicy ："调用者运行" 一种调节机制，该策略既不会抛弃任务，也不会抛出异常，而是将某些任务回退到调用者，从而降低新任务的流量
> - DiscardOldestPolicy ：抛弃队列中等待最久的任务，然后把当前任务加入队列中尝试再次提交当前任务
> -  DiscardPolicy ：直接丢弃任务，不予任何处理也不抛出异常。如果允许任务丢失，这是最好的一种方案。

**面试题：**

> 上面说了我们可以有三种方式去创建线程池
>
> ```java
> // 一个线程池里面有 5 个处理线程
> ExecutorService threadPool = Executors.newFixedThreadPool(5);
>
> // 一个线程池里面有 1 个处理线程
> ExecutorService threadPool = Executors.newSingleThreadExecutor();
>
> // 一个线程池里面有 N 个处理线程
> ExecutorService threadPool = Executors.newCachedThreadPool();
> ```
>
> 那么在实际使用中用哪一个比较好？
>
> 答案是一个都不用，我们最好使用手写的线程池。
>
> 原因：
>
> 根据阿里巴巴 java 开发手册：
>
> ```
> 线程池不允许使用Executors去创建，而是通过ThreadPoolExecutor的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。
>
> Executors返回的线程池对象的弊端如下：
>
> 1）FixedThreadPool和SingleThreadPool:
>
> 允许的请求队列长度为Integer.MAX_VALUE，可能会堆积大量的请求，从而导致OOM。
>
> 2）CachedThreadPool和ScheduledThreadPool:
>
> 允许的创建线程数量为Integer.MAX_VALUE，可能会创建大量的线程，从而导致OOM。
> ```
>
> 看源码：
>
> ```java
> public static ExecutorService newSingleThreadExecutor() {
>         return new FinalizableDelegatedExecutorService
>             (new ThreadPoolExecutor(1, 1,
>                                     0L, TimeUnit.MILLISECONDS,
>                                     new LinkedBlockingQueue<Runnable>()));
>     
>  // 使用的阻塞队列是 LinkedBlockingQueue，而 LinkedBlockingQueue 由链表结构组成的有界（大小默认值为 Integer.MAX_VALUE）阻塞队列，这样阻塞队列里面就能放无数个线程，从而导致 OOM
> ```
>
> 

#### 6、自定义线程池

```java
public static void main(String[] args) {
        /**
         * 这里我使用 ThreadPoolExecutor 创建了一个线程池
         * 线程池里面有 2 个核心线程，最大线程数是 5，阻塞队列里面可以有 3 个线程等待
         * 使用的拒绝策略是直接抛异常
         *
         * 所以我这个线程池，如果现在只来 2 个线程，那么线程池里面 2 个核心线程就可以处理，
         * 如果这时候又来了 3 个线程，且 2 个核心线程任务还没处理完，这时候放在阻塞队列里面，
         * 如果这时候又来了 3 个线程，且 2 个核心线程任务还没处理完，因为最大线程数是 5 ，所以
         * 又会增加 3 个处理线程，此时如果再来请求，就会抛异常了
         * 因此，线程池支持线程数 = 最大线程数 + 阻塞队列支持线程数
         * 注：最大线程数包括核心线程数
         */
        ExecutorService threadPool = new ThreadPoolExecutor(2,5
                ,2L, TimeUnit.SECONDS,new LinkedBlockingDeque<>(3),Executors.defaultThreadFactory()
                ,new ThreadPoolExecutor.AbortPolicy());
        try {
            // 模拟 5 个用户来办业务，每个用户就是一个来自外部的请求线程
            for (int i = 1; i <= 5; i++) {
                threadPool.execute(new Runnable() {
                    @Override
                    public void run() {
                        System.out.println(Thread.currentThread().getName() + " 办理业务");
                    }
                });
            }
        } finally {
            threadPool.shutdown();
        }
    }
```

#### 7、死锁编码及定位分析

死锁是什么？

> 死锁是指两个或两个以上的进程在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力干涉那它们都将无法推进下去，如果系统资源充足，进程的资源请求都能够得到满足，死锁出现的可能性就很低，否则就会因争夺有限的资源而陷入死锁。

```java
/**
 * 死锁是指两个或两个以上的进程在执行过程中，因争夺资源而造成的一种互相等待现象
 */
public class DemoLockDemo {
    public static void main(String[] args) {
        String lockA = "lockA";
        String lockB = "lockB";
        new Thread(new HoldDeadLock(lockA,lockB)).start();
        new Thread(new HoldDeadLock(lockB,lockA)).start();
    }
}

class HoldDeadLock implements Runnable {

    private String lockA;
    private String lockB;

    public HoldDeadLock(String lockA, String lockB) {
        this.lockA = lockA;
        this.lockB = lockB;
    }

    @Override
    public void run() {
        synchronized (lockA) {
            System.out.println(Thread.currentThread().getName() + "自己持有" + lockA + "尝试获得" + lockB);
            // 暂停一会线程
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (lockB) {
                System.out.println(Thread.currentThread().getName() + "自己持有" + lockB + "尝试获得" + lockA);
            }
        }
    }
}
```

**死锁问题定位：**

使用  jps 命令查到进程号，然后使用 jstack 命令根据进程号打印出堆栈信息

### 七、JVM

#### 1、GCRoots 的理解

**什么是垃圾？**

> 简单的说就是内存中已经不再被使用到的空间就是垃圾

**要进行垃圾回收，如何判断一个对象是否可以被回收？**

> - 引用计数法
>   - Java 中引用和对象是由关联的。如果要操作对象，则必须用引用进行。因此，一个简单的办法就是通过引用计数来判断一个对象是否可以回收。简单说，给对象中添加一个引用计数器，每当有一个地方引用它，计数器值加 1，每当有一个引用失效时，计数器值减 1。任何时刻计数器值为零的对象就是不可能再被使用的，那么这个对象就是可回收对象。
>   - 目前这种算法没有人使用了，因为解决不掉循环引用的问题，了解即可。而且每次对象赋值的时候，还要维护引用计数器，且计数器本身也有一定的消耗
> - 枚举根节点做可达性分析（根搜索路径）
>   - 为了解决引用计数法的循环引用问题，Java 使用了可达性分析的方法
>   - 所谓 『GC roots』就是一组必须活跃的引用
>   - **基本思路就是通过一系列名为 『GC roots』的对象作为起始点**，从这个被称为 『GC roots』的对象开始向下搜索，如果一个对象到 『GC roots』 没有任何引用链相连时，则说明这个对象不可用。也即给定一个集合的引用作为根出发，通过引用关系遍历对象图，能被遍历到的（可到达的）对象就被判定为存活，没有被遍历到的自然就被判定为死亡。

![根节点可达性分析](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/%E6%A0%B9%E8%8A%82%E7%82%B9%E5%8F%AF%E8%BE%BE%E6%80%A7%E5%88%86%E6%9E%90.png)

Java 中可以作为 GC Roots 的对象

> - 虚拟机栈（栈帧中的局部变量区，也叫做局部变量表）中引用的对象
> - 方法区中的类静态属性引用的对象
> - 方法区中常量引用的对象
> - 本地方法栈中 JNI（Native方法）引用的对象

#### 2、JVM 的参数类型

> - 标配参数
>   - `-version`
>   - `-help`
>   - `-showversion`
>   - 意思就是在 jdk 各个版本之间很稳定，很少有大的变化
> - X 参数
>   - `-Xint`  ：解释执行
>   - `-Xcomp` ：第一次使用就编译成本地代码
>   - `-Xmixed` ：混合模式
> - XX 参数
>   - Boolean 类型
>     - `-XX`    ： 「+ 」 或者 「-」 某个属性值
>     -  「+ 」 表示开启     「-」 表示关闭
>   - KV 设值类型
>     - `-XX`     :   属性key=属性值value

#### 3、JVM 的 XX 参数之布尔类型

> - 是否打印 GC 收集细节
>   - `-XX:-PrintGCDetails`
>   - `-XX:+PrintGCDetails`
> - 是否使用串行垃圾回收器
>   - `-XX:-UseSerialGC`
>   - `-XX:+UseSerialGC`

#### 4、JVM 的 XX 参数之设值类型

> - `-XX:MetaspaceSize=128M`
> - `-XX:MaxTenuringThreshold=15`

**注：**

> 如何使用 `jinfo` 查看当前运行程序的配置
>
> `jinfo -flag 配置项 进程编号`
>
> ```
> jinfo -flag InitialHeapSize 1622
> -XX:InitialHeapSize=134217728
> ```
>
> 

**两个经典参数：**

> - `-Xms`   ：  等价于    `-XX:InitialHeapSize`
> - `-Xmx`   :     等价于    `-XX:MaxHeapSize`

#### 5、JVM 初始默认值

> - `-XX:+PrintFlagsInitial`
>
>   - 查看初始默认值
>
>   - 使用方式
>
>     - ```ja
>       java -XX:+PrintFlagsInitial
>       ```
>
> - `-XX:+PrintFlagsFinal`
>
>   - 查看修改更新
>
>   - 使用方式
>
>     - ```java
>       java -XX:+PrintFlagsFinal -version
>       ```
>
>     - ​
>
> - `-XX:+PrintCommandLineFlags`
>
>   - 打印命令行参数
>
>   - 使用方式
>
>     - ```java
>       java -XX:+PrintCommandLineFlags -version
>       ```
>
>       ​

**注：**

```java
bool PrintFlagsFinal                          := true                                {product}
uintx InitialCodeCacheSize                      = 2555904                             {pd product}

//  :=     表示 jvm 或者用户修改过
//  =      表示初始默认值
```

#### 6、JVM 常用基本配置参数

常用参数：

> - -Xms  
>
>   - 初始大小内存，默认为物理内存的 1/64
>   - 等价于 `-XX:InitialHeapSize`
>
> - -Xmx
>
>   - 最大分配内存，默认为物理内存的 1/4
>   - 等价于 `-XX:MaxHeapSize`
>
> - -Xss
>
>   - 设置单个线程栈的大小，一般默认为 512K ~ 1024K
>   - 等价于  `-XX:ThreadStackSize`
>
> - -XX:MetaspaceSize
>
>   - 设置元空间大小
>     - 元空间的本质和永久代类似，都是对 JVM 规范中方法区的实现。不过元空间与永久代之间最大的区别在于：**元空间并不是在虚拟机中，而是使用本地内存** 。因此，默认情况下，元空间的大小仅受本地内存限制
>   - `-Xms10m -Xmx10m -XX:MetaspaceSize=1024m -XX:+PrintFlagsFinal`
>
> - -XX:+PrintGCDetails
>
>   - 输出详细 GC 收集日志信息
>
> - -XX:SurvivorRatio
>
>   - 设置新生代中 eden 和 s0/s1 空间的比例
>   - 默认 `-XX:SurvivorRatio=8`    `Eden:S0:S1 = 8:1:1`    假如  `-XX:SurvivorRatio=4`     `Eden:S0:S1 = 4:1:1`        SurvivorRatio 的值就是设置 eden 区的比例占多少，S0/S1 相同
>
> - -XX:NewRatio
>
>   - 配置年轻代与老年代在堆结构的占比
>   - 默认 `-XX:NewRatio=2`   新生代占 1，老年代占 2  ，年轻代占整个堆的 1/3    假如   `-XX:NewRatio=4`  新生代占 1，老年代占 4，年轻代占整个堆的 1/5  `NewRatio` 的值就是设置老年代的占比，剩下的 1 给新生代  
>
> - -XX:MaxTenuringThreshold
>
>   - 设置垃圾最大年龄
>
>   - ```java
>     //查看默认进入老年代的年龄：
>     jps -l
>     2574 com.iflytek.java.HelloGC
>
>     jinfo -flag MaxTenuringThreshold 2574
>     -XX:MaxTenuringThreshold=15
>     ```
>
>   - `-XX:MaxTenuringThreshold=0` ：设置垃圾最大年龄。如果设置为 0 的话，则年轻代对象不经过 Survivor 区，直接进入年老代。对于年老代比较多的应用，可以提高效率。如果将此值设置为一个较大的值，则年轻代对象会在 Survivor 区进行多次复制，这样可以增加对象在年轻代的存活时间

**JVM 参数设置-典型的一个配置案例：**

```java
-Xms128m -Xmx4096m -Xss1024k -XX:MetaspaceSize=512m -XX:+PrintCommandLineFlags -XX:+PrintGCDetails -XX:+UseSerialGC

// 小说明：
-XX:+UseSerialGC        // 串行垃圾回收器
-XX:+UseParallelGC      // 并行垃圾回收器
```

#### 7、GC 回收前后对比

使用 PrintGCDetails 可以打印详细的 GC 收集日志信息

**YoungGC前后：**

![YoungGC前后对比](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/YoungGC%E5%89%8D%E5%90%8E%E5%AF%B9%E6%AF%94.png)

**FullGC前后：**

![FullGC前后对比](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/FullGC%E5%89%8D%E5%90%8E%E5%AF%B9%E6%AF%94.png)

**强引用、软引用、弱引用、虚引用**

#### 8、强引用

> 当内存不足，JVM 开始垃圾回收，对于强引用的对象，就算是出现了 OOM 也不会对该对象进行回收，死都不收。
>
> 强引用是我们最常见的普通对象引用，只要还有强引用指向一个对象，就能表明对象还活着。垃圾收集器就不会碰这种对象。在 Java 中最常见的就是强引用，把一个对象赋给一个引用变量，这个引用变量就是一个强引用。当一个对象被强引用变量引用时，它处于可达状态，它是不可能被垃圾回收机制回收的，即使该对象以后永远都不会被用到，JVM 也不会回收。因此强引用是造成 JAVA 内存泄漏的主要原因之一。

```java
public class StrongReferenceDemo {
    public static void main(String[] args) {
        Object obj1 = new Object();
        Object obj2 = obj1;
        obj1 = null;
        System.gc();
        System.out.println(obj2);
    }
}
```

#### 9、软引用

> 软引用是一种相对于强引用弱化了一些的引用，需要用 `java.lang.ref.SoftReference` 类来实现，可以让对象豁免一些垃圾收集
>
> 对于只有软引用的对象来说：
>
> - 当系统内存充足时，它不会被回收
>
>
> - 当系统内存不足时，它会被回收
>
> 软引用通常用在对内存敏感的程序中，比如高速缓存就有用到软引用，内存够用的时候就保留，不够用就回收。

```java
public class SoftReferenceDemo {

    /**
     * 内存够用就保留，不够用就回收
     */
    public static void softRefMemoryEnough() {
        Object obj1 = new Object();
        SoftReference<Object> softReference = new SoftReference<Object>(obj1);
        System.out.println(obj1);
        System.out.println(softReference.get());

        // 这里我把 obj1 置为 null，然后手动 gc
        obj1 = null;
        System.gc();

        // gc 过后，在内存充足的情况下看软引用是否会被回收
        System.out.println(obj1);
        System.out.println(softReference.get());  // java.lang.Object@60e53b93  发现并没有被回收
    }

    /**
     * JVM 配置，故意产生大对象并配置小的内存，让它内存不够用导致 OOM，看软引用的回收情况
     * -Xms5m -Xmx5m -XX:+PrintGCDetails
     */
    public static void softRefMemoryNotEnough() {
        Object obj1 = new Object();
        SoftReference<Object> softReference = new SoftReference<Object>(obj1);
        System.out.println(obj1);
        System.out.println(softReference.get());

        obj1 = null;

        try {
            byte[] bytes = new byte[30 * 1024 * 1024];
        } catch (Throwable t) {
            t.printStackTrace();

        } finally {
            System.out.println(obj1);   // null
            System.out.println(softReference.get()); // null 会发现在内存不够的情况下，软引用被回收了
        }

    }

    public static void main(String[] args) {
//        softRefMemoryEnough();
        softRefMemoryNotEnough();
    }
}
```

#### 10、弱引用

> 弱引用需要用 java.lang.ref.WeakReference 类来实现，它比软引用的生存期更短。对于只有弱引用的对象来说，只要垃圾回收机制一运行，不管 JVM 内存空间是否足够，都会回收该对象占用的内存。

```java
public class WeakReferenceDemo {
    public static void main(String[] args) {
        Object obj1 = new Object();
        WeakReference<Object> weakReference = new WeakReference<Object>(obj1);
        System.out.println(obj1);
        System.out.println(weakReference.get());
        obj1 = null;
        System.gc();
        System.out.println("========");
        // 手动 gc 过后，发现弱引用被回收了，在内存充足的情况下弱引用也是会被回收的
        System.out.println(obj1);  // null
        System.out.println(weakReference.get());  // null

    }
}
```












































































































































































































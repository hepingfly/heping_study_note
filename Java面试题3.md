# Java 面试题3

### 一、Java 基础

#### 1、字符串常量池

1）、面试题

```java
public class StringPool {
    public static void main(String[] args) {
        String str1 = new StringBuilder("heping").append("fly").toString();
        System.out.println(str1);
        System.out.println(str1.intern());
        System.out.println(str1.intern() == str1);

        System.out.println("**********************");
        String str2 = new StringBuilder("ja").append("va").toString();
        System.out.println(str2);
        System.out.println(str2.intern());
        System.out.println(str2.intern() == str2);
    }
}
// 判断 str1.intern() == str1 的结果？ 和 str2.intern() == str2 的结果？
```

- 面试题答案

  - ```java
    打印结果为：
    hepingfly
    hepingfly
    true
    **********************
    java
    java
    false
    ```

  - 总结：只有字符串 java 打印结果是 false ，别的字符串都是 true

- 面试题解析

按照代码的结果显示， java 字符串答案为 false。必然是有两个不同的 java ，那么另外一个 java 字符串时如何加载进来的？

原因就是有一个初始化的 java 字符串（JDK 自带）在加载 `sun.misc.Version` 这个类的时候进入常量池。所以说我们自己 new 的这个 java 字符串对象和 JDK 自带的 java 字符串对象，根本不是一个对象。

```java
String str2 = new StringBuilder("ja").append("va").toString();   // 这个是我们自己 new 出来的字符串对象

System.out.println(str2.intern()); // str2.intern() 方法，它会去字符串常量池中看有没有一个包含 java 对象的字符串，因为 jdk 会自带，所以字符串常量池中有这个对象了，所以直接返回。

// 因此这两个对象不是同一个对象，比较是否相等肯定返回 false
```



`String` 类中 `intern()` 方法说明：

> `public native String intern();`
>
> 它是一个 native 方法，作用是如果字符串常量池中已经包含一个等于此 String 对象的字符串，则返回代表池中这个字符串的 String 对象的引用。否则，会把此 String 对象包含的字符串添加到常量池中，并且返回此 String 对象的引用。

#### 2、两数求和

1）、面试题

```java
给定一个整数数组 nums 和一个目标值 target ,请你在该数组中找出和为目标值的那两个整数，并返回他们的数组下标。
你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。
例如：
给定 nums = [2,7,11,15],target=9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0,1]
```

- 面试题答案

```java
public class TwoSum {

    /**
     * 遍历
     * 通过双重循环遍历数组中所有元素的两两组合，当出现符合的两个数和的时候返回两个元素的下标
     * @author hepingfly
     * @date  2021/1/16 9:41 下午
     * @return
     */
    public static int[] sum1(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (target - nums[i] == nums[j]) {
                    return new int[]{i, j};
                }
            }
        }
        return null;
    }
  
  // 方法二：  采用 map 的方式，这种时间复杂度为O(1)，效率更高
    public static int[] sum2(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int num = target - nums[i];
            if (map.containsKey(num)) {
                return new int[]{map.get(num), i};
            }
            map.put(nums[i], i);
        }
        return null;
    }
    
    //---------------------------------------------------------------------------------

    public static void main(String[] args) {
        int[] nums = new int[]{2,7,9,11};
        int target = 9;
        int[] sum = sum1(nums, target);
        System.out.println(sum[0] + "," + sum[1]);

    }
```



### 二、JUC

#### 1、可重入锁

可重入锁又叫递归锁。是指同一个线程在外层方法获取锁的时候，进入该线程的内层方法会自动获取锁（前提是锁对象是同一个对象）。不会因为之前已经获取过锁还没释放而阻塞。

Java 中 `ReentrantLock` 和 `synchronized` 都是可重入锁，可重入锁的一个优点是一定程度上可以避免死锁。

生活中的例子类比：

家里的大门有一把锁，卫生间有一把锁，在进入大门之后就可以自动进入卫生间，而不需要阻塞等待。

可重入锁的种类：

> - 隐式锁（synchronized关键字使用的锁）默认是可重入锁。
> - 显示锁（即 Lock）也有 ReentrantLock 这样的可重入锁。

`synchronized` 可重入锁代码证明：

- 同步代码块

```java
public class SynchronizedDemo {
    static Object obj = new Object();
    public static void method1() {
        new Thread(() -> {
            // 一个线程在外层方法获取锁之后，进入到内层方法就会自动获取锁
            synchronized (obj) {
                System.out.println(Thread.currentThread().getName() + "外层方法调用");
                synchronized (obj) {
                    System.out.println(Thread.currentThread().getName() + "中层方法调用");
                    synchronized (obj) {
                        System.out.println(Thread.currentThread().getName() + "内层方法调用");
                    }
                }
            }
        },"thread1").start();
    }
    public static void main(String[] args) {
        method1();
    }
```

- 同步方法

```java
public class SynchronizedDemo2 {
    public synchronized void method1() {
        System.out.println("外层方法");
        method2();
    }
    public synchronized void method2() {
        System.out.println("中层方法");
        method3();
    }
    public synchronized void method3() {
        System.out.println("内层方法");
    }
    public static void main(String[] args) {
        new SynchronizedDemo2().method1();

    }
}
```

`synchronized` 可重入的实现机制：

> 每个锁对象拥有一个锁计数器和一个指向持有该锁的线程的指针。
>
> 当执行 monitorenter 时，如果目标锁对象的计数器为零，那么说明它没有被其它线程所持有， Java 虚拟机会将该锁对象的持有线程设置为当前线程，并且将其计数器加 1
>
> 在目标锁对象的计数器不为零的情况下，如果锁对象的持有线程是当前线程，那么 Java 虚拟机可以将其计数器加 1，否则需要等待，直至持有线程释放该锁。
>
> 当执行 monitorexit 时， Java 虚拟机则需将锁对象的计数器减 1。计数器为零代表锁对象已被释放。

#### 2、LockSupport

> `LockSupport` 是用来创建锁和其他同步类的基本线程阻塞原语。
>
> `LockSupport` 中的 `park()` 和 `unpark()` 的作用分别是阻塞线程和解除阻塞线程。

所谓的 `LockSupport` 就是线程等待唤醒机制（wait/notify）的加强改良版。

1）、线程等待唤醒机制（wait/notify）

三种让线程等待和唤醒的方法

> - 方式一：使用 Object 中的 wait() 方法让线程等待，使用 Object 中的 notify() 方法唤醒线程
> - 方式二：使用 JUC 包中 Condition 的 await() 方法让线程等待，使用 signal() 方法唤醒线程
> - 方式三：LockSupport 类可以阻塞当前线程以及唤醒指定被阻塞的线程。

① Object 类中的 wait 和 notify 实现线程等待和唤醒

- 正常情况下代码实现

```java
/**
 * @auther hepingfly
 * @date 2021/1/18 10:39 下午
 */
public class LockSupportDemo {
    static Object obj = new Object();  // 使用同一把锁
    public static void main(String[] args) {
        new Thread(() -> {
            synchronized (obj) {
                System.out.println(Thread.currentThread().getName() + "进入");
                try {
                    obj.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + "被唤醒");
            }
        },"线程1").start();
        new Thread(() -> {
            synchronized (obj) {
                obj.notify();
                System.out.println(Thread.currentThread().getName() + "通知");
            }
        },"线程2").start();
    }
  // ---------------上面是正常情况，打印出来的结果为：---------------------------------------
线程1进入
线程2通知
线程1被唤醒
```

- 异常情况一（去掉 synchronized 关键字）

```java
/**
 * @auther hepingfly
 * @date 2021/1/18 10:39 下午
 */
public class LockSupportDemo {
    static Object obj = new Object();
    public static void main(String[] args) {
        new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "进入");
                try {
                    obj.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + "被唤醒");
        },"线程1").start();
        new Thread(() -> {
                obj.notify();
                System.out.println(Thread.currentThread().getName() + "通知");
        },"线程2").start();
    }
}
```

运行直接报异常：

![线程等待唤醒去掉synchronized](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/%E7%BA%BF%E7%A8%8B%E7%AD%89%E5%BE%85%E5%94%A4%E9%86%92%E5%8E%BB%E6%8E%89synchronized.png)

**结论：**

> Object 类中的 wait、notify、notifyAll 用于线程等待和唤醒的方法，都必须在 synchronized 内部执行

- 异常情况二（将 notify 方法放在 wait 方法前面）

```java
/**
 * @auther hepingfly
 * @date 2021/1/18 10:39 下午
 */
public class LockSupportDemo {
    static Object obj = new Object();
    public static void main(String[] args) {
        new Thread(() -> {
            try {
                Thread.sleep(3000); // 这里让线程1先睡上 3 秒，这样线程2就会先执行
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (obj) {
                System.out.println(Thread.currentThread().getName() + "进入");
                try {
                    obj.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + "被唤醒");
            }
        },"线程1").start();
        new Thread(() -> {
            synchronized (obj) {
                obj.notify();
                System.out.println(Thread.currentThread().getName() + "通知");
            }
        },"线程2").start();
    }
}
```

执行结果为程序无法结束一直阻塞

**结论：**

> 必须要先 wait 后 notify或者 notifyAll ，等待中的线程才会被唤醒，否则无法唤醒。



② Condition 接口中的 await 和 signal 方法实现线程等待和唤醒

- 正常情况下代码实现

```java
/**
 * @auther hepingfly
 * @date 2021/1/18 10:39 下午
 */
public class LockSupportDemo {
    static Lock lock = new ReentrantLock();
    static Condition condition = lock.newCondition();
    public static void main(String[] args) {
        new Thread(() -> {
           lock.lock();
           try {
               System.out.println(Thread.currentThread().getName() + "进入");
               condition.await();
           } catch (InterruptedException e) {
               e.printStackTrace();
           } finally {
               lock.unlock();
           }
           System.out.println(Thread.currentThread().getName() + "被唤醒");
        },"线程1").start();
        new Thread(() -> {
            lock.lock();
            try {
                condition.signal();
                System.out.println(Thread.currentThread().getName() + "通知");
            }  finally {
                lock.unlock();
            }
        },"线程2").start();
    }
    
// ---------------上面是正常情况，打印出来的结果为：---------------------------------------
线程1进入
线程2通知
线程1被唤醒
```

- 异常情况一（去掉 lock 关键字）

```java
/**
 * @auther hepingfly
 * @date 2021/1/18 10:39 下午
 */
public class LockSupportDemo {
    static Object obj = new Object();
    static Lock lock = new ReentrantLock();
    static Condition condition = lock.newCondition();
    public static void main(String[] args) {
        new Thread(() -> {
           try {
               System.out.println(Thread.currentThread().getName() + "进入");
               condition.await();
           } catch (InterruptedException e) {
               e.printStackTrace();
           } finally {
           }
           System.out.println(Thread.currentThread().getName() + "被唤醒");
        },"线程1").start();
        new Thread(() -> {
            try {
                condition.signal();
                System.out.println(Thread.currentThread().getName() + "通知");
            }  finally {
            }
        },"线程2").start();
    }
 // 去掉 lock 和 unlock 关键字，仅仅保留 await() 和 signal() 方法，发现会直接报错
```

运行直接报异常：

![线程等待唤醒去掉 lock](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/%E7%BA%BF%E7%A8%8B%E7%AD%89%E5%BE%85%E5%94%A4%E9%86%92%E5%8E%BB%E6%8E%89lock.png)

**结论：**

> Condition接口中的 await、signal、signalAll 用于线程等待和唤醒的方法，都必须在 lock 块内部执行

- 异常情况二（将 signal 方法放在 await 方法前面）

```java
/**
 * @auther hepingfly
 * @date 2021/1/18 10:39 下午
 */
public class LockSupportDemo {
    static Object obj = new Object();
    static Lock lock = new ReentrantLock();
    static Condition condition = lock.newCondition();
    public static void main(String[] args) {
        new Thread(() -> {
            try {
                Thread.sleep(3000);    // 这里让线程1睡上 3 秒钟
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
           lock.lock();
           try {
               System.out.println(Thread.currentThread().getName() + "进入");
               condition.await();
           } catch (InterruptedException e) {
               e.printStackTrace();
           } finally {
               lock.unlock();
           }
           System.out.println(Thread.currentThread().getName() + "被唤醒");
        },"线程1").start();
        new Thread(() -> {
            lock.lock();
            try {
                condition.signal();
                System.out.println(Thread.currentThread().getName() + "通知");
            }  finally {
                lock.unlock();
            }
        },"线程2").start();
    }
```

执行结果为程序无法结束一直阻塞

**结论：**

> 必须要先 await 后 signal或者 signalAll ，等待中的线程才会被唤醒，否则无法唤醒。

③ LockSupport 类中的 park 等待和 unpark 唤醒

我们主要是通过 park 和 unpark 方法来实现阻塞和唤醒线程的操作

> LockSupport 类使用了一种名为 Permit （许可）的概念来做到阻塞和唤醒线程的功能，每个线程都有一个许可（permit），permit 只有两个值 1 和 0，默认是 0
>
> - 阻塞
>   - `park()/park(Object blocker)`
>   - permit 默认是 0，所以一开始调用 park() 方法，当前线程就会阻塞，直到别的线程将当前线程的 permit 设置为 1时，park 方法会被唤醒，然后会将 permit 再次设置为 0 并返回。
>   - 阻塞当前线程/阻塞传入的具体线程
> - 唤醒
>   - `unpark(Thread thread)`
>   - 调用 `unpark(Thread thread)` 方法后，就会将 thread 线程的许可 permit 设置为 1（注意多次调用 unpark 方法，不会累加，permit 值还是 1）会自动唤醒 thread 线程，即之前阻塞中的 `LockSupport.park()` 方法会立即返回
>   - 唤醒处于阻塞状态的指定线程

案例1：先 `park()` 后 `unpark()`

```java
/**
 * @auther hepingfly
 * @date 2021/1/18 10:39 下午
 */
public class LockSupportDemo {
    /**
     * 这个好处就是我可以单纯的去阻塞唤醒线程
     * 而不需要去加一些锁块之类的代码
     * @param args
     */
    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "进入");
            LockSupport.park();  // 类似于 wait() 方法，被阻塞，等待通知，要放行的话需要许可证
            System.out.println(Thread.currentThread().getName() + "被唤醒");
        }, "线程1");
        thread1.start();
        // 暂停几秒钟
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        /**
         * 3秒钟以后，线程2运行，线程2要去唤醒线程1，相当于要给线程1发放许可证，线程1才能被放行
         */
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "通知");
            LockSupport.unpark(thread1);
        },"线程2").start();
    }
}

// -----------------------------运行结果
线程1进入
线程2通知
线程1被唤醒
```

案例2：先  `unpark()` 后  `park()` 

```java

public class LockSupportDemo {
    // 先 unpark 后 park 也是可以运行成功的,不像 wait 和 notify 会直接报错
    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            // 暂停几秒钟
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "进入");
            LockSupport.park(); // 如果先 unpark 后 park , 那么这行会直接不起作用
            System.out.println(Thread.currentThread().getName() + "被唤醒");
        }, "线程1");
        thread1.start();

  
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "通知");
            LockSupport.unpark(thread1);
        },"线程2").start();
    }
}
//-------------------运行结果
线程2通知
线程1进入
线程1被唤醒
```

总结：

> LockSupport 是用来创建锁和其他同步类的基本线程阻塞原语。
>
> LockSupport 是一个线程阻塞工具类，所有的方法都是静态方法，可以让线程在任意位置阻塞，阻塞之后也有对应的唤醒方法。归根结底，LockSupport 调用的是 Unsafe 类中的 native 方法。
>
> **LockSupport 提供 `park()` 和 `unpark()`  方法实现阻塞线程和解除线程阻塞的过程**
>
> LockSupport 和每个使用它的线程都有一个许可（permit）关联。 permit 默认是 0。
>
> - 调用一次 unpark 就加 1 变成 1
> - 调用一次 park 会消费 permit ，也就是将 1 变成 0，同时 park 立即返回。
> - 如果再次调用 park 会变成阻塞（因为 permit 为 0 会阻塞在这里，一直到 permit 为 1），这时候调用 unpark 会把 permit 置为 1
> - 每个线程都有一个相关的 permit，permit 最多只有一个，重复调用 unpark 不会积累凭证。
>
> 简单来说：
>
> 线程阻塞需要消耗凭证（permit），这个凭证最多只有一个。
>
> - 当调用 park 方法时
>   - 如果有凭证，则会直接消耗掉这个凭证然后正常退出
>   - 如果没有凭证，就必须阻塞等待凭证可用
> - 当调用 unpark 方法时
>   - 它会增加一个凭证，但凭证最多只能有一个，无法累加。

面试题：

```
为什么可以先唤醒线程后阻塞线程？
答：先唤醒线程意味着你调用了 unpark() 方法，那么凭证加1，再去阻塞线程，即调用 park() 方法，这个时候有凭证，所以直接消耗掉凭证然后正常退出

为什么唤醒两次后阻塞两次，但最终结果还会阻塞线程？
答：唤醒两次意味着调用了两次 unpark() 方法，但是凭证无法累加最多只有 1，然后阻塞两次，即调用两次 park() 方法，需要消费 2 张凭证才能正常退出，但是只有 1 张凭证，所以凭证不够，阻塞。
```



#### 3、AQS

> AQS ：`AbstractQueuedSynchronizer` ，抽象队列同步器
>
> 专业一点的解释：
>
> AQS 是用来构建锁或者其他同步器组件的重量级基础框架及整个 JUC 体系的基石，通过内置的 FIFO 队列来完成资源获取线程的排队工作，并通过一个 int 类型变量表示持有锁的状态。



### 三、Spring

#### 1、Spring 的 AOP 顺序

**1）、AOP 常用注解**

> - @Before 
>   - 前置通知：目标方法之前执行
> - @After
>   - 后置通知：目标方法之后执行（始终执行）
> - @AfterReturning
>   - 返回通知：执行方法结束前执行（异常不执行）
> - @AfterThrowing
>   - 异常通知：出现异常的时候执行
> - @Around
>   - 环绕通知：环绕目标方法执行

```java
@Around("execution(XXXXXX)")
public void around(ProceedingJoinPoint joinPoint) {
    try {
        System.out.println("环绕通知之前");
        joinPoint.proceed();
        System.out.println("环绕通知之后");
    } catch (Throwable t) {
        
    } finally {
        
    }
}
```

**小知识：**

> SpringBoot1.X 版本对应 Spring4
>
> SpringBoot 2.X 版本对应 Spring5

在 Spring4 版本下 AOP 的执行顺序：

> 正常情况：
>
> @Before -------业务代码  --------  @After  ---------    @AfterReturning
>
> 加上环绕通知，环绕通知一半在 @Before 之前执行，一半在 @After 之前执行
>
> 异常情况：
>
> @Before -------业务代码    --------  @After  ---------    @AfterThrowing
>
> 加上环绕通知，异常情况下环绕通知会执行一半

![spring4正常情况 aop 执行顺序](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/spring4%E6%AD%A3%E5%B8%B8%E6%83%85%E5%86%B5aop%E6%89%A7%E8%A1%8C%E9%A1%BA%E5%BA%8F.png)

![spring4异常情况aop执行顺序](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/spring4%E5%BC%82%E5%B8%B8%E6%83%85%E5%86%B5aop%E6%89%A7%E8%A1%8C%E9%A1%BA%E5%BA%8F.png)

在 Spring5 版本下 AOP 的执行顺序：

> 正常情况：
>
> @Before -------业务代码  -------- @AfterReturning ---------    @After  
>
> 加上环绕通知，环绕通知一半在 @Before 之前执行，一半在 @After 之后执行
>
> 异常情况：
>
> @Before -------业务代码    -------- @AfterThrowing  ---------    @After  
>
> 加上环绕通知，异常情况下环绕通知会执行一半

![spring5正常和异常情况aop执行顺序.png](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/spring5%E6%AD%A3%E5%B8%B8%E5%92%8C%E5%BC%82%E5%B8%B8%E6%83%85%E5%86%B5aop%E6%89%A7%E8%A1%8C%E9%A1%BA%E5%BA%8F.png)

#### 2、Spring 循环依赖


















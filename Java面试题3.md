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












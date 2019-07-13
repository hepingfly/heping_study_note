## Java 面试题二

### 一、JUC 多线程和高并发

#### 1、volatile 是什么

**谈谈你对 volatile 的理解**

> - volatile 是 Java 虚拟机提供的轻量级的同步机制
>   - 保证可见性
>   - 不保证原子性
>   - 禁止指令重排

**谈谈 JMM 是什么**

> JMM（Java Memory Model）Java 内存模型，本身是一种抽象的概念*并不真实存在*，它描述的是一组规则或规范，通过这组规范定义了程序中各个变量（包括实例字段，静态字段和构成数组对象的元素）的访问方式。
>
> JMM 有三大特性：
>
> - 可见性
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


# Java 面试题一

### 一、变量相关面试题

```java
public class Test {
    public static void main(String[] args) {
        int i = 1;
        i = i++;
        int j = i++;
        int k = i + ++i * i++;
        System.out.println("i=" + i);    // i =4
        System.out.println("j=" + j);    // j = 1
        System.out.println("k=" + k);    // k = 11
    }
}
```

### 二、单例设计模式

**单例模式：**

> 即某个类在整个系统中只能有一个实例对象可被获取和使用的代码模式

**要点：**

> ① 某个类只能有一个实例
>
> ​	私有化其构造器
>
> ② 它必须自行创建这个实例
>
> ​	含有一个该类的静态变量来保存这个唯一的实例
>
> ③它必须自行向整个系统提供这个实例
>
> ​	直接暴露或静态变量的 get 方法获取

**单例模式可以分为懒汉式和饿汉式：**

1）、饿汉式

> 直接创建对象，不存在线程安全问题。
>
> 关于饿汉式可以有下面三种写法：
>
> ① 直接实例化饿汉式（这种比较简单直观）
>
> ② 枚举式（这种方式比较简洁）
>
> ③ 静态代码块饿汉式（适合复杂实例化）

方式一：

```java
/**
 * 饿汉式：
 *  在类初始化时，直接创建实例对象，不管你是否需要这个对象
 * 1）、私有化构造器
 * 2）、创建实例并用静态变量保存
 * 3）、对外提供这个实例
 * 4）、强调这是一个单例,我们可以用 final 修饰
 */
public class Singleton1 {

    public static final Singleton1 INSTANCE = new Singleton1();
    private Singleton1() {

    }
}
```

方式二：

```java
/**
 * 枚举类型：表示该类型的对象是有限个
 * 我们可以指定为 1 个，就成了单例
 *
 */
public enum  Singleton2 {
    INSTANCE;
}
```

方式三：

```java
/**
 * 这种方式适合在静态代码块中做一些操作的
 */
public class Singleton3 {

    public static final Singleton3 INSTANCE;
    private String info;
    static {
        Properties properties = new Properties();
        try {
            properties.load(Singleton3.class.getClassLoader().getResourceAsStream("single.properties"));
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        INSTANCE = new Singleton3(properties.getProperty("info"));
    }
    private Singleton3(String info) {
        this.info = info;
    }

    public String getInfo() {
        return info;
    }

    public void setInfo(String info) {
        this.info = info;
    }
}
```

2）、懒汉式

> 延迟创建对象
>
> ① 懒汉式线程不安全（适用于单线程）
>
> ② 我们可以把它改造成线程安全（适用于多线程）
>
> ③ 我们还可以使用静态内部类的方式（适用于多线程）

方式一：

```java
/**
 * 懒汉式：
 *  延迟创建这个实例对象
 *
 *  1、构造器私有化
 *  2、用一个静态变量保存这个唯一的实例
 *  3、提供一个静态方法，获取这个实例对象
 */
public class Singleton4 {
    private static Singleton4 instance;
    private Singleton4() {

    }
    public static Singleton4 getInstance() {
        if (instance == null) {
            instance = new Singleton4();
        }
        return instance;
    }
}
```

方式二：

```java
package singleton;

/**
 * 懒汉式：
 * 改造成线程安全
 */
public class Singleton5 {
    private static Singleton5 instance;
    private Singleton5() {

    }
    public static Singleton5 getInstance() {
        if (instance == null) {     // 为了性能考虑
            synchronized (Singleton5.class) {    // 加锁
                if (instance == null) {
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    instance = new Singleton5();
                }
            }
        }
        return instance;
    }
}

```

方式三：

```java
/**
 * 在内部类被加载和初始化时，才创建 INSTANCE 实例
 * 静态内部类不会自动随着外部类的加载和初始化而初始化，它是要单独去加载和初始化的
 * 这种方式是在内部类加载和初始化时，创建的，因此是线程安全的
 */
public class Singleton6 {
    private Singleton6() {

    }
    private static class Inner {
        private static final Singleton6 INSTANCE = new Singleton6();
    }
    public Singleton6 getInstance() {
        return Inner.INSTANCE;
    }
}
```

### 三、类初始化和实例初始化

类初始化过程：

> 1）、一个类要创建实例需要先加载并初始化该类
>
> ​	① main 方法所在的类需要先加载和初始化
>
> 2）、一个子类要初始化需要先初始化其父类
>
> 3）、一个类初始化就是执行 `<clinit>()` 方法
>
> ​	① `<clinit>()` 方法由静态类变量显示赋值代码和静态代码块组成
>
> ​	② 类变量显示赋值代码和静态代码块从上到下顺序执行
>
> ​	③ `<clinit>()` 方法只执行一次

Father.java :

```java
package init;

/**
 * 父类初始化，就是调用<clinit>() 方法
 *  ① int j = method()   静态类变量显示赋值代码
 *  ② 父类的静态代码块
 */
public class Father {

    private int i = test();
    private static int j = method();
    static {
        System.out.print("(1)");
    }
    Father() {
        System.out.print("(2)");
    }
    {
        System.out.print("(3)");
    }
    public int test() {
        System.out.print("(4)");
        return 1;
    }
    public static int method() {
        System.out.print("(5)");
        return 1;
    }
}

```

Son.java :

```java
package init;

/**
 * 初始化子类时，会先初始化父类
 * 子类初始化，就是调用<clinit>() 方法
 * ① int j = method()   静态类变量显示赋值代码
 * ② 子类的静态代码块
 *
 * 先初始化父类：(5)(1)
 * 再初始化子类：(10)(6)
 *
 */
public class Son extends Father {
    private int i = test();
    private static int j = method();
    static {
        System.out.print("(6)");
    }
    Son() {
        System.out.print("(7)");
    }
    {
        System.out.print("(8)");
    }
    public int test() {
        System.out.print("(9)");
        return 1;
    }
    public static int method() {
        System.out.print("(10)");
        return 1;
    }

    public static void main(String[] args) {
		// 我 main 方法里面什么都不写，它还是会进行一些初始化工作
        // 上面我们说了 main 方法所在的类需要先加载和初始化,那么 Son 类就会被初始化,Son 类被初始化之前会先初始化它的父类 Father 类
        
        // 最终结果是  (5)(1)(10)(6)
    }
}

```

**实例初始化过程**

> 1）、实例初始化就是执行 `<init>() 方法`
>
> ​	① `<init>() 方法` 可能重载有多个，有几个构造器就有几个  `<init>() 方法` 
>
> ​	② `<init>() 方法` 由非静态实例变量显示赋值代码和非静态代码块、对应构造器代码组成
>
> ​	③ 非静态实例变量显示赋值代码和非静态代码块代码从上到下顺序执行，而对应构造器的代码最后执行
>
> ​	④ 每次创建实例对象，调用对应构造器，执行的就是对应的  `<init>() 方法` 
>
> ​	⑤  `<init>() 方法` 的首行是 `super()` 或 `super(实参列表)` ，即对应父类的  `<init>() 方法` 

**方法的重写（override）：**

> 1）、哪些方法不可以被重写
>
> ​	① final 修饰的方法
>
> ​	② 静态方法
>
> ​	③ private 等子类中不可见方法
>
> 2）、对象的多态性
>
> ​	① 子类如果重写了父类的方法，通过子类对象调用的一定是子类重写过的代码
>
> ​	② 非静态方法默认的调用对象是 this
>
> ​	③ this 对象在构造器或者 `<init>` 方法中就是正在创建的对象

Father.java ：

```java
package init;

/**
 *  父类实例初始化：
 * ① super()       最前
 * ② int i = test()  非静态变量显示赋值代码
 * ③ 父类的非静态代码块
 * ④ 父类的无参构造器   最后
 *
 * 非静态方法前面其实有一个默认的对象 this
 * this 在构造器(或 <init> 方法) 它表示的是正在创建的对象，因为这里是在创建 Son 对象，所以
 * test() 执行的是子类重写的代码      int i = test()
 */
public class Father {

    private int i = test();
    private static int j = method();
    static {
        System.out.print("(1)");
    }
    Father() {
        System.out.print("(2)");
    }
    {
        System.out.print("(3)");
    }
    public int test() {
        System.out.print("(4)");
        return 1;
    }
    public static int method() {
        System.out.print("(5)");
        return 1;
    }
}

```

Son.java ：

```java
package init;

/**
 * 子类实例初始化：
 * ① super()       最前    会先去初始化父类实例  (9)(3)(2)
 * ② int i = test()  非静态变量显示赋值代码      (9)
 * ③ 子类的非静态代码块                          (8)
 * ④ 子类的无参构造器   最后                      (7)
 *
 */
public class Son extends Father {
    private int i = test();
    private static int j = method();
    static {
        System.out.print("(6)");
    }
    Son() {
        System.out.print("(7)");
    }
    {
        System.out.print("(8)");
    }
    public int test() {
        System.out.print("(9)");
        return 1;
    }
    public static int method() {
        System.out.print("(10)");
        return 1;
    }

    public static void main(String[] args) {
        // 因为前面我们说了，每次创建实例对象，调用对应构造器，执行的就是对应的  <init>() 方法
        // 所以上面的实例初始化会执行两遍
        Son son1 = new Son();
        System.out.println();
        Son son2 = new Son();
        
        // 最终结果是:
        // (5)(1)(10)(6)(9)(3)(2)(9)(8)(7)
		// (9)(3)(2)(9)(8)(7)
    }
}

```


















































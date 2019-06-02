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



### 四、方法的参数传递机制

方法的参数传递机制：

> - 形参是基本数据类型
>   - 传递的是数据值
> - 形参是引用数据类型
>   - 传递的是地址值
>   - 特殊类型：String 、包装类等对象不可变性

```java
package arg;

import java.util.Arrays;

public class Exam4 {

    public static void change(int j, String s, Integer n, int[] a, MyData m) {
        j += 1;
        s += "world";
        n += 1;
        a[0] += 1;
        m.a += 1;
    }

    public static void main(String[] args) {
        int i = 1;
        String str = "hello";
        Integer num = 200;
        int[] arr = {1,2,3,4,5};
        MyData m = new MyData();
        change(i,str,num,arr,m);
        System.out.println("i=" + i);
        System.out.println("str=" + str);
        System.out.println("num=" + num);
        System.out.println("arr=" + Arrays.toString(arr));
        System.out.println("m.a=" + m.a);
    }

}
class MyData {
    int a = 10;
}

//i=1
//str=hello
//num=200
//arr=[2, 2, 3, 4, 5]
//m.a=11

```



![方法的参数传递机制](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/%E6%96%B9%E6%B3%95%E5%8F%82%E6%95%B0%E4%BC%A0%E9%80%92%E6%9C%BA%E5%88%B6.png?q-sign-algorithm=sha1&q-ak=AKID3sTbFoAsEK82wWBh3fhCgO3DsYRUgPKE&q-sign-time=1559476903;1559480503&q-key-time=1559476903;1559480503&q-header-list=&q-url-param-list=&q-signature=05a9f027133043287ebd631063fa90ece312ded2&x-cos-security-token=caaeba7f385648eee37be0d837c519421fcfa29c10001)



> 我们都知道你声明一个变量，这个变量是存在栈空间的，你在方法中声明的变量，肯定都是局部变量
>
> 局部变量按照方法在栈空间中分配区域。首先看 main 方法栈中的局部变量，int i = 1，在 main 方法栈中，会有一个变量 i ，这个 i 是 int 类型的，然后把 1 存入栈中。
>
>  String str = "hello"  ，在 main 方法栈中会有一个变量 str ，这个 str 是 String 类型，因为 String 是引用类型，并且这个 str 的值是一个字符串常量 hello ，所以会把这个 hello 存到常量池中，在常量值中开辟一块空间存 hello，变量 str 在栈中，栈空间具体存的是 hello 的地址值，假设是 0x123，通过这个地址值指向常量池中的 hello。
>
> Integer num = 200，在 main 方法栈中会有一个变量 num ，这个变量是 Integer 类型 ，Integer 是包装类，所以会在堆空间中开辟一块空间，把 200 存在堆空间中，然后栈中变量 num 存这个 200 在堆空间的地址，假设叫 0x9090，通过这个地址指向堆空间中的 200
>
> int[] arr = {1,2,3,4,5}    ，在 main 方法栈中会有一个变量 arr，这个变量是 int 类型的数组， 因为 arr 是数组类型，所以在堆空间中也会开辟一块空间去存储数组中的元素，因此 main 方法栈中 arr 存的是数组的首地址值。通过这个首地址值指向堆空间中的数组
>
> MyData m = new MyData()，  在 main 方法栈中会有一个变量 m，这个 m 是一个对象，所以会在堆空间中开辟一块空间去存储这个对象，这个对象有一个成员变量（属性）即 a = 10，在 main 方法栈中 m 这个变量存储对象的地址值，通过地址值指向堆空间中的对象
>
> change(i,str,num,arr,m)    调用这个方法去传递参数，基本数据类型传递的是数据值，引用类型传递的是地址值。所以形参的这几个变量会在 change 栈中被声明，然后在栈空间中存相对应的值。i 是基本数据类型，栈空间中存传过来的 1，str 是引用数据类型，栈空间中存传过来的地址值，同样 num  arr m 都是引用类型，在栈空间中存的都是传过来的地址值。

重点解释一下下面的代码：

```java
public static void change(int j, String s, Integer n, int[] a, MyData m) {
        s += "world";
        n += 1;
    }

// 这里的 s 是 String 类型，n 是 Integer 类型，包装类，String 类型和包装类都是不可变的，我们知道这里的 s 接收到的是一个地址，指向常量池中的 hello，但是我们做了 s+="world" ，因为 String 是不可变的，所以它又会在常量池中开辟一块空间存 "helloworld" 这个常量，然后让 s 指向 "helloworld" 这个常量，包装类 n 同理，会在堆空间中开辟一块空间存 201 ，然后让 n 执行这个 201
```



### 五、成员变量和局部变量

局部变量与成员变量的区别：

> - 声明的位置
>   - 局部变量：方法体中，形参，非静态代码块中
>   - 成员变量：类中方法外
>     - 类变量：有 static 修饰
>     - 实例变量：没有 static 修饰
> - 修饰符
>   - 局部变量：只能用 final 修饰
>   - 成员变量：public  protected   private   final   static   volatile   transient
> - 值存储的位置
>   - 局部变量：栈
>   - 实例变量：堆
>   - 类变量：方法区
> - 作用域
>   - 局部变量：从声明处开始，到所属的大括号结束
> - 生命周期
>   - 局部变量：每一个线程，每一次调用执行都是新的生命周期
>   - 实例变量：随着对象的创建而初始化，随着对象的被回收而消亡，每一个对象的实例变量是独立的
>   - 类变量：随着类的初始化而初始化，随着类的卸载而消亡，该类的所有对象的类变量是共享的

堆、栈、方法区：

> 堆（Heap）：此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例都在这里分配内存。这一点在 Java 虚拟机规范中的描述是：所有的对象实例以及数组都要在堆上分配
>
> 栈（Stack）：虚拟机栈。虚拟机栈用于存储局部变量表等。局部变量表存放了编译期可只长度的各种基本数据类型（byte、short、int、long、float、double、char、boolean）、对象引用。方法执行完自动释放。
>
> 方法区（Method Area）：用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据

```java
public class Exam5 {
    static int s;  // 类变量
    int i;  // 成员变量，实例变量
    int j;  // 成员变量，实例变量
    {
        int i = 1;  // i 局部变量
        i++;
        j++;
        s++;
    }
    public void test(int j) {  // 形参 j 局部变量
        j++;
        i++;
        s++;
    }

    public static void main(String[] args) { // 形参 args 局部变量
        Exam5 obj1 = new Exam5();  // obj1 局部变量
        Exam5 obj2 = new Exam5();  // obj2 局部变量
        obj1.test(10);
        obj1.test(20);
        obj2.test(30);
        System.out.println(obj1.i + "," + obj1.j + "," + obj1.s);
        System.out.println(obj2.i + "," + obj2.j + "," + obj2.s);
    }
}
```

![成员变量与局部变量](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-%E9%9D%A2%E8%AF%95%E9%A2%98/%E6%88%90%E5%91%98%E5%8F%98%E9%87%8F%E4%B8%8E%E5%B1%80%E9%83%A8%E5%8F%98%E9%87%8F.png?q-sign-algorithm=sha1&q-ak=AKIDyaYcdagBQ5H4eCBcScu7NdjyQNAlstRa&q-sign-time=1559476751;1559480351&q-key-time=1559476751;1559480351&q-header-list=&q-url-param-list=&q-signature=650b44fb160b57b37a5bd79de0e53764e100649e&x-cos-security-token=63139b257a140e56733e76d9759763b31665498810001)



### 六、SpringMVC 解决 POST 、GET 请求中文乱码问题

1）、解决 POST 请求乱码问题

通过在 web.xml 配置字符编码过滤器

```xml
<filter>
		<filter-name>characterEncodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<!--要使用的字符集，一般我们使用UTF-8(保险起见UTF-8最好)-->
            <!-- 这里其实就是给上面的 CharacterEncodingFilter 中的属性设置值， encoding 是这个类的属性-->
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>characterEncodingFilter</filter-name>
		<!--这里拦截所有的请求 -->
		<url-pattern>/*</url-pattern>
	</filter-mapping>
```

2）、解决 GET 请求乱码问题

通过修改 tomcat 的配置文件 server.xml 中，第一个 Connector 标签，加上 `URIEncoding="UTF-8"` 即可














































# Java9笔记

### 一、概念介绍

> - JEP 
>   - JDK Enhancement Proposals  ：jdk 改进提案，每当需要有新的设想的时候，JEP 可以在 JCP（Java Community Process）之前或者同时提出**非正式的规范**（specification），被正式认可的 JEP 正式写进 JDK 的发展路线图并分配版本号
> - JSR
>   - Java Specification Requests ：Java 规范提案。新特性的规范出现在这一阶段，是指向 JCP（Java Community Process）提出新增一个 **标准化技术规范**的正式请求。请求可以来自于小组/项目 、JEP、JCP 成员或者 Java 社区成员的提案，每个 Java 版本都由相应的 JSR 支持。

### 二、Java9 新特性

#### 1、目录结构变化

1）、Java9 之前的目录结构

![Java9 之前的目录结构](/Users/shenheping/Desktop/Java9之前目录结构.png)



2）、Java9 之后的目录结构

![Java9 之后的目录结构](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/Java9%E4%B9%8B%E5%90%8E%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84.png)

说明：

> 在 JDK9 中
>
> - 删除了 JRE 的子目录
> - `bin` 目录包含所有的命令
> - `conf` 目录包含用户可编辑的配置文件
> - `include` 目录包含编译本地代码时使用的 C/C++头文件
> - `jmods` 目录包含 JMOD 格式的平台模块
> - `legal` 目录包含法律声明
> - `lib` 目录包含一些动态链接库

#### 2、模块化系统

1）、模块化系统产生的背景

> Java 已经发展超过 20 年（95 年发布），Java 和相关生态在不断丰富的同时也暴露出了一些问题。
>
> - Java 运行环境的膨胀和臃肿。每次 JVM 启动的时候，至少会有 30~60M 的内存加载。主要原因是 JVM 需要加载 `rt.jar` ，不管其中的类是否被 classloader 加载，第一步整个 jar 都会被 JVM 加载到内存中去，而模块化可以根据模块的需要加载程序运行需要的 class 。
> - 很难真正的对代码进行封装，而系统并没有对不同部分（也就是 jar 文件）之间的依赖关系有个明确的概念。每一个公共的类都可以被类路径之下任何其他的公共类访问到，这样就会导致无意中使用了并不想被公开访问的 API 。
> - 本质上说，模块（module）的概念，其实就是 package 外再包裹一层，也就是说，用模块来管理各个 package ，通过声明某个 package 暴露，不声明默认就是隐藏。因此模块化是的代码组织上更安全，因为它可以指定哪些部分暴露，哪些部分隐藏。

2）、代码说明

![Java9模块化](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/java9%E6%A8%A1%E5%9D%97%E5%8C%96.png)



① 创建一个 `java9demo` 的工程，然后在这个工程下面创建 2 个 module。一个 `java9A` ，一个 `java9B`。

② 在 `java9A` 中创建一个实体类 Person，然后想在 `java9B` 中引用它，正常情况下是引用不到的，因为是存在两个模块中。

③ 我们在 `java9A` 模块的 `src` 目录下新建 `module-info.java ` 文件

```java
module java9A {
    // 这里 exports 后面跟具体的包名
    exports com.hepingfly.bean;
}
```

④ 我们在 `java9B` 模块的 `src` 目录下新建 `module-info.java ` 文件

```java
module java9demo {
    // requires 后面跟具体要引入的 module 的名字
    requires java9A;
}
```

⑤ 这样在 `java9B` 模块中就能引用 `java9A` 模块暴露出来的实体类了。

```java
public class ModuleTest {
    // 我在 java9B 模块中想引用 java9A 模块中的 Person 类
    public static void main(String[] args) {
        Person person = new Person();
        person.setName("hepingfly");
        System.out.println(person.getName());   // 打印 hepingfly
    }
}
```

#### 3、jshell 命令的使用

1）、产生的背景

像 Python 和 Scala 之类的语言早就有交互式编程环境 REPL（read-evaluate-print-loop）了，以交互的方式对语句和表达式进行求值。开发者只需要输入一些代码，就可以在编译前获得对程序的反馈。而之前的 Java 版本要想执行代码，必须创建文件、声明类、提供测试方法方可实现。

2）、jshell 简单介绍

在 Java9 中终于拥有了 REPL 工具：jShell 。利用 jshell 可以在没有创建类的情况下直接声明变量，计算表达式，执行语句。即开发时可以在命令行里直接运行 Java 代码，无需创建 Java 文件。

3）、基本使用

① JDK9 以上的版本如果你配置好了环境变量直接在命令行输入 `jshell` 即可进入交互界面（原因是 `JDK` 的 `bin` 目录下有 `jshell` 这个工具）

![进入 jshell 交互模式](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/%E8%BF%9B%E5%85%A5jshell%E4%BA%A4%E4%BA%92%E6%A8%A1%E5%BC%8F.png)

② 输入命令获得帮助信息

![获得帮助信息命令](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/%E5%B8%AE%E5%8A%A9%E4%BF%A1%E6%81%AF%E5%91%BD%E4%BB%A4.png)

③ 定义变量，计算变量值

![定义变量计算变量](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/%E5%AE%9A%E4%B9%89%E5%8F%98%E9%87%8F%E8%AE%A1%E7%AE%97%E5%8F%98%E9%87%8F.png)

④ 定义方法，调用方法

![定义方法调用方法](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/%E5%AE%9A%E4%B9%89%E5%92%8C%E8%B0%83%E7%94%A8%E6%96%B9%E6%B3%95.png)

⑤ 导入指定的包

![导入指定包](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/%E5%AF%BC%E5%85%A5%E6%8C%87%E5%AE%9A%E7%9A%84%E5%8C%85.png)

⑥ 列出当前 session 内所有有效的代码片段

![list命令](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/list%E5%91%BD%E4%BB%A4.png)

⑦ 查看当前 session 下所有创建过的变量

![var命令](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/var%E5%91%BD%E4%BB%A4.png)

⑧ 查看当前 session 下所有创建过的方法

![methods命令](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/methods%E5%91%BD%E4%BB%A4.png)

⑨ 从外部文件加载源代码

源代码：

```java
// hello.java 文件中的内容
public void hello() {
 System.out.println("helllo hepingfly");
}
hello();
```

![open命令](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/open%E5%91%BD%E4%BB%A4.png)

⑩ 退出 `jshell`

![exit命令](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/exit%E5%91%BD%E4%BB%A4.png)

注：

> - `jshell` 可以使用 tab 键进行自动补全
> - jshell 可以从文件中加载语句或者将语句保存到文件中
> - 在 jshell 中定义相同方法名和参数列表的方法，即为对现有方法的覆盖。
> - 在 jshell 中你会发现不会出现编译时异常，因为 jshell 帮你隐藏了。

#### 4、接口中定义私有方法

在 Java9 中，接口更加灵活和强大，**方法的访问权限修饰符可以声明为 private 的了**，此时方法将不会成为你对外暴露的 API 的一部分。

关于接口中可以声明的内容，不同版本之间的对比：

① JDK7：

> JDK7：
> 1）、只能声明全局常量（public static final）
> 2）、抽象方法（public abstract）

② JDK8：

> JDK8：
> 1）、可以声明静态方法
> 2）、可以声明默认方法
> 静态方法和默认方法在接口中都是有方法体的， 也就意味着我们可以通过
> 接口名去调用这个静态方法，或者通过这个接口的实现类对象来调用默认方法

③ JDK9：

> JDK9：
> 1）、支持声明私有方法



```java
/**
 * java9 接口
 * @author hepingfly
 * @date 2020/10/24 2:31 下午
 */
public interface Java9Interface {

    /**
     * JDK7：
     * 1）、只能声明全局常量（public static final）
     * 2）、抽象方法（public abstract）
     * @author hepingfloy
     * @date  2020/10/24 2:33 下午
     */
    void hello();

    /**
     * JDK8：
     * 1）、可以声明静态方法
     * 2）、可以声明默认方法
     * 静态方法和默认方法在接口中都是有方法体的， 也就意味着我们可以通过
     * 接口名去调用这个静态方法，或者通过这个接口的实现类对象来调用默认方法
     * @author hepingfly
     * @date  2020/10/24 2:36 下午
     */
    static void hello2() {
        System.out.println("hepingfly");
    }

    default void hello3() {
        System.out.println("hepingfly");
    }

    /**
     * JDK9：
     * 1）、支持声明私有方法
     * @author hepingfly
     * @date  2020/10/24 2:53 下午
     */
    private void hello4() {
        System.out.println("hepingfly");
    }
}
```

![Java 各版本接口对比](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/Java%E5%90%84%E7%89%88%E6%9C%AC%E6%8E%A5%E5%8F%A3%E5%AF%B9%E6%AF%94.png)

#### 5、钻石操作符使用升级

可以与匿名实现类共同使用钻石操作符。

```java
public class DiamondOperator {
    public static void main(String[] args) {
        // java8 中这么写会报错,java9 可以
        Set<String> set = new HashSet<>(){};
    }
}
```

#### 6、try 语句结构升级

在 Java9 中可以在 try 子句中使用已经初始化过的资源。

```java
public class Java9Try {

    /**
     *  Java7 中关于异常，我们需要手动去处理，去关闭
     * @author hepingfly
     * @date  2020/10/24 4:09 下午
     */
    public void testJava7() {
        InputStream is = null;
        try {
            is = new FileInputStream(new File("a.txt"));
            System.out.println("hepingfly");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (null != is) {
                    is.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
     }

     public void testJava8() {
         /**
          * 在 try 后面的小括号中声明的一些资源，它会自动的关闭，就不需要你手动的去关了
          * 在 Java8 中要求资源对象的实例化，必须放在 try 后面的小括号内
          */
        try (InputStream is = new FileInputStream(new File("a.txt"))) {
            System.out.println("hepingfly");
        } catch (Exception e) {
            e.printStackTrace();
        }
     }

     public void testJava9() {
         /**
          * 可以在 try 后面的小括号中调用已经实例化好的资源对象
          * 多个资源对象以分号分隔
          */
         InputStreamReader is = new InputStreamReader(System.in);
         OutputStreamWriter os = new OutputStreamWriter(System.out);
         try (is;os) {
             System.out.println(is.read());
         } catch (Exception e) {
             e.printStackTrace();
         }
     }
}
```



#### 7、下划线命名标识符的限制

```java
public class Java9Underline {
    public static void main(String[] args) {
        // _str 可以编译通过
        String _str = "hello";
        // 单独一个 _ 作为变量名，无法编译通过
        String _ = "hello";
    }
}
```



#### 8、String 底层存储结构的变化

JDK8 之前，底层使用 char[] 存储，JDK9 底层使用 byte[] 存储。

![JDK9-String底层存储](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/JDK9-String%E5%BA%95%E5%B1%82%E5%AD%98%E5%82%A8.png)

#### 9、创建只读集合

1）、Java8 创建只读集合方式

```java
public static void testJava8() {
    // 创建一个只读的 list
    List<String> list = Collections.unmodifiableList(Arrays.asList("heping","fly"));
    list.forEach(System.out::println);
    // 创建一个只读的 set
    Set<String> set = Collections.unmodifiableSet(new HashSet<>(Arrays.asList("heping","fly")));
    set.forEach(System.out::println);
    // 创建一个只读的 map
    Map<Object, Object> map = Collections.unmodifiableMap(new HashMap<>() {
        {
            put("name", "hepingfly");
            put("age", "18");
        }
    });
    map.forEach((x,y) -> System.out.println(x + ":" + y));
}
```

2）、Java9 创建只读集合方式

```java
public static void testJava9() {
    // 创建一个只读的 list
    List<String> list = List.of("heping", "fly");
    // 创建一个只读的 set
    Set<String> set = Set.of("heping", "fly");
    // 创建一个只读的 map
    Map<String, String> map = Map.of("name", "heping", "age", "18");
    Map<String, String> map2 = Map.ofEntries(Map.entry("name", "heping"), Map.entry("age", "12"));
}
```



#### 10、StreamAPI 增强

在 Java9 中，Stream API 变的更好，Stream 接口中添加了 4 个新的方法，`dropWhile` 、 `takeWhile` 、`ofNullable` 、 还有个 iterator 方法的新重载方法。

除了对 Stream 本身的扩展，Optional 和 Stream 之间的结合也得到了改进。现在可以通过 Optional 的新方法 stream() 将一个 Optional 对象转换为一个（可能是空的）Stream 对象。

① takeWhile

> 用于从 Stream 中获取一部分数据，接收一个 predicate 来进行选择。在有序的 Stream 中，takeWhile 返回从开头开始的尽量多的元素。

```java
/**
  * takeWhile 方法
 */
public static void test1() {
    List<Integer> list = Arrays.asList(13,11,45,18,20,16,96);
    Stream<Integer> stream = list.stream();
    /**
         * 下面这行代码的意思就是：
         * 当满足 x < 20 这个条件的时候我就 take(取)
         * ① 13 小于 20 满足，我进行 take 然后输出
         * ② 11 小于 20 满足，我进行 take 然后输出
         * ③ 45 不小于 20 ，那么就停止，即使后面有小于 20 的数，我到这也停止了，后面的就不输出了
         */
    stream.takeWhile((x) -> x < 20).forEach(System.out::println);
}
```

② dropWhile 

> dropWhile 的行为与 takeWhile 相反，返回剩余的元素。

```java
public static void test2() {
    List<Integer> list = Arrays.asList(13,11,45,18,20,16,96);
    Stream<Integer> stream = list.stream();
    /**
         * 下面这行代码的意思就是：
         * 当满足 x < 20 这个条件的时候我就 drop(丢)
         * ① 13 小于 20 满足，我进行 drop 丢掉
         * ② 11 小于 20 满足，我进行 drop 丢掉
         * ③ 45 不小于 20 ，那么就停止，后面所有的数我都不进行 drop 了，即使有小于 20 的数
         */
    stream.dropWhile((x) -> x < 20).forEach(System.out::println);
}
```

③ ofNullable 的使用

> Java8 中 Stream 不能完全为 Null ，否则会报空指针异常。而 Java9 中的 ofNullable 方法允许我们创建一个单元素 Stream ,可以包含一个非空元素，也可以创建一个空 Stream 。

```java
public static void test3() {
    Stream<Integer> stream = Stream.of(1, 2, 3, null);
    stream.forEach(System.out::println);

    // 如果只有单个元素，此元素不能为 null,否则报空指针异常
    Stream<Object> stream1 = Stream.of(null);

    // Java9 新增 ofNullable 允许单个元素为空
    Stream<Object> stream2 = Stream.ofNullable(null);
    System.out.println(stream2.count());   // 0
}
```

④ iterate 函数重载

```java
public static void test4() {
    // 需要通过 limit 来控制终止
    Stream.iterate(0, x -> x + 1).limit(10).forEach(System.out::println);

    // 这里就可以通过传入一个断言型函数来进行终止
    Stream.iterate(0, x -> x < 10, x -> x + 1).forEach(System.out::println);
}
```



#### 11、Optional 提供的 Stream 方法

```java
public static void test1() {
    List<String> list = Arrays.asList("a","b","c");
    Optional<List<String>> optional = Optional.ofNullable(list);
    // optional 类中提供了转换为 stream 的方法
    Stream<List<String>> stream = optional.stream();
    stream.forEach(System.out::println);
}
```


































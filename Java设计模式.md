#  Java设计模式

## 设计模式原则

设计模式是对软件设计中普遍存在（反复出现）的各种问题，所提出的解决方案。

**设计模式七大原则**

> - 单一职责原则
> - 接口隔离原则
> - 依赖倒转原则
> - 里式替换原则
> - 开闭原则
> - 迪米特法则
> - 合成复用原则

### 单一职责原则

**基本介绍：**

> 对类来说，即一个类应该只负责一项职责。假设类A 负责两个不同的职责：职责1和职责2。当职责1需求变更而改变A 时，可能造成职责2执行错误，所以需要将类A 的粒度分解为 A1，A2。

**注意事项和细节：**

> - 降低类的复杂度，一个类只负责一项职责
> - 提高类的可读性，可维护性
> - 降低变更引起的风险
> - 通常情况下，我们应当遵守单一职责原则，只有逻辑足够简单，才可以在代码级违反单一职责原则。只有类中方法数量足够少，可以在方法级别保持单一职责原则

代码可以参考：

`<https://github.com/hepingfly/heping_DesignPattern/tree/master/src/com/hepingfly/principle/single>`

### 接口隔离原则

**基本介绍：**

> 客户端不应该依赖它不需要的接口，即一个类对另一个类的依赖应该建立在最小的接口上

**说明：**

> 类A 通过接口 Interface1 依赖类B，类C 通过接口 Interface1 依赖类D，如果接口 Interface1 对于类A 和类C 来说，不是最小接口。那么类B 和类D 必须去实现他们不需要的方法。
>
> 因此按照接口隔离原则应该这么处理：
>
> 将接口 Interface1 拆分为几个独立的接口，类A 和类C 分别与他们需要的接口建立依赖关系

代码参考：

`<https://github.com/hepingfly/heping_DesignPattern/tree/master/src/com/hepingfly/principle/segregation>`

### 依赖倒转原则

**基本介绍：**

> - 高层模块不应该依赖低层模块，二者都应该依赖其抽象
> - 抽象不应该依赖细节，细节应该依赖抽象
> - 依赖倒转原则的中心思想是面向接口编程
> - 依赖倒转原则是基于这样的设计理念：相对于细节的多变性，抽象的东西要稳定的多。以抽象为基础搭建的架构比以细节为基础的架构要稳定的多。在 Java 中，抽象指的是接口或抽象类，细节就是具体的实现类
> - 使用接口或抽象类的目的是制定好规范，而不涉及任何具体的操作，把展现细节的任务交给他们的实现类去完成

**依赖关系传递的三种方式：**

> 1、接口传递
>
> 2、构造方法传递
>
> 3、setter 方式传递

**使用前：**

```java
/**
 * 依赖倒转原则
 */
public class DependecyInversion {
    public static void main(String[] args) {
        Person person = new Person();
        person.receive(new Email());
    }
}

class Email {
    public String getInfo() {
        return "电子邮件信息：hello,world";
    }
}

/**
 * 完成 Person 接收消息的功能
 * 方式一（下面这种方式）分析：
 * 1. 简单，比较容易想到
 * 2. 如果我们获取的对象是微信，短信等，则需要新增类，同时 Person 也需要增加相应的接收方法
 * 3.解决方式：
 *      引入一个抽象的接口 IReceiver ，表示接收者，这样 Person 类与接口 IReceiver 发生依赖
 *      因为 Email wechat 等属于接收的参数，让他们各自实现 IReceiver 接口就行
 */
class Person {
    public void receive(Email email) {
        System.out.println(email.getInfo());
    }
}
```

**使用后：**

```java
/**
 * 依赖倒转原则
 */
public class DependecyInversion {
    public static void main(String[] args) {
        Person person = new Person();
        person.receive(new Email());
        person.receive(new Wechat());
    }
}

interface IReceiver {
    public String getInfo();
}

// 电子邮件
class Email implements IReceiver {
    public String getInfo() {
        return "电子邮件信息：hello,world";
    }
}

// 微信
class Wechat implements IReceiver {

    @Override
    public String getInfo() {
        return "电子邮件信息：wechat";
    }
}


class Person {
    public void receive(IReceiver receiver) {
        System.out.println(receiver.getInfo());
    }
}
```

### 里式替换原则

**关于面向对象继承性的思考和说明：**

> - 继承包含这样一层含义：父类中凡是已经实现好的方法，实际上是在设定规范和契约，虽然它不强制要求所有的子类必须遵循这些契约，但是如果子类对这些已经实现的方法任意修改，就会对整个继承体系造成破坏。
> - 继承在给程序设计带来便利的同时，也带来了弊端。比如使用继承会给程序带来侵入性，程序的可移植性降低，增加对象间的耦合性。如果一个类被其它的类所继承，则当这个类需要修改时，必须考虑到所有的子类，并且父类修改后，所有涉及到子类的功能都有可能产生故障。

**基本介绍：**

> - 里式替换原则在 1998 年，由麻省理工学院的一位姓里的女士提出来的
> - 总的来说，表达的意思就是，所有引用基类的地方必须能透明的使用其子类对象
> - 在使用继承时，遵循里式替换原则，在子类中尽量不要重写父类的方法
> - 里式替换原则告诉我们，继承实际上让两个类耦合性增强了，在适当的情况下，可以通过聚合、组合、依赖来解决问题。

```java
/**
 * 里氏替换原则
 */
public class Liskov {
    public static void main(String[] args) {
        A a = new A();
        System.out.println("3-2=" + a.func1(3,2));
        B b = new B();
        // 其实这里我想要调用 A 类中的方法，但是我 B 类重写了，所以这里调用的是 B 类方法
        System.out.println("4-2=" + b.func1(4,2));   // 就可能导致原来运行正常的相减功能发生错误

    }
}

class A {
    // 返回两个数的差
    public int func1(int num1, int num2) {
        return num1 - num2;
    }
}

// B 继承
class B extends A {
    // 在这里可能无意间重写了 A 类的方法
    public int func1(int a, int b) {
        return a + b;
    }
    public int func2(int a, int b) {
        return func1(a,b) + 9;
    }
}
```

**解决方法：**

> - 我们发现原来正常的相减功能发生了错误。原因就是类B 无意中重写了父类的方法，造成原有功能出现错误。在实际编程中，我们常常会通过重写父类的方法完成新的功能，这样写起来虽然简单，但整个继承体系的复用性会比较差。特别是运行多态比较频繁的时候。
> - 通用的做法是：原来的父类和子类都继承一个更通俗的基类，原有的继承关系去掉，采用依赖，聚合，组合等关系代替。

```java
/**
 * 里氏替换原则
 */
public class Liskov {
    public static void main(String[] args) {
        A a = new A();
        System.out.println("3-2=" + a.func1(3,2));
        B b = new B(a);
        // 因为 B 不再继承 A ，因此调用者就不会误以为这是调用 A 中的方法了
        System.out.println("1+2=" + b.func1(1,2));

        // 如果想调用 A 中的方法
        System.out.println("4-2=" +b.func3(4,2));

    }
}

// 创建一个更加基础的基类
class Base {
    // 把更加基础的方法和成员写到 Base 类
}

class A extends Base {
    // 返回两个数的差
    public int func1(int num1, int num2) {
        return num1 - num2;
    }
}

// B 继承
class B extends Base {
    // 在这里可能无意间重写了 A 类的方法
    public int func1(int a, int b) {
        return a + b;
    }
    public int func2(int a, int b) {
        return func1(a,b) + 9;
    }

    // 如果 B 想使用 A 中的方法
    A a;
    public B(A a) {
        this.a = a;
    }
    public int func3(int num1, int num2) {
        return a.func1(num1,num2);
    }
}
```

### 开闭原则

**基本介绍：**

> - 开闭原则是编程中最基础，最重要的设计原则
> - 一个软件实体，如：类，模块，和函数应该对扩展（提供方）开放，对修改（使用方）关闭。用抽象构建框架，用实现扩展细节。
> - 当程序需要变化时，尽量通过扩展软件实体的行为来实现变化，而不是通过修改已有的代码来实现变化
> - 编程中遵循其他原则，以及使用设计模式的目的就是遵循开闭原则

**使用前：**

```java
/**
 * 开闭原则
 * 下面代码设计的方式
 * 优点是比较好理解，简单易操作
 * 缺点是违反了 ocp 原则，即当我们给类增加新功能的时候，尽量不修改代码，或尽可能少的修改代码
 * 比如，我们要绘制一个三角形，会发现需要修改大量代码
 */
public class Ocp {
    public static void main(String[] args) {
        GraphicEditor ge = new GraphicEditor();
        ge.drawShape(new Rectangle());
        ge.drawShape(new Circle());
    }
}

// 绘图类
class GraphicEditor {
    public void drawShape(Shape shape) {
        if (shape.type == 1) {
            drawRectangle(shape);
        } else if (shape.type == 2) {
            drawCircle(shape);
        }

    }
    public void drawRectangle(Shape shape) {
        System.out.println("矩形");
    }
    public void drawCircle(Shape shape) {
        System.out.println("圆形");
    }

}

// Shape类，基类
class Shape {
    int type;
}

class Rectangle extends Shape {
    public Rectangle() {
        super.type = 1;
    }
}

class Circle extends Shape {
    public Circle() {
        super.type = 2;
    }
}
```

**使用后：**

```java
/**
 * 改进思路：
 * 把 Shape 做成抽象类，并提供一个抽象的 draw 方法，让子类去实现即可
 * 这样有新的图形种类时，只需要让新的图形类继承 Shape,并实现 draw 方法，这样使用方就不需要修改代码
 * 满足了开闭原则
 */
public class Ocp {
    public static void main(String[] args) {
        GraphicEditor ge = new GraphicEditor();
        ge.drawShape(new Rectangle());
        ge.drawShape(new Circle());
        // 可以很方便的进行扩展，调用方不需要改动，只需要新增一个类,对扩展开放，对修改关闭
        ge.drawShape(new Triangle());
    }
}

// 绘图类
class GraphicEditor {
    public void drawShape(Shape shape) {
        shape.draw();
    }
}

// Shape类，基类
abstract class Shape {
    int type;

    protected abstract void draw();
}

class Rectangle extends Shape {
    public Rectangle() {
        super.type = 1;
    }

    @Override
    protected void draw() {
        System.out.println("绘制矩形");
    }
}

class Circle extends Shape {
    public Circle() {
        super.type = 2;
    }

    @Override
    protected void draw() {
        System.out.println("绘制圆形");
    }
}

// 假如我现在想扩展一个类
class Triangle extends Shape {
    @Override
    protected void draw() {
        System.out.println("绘制三角形");
    }
}
```

### 迪米特法则

**基本介绍**

> - 一个对象应该对其他对象保持最少的了解
> - 类与类关系越密切，耦合度越大
> - 迪米特法则又叫最少知道原则，即一个类对自己依赖的类知道的越少越好。也就是说，对于被依赖的类不管多么复杂，都尽量将逻辑封装在类的内部
> - 迪米特法则还有个更简单的定义：只与直接的朋友通信
> - 直接的朋友：每个对象都会与其他的对象有耦合关系，只要两个对象之间有耦合关系，我们就说这两个对象之间是朋友关系。耦合的方式有很多，依赖，关联，组合，聚合等。其中，**我们称出现成员变量，方法参数，方法返回值中的类为直接的朋友**，而出现在局部变量中的类不是直接的朋友。也就是说，陌生的类最好不要以局部变量的方式出现在类的内部

**注意事项：**

> - 迪米特法则的核心是降低类之间的耦合
> - 但是注意：由于每个类都减少了不必要的依赖，因此迪米特法则只是要求降低类间的耦合关系，并不是要求完全没有依赖关系

**使用前：**

```java
public class Demeter1 {
    public static void main(String[] args) {
        SchoolManager schoolManager = new SchoolManager();
        schoolManager.printAllEmployee(new CollegeManager());
    }
}

// 学校总部员工类
class Employee {
    private String id;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }
}
// 学院的员工
class CollegeEmployee {
    private String id;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }
}

class CollegeManager {
    // 返回学院总部员工
    public List<CollegeEmployee> getAllCollegeEmployee() {
        List<CollegeEmployee> list = new ArrayList<CollegeEmployee>();
        for (int i = 0; i < 10; i++) {
            CollegeEmployee employee = new CollegeEmployee();
            employee.setId("员工id" + i);
            list.add(employee);
        }
        return list;
    }
}

/**
 * SchoolManager 的直接朋友有：Employee   CollegeManager
 * CollegeEmployee 不是直接朋友，而是一个陌生类，违背了迪米特法则
 */
class SchoolManager {
    // 返回学校总部员工
    public List<Employee> getAllCollegeEmployee() {
        List<Employee> list = new ArrayList<Employee>();
        for (int i = 0; i < 5; i++) {
            Employee employee = new Employee();
            employee.setId("员工id" + i);
            list.add(employee);
        }
        return list;
    }

    public void printAllEmployee(CollegeManager collegeManager) {
        /*
           这里的 CollegeEmployee 不是 SchoolManager 的直接朋友
           而是以局部变量的方式，出现在 SchoolManager 中
           这样就违反了迪米特法则
         */
        List<CollegeEmployee> list1 = collegeManager.getAllCollegeEmployee();
        System.out.println("---------学院员工-------");
        for (CollegeEmployee ce : list1) {
            System.out.println(ce.getId());
        }

        System.out.println("---------学校员工-------");
        List<Employee> list2 = this.getAllCollegeEmployee();
        for (Employee emp :list2) {
            System.out.println(emp.getId());
        }

    }
}
```

**使用后**

```java
public class Demeter1 {
    public static void main(String[] args) {
        SchoolManager schoolManager = new SchoolManager();
        schoolManager.printAllEmployee(new CollegeManager());
    }
}

// 学校总部员工类
class Employee {
    private String id;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }
}

// 学院的员工
class CollegeEmployee {
    private String id;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }
}

class CollegeManager {
    // 返回学院总部员工
    public List<CollegeEmployee> getAllCollegeEmployee() {
        List<CollegeEmployee> list = new ArrayList<CollegeEmployee>();
        for (int i = 0; i < 10; i++) {
            CollegeEmployee employee = new CollegeEmployee();
            employee.setId("员工id" + i);
            list.add(employee);
        }
        return list;
    }

    // 输出学院员工的信息
    public void printCollegeEmployee() {
        List<CollegeEmployee> employees = getAllCollegeEmployee();
        System.out.println("------------学院员工信息------------");
        for (CollegeEmployee emp : employees) {
            System.out.println(emp.getId());
        }
    }
}

/**
 * 改进后的 SchoolManager
 */
class SchoolManager {
    // 返回学校总部员工
    public List<Employee> getAllCollegeEmployee() {
        List<Employee> list = new ArrayList<Employee>();
        for (int i = 0; i < 5; i++) {
            Employee employee = new Employee();
            employee.setId("员工id" + i);
            list.add(employee);
        }
        return list;
    }

    public void printAllEmployee(CollegeManager collegeManager) {
        /*
           将方法直接写到 collegeManager 里面去，降低耦合
         */
        collegeManager.printCollegeEmployee();

        System.out.println("---------学校员工-------");
        List<Employee> list2 = this.getAllCollegeEmployee();
        for (Employee emp :list2) {
            System.out.println(emp.getId());
        }

    }
}
```



## 设计模式使用

设计模式分为三种类型：

> - 创建型模式
>   - 单例模式、抽象工厂模式、原型模式、建造者模式、工厂模式
> - 结构型模式
>   - 适配器模式、桥接模式、装饰模式、组合模式、外观模式、享元模式、代理模式
> - 行为型模式
>   - 模板方法模式、命令模式、访问者模式、迭代器模式、观察这模式、中介者模式、备忘录模式、解释器模式、状态模式、策略模式、责任链模式

#### 1、单例模式-饿汉式

> 单例模式，就是采取一定的方法保证在整个软件系统中，对某个类只能存在一个对象实例，并且该类只提供一个取得其对象实例的方法

单例模式有 8 种方式：

> - 饿汉式（静态常量）
> - 饿汉式（静态代码块）
> - 懒汉式（线程不安全）
> - 懒汉式（线程安全，同步方法）
> - 懒汉式（线程安全，同步代码块）
> - 双重检查
> - 静态内部类
> - 枚举

```java
/**
 * 单例模式之饿汉式，静态变量
 */
public class SingletonTest01 {
    public static void main(String[] args) {
        Singleton instance1 = Singleton.getInstance();
        Singleton instance2 = Singleton.getInstance();
        System.out.println(instance1 == instance2);
    }
}

class Singleton {
    // 1、私有化构造器
    private Singleton() {

    }

    // 2.在对象内部创建对象实例
    private static final Singleton INSTANTCE = new Singleton();

    // 3.提供一个公有的方法，返回对象实例
    public static Singleton getInstance() {
        return INSTANTCE;
    }
}
```

**优缺点说明：**

> 优点：
>
> - 这种方式写法比较简单，就是在类加载的时候就完成了实例化。避免了线程同步问题。
>
> 缺点：
>
> - 在类加载的时候就完成实例化，没有达到懒加载的效果。如果从始至终从未用过这个实例，则会造成内存浪费

**饿汉式静态代码块**

```java
/**
 * 单例模式之饿汉式，静态代码块
 */
public class SingletonTest02 {
    public static void main(String[] args) {
        Singleton instance1 = Singleton.getInstance();
        Singleton instance2 = Singleton.getInstance();
        System.out.println(instance1 == instance2);
    }
}

class Singleton {
    // 1、私有化构造器
    private Singleton() {
    }
    // 2.在对象内部创建对象实例
    private static Singleton instance;

    static {
        // 在静态代码块中，实例化对象
        instance = new Singleton();
    }

    // 3.提供一个公有的方法，返回对象实例
    public static Singleton getInstance() {
        return instance;
    }
}
```

#### 2、单例模式-懒汉式

**懒汉式-线程不安全**

```java
/**
 * 懒汉式-线程不安全
 */
public class SingletonTest03 {
    public static void main(String[] args) {
        Singleton instance1 = Singleton.getInstance();
        Singleton instance2 = Singleton.getInstance();
        System.out.println(instance1 == instance2);
    }
}
class Singleton {
    private static Singleton instance;
    private Singleton() {

    }

    // 提供一个静态的公有方法，当使用到该方法时，才去创建 instance
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

**优缺点：**

> - 起到了懒加载的效果，但是只能在单线程下使用
> - 如果在多线程下，一个线程进入  `if (instance == null)` 语句块，还未来得及往下执行，另一个线程也通过了这个判断语句，这时便会产生多个实例。 

**懒汉式-线程安全**

```java
/**
 * 懒汉式-线程安全
 */
public class SingletonTest04 {
    public static void main(String[] args) {
        Singleton instance1 = Singleton.getInstance();
        Singleton instance2 = Singleton.getInstance();
        System.out.println(instance1 == instance2);
    }
}

class Singleton {
    private static Singleton instance;
    private Singleton() {

    }

    // 使用 synchronized 解决线程安全问题
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

优缺点：

> - 解决了线程不安全问题
> - 效率太低了，一个线程必须等另一个线程执行完释放锁，才能继续执行

#### 3、单例模式-双重检查

```java
/**
 * 单例模式之双重检查
 */
public class SingletonTest06 {
    public static void main(String[] args) {
        Singleton instance1 = Singleton.getInstance();
        Singleton instance2 = Singleton.getInstance();
        System.out.println(instance1 == instance2);
    }
}

class Singleton {
    private static volatile Singleton instance = null;
    private Singleton() {
    }

    public static Singleton getInstance() {
        /**
         * 假如说现在有两个线程都进入到 instance == null 这个条件里面
         * 进去之后由于有 synchronized，那么只会有一个线程握住锁，握住锁以后
         * 假设这时候 instance 为空，那么这个线程就会去创建实例，创建完实例后
         * 线程执行结束，这个时候另一个线程握住锁，进来之后会再判断一次 instance 是否为空
         * 显然这时候 instance 已经不为空了，则不创建实例
         * 同时，如果下次在获取实例，在第一层判断就会被拦下，直接返回 instance，
         * 避免了同步方法被多次执行
         */
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

**优缺点：**

> - 双重检查是多线程开发中经常使用到的，在代码中我们对 instance 进行了两次为空检查，这样就可以保证线程安全了
> - 这样实例化代码只用执行一次，后面再次访问时，判断 instance 是否为空，直接 return 实例化对象，也避免进行反复方法同步
> - 这种方式在开发中推荐使用

#### 4、单例模式-静态内部类

```java
package com.hepingfly.singleton.type7;

/**
 * 单例模式静态内部类
 */
public class SingletonTest07 {
    public static void main(String[] args) {
        Singleton instance1 = Singleton.getInstance();
        Singleton instance2 = Singleton.getInstance();
        System.out.println(instance1 == instance2);
    }
}
class Singleton {
    private Singleton() {

    }
    private static class SingletonInstance {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance() {
        return SingletonInstance.INSTANCE;
    }
}

```

**优缺点：**

> - 这种方式采用了类装载机制来保证初始化实例时只有一个线程
> - 静态内部类方式在 Singleton 类被装载时并不会立即实例化，而是在需要实例化时，调用 getInstance 方法，才会装载 SingletonInstance 类，从而完成 Singleton 的实例化
> - 类的静态属性只会在第一次加载类的时候初始化，所以在这里 JVM 帮助了我们保证线程的安全性，在类进行初始化时，别的线程是无法进入的
> - 优点：避免了线程不安全，利用静态内部类特点实现延迟加载，效率高

#### 5、单例模式-枚举

```java
/**
 * 单例模式之枚举
 */
public class SingletonTest08 {
    public static void main(String[] args) {
        Singleton instance1 = Singleton.INSTANCE;
        Singleton instance2 = Singleton.INSTANCE;
        System.out.println(instance1 == instance2);
    }
}

enum Singleton {
    INSTANCE;
}
```

#### 6、简单工厂模式

**需求：**

> 一个披萨的项目：要便于披萨种类的扩展，要便于维护
>
> - 披萨的种类很多（比如：GreekPizz、CheezePiza）
> - 披萨的制作有  prepare   bake  cut   box
> - 完成披萨店订购功能

**基本介绍：**

> - 简单工厂模式是属于创建型模式，是工厂模式的一种。**简单工厂模式是由一个工厂对象决定创建出哪一种产品类的实例。**简单工厂模式是工厂模式家族中最简单实用的模式
> - 简单工厂模式：定义一个创建对象的类，由这个类来封装实例化对象的行为
> - 在软件开发中，当我们会用到大量的创建某种、某类或者某批对象时，就会用到工厂模式。

代码见：

`<https://github.com/hepingfly/heping_DesignPattern/tree/master/src/com/hepingfly/principle/factory/simplefactory/pizzastore>`

#### 7、工厂方法模式

**新的需求：**

> 客户在点披萨时，可以点不同口味的披萨，比如：北京的奶酪披萨、北京的胡椒披萨、伦敦的奶酪披萨、伦敦的胡椒披萨

**思路1：**

> 使用简单工厂模式，创建不同的简单工厂类，比如 BJPizzaSimpleFactory 、LDPizzaSimpleFactory 等等。从当前的案例来说也是可以的，但是考虑到项目的规模，以及软件的可维护性、可扩展性并不是很好。

**思路2：**

> 使用工厂方法模式

**工厂方法模式定义：**

> 定义一个创建对象的抽象方法，由子类决定要实例化的类。工厂方法模式**将对象的实例化推迟到子类**

代码见：

<https://github.com/hepingfly/heping_DesignPattern/tree/master/src/com/hepingfly/principle/factory/factorymethod/pizzastore>

#### 8、抽象工厂模式

**基本介绍：**

> - 抽象工厂模式：定义了一个 interface 用于创建相关或有依赖关系的对象簇，而无需指明具体的类
> - 抽象工厂模式可以将简单工厂模式和工厂方法模式进行整合
> - 从设计层面看，抽象工厂模式就是对简单工厂模式的改进（或者称为进一步的抽象）
> - 将工厂抽象成两层，抽象工厂和具体实现的工厂子类。程序员可以根据创建对象类型使用对应的工厂子类。这样将单个的简单工厂变成了工厂簇，更利于代码的维护和扩展。

代码见：

`<https://github.com/hepingfly/heping_DesignPattern/tree/master/src/com/hepingfly/principle/factory/absfactory/pizzastore>`

**工厂模式小结：**

> 1、工厂模式的意义：
>
> 将实例化对象的代码提取出来，放到一个类中统一管理和维护，达到和主项目依赖关系的解耦
>
> 2、三种工厂模式：
>
> 简单工厂模式、工厂方法模式、抽象工厂模式
>
> 3、设计模式的依赖抽象原则
>
> ① 创建对象实例时，不要直接 new 而是把这个 new 的动作放到一个工厂方法中，并返回
>
> ② 不要让类继承具体类，而是继承抽象类或是实现接口
>
> ③ 不要覆盖基类中已经实现的方法

#### 9、原型模式

**需求：**

> 克隆羊问题：
>
> 现在有一只羊 tom ，姓名：tom   年龄： 1   颜色：白色，请编写程序创建和 tom 羊属性完全相同的 10 只羊 

**传统方法**

```java
public class Sheep {
    private String name;
    private int age;
    private String color;
    // getter setter....   
}

//---------------------------------------------
public class Client {
    public static void main(String[] args) {
        Sheep sheep = new Sheep("tom",1,"白色");
        Sheep sheep1 = new Sheep(sheep.getName(),sheep.getAge(),sheep.getColor());
        System.out.println(sheep);
        System.out.println(sheep1);
    }
}
```

**传统方法的优缺点：**

> - 优点是比较好理解，简单易操作
> - 在创建新的对象时，总是需要重新获取原始对象的属性，如果创建的对象比较复杂，效率较低
> - 总是需要重新初始化对象，而不是动态的获得对象运行时的状态，不够灵活

改进的思路：

> Java 中 Object 类是所有类的根类，Object 类提供了一个 clone() 方法，该方法可以将一个 Java 对象复制一份，但是必须要实现 Cloneable 接口，该接口表示该类能够复制并且具有复制的能力。

原型模式基本介绍：

> - 原型模式是指：用原型实例指定创建对象的种类，并且通过拷贝这些原型，创建新的对象
> - 原型模式是一种创建型设计模式，允许一个对象再创建另外一个可定制的对象，无需知道如何创建的细节
> - 工作原理：通过将一个原型对象传给那个要发动创建的对象，这个要发动创建的对象通过请求原型对象拷贝它们自己来实施创建

**改进后：**

```java
public class Sheep implements Cloneable {
    private String name;
    private int age;
    private String color;
    
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

//--------------------------------------
public class Client {
    public static void main(String[] args) throws CloneNotSupportedException {
        Sheep sheep = new Sheep("tom",1,"白色");
        Sheep sheep1 = (Sheep) sheep.clone();   // 会把对象属性都拷贝一份
        System.out.println(sheep);
        System.out.println(sheep1);
    }
}
```

#### 10、浅拷贝、深拷贝

**浅拷贝介绍**

> - 对于数据类型是基本数据类型的成员变量，浅拷贝会直接进行值传递，也就是将该属性值复制一份给新的对象。
> - 对于数据类型是引用类型的成员变量，比如说成员变量时某个数组、某个类的对象，那么浅拷贝会进行引用传递，也就是只将该成员变量的引用值（内存地址）复制一份给新的对象。因为实际上两个对象的成员变量都指向同一个实例，这种情况下，在一个对象中修改该成员变量会影响另一个对象该成员变量的值。
> - Object 类的 clone 方法，默认是浅拷贝

**深拷贝介绍**

> - 复制对象的所有基本数据类型的成员变量值
> - 为所有引用数据类型的成员变量申请存储空间，对象进行深拷贝要对整个对象进行拷贝

**实现深拷贝的方式：**

> - 重新 clone 方法来实现深拷贝
> - 通过对象序列化实现深拷贝

```java
public class DeepCloneableTarget implements Serializable,Cloneable {

    private String cloneName;
    private String cloneClass;

    public DeepCloneableTarget(String cloneName, String cloneClass) {
        this.cloneName = cloneName;
        this.cloneClass = cloneClass;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
//============================================================
public class DeepProtoType implements Serializable,Cloneable {

    private String name;
    private DeepCloneableTarget target;

    /**
     * 深拷贝实现方式1：重写clone 方法
     * @return
     * @throws CloneNotSupportedException
     */
    @Override
    protected Object clone() throws CloneNotSupportedException {
        Object clone = super.clone();
        DeepProtoType d = (DeepProtoType) clone;
        DeepCloneableTarget t = (DeepCloneableTarget)target.clone();
        d.target = t;
        return d;
    }

    public void setTarget(DeepCloneableTarget target) {
        this.target = target;
    }

    /**
     * 实现方式二：
     * 通过对象序列化实现
     * @return
     */
    public Object deepClone() {
        ByteArrayOutputStream bos = null;
        ObjectOutputStream oos = null;
        ByteArrayInputStream bis = null;
        ObjectInputStream ois = null;
        DeepProtoType deepProtoType = null;

        try {
            bos = new ByteArrayOutputStream();
            oos = new ObjectOutputStream(bos);
            oos.writeObject(this);  // 当前这个对象以流的形式输出
            // 反序列化
            bis = new ByteArrayInputStream(bos.toByteArray());
            ois = new ObjectInputStream(bis);
            deepProtoType = (DeepProtoType) ois.readObject();

        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } finally {
            try {
                ois.close();
                bis.close();
                oos.close();
                bos.close();
            } catch (Exception e) {

            }
        }
        return deepProtoType;
    }
}
```

**原型模式注意事项和细节：**

> - 创建新的对象比较复杂时，可以利用原型模式简化对象的创建过程，同时也能够提高效率
> - 不用重新初始化对象，而是动态的获得对象运行时状态
> - 在实现深拷贝的时候，可能需要复杂一点的代码
>
> 缺点：
>
> 需要为每一个类配置一个克隆方法，这对全新的类来说不是很难，但对已有的类进行改造时，需要修改其源代码，违背了 ocp 原则

#### 11、建造者模式

**盖房子需求：**

> - 需要建房子：这一过程为打桩、砌墙、封顶
> - 房子有各种各样的，比如：普通房、高楼、别墅，各种房子的过程虽然一样，但是要求不尽相同

```java
public abstract class AbstractHouse {
    // 打地基
    public abstract void buildBasic();

    // 砌墙
    public abstract void buildWalls();

    // 封顶
    public abstract void roofed();

    public void build() {
        buildBasic();
        buildWalls();
        roofed();
    }
}
// =====================================
public class CommonHouse extends AbstractHouse {
    @Override
    public void buildBasic() {
        System.out.println("普通房子打地基");
    }

    @Override
    public void buildWalls() {
        System.out.println("普通房子砌墙");
    }

    @Override
    public void roofed() {
        System.out.println("普通房子封顶");
    }
}

// =============================================
public class Client {
    public static void main(String[] args) {
        CommonHouse house = new CommonHouse();
        house.build();
    }
}
```

**使用传统方式分析：**

> - 优点是比较好理解，简单易操作
> - 缺点是：设计的程序结构，过于简单，没有设计缓存层对象，程序的扩展和维护不好。也就是说，这种设计方案，把产品（即房子）和创建产品的过程（即建房子的流程）封装在一起，耦合性增强。
> - 解决方案：将产品和产品建造过程解耦         建造者模式

**建造者模式基本介绍：**

> - 建造者模式，又叫生成器模式。是一种对象构建模式，它可以将复杂对象的建造过程抽象出来，使这个抽象过程的不同实现方法可以构造出不同表现的对象
> - 建造者模式是一步一步创建一个复杂的对象，它允许用户只通过指定复杂对象的类型和内容就可以构建他们，用户不需要知道内部的具体构建细节

**建造者模式的四个角色：**

> - Produce（产品角色）：一个具体的产品对象
> - Builder（抽象建造者）：创建一个 Product 对象的各个部件指定的接口/抽象类
> - ConcreteBuilder（具体建造者）：实现接口，构建和装配各个部件
> - Director（指挥者）：构建一个使用 Builder 接口的对象。它主要是用于创建一个复杂的对象。它主要有两个作用：一是，隔离了客户与对象的生成过程，二是，负责控制产品对象的生产过程

代码见：

`<https://github.com/hepingfly/heping_DesignPattern/tree/master/src/com/hepingfly/builder/improve>`

**建造者模式细节：**

> - 客户端不必知道产品内部组成的细节，将产品本身与产品创建的过程解耦，是的相同的创建过程可以创建不同的对象
> - 每一个具体建造者都相对独立，而与其他的具体建造者无关，因此可以很方便的替换具体建造者或增加新的具体建造者，用户使用不同的具体建造者即可得到不同的产品对象
> - 可以更加精细的控制产品的创建过程。将复杂产品的创建步骤分解在不同的方法中，使得创建过程更加清晰，也更方便使用程序来控制创建过程
> - 增加新的具体建造者无需修改原有类库代码，符合开闭原则
> - 建造者模式所创建的产品一般具有较多的共同点，其组成部分相似，如果产品之间的差异性很大，则不适合使用建造者模式，因此其使用范围受到一定影响

#### 12、适配器模式

**基本介绍：**

> - 适配器模式将某个类的接口转换成客户端期望的另一个接口表示，主要目的是兼容性，让原本因接口不匹配不能一起工作的两个类可以协同工作
> - 适配器模式属于结构性模式
> - 主要分为三类：类适配器模式、对象适配器模式、接口适配器模式

**工作介绍：**

> - 适配器模式：将一个类的接口转换成另一个接口。让原本接口不兼容的类可以兼容
> - 从用户的角度看不到被适配者，是解耦的
> - 用户调用适配器转换出来的目标接口方法，适配器再调用被适配者的相关接口方法
> - 用户收到反馈结果，感觉只是和目标接口交互

代码见：

`<https://github.com/hepingfly/heping_DesignPattern/tree/master/src/com/hepingfly/adapter/classadapter>`

**类适配器细节：**

> - Java 是单继承机制，所以类适配器需要继承 src 类，这算一个缺点。因为这要求 dst 必须是接口，有一定局限性
> - src 类的方法在 Adapter 中都会暴露出来，也增加了使用成本
> - 由于其继承了 src 类，所以它可以根据需求重写 src 类的方法，是的 Adapter 的灵活性增强了

**对象适配器模式**

**介绍**

> - 基本思路和类的适配器模式相同，只是将 Adapter 类作修改，不是继承 src 类，而是持有 src 类的实例，以解决兼容性的问题。即：持有 src 类，实现 dst 接口，完成 src - dst 的适配
> - 根据 “合成复用原则”，在系统中尽量使用关联关系来替代继承关系
> - 对象适配器模式是适配器模式中常用的一种

代码见：

`<https://github.com/hepingfly/heping_DesignPattern/tree/master/src/com/hepingfly/adapter/objectadapter>`

**对象适配器模式细节：**

> - 对象适配器是类适配器其实是一种思想，只不过实现方式不同。根据合成复用原则，使用组合替代继承，所以它解决了类适配器必须继承 src 的局限性问题，也不在要求 dst 必须是接口
> - 使用成本更低，更灵活

**接口适配器模式**

介绍

> - 当不需要全部实现接口提供的方法时，可先设计一个抽象类实现接口，并为该接口中每个方法提供一个默认实现（空方法），那么该抽象类的子类可有选择的覆盖父类的某些方法来实现需求
> - 适用于一个接口不想使用其所有的方法的情况

代码见：

`<https://github.com/hepingfly/heping_DesignPattern/tree/master/src/com/hepingfly/adapter/interfaceadapter>`

#### 13、桥接模式

**需求：**

> 现在对不同手机类型的不同品牌实现操作编程（比如：开机、关机、上网、打电话等）

**基本介绍：**

> - 桥接模式是指，将实现与抽象放在两个不同的类层次中，使两个层次可以独立改变。
> - 桥接模式是一种结构性设计模式
> - 桥接模式基于类的最小设计原则，通过使用封装、聚合及继承等行为让不同的类承担不同的职责。它的主要特点是把抽象与行为实现分离开来，从而可以保持各部分的独立性以及应对他们的功能扩展。

代码见：

`<https://github.com/hepingfly/heping_DesignPattern/tree/master/src/com/hepingfly/bridge>`

**注意事项和细节：**

> - 实现了抽象和实现的部分分离，从而极大的提高了系统的稳定性，让抽象部分和实现部分独立开来，这有助于系统进行分层设计，从而产生了更好的结构化系统。
> - 对于系统的高层部分，只需要知道抽象部分和实现部分的接口就可以了，其他的部分由具体的业务来完成。
> - 桥接模式替代多层继承方案，可以减少子类的个数，降低系统的管理和维护成本。
> - 桥接模式要求正确识别出系统中两个独立变化的维度，因此其使用范围有一定的局限性，即需要有这样的应用场景。
> - 对于那些不希望使用继承或因为多层次继承导致系统类的个数急剧增加的系统，桥接模式尤为适用

#### 14、装饰者模式

**需求：**

> 星巴克咖啡订单项目：
>
> - 咖啡种类：Espresso（意大利浓咖啡）、ShortBlack、LongBlack（美式咖啡）、Decaf（无音咖啡）
> - 调料：Milk、Soy（豆浆）、Chocolate
> - 要求在扩展新的咖啡种类时，具有良好的扩展性、改动方便、维护方便
> - 使用面向对象来计算不同种类咖啡的费用：客户可以点单品咖啡，也可以单品咖啡 + 调料组合

**装饰者模式定义：**

> 动态的将新功能附加到对象上。在对象功能扩展方面，它比继承更有弹性，装饰者模式也体现了开闭原则。

代码见：

`<https://github.com/hepingfly/heping_DesignPattern/tree/master/src/com/hepingfly/decorator>`

#### 15、组合模式

**需求：**

> 学院院系需求
>
> 展示一个学院院系结构：要在一个页面中展示出学校的院系组成，一个学校有多个学院，一个学院有多个系

**基本介绍：**

> - 组合模式，又叫部分整体模式，它创建了对象组的树形结构，将对象组合成树状结构以表示 整体-部分 的层次关系
> - 组合模式依据树形结构来组合对象，用来表示部分以及整体层次
> - 这种类型的设计模式属于结构型模式
> - 组合模式使得用户对单个对象和组合对象的访问具有一致性，即：组合能让客户以一致的方式处理个别对象以及组合对象

**解决的问题：**

> 组合模式解决这样的问题，当我们要处理的对象，可以生成一颗树形结构，而我们要对树上的节点和叶子进行操作时，它能够提供一致的方式，而不用考虑它是节点还是叶子。

代码见：

`<https://github.com/hepingfly/heping_DesignPattern/tree/master/src/com/hepingfly/composite>`

组合模式注意事项和细节：

> - 具有较强的扩展性。当我们要更改组合对象时，我们只需要调整内部的组合关系，客户端不用做出任何改动。
> - 方便创建出复杂的层次结构。客户端不用理会组合里面的组成细节。
> - 需要遍历组织结构，或者处理的对象具有树形结构时，非常适合使用组合模式
> - 要求较高的抽象性。如果节点和叶子有很多差异性的话，比如很多方法和属性都不一样，不适合使用组合模式。




















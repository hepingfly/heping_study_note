# Java设计模式

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
























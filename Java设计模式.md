# Java设计模式

### 设计模式介绍

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


















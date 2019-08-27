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




























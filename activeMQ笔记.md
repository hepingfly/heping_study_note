# ActiveMQ笔记

### 一、activeMQ 入门

**为什么要引入 MQ？**

> 系统之间直接调用存在的问题：
>
> - 系统之间接口耦合比较严重
> - 面对大流量并发时，容易被冲垮
> - 等待同步存在性能问题

**所以我们在设计系统时可以明确要达到的目标：**

> ① 要做到系统解耦，当新的模块接进来时，可以做到代码改动最小。能够解耦
>
> ② 设置流量缓冲池，可以让后端系统按照自身吞吐能力进行消费，不被冲垮。能够削峰
>
> ③ 强弱依赖梳理，能将非关键调用链路的操作异步化并提升整体系统的吞吐能力。能够异步

**MQ 的作用：**

> 发送者把消息发送给消息服务器，消息服务器将消息存放在若干队列/主题中，在合适的时候，消息服务器会将消息转发给接受者。在这个过程中，**发送和接收是异步的**，也就是发送无需等待，而且发送者和接收者的生命周期也没有必然关系。尤其在 发布pub/订阅sub 模式下，也可以完成一对多的通信，即让一个消息有多个接收者。 

**MQ 的特点：**

> - 采用异步处理模式
>
>   - ```
>     消息发送者可以发送一个消息而无需等待响应。消息发送者将消息发送到一条虚拟通道（主题或队列）上；消息接收者则订阅或监听该通道，一条信息可能最终转发给一个或多个消息接收者，这些接收者都无需对消息发送者做出同步回应。整个过程都是异步的。
>
>     案例：
>     也就是说，一个系统跟另外一个系统之间进行通信的时候，假如系统A 想发送一个消息给系统B ，让它去处理。但是系统A 不关注系统B 怎么处理或者有没有处理好，所以系统A 把消息发送给 MQ ，然后就不管这条消息的死活了，接口系统B 从 MQ 里消费出来处理即可。至于怎么处理，是否处理完毕，什么时候处理，都是系统B 的事，与系统A 无关。
>
>     这样的一种通信方式，就是所谓的异步通信方式，对于系统A 来说，只要把消息发送给 MQ ，然后系统B 就会异步的去处理了，系统A 不需要同步的等待系统B 处理完。这样的好处就是：解耦
>     ```
>
> - 应用系统之间解耦
>
>   -  发送者和接收者不必了解对方，只需要确认消息
>   - 发送者和接收者不必同时在线

**MQ 能干嘛？**

> - 解耦
> - 削峰
> - 异步

### 二、ActiveMQ 安装和控制台

**下载：**

直接去 activeMQ 官网下载

**activeMQ 启动：**

> - 启动：`./activemq start`
> - 关闭： `./activemq stop`
> - 带运行日志的启动方式：  `./activemq start > /opt/myrunmq.log`

**控制台访问：**

> `http://ip:8161`
>
> 默认用户名和密码都是 admin

**注：**

> - ActiveMQ 采用 **61616** 端口提供 JMS 服务
> - ActiveMQ 采用 **8161** 端口提供管理控制台服务 

### 三、Java 编码实现 ActiveMQ 通讯

> - 在点对点的消息传递域中，目的地被称为队列（queue）
> - 在发布订阅消息传递域中，目的地被称为主题（topic）

#### 1、消息生产者编码

```java
public class JmsProduce {
    private static final String ACTIVE_URL = "tcp://192.168.148.141:61616";
    private static final String QUEUE_NAME = "queue01";

    public static void main(String[] args) throws JMSException {
        // 1.创建连接工厂，按照给定的 url 地址，采用默认用户名和密码
        ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(ACTIVE_URL);

        // 2.通过连接工厂，获得连接 connection 并启动访问
        Connection connection = connectionFactory.createConnection();
        connection.start();

        // 3.创建会话 session,第一个参数是事务，第二个参数是签收
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);

        // 4.创建目的地（具体是队列还是主题）
        Queue queue = session.createQueue(QUEUE_NAME);

        // 5.创建消息生产者
        MessageProducer producer = session.createProducer(queue);

        // 6.通过使用消息生产者生产 3 条消息发送到 MQ 的队列里面
        for (int i = 1; i <= 3; i++) {
            // 7.创建消息
            TextMessage textMessage = session.createTextMessage("hello" + i);
            // 8.通过生产者发送给 MQ
            producer.send(textMessage);
        }

        // 9.关闭资源
        producer.close();
        session.close();
        connection.close();
        System.out.println("消息发送到 MQ 完成");
    }
}
```




























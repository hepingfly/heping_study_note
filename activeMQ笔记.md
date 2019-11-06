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

#### 2、消息消费者编码

```java
public class JmsConsumer {
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

        // 5.创建消费者
        MessageConsumer consumer = session.createConsumer(queue);
        while (true) {
            // 这里需要注意，生产者往队列里面放的是 TextMessage ，所以消费的时候也要强转成 TextMessage
            TextMessage textMessage = (TextMessage) consumer.receive();
            if (textMessage != null) {
                System.out.println("消费者接收到消息：" + textMessage.getText());
            } else {
                break;
            }
        }
        consumer.close();
        session.close();
        connection.close();
    }
}
```

#### 3、消息消费者 MessageListener 

```java
public class JmsConsumer {
    private static final String ACTIVE_URL = "tcp://192.168.148.141:61616";
    private static final String QUEUE_NAME = "queue01";

    public static void main(String[] args) throws JMSException, IOException {
        // 1.创建连接工厂，按照给定的 url 地址，采用默认用户名和密码
        ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(ACTIVE_URL);

        // 2.通过连接工厂，获得连接 connection 并启动访问
        Connection connection = connectionFactory.createConnection();
        connection.start();

        // 3.创建会话 session,第一个参数是事务，第二个参数是签收
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);

        // 4.创建目的地（具体是队列还是主题）
        Queue queue = session.createQueue(QUEUE_NAME);

        // 5.创建消费者
        MessageConsumer consumer = session.createConsumer(queue);

        /**
         * 通过监听的方式来消费消息
         */
        consumer.setMessageListener(new MessageListener() {
            public void onMessage(Message message) {
                if (message != null && message instanceof TextMessage) {
                    TextMessage textMessage = (TextMessage) message;
                    try {
                        System.out.println(textMessage.getText());
                    } catch (JMSException e) {
                        e.printStackTrace();
                    }
                }
            }
        });
        System.in.read();
    }
}
```

#### 4、消费者三大消费情况

> 第一种情况：
>
> 先生产，只启动 1号消费者。问：1号消费者能消费消息吗
>
> 答：可以
>
> 第二种情况：
>
> 先生产，先启动 1 号消费者再启动 2 号消费者。问：2号消费者还能消费消息吗
>
> 答：不可以。1 号消费者可以消费消息
>
> 第三种情况：
>
> 先启动 2 个消费者，再生产 6 条消息。问：消费情况
>
> 答：2个消费者消费消息一人一半

#### 5、队列总结

**JMS 开发的基本步骤：**

> 1. 创建一个 connection factory
> 2. 通过 connection factory 来创建 JMS connection
> 3. 启动 JMS connection
> 4. 通过 connection 创建 JMS session
> 5. 创建 JMS destination
> 6. 创建 JMS producer 或者创建 JMS message 并设置 destionation
> 7. 创建 JMS consumer 或者注册一个 JMS message listener
> 8. 发送或者接收 JMS message
> 9. 关闭所有 JMS 资源

**点对点传递域特点：**

> - 每个消息只能有一个消费者，类似 1对1 的关系。好比个人快递自己领取自己的。
> - 消息的生产者和消费者之间没有时间上的相关性。无论消费者在生产者发送消息的时候是否处于运行状态，消费者都可以提取消息。类似于我们发送短信，发送者发送后不见得接收者会立即接收查看。
> - 消息被消费后队列中不会再存储，所以消费者不会消费到已经被消费掉的消息。

#### 6、Topic 编码

**发布/订阅消息传递域特点：**

> - 生产者将消息发布到 topic 中，每个消息可以有多个消费者，属于 1:N 的关系
> - 生产者和消费者之间有时间上的相关性。订阅某一主题的消费者只能消费自它订阅之后发布的消息。
> - 生产者生产时， topic 不保存消息它是无状态的不落地，假如无人订阅就去生产，那就是一条废消息，所以一般先启动消费者，再启动生产者
>
> JMS 规范允许客户创建持久订阅，这在一定程度上放松了时间上的相关性要求。持久订阅允许消费者消费它在未处于激活状态时发送的消息。一句话，好比我们的微信公众号订阅。

**注：**

**先启动消费者再启动生产者，不然发送的消息是废消息**

**消息生产者：**

```java

public class JmsProduce_Topic {
    private static final String ACTIVE_URL = "tcp://192.168.148.141:61616";
    private static final String TOPIC_NAME = "topic-shp";

    public static void main(String[] args) throws JMSException {
        // 1.创建连接工厂，按照给定的 url 地址，采用默认用户名和密码
        ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(ACTIVE_URL);

        // 2.通过连接工厂，获得连接 connection 并启动访问
        Connection connection = connectionFactory.createConnection();
        connection.start();

        // 3.创建会话 session,第一个参数是事务，第二个参数是签收
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);

        // 4.创建目的地（具体是队列还是主题）
        Topic topic = session.createTopic(TOPIC_NAME);

        // 5.创建消息生产者
        MessageProducer producer = session.createProducer(topic);

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

**消息消费者：**

```java
public class JmsConsumer_Topic {
    private static final String ACTIVE_URL = "tcp://192.168.148.141:61616";
    private static final String TOPIC_NAME = "topic-shp";

    public static void main(String[] args) throws JMSException, IOException {
        // 1.创建连接工厂，按照给定的 url 地址，采用默认用户名和密码
        ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(ACTIVE_URL);

        // 2.通过连接工厂，获得连接 connection 并启动访问
        Connection connection = connectionFactory.createConnection();
        connection.start();

        // 3.创建会话 session,第一个参数是事务，第二个参数是签收
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);

        // 4.创建目的地（具体是队列还是主题）
        Topic topic = session.createTopic(TOPIC_NAME);

        // 5.创建消费者
        MessageConsumer consumer = session.createConsumer(topic);

        /**
         * 通过监听的方式来消费消息
         */
        consumer.setMessageListener(new MessageListener() {
            public void onMessage(Message message) {
                if (message != null && message instanceof TextMessage) {
                    TextMessage textMessage = (TextMessage) message;
                    try {
                        System.out.println(textMessage.getText());
                    } catch (JMSException e) {
                        e.printStackTrace();
                    }
                }
            }
        });
        System.in.read();
    }
}
```

#### 7、topic 和 queue 对比总结

|            | Topic                                                        | Queue                                                        |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 工作模式   | 「订阅-发布模式」，如果当前没有订阅者，消息将会被丢弃。如果有多个订阅者，那么这些订阅者都会收到消息 | 「负载均衡模式」，如果当前没有消费者，消息也不会丢弃，如果有多个消费者，那么一条消息也只会发送给其中一个消费者，并且要求消费者 ack 信息 |
| 有无状态   | 无状态                                                       | Queue 数据默认会在 mq 服务器上以文件形式保存，比如 activeMQ 一般保存在 $AMQ_HOME\data\kr-store\data 下面，也可以配置成 DB 存储 |
| 传递完整性 | 如果没有订阅者，消息会被丢弃                                 | 消息不会丢弃                                                 |
| 处理效率   | 由于消息要按照订阅者的数量进行复制，所以处理性能会随着订阅者的增加而明显降低，并且还要结合不同消息协议自身的性能差异 | 由于一条消息只发送给一个消费者，所以就算消费者再多，性能也不会有明显降低。当然不同消息协议的具体性能也是有差异的 |

### 四、JMS 规范和落地产品

#### 1、JMS 是什么

Java Message Service（Java 消息服务是 JavaEE 中的一个技术）

> 什么是 Java 消息服务
>
> Java 消息服务指的是两个应用程序之间进行异步通信的 API ，它为标准消息协议和消息服务提供了一组通用接口，包括创建、发送、读取消息等，用于支持 Java 应用程序开发。在 JavaEE 中，当两个应用程序使用 JMS 进行通信时，它们之间并不是直接相连的，而是通过一个共同的消息收发组件服务组件关联起来以达到解耦/异步削峰的效果。

#### 2、JMS 组成的四大元素

> - JMS provider ：实现 JMS 接口和规范的消息中间件，也就是我们的 MQ 服务器
> - JMS producer ：消息生产者，创建和发送 JMS 消息的客户端应用
> - JMS consumer ：消息消费者，接收和处理 JMS 消息的客户端应用
> - JMS message 
>   - 消息头
>   - 消息属性
>   - 消息体

#### 3、Message之消息头

> - JMSDestination ：消息发送的目的地，主要是指 Queue 和 Topic
>
> - JMSDeliveryMode ：持久模式和非持久模式
>
>   - ```
>     一条持久性的消息：应该被传送 「一次仅仅一次」，这就意味着如果 JMS 提供者出现故障，该消息并不会丢失，它会在服务器恢复之后再次传递
>     一条非持久的消息：最多会传送一次，这意味着如果服务器出现故障，该消息将永远丢失
>     ```
>
> - JMSExpiration 
>
>   - ```
>     可以设置消息在一定时间后过期，默认是永不过期
>     消息过期时间，等于 Destionation 的 send 方法中的 timeToLive 值加上发送时刻的 GMT 时间值
>     如果 timeToLive 值等于零，则 JMSExpiration 被设为零，表示该消息永不过期
>     如果发送后，在消息过期时间之后消息还没有被发送到目的地，则该消息被清除
>     ```
>
> - JMSPriority
>
>   - ```
>     消息优先级，从 0-9 十个级别，0到4 是普通消息，5到9 是加急消息
>     JMS 不要求 MQ 	严格按照这十个优先级发送消息，但必须保证加急消息要先于普通消息到达。默认是 4 级
>     ```
>
> - JMSMessageID ：唯一识别每个消息的标识，由 MQ 产生

#### 4、Message之消息体

封装具体的消息数据

> 主要有以下 5 种消息体格式：
>
> - **TextMessage**
>   - 普通字符串消息，包含一个 string
> - **MapMessage**
>   - 一个 Map 类型的消息， key 为 string 类型，而值为 Java 的基本类型
> - BytesMessage
>   - 二进制数组消息，包含一个 byte[] 数组
> - StreamMessage
>   - Java 数据流消息，用标准流操作来顺序的填充和读取
> - ObjectMessage
>   - 对象消息，包含一个可序列化的 Java 对象

**注：**

发送和接收的消息体类型必须一致对应

**消息生产者：**

```java
public class JmsProduce {
    private static final String ACTIVE_URL = "tcp://192.168.148.148:61616";
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
			
            // 创建一个 map 类型的消息
            MapMessage mapMessage = session.createMapMessage();
            mapMessage.setString("k1","mapMessage----v1");
            producer.send(mapMessage);
        }

        // 9.关闭资源
        producer.close();
        session.close();
        connection.close();
        System.out.println("消息发送到 MQ 完成");
    }
}
```

**消息消费者：**

```java
public class JmsConsumer {
    private static final String ACTIVE_URL = "tcp://192.168.148.148:61616";
    private static final String QUEUE_NAME = "queue01";

    public static void main(String[] args) throws JMSException, IOException {
        // 1.创建连接工厂，按照给定的 url 地址，采用默认用户名和密码
        ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(ACTIVE_URL);

        // 2.通过连接工厂，获得连接 connection 并启动访问
        Connection connection = connectionFactory.createConnection();
        connection.start();

        // 3.创建会话 session,第一个参数是事务，第二个参数是签收
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);

        // 4.创建目的地（具体是队列还是主题）
        Queue queue = session.createQueue(QUEUE_NAME);

        // 5.创建消费者
        MessageConsumer consumer = session.createConsumer(queue);

        /**
         * 通过监听的方式来消费消息
         */
        consumer.setMessageListener(new MessageListener() {
            public void onMessage(Message message) {
                if (message != null && message instanceof TextMessage) {
                    TextMessage textMessage = (TextMessage) message;
                    try {
                        System.out.println(textMessage.getText());
                    } catch (JMSException e) {
                        e.printStackTrace();
                    }
                }
                if (message != null && message instanceof MapMessage) {
                    MapMessage mapMessage = (MapMessage) message;
                    try {
                        // 通过 key 来获取消息
                        System.out.println(mapMessage.getString("k1"));
                    } catch (JMSException e) {
                        e.printStackTrace();
                    }
                }
            }
        });
        System.in.read();
    }
}
```

#### 5、Message之消息属性

如果需要除消息头字段以外的值，那么可以使用消息属性

> 他们是以属性名和属性值对的形式制定的。可以将属性视为消息头的扩展，属性指定一些消息头没有包括的附加信息，比如可以在属性里指定消息选择器。
>
> 消息的属性就像可以分配给一条消息的附加消息头一样。它们允许开发者添加有关消息的不透明附加信息。它们还用于暴露消息选择器在消息过滤时使用的数据。

**消息生产者：**

```java
TextMessage textMessage = session.createTextMessage("hello" + i);
if (i == 2) {
    // 设置消息属性
    textMessage.setStringProperty("c01","vip");
}
```

**消息消费者：**

```java
if (message != null && message instanceof TextMessage) {
    TextMessage textMessage = (TextMessage) message;
    try {
        System.out.println(textMessage.getText());
        // 获取消息属性
        System.out.println(textMessage.getStringProperty("c01"));
    } catch (JMSException e) {
        e.printStackTrace();
    }
}
```

------

**JMS 可靠性**

#### 6、JMS可靠性之非持久化

消息非持久化：当 activeMQ 宕机，消息就不存在了

```java
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
producer.setDeliveryMode(DeliveryMode.NON_PERSISTENT);      // 设置消息非持久化
```

那么这样设置的结果就是：

我消息生产者把消息发送到 activeMQ 了，然后 activeMQ 宕机了，然后再把 activeMQ 重新启动，此时消息消费者想要去消费消息就消费不到了，因为生产者发送的消息没有持久化。

#### 7、JMS可靠性之持久化

消息持久化：当 activeMQ 宕机，消息就依然存在

```java
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
producer.setDeliveryMode(DeliveryMode.PERSISTENT);           // 设置消息持久化
```

那么这样设置的结果就是：

我消息生产者把消息发送到 activeMQ 了，然后 activeMQ 宕机了，然后再把 activeMQ 重新启动，此时消息还存在消息队列中，消费者如果想要消费消息，还是可以消费到的，因为生产者发送的消息进行了持久化。

**说明：**

> 持久化消息
>
> 这是队列的默认发送模式，此模式保证这些消息只被传送一次和成功使用一次。对于这些消息，可靠性是优先考虑的因素。
>
> 可靠性的另一个重要方面是确保持久性消息传送至目标后，消息服务在向消费者传送他们之前不会丢失这些信息。

#### 8、持久化 topic

**消息生产者：**

```java
public class JmsProduce_Topic_Persist {
    private static final String ACTIVE_URL = "tcp://192.168.148.148:61616";
    private static final String TOPIC_NAME = "topic-persist";

    public static void main(String[] args) throws JMSException {
        ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(ACTIVE_URL);
        Connection connection = connectionFactory.createConnection();
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        Topic topic = session.createTopic(TOPIC_NAME);
        MessageProducer producer = session.createProducer(topic);
        producer.setDeliveryMode(DeliveryMode.PERSISTENT);
        connection.start();   // 设置完持久化模式后，再开启连接
        for (int i = 1; i <= 3; i++) {
            TextMessage textMessage = session.createTextMessage("hello" + i);
            producer.send(textMessage);
        }
        producer.close();
        session.close();
        connection.close();
        System.out.println("消息发送到 MQ 完成");
    }
}
```

**消息消费者：**

```java
public class JmsConsumer_Topic_Persist {
    private static final String ACTIVE_URL = "tcp://192.168.148.148:61616";
    private static final String TOPIC_NAME = "topic-persist";
    public static void main(String[] args) throws JMSException {
        // 1.创建连接工厂，按照给定的 url 地址，采用默认用户名和密码
        ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(ACTIVE_URL);

        // 2.通过连接工厂，获得连接 connection 并启动访问
        Connection connection = connectionFactory.createConnection();
        connection.setClientID("heping");   // 表明 heping 这个用户订阅

        // 3.创建会话 session,第一个参数是事务，第二个参数是签收
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);

        // 4.创建目的地（具体是队列还是主题）
        Topic topic = session.createTopic(TOPIC_NAME);
        TopicSubscriber topicSubscriber = session.createDurableSubscriber(topic, "这个参数是备注");
        connection.start();

        Message message = topicSubscriber.receive();
        while (message != null) {
            TextMessage textMessage = (TextMessage) message;
            System.out.println("收到的持久化 topic" + textMessage.getText());
            message = topicSubscriber.receive(1000);
        }
        session.close();
        connection.close();

        /**
         * 一定要先运行一次消费者，等于向 MQ 注册，类似我订阅了这个主题
         * 然后再运行生产者发送消息，此时无论消费者是否在线，都会收到。不在线的话，下次连接的时候，会把没有收过的消息都接收下来
         */
    }
}
```

#### 9、消息生产者事务

```java
public class JmsProduce_TX {
    private static final String ACTIVE_URL = "tcp://192.168.148.148:61616";
    private static final String QUEUE_NAME = "queue01";

    public static void main(String[] args) throws JMSException {
        // 1.创建连接工厂，按照给定的 url 地址，采用默认用户名和密码
        ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(ACTIVE_URL);

        // 2.通过连接工厂，获得连接 connection 并启动访问
        Connection connection = connectionFactory.createConnection();
        connection.start();

        // 3.创建会话 session,第一个参数是事务，第二个参数是签收
        Session session = connection.createSession(true, Session.AUTO_ACKNOWLEDGE);

        // 4.创建目的地（具体是队列还是主题）
        Queue queue = session.createQueue(QUEUE_NAME);

        // 5.创建消息生产者
        MessageProducer producer = session.createProducer(queue);
        producer.setDeliveryMode(DeliveryMode.PERSISTENT);

        // 6.通过使用消息生产者生产 3 条消息发送到 MQ 的队列里面
        for (int i = 1; i <= 3; i++) {
            // 7.创建消息
            TextMessage textMessage = session.createTextMessage("hello" + i);
            // 8.通过生产者发送给 MQ
            producer.send(textMessage);

        }

        // 9.关闭资源
        producer.close();
        session.commit();   // 提交事务
        session.close();
        connection.close();
        System.out.println("消息发送到 MQ 完成");


    }

    /**
     * 使用事务的好处，正常情况下比如我们发 10 条消息，如果都正常
     * 直接 session.commit() 即可
     * 但如果其中有一条失败了，我们希望可以回滚，就可以使用
     * session.rollback()
     * try {

     } catch (Exception e) {
        session.rollback()
     }

     */
    // 此外，作为消息生产者，开启事务后，消息生产完，需要及时提交事务，如果未提交事务
    // 你生产的消息根本没有到消息队列中去
}
```

#### 10、消息消费者事务

```java
public class JmsConsumer_TX {
    private static final String ACTIVE_URL = "tcp://192.168.148.148:61616";
    private static final String QUEUE_NAME = "queue01";

    public static void main(String[] args) throws JMSException, IOException {
        // 1.创建连接工厂，按照给定的 url 地址，采用默认用户名和密码
        ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(ACTIVE_URL);

        // 2.通过连接工厂，获得连接 connection 并启动访问
        Connection connection = connectionFactory.createConnection();
        connection.start();

        // 3.创建会话 session,第一个参数是事务，第二个参数是签收
        Session session = connection.createSession(true, Session.AUTO_ACKNOWLEDGE);

        // 4.创建目的地（具体是队列还是主题）
        Queue queue = session.createQueue(QUEUE_NAME);

        // 5.创建消费者
        MessageConsumer consumer = session.createConsumer(queue);

        /**
         * 同步阻塞方式（receive）：订阅者或接受者调用 consumer 的 receive 方法来接收消息
         * receive 方法能够在接收到消息之前（或超时之前）将一直阻塞
         */
        while (true) {
            TextMessage textMessage = (TextMessage) consumer.receive(3000);
            if (textMessage != null) {
                System.out.println("消费者接收到消息：" + textMessage.getText());
            } else {
                break;
            }
        }
        consumer.close();
        session.commit();   // 提交事务
        session.close();
        connection.close();
    }
    // 作为消费者，开启事务后，消费完就要把事务提交，如果未提交事务，会出现重复消费消息的情况
    // 就是消息已经被你消费过了，但是还是可以消费的情况
}
```

**注：**

**事务偏生产者，签收偏消费者**

#### 11、消息非事务模式下签收

```java
public class JmsProduce_TX {
    public static void main(String[] args) throws JMSException {
        
        // 假如生产者生产消息，指定的是手动方式签收
        Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    }
}

//-----------------------那么消费者也需要手动方式签收---------------
public class JmsConsumer_TX {
        Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
        while (true) {
            TextMessage textMessage = (TextMessage) consumer.receive(3000);
            if (textMessage != null) {
                System.out.println("消费者接收到消息：" + textMessage.getText());
                textMessage.acknowledge();   // 这里要手动进行签收，否则会出现消息可以重复消费情况
            } else {
                break;
            }
        }
}

```

#### 12、消息有事务模式下签收

**生产事务开启，只有 commit 后才能将全部消息变为已消费。**

**签收和事务的关系：**

> 在事务性会话中，当一个事务被成功提交则消息被自动签收。如果事务回滚，则消息会被再次传送。
>
> 非事务性会话中，消息何时被确认取决于创建会话时的应答模式（acknowledgement mode）

#### 13、点对点和发布订阅总结

**JMS点对点总结：**

> 点对点模型是基于队列的，生产者发消息到队列，消费者从队列接收消息，队列的存在使得消息的异步传输成为可能。
>
> - 如果在 Session 关闭时，有部分消息已被收到但还没有被签收，那当消费者下次连接到相同的队列时，这些消息还会被再次接收
> - 队列可以长久的保存消息直到消费者收到消息。消费者不需要因为担心消息会丢失而时刻和队列保持激活的连接状态，充分体现了异步传输的模式

**JMS 发布订阅总结：**

> JMS Pub/Sub 模型定义了如何向一个内容节点发布和订阅消息，这些节点被称作 topic
>
> 主题可以被认为是消息的传输中介，发布者（publisher）发布消息到主题，订阅者（subscribe）从主题订阅消息。
>
> 主题使得消息订阅者和消息发布者保持互相独立，不需要接触即可保证消息的传送

**非持久订阅：**

> 非持久订阅只有当客户端处于激活状态，也就是和 MQ 保持连接状态才能收到发送到某个主题的消息。
>
> 如果消费者处于离线状态，生产者发送的主题消息将会丢失作废，消费者永远不会收到。
>
> **先要订阅注册才能接收到发布，只给订阅者发布消息**

**持久订阅：**

> 客户端首先向 MQ 注册一个自己的身份 ID 识别号，当这个客户端处于离线时，生产者会为这个 ID 保存所有发送到主题的消息，当客户端再次连接到 MQ 时会根据消费者的 ID 得到所有当自己处于离线时发送到主题的消息。
>
> 非持久订阅状态下，不能恢复或重新派送一个未签收的消息
>
> 持久订阅才能恢复或重新派送一个未签收的消息

### 五、ActiveMQ 的 Broker

#### 1、Broker 是什么

> 相当于一个 ActiveMQ 服务器实例
>
> Broker 其实就是实现了用代码的形式启动 ActiveMQ ，将 MQ 嵌入到 Java 代码中，以便随时用随时启动，在用的时候再去启动，这样能节省资源，也保证了可靠性。

#### 2、按照不同 conf 配置文件启动 activemq

**启动命令：**

> `./activemq start xbean:file:/opt/apache-activemq-5.15.9/conf/activemq2.xml`

#### 3、Broker

pom.xml 需引入以下依赖：

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.5</version>
</dependency>
```

```java
public class EmbedBroker {
    public static void main(String[] args) throws Exception {
        //ActiveMQ 也支持在 vm 中通信，基于嵌入式的 broker
        BrokerService brokerService = new BrokerService();
        brokerService.setUseJmx(true);
        brokerService.addConnector("tcp://localhost:61616");
        brokerService.start();
    }
}

// 这样你运行这个 main 方法就等于启动了一个迷你的 activemq 服务，你用生产者和消费者及可以去连接这个服务
// 使用 tcp://192.168.148.148:61616  这个 url 去连接
```

### 六、Spring 整合 ActiveMQ

#### 1、整合之队列生产者

**pom.xml**

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.5</version>
</dependency>
 
<!--activemq 对 JMS 的支持，整合 spring 和 activemq-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jms</artifactId>
    <version>4.3.23.RELEASE</version>
</dependency>

<!--activemq 所需要的 pool 包配置-->
<dependency>
    <groupId>org.apache.activemq</groupId>
    <artifactId>activemq-pool</artifactId>
    <version>5.15.9</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>4.3.23.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.3.23.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>4.3.23.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-orm</artifactId>
    <version>4.3.23.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>1.6.1</version>
</dependency>
<dependency>
    <groupId>aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.5.3</version>
</dependency>

<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>2.1_2</version>
</dependency>
```

**spring 配置文件：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--配置自动扫描的包-->
    <context:component-scan base-package="com.hepingfly"></context:component-scan>

    <!--配置生产者-->
    <bean id="jmsFactory" class="org.apache.activemq.pool.PooledConnectionFactory" destroy-method="stop">
        <property name="connectionFactory">
            <!--真正可以产生 Connection 的 ConnectionFactory，由对应的 JMS 服务厂商提供-->
            <bean class="org.apache.activemq.ActiveMQConnectionFactory">
                <property name="brokerURL" value="tcp://192.168.148.148:61616"></property>
            </bean>
        </property>
        <property name="maxConnections" value="100"></property>
    </bean>
    <!--这个是队列的目的地-->
    <bean id="destinationQueue" class="org.apache.activemq.command.ActiveMQQueue">
        <constructor-arg index="0" value="spring-active-queue"></constructor-arg>
    </bean>

    <!--Spring 提供的 JMS 工具类，它可以进行消息的发送和接收-->
    <bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
        <property name="connectionFactory" ref="jmsFactory"></property>
        <property name="defaultDestination" ref="destinationQueue"></property>
        <property name="messageConverter">
            <bean class="org.springframework.jms.support.converter.SimpleMessageConverter"></bean>
        </property>
    </bean>
</beans>
```

**生产者代码：**

```java
@Service
public class SpringMQ_Produce {

    @Autowired
    private JmsTemplate jmsTemplate;
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        SpringMQ_Produce produce = ctx.getBean(SpringMQ_Produce.class);
        produce.jmsTemplate.send(new MessageCreator() {
            public Message createMessage(Session session) throws JMSException {
                TextMessage textMessage = session.createTextMessage("****spring 和 activemq 整合");
                return textMessage;
            }
        });
        System.out.println("send task over...");
    }
}
```

#### 2、整合队列消费者

**消费者代码：**

```java
@Service
public class SpringMQ_Consumer {

    @Autowired
    private JmsTemplate jmsTemplate;
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        SpringMQ_Consumer consumer = ctx.getBean(SpringMQ_Consumer.class);
        String message = (String) consumer.jmsTemplate.receiveAndConvert();
        System.out.println("消费者受到消息：" + message);
    }
}
```

#### 3、整合之主题生产者消费者

**spring 配置文件：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--配置自动扫描的包-->
    <context:component-scan base-package="com.hepingfly"></context:component-scan>

    <!--配置生产者-->
    <bean id="jmsFactory" class="org.apache.activemq.pool.PooledConnectionFactory" destroy-method="stop">
        <property name="connectionFactory">
            <!--真正可以产生 Connection 的 ConnectionFactory，由对应的 JMS 服务厂商提供-->
            <bean class="org.apache.activemq.ActiveMQConnectionFactory">
                <property name="brokerURL" value="tcp://192.168.148.148:61616"></property>
            </bean>
        </property>
        <property name="maxConnections" value="100"></property>
    </bean>
    <!--这个是主题-->
    <bean id="destinationTopic" class="org.apache.activemq.command.ActiveMQTopic">
        <constructor-arg index="0" value="spring-active-topic"></constructor-arg>
    </bean>

    <!--Spring 提供的 JMS 工具类，它可以进行消息的发送和接收-->
    <bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
        <property name="connectionFactory" ref="jmsFactory"></property>
        <property name="defaultDestination" ref="destinationTopic"></property>
        <property name="messageConverter">
            <bean class="org.springframework.jms.support.converter.SimpleMessageConverter"></bean>
        </property>
    </bean>
</beans>
```

**生产者代码：**

```java
@Service
public class SpringMQ_Produce {

    @Autowired
    private JmsTemplate jmsTemplate;
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        SpringMQ_Produce produce = ctx.getBean(SpringMQ_Produce.class);
        produce.jmsTemplate.send(new MessageCreator() {
            public Message createMessage(Session session) throws JMSException {
                TextMessage textMessage = session.createTextMessage("****spring 和 activemq 整合");
                return textMessage;
            }
        });
        System.out.println("send task over...");
    }
```

**消费者代码：**

```java
@Service
public class SpringMQ_Consumer {
    @Autowired
    private JmsTemplate jmsTemplate;
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        SpringMQ_Consumer consumer = ctx.getBean(SpringMQ_Consumer.class);
        String message = (String) consumer.jmsTemplate.receiveAndConvert();
        System.out.println("消费者受到消息：" + message);
    }
}
```

#### 4、整合之监听器配置

在 spring 里面实现消费者不启动，直接通过配置监听完成

spring 配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--配置自动扫描的包-->
    <context:component-scan base-package="com.hepingfly"></context:component-scan>

    <!--配置生产者-->
    <bean id="jmsFactory" class="org.apache.activemq.pool.PooledConnectionFactory" destroy-method="stop">
        <property name="connectionFactory">
            <!--真正可以产生 Connection 的 ConnectionFactory，由对应的 JMS 服务厂商提供-->
            <bean class="org.apache.activemq.ActiveMQConnectionFactory">
                <property name="brokerURL" value="tcp://192.168.148.148:61616"></property>
            </bean>
        </property>
        <property name="maxConnections" value="100"></property>
    </bean>

    <!--这个是主题-->
    <bean id="destinationTopic" class="org.apache.activemq.command.ActiveMQTopic">
        <constructor-arg index="0" value="spring-active-topic"></constructor-arg>
    </bean>

    <!--Spring 提供的 JMS 工具类，它可以进行消息的发送和接收-->
    <bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
        <property name="connectionFactory" ref="jmsFactory"></property>
        <property name="defaultDestination" ref="destinationTopic"></property>
        <property name="messageConverter">
            <bean class="org.springframework.jms.support.converter.SimpleMessageConverter"></bean>
        </property>
    </bean>

    <!--配置监听程序-->
    <bean id="jmscontainer" class="org.springframework.jms.listener.DefaultMessageListenerContainer">
        <property name="connectionFactory" ref="jmsFactory"></property>
        <property name="destination" ref="destinationTopic"></property>
        <property name="messageListener" ref="myMessageListener"></property>
    </bean>
</beans>
```

**监听器类：**

```java
@Component
public class MyMessageListener implements MessageListener {
    public void onMessage(Message message) {
        if (message != null && message instanceof TextMessage) {
            TextMessage textMessage = (TextMessage) message;
            try {
                System.out.println(textMessage.getText());
            } catch (JMSException e) {
                e.printStackTrace();
            }
        }
    }
}
```

**生产者代码：**

```java
@Service
public class SpringMQ_Produce {

    @Autowired
    private JmsTemplate jmsTemplate;
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        SpringMQ_Produce produce = ctx.getBean(SpringMQ_Produce.class);
        produce.jmsTemplate.send(new MessageCreator() {
            public Message createMessage(Session session) throws JMSException {
                TextMessage textMessage = session.createTextMessage("****spring 和 activemq 整合");
                return textMessage;
            }
        });
        System.out.println("send task over...");
    }
```

**消费者代码：**

```java
@Service
public class SpringMQ_Consumer {
    @Autowired
    private JmsTemplate jmsTemplate;
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        SpringMQ_Consumer consumer = ctx.getBean(SpringMQ_Consumer.class);
        String message = (String) consumer.jmsTemplate.receiveAndConvert();
        System.out.println("消费者受到消息：" + message);
    }
}
```

因为是主题，所以一般我们先启动消费者后启动生产者，但是我配置了监听器，所以就算我消费者不启动，也能监听到生产者发送消息。

### 七、SpringBoot 整合 ActiveMQ

#### 1、整合之队列生产者

① **pom.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.5.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.hepingfly.springboot.activemq</groupId>
	<artifactId>activemq_produce</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>activemq_produce</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-activemq</artifactId>
			<version>2.1.5.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

② **application.yml**

```yaml
server:
  port: 7777
spring:
  activemq:
    broker-url: tcp://192.168.148.148:61616 # activemq 服务器地址
    user: admin
    password: admin
  jms:
    pub-sub-domain: false    # false 表示使用队列, true 表示使用主题

# 自己定义队列名称
myqueue: boot-activemq-queue
```

③ **配置 bean**

```java
@Component
@EnableJms   // 开启 JMS 功能
public class ConfigBean {

    @Value("${myqueue}")
    private String myQueue;

    @Bean
    public Queue queue() {
        return new ActiveMQQueue(myQueue);
    }
}
```

④ **生产者发送消息**

```java
@Component
public class Queue_Produce {

    @Autowired
    private JmsMessagingTemplate jmsMessagingTemplate;

    @Autowired
    private Queue queue;

    public void sendMsg() {
        jmsMessagingTemplate.convertAndSend(queue,"hello shp");
    }

}
```

⑤ **spring 主程序类**

```java
@SpringBootApplication
public class ActivemqProduceApplication {
	public static void main(String[] args) {
		SpringApplication.run(ActivemqProduceApplication.class, args);
	}
}
```

⑥单元测试类

```java
@SpringBootTest(classes = ActivemqProduceApplication.class)
@RunWith(SpringJUnit4ClassRunner.class)
@WebAppConfiguration
public class QueueTest {

    @Autowired
    private Queue_Produce queue_produce;

    @Test
    public void testSend(){
        queue_produce.sendMsg();
    }
}
```

#### 2、整合之队列生产者间隔定投

要求每隔 3 秒钟往 MQ 推送消息

步骤：

① 修改生产者 Queue_Produce ，新增定时投递方法

```java
@Component
public class Queue_Produce {

    @Autowired
    private JmsMessagingTemplate jmsMessagingTemplate;
    @Autowired
    private Queue queue;

    public void sendMsg() {
        jmsMessagingTemplate.convertAndSend(queue,"hello shp");
    }

    /**
     * 间隔 3 秒钟往 MQ 中推送消息
     */
    @Scheduled(fixedDelay = 3000)
    public void sendMsgScheduled() {
        jmsMessagingTemplate.convertAndSend(queue,"hello shp");
    }
}
```

② 修改主启动类

```java
@SpringBootApplication
@EnableScheduling       // 主启动类上面要开启定时注解 @Scheduled 的使用
public class ActivemqProduceApplication {

	public static void main(String[] args) {
		SpringApplication.run(ActivemqProduceApplication.class, args);
	}
}
```

③其他步骤同上面生产者步骤

#### 3、整合之队列消费者

① **pom.xml**

同生产者。

② **application.yml**

```yaml
server:
  port: 8888
spring:
  activemq:
    broker-url: tcp://192.168.148.148:61616 # activemq 服务器地址
    user: admin
    password: admin
  jms:
    pub-sub-domain: false    # false 表示使用队列, true 表示使用主题

# 自己定义队列名称
myqueue: boot-activemq-queue
```

③**消费者通过监听的方式消费消息**

```java
@Component
public class Queue_Consumer {
    @JmsListener(destination = "${myqueue}")
    public void receive(TextMessage textMessage) throws JMSException {
        System.out.println("消费者受到消息：" + textMessage.getText());
    }
}
```

④ **spring 主程序类**

```java
@SpringBootApplication
public class ActivemqConsumerApplication {
	public static void main(String[] args) {
		SpringApplication.run(ActivemqConsumerApplication.class, args);
	}
}
```

#### 4、整合之主题生产者

① **pom.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.5.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.hepingfly.springboot.activemq</groupId>
	<artifactId>activemq_produce</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>activemq_produce</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-activemq</artifactId>
			<version>2.1.5.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

② **application.yml**

```yaml
server:
  port: 7777
spring:
  activemq:
    broker-url: tcp://192.168.148.148:61616 # activemq 服务器地址
    user: admin
    password: admin
  jms:
    pub-sub-domain: true    # false 表示使用队列, true 表示使用主题

# 自己定义主题名称
mytopic: boot-activemq-topic
```

③ **配置 bean**

```java
@Component
public class ConfigBean {
    @Value("${mytopic}")
    private String topicName;

    @Bean
    public Topic topic() {
        return new ActiveMQTopic(topicName);
    }
}
```

④ **生产者发送消息**

```java
@Component
public class Topic_Produce {

    @Autowired
    private JmsMessagingTemplate jmsMessagingTemplate;

    @Autowired
    private Topic topic;

    /**
     * 使用了这个注解，每隔 3 秒钟，生产者就会自动产生一条主题
     */
    @Scheduled(fixedDelay = 3000)
    public void productTopic() {
        jmsMessagingTemplate.convertAndSend(topic, UUID.randomUUID().toString().substring(0,6));
    }
}
```

⑤ **spring 主程序类**

```java
/**
 * 使用了 @Scheduled 注解，在主启动类上就必须标上 @EnableScheduling
 */
@SpringBootApplication
@EnableScheduling
public class TopicProduceApplication {
	public static void main(String[] args) {
		SpringApplication.run(TopicProduceApplication.class, args);
	}
}
```

#### 5、整合之主题消费者

① **pom.xml**

同生产者。

② **application.yml**

```yaml
server:
  port: 6666
spring:
  activemq:
    broker-url: tcp://192.168.148.148:61616 # activemq 服务器地址
    user: admin
    password: admin
  jms:
    pub-sub-domain: true    # false 表示使用队列, true 表示使用主题

# 自己定义主题名称
mytopic: boot-activemq-topic
```

③**消费者通过监听的方式消费消息**

```java
@Component
public class Topic_Consumer {

    @JmsListener(destination = "${mytopic}")
    public void receive(TextMessage textMessage) {
        try {
            System.out.println("消费者收到的订阅主题：" + textMessage.getText());
        } catch (JMSException e) {
            e.printStackTrace();
        }
    }
}
```

④ **spring 主程序类**

```java
@SpringBootApplication
public class ActivemqConsumerApplication {
	public static void main(String[] args) {
		SpringApplication.run(ActivemqConsumerApplication.class, args);
	}
}
```

### 八、ActiveMQ 传输协议

#### 1、activeMQ传输协议简介

activeMQ 支持的 client-broker 通讯协议有：TCP、NIO、UDP、SSL、Http、VM

其中配置 Transport Connector 的文件在 activeMQ 安装目录的 `conf/activemq.xml` 中

```xml
 <transportConnectors>
            <!-- DOS protection, limit concurrent connections to 1000 and frame size to 100MB -->
            <transportConnector name="openwire" uri="tcp://0.0.0.0:61616?maximumConnections=1000&amp;wireFormat.maxFrameSize=104857600"/>
            <transportConnector name="amqp" uri="amqp://0.0.0.0:5672?maximumConnections=1000&amp;wireFormat.maxFrameSize=104857600"/>
            <transportConnector name="stomp" uri="stomp://0.0.0.0:61613?maximumConnections=1000&amp;wireFormat.maxFrameSize=104857600"/>
            <transportConnector name="mqtt" uri="mqtt://0.0.0.0:1883?maximumConnections=1000&amp;wireFormat.maxFrameSize=104857600"/>
            <transportConnector name="ws" uri="ws://0.0.0.0:61614?maximumConnections=1000&amp;wireFormat.maxFrameSize=104857600"/>
</transportConnectors>

```

> 在上面的配置信息中，
>
> URI 描述信息的头部都是采用协议名称，如：
>
> 描述 amqp 协议的监听端口时，采用的 URI 描述格式为：`ampq://…...`
>
> 描述 stomp 协议的监听端口时，采用的 URI 描述格式为：`stomp://…...`
>
> 唯独在进行 openwire 协议描述时，URI 采用的是 `tcp://……`   **这是因为 activeMQ 中默认的消息协议就是 openwire**






















































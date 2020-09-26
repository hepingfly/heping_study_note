# SpringBoot 高级

### 一、缓存

#### 1、JSR107

Java Caching 定义了 5 个核心接口，分别是 `CachingProvider`、`CacheManager`、`Cache`、`Entry`、`Expiry`

> - `CachingProvider` 定义了创建、配置、获取、管理和控制多个 `CacheManager` 。一个应用可以在运行期间访问多个 `CachingProvider` 。
> - `CacheManager` 定义了创建、配置、获取、管理和控制多个唯一命名的 Cache。这些 Cache 存在于 `CacheManager` 的上下文中。一个 `CacheManager` 仅被一个 CachingProvider 所拥有。
> - `Cache` 是一个类似 Map 的数据结构并临时存储以 Key 为索引的值。一个 `Cache` 仅被一个 `CacheManager` 所拥有。
> - `Entry` 是一个存储在 `Cache` 中的 key-value 对。
> - `Expiry` 每一个存储在 Cache 中的条目有一个定义的有效期。一旦超过这个时间，条目为过期的状态。一旦过期，条目将不可访问、更新和删除。缓存有效期可以通过 `ExpiryPolicy` 设置。

#### 2、Spring 缓存抽象

**1）、Spring 缓存**

Spring 从 3.1 开始定义了 `org.springframework.cache.Cache`  和 `org.springframework.cache.CacheManager` 接口来统一不同的缓存技术。并支持使用 `JCache（JSR-107）`注解简化我们的开发。

> - Cache 接口包含缓存的各种操作集合。
> - Cache 接口下 Spring 提供了各种 xxxCache 的实现，比如：RedisCache、EhCache、ConcurrentMapCache
> - 每次调用需要缓存功能的方法时，Spring 会检查指定参数的指定目标方法是否已经被调用过，如果有就直接从缓存中获取方法调用后的结果，如果没有就调用方法并缓存结果后返回给用户。下次调用直接从缓存中获取。

**2）、缓存注解及概念**

| 缓存相关       | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| Cache          | 缓存接口，定义缓存操作。实现有：RedisCache、EhCache、ConcurrentMapCache等 |
| CacheManager   | 缓存管理器，管理各种缓存（Cache）组件                        |
| @Cacheable     | 主要针对方法配置，能够根据方法的请求参数对其结果进行缓存     |
| @CacheEvict    | 清空缓存                                                     |
| @CachePut      | 保证方法被调用，又希望结果被缓存                             |
| @EnableCaching | 开启基于注解的缓存                                           |
| keyGenerator   | 缓存数据时 key 的生成策略                                    |
| serialize      | 缓存数据时 value 序列化策略                                  |

#### 3、@cacheable 使用

1）、缓存使用步骤

> 1、开启基于注解的缓存 `@EnableCaching`
>
> 2、标注缓存注解即可

① 第一步：开启基于注解的缓存，使用 `@EnableCaching` 标注在 springboot 主启动类上

```java
@MapperScan("com.hepingfly.springbootcach.mapper")
@SpringBootApplication
@EnableCaching        // 开启基于注解的缓存
public class SpringbootCachApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootCachApplication.class, args);
    }
}
```

② 标注缓存注解

```java
@Cacheable(cacheNames = "emps", key = "#id")   //  #id 就表示取出参数 id 的值作为 key
public Employee getEmployee(Integer id) {
    System.out.println("查询" + id);
    Employee employee = employeeMapper.getEmpById(id);
    return employee;
}
```

> - 使用 `@Cacheable` 注解将运行结果缓存，以后查询相同的数据，直接从缓存中取，不需要调用方法
> - 说一下 `CacheManager` ,它是用来管理多个 cache 组件的，对缓存的真正 CRUD 操作是在 Cache 组件中，每一个缓存组件都有自己唯一的名字

缓存 Cache 的几个属性：

> - `cacheNames/value` ：用来指定缓存组件的名字
> - `key` ：缓存数据时使用的 key，可以用它来指定。默认是使用方法参数的值。（这个 key 你可以使用 spEL 表达式来编写）
> - `keyGenerator` ：key 的生成器。 key 和 keyGenerator 二选一使用
> - `cacheManager` ：可以用来指定缓存管理器。从哪个缓存管理器里面获取缓存。
> - `condition` ：可以用来指定符合条件的情况下才缓存
> - `unless` ：否定缓存。当 unless 指定的条件为 true ，方法的返回值就不会被缓存。当然你也可以获取到结果进行判断。（通过 `#result` 获取方法结果）
> - sync ：是否使用异步模式。

**下面总结一下使用 spEL 去编写 key 可以用的一些元数据：**

| 名字          | 位置               | 描述                                                         | 示例                   |
| ------------- | ------------------ | ------------------------------------------------------------ | ---------------------- |
| methodName    | root object        | 当前被调用的方法名                                           | `#root.methodName`     |
| method        | root object        | 当前被调用的方法                                             | `#root.method.name`    |
| target        | root object        | 当前被调用的目标对象                                         | `#root.target`         |
| targetClass   | root object        | 当前被调用的目标对象类                                       | `#root.targetClass`    |
| args          | root object        | 当前被调用的方法的参数列表                                   | `#root.args[0]`        |
| caches        | root object        | 当前方法调用使用的缓存列表（如 `@Cacheable(value={"cache1","cache2"})`，则有两个 cache） | `#root.caches[0].name` |
| argument name | evaluation context | 方法参数的名字，可以直接使用 `#参数名` ，也可以使用 `#p0` 或 `#a0` 的形式，0 代表参数的索引。 | `#a0`                  |
| result        | evaluation context | 方法执行后的返回值（仅当方法执行后的判断有效）               | `#result`              |

#### 4、@cacheable 运行流程

```
1、方法运行之前，先去查询 Cache（缓存组件），按照 cacheNames 指定的名字去获取 cache组件。第一次获取缓存如果没有 cache 组件会自动创建。(这个 Cache 组件是通过 CacheManager 去获取的)

2、去 cache 中查找缓存的内容，使用一个 key，默认就是方法的参数。 key 是按照某种策略生成的，默认是使用 keyGenerator 生成的，这个 keyGenerator 默认使用 SimpleKeyGenerator 生成 key

3、没有查到缓存就调用目标方法

4、将目标方法返回的结果，放进缓存中

--------------------------------
总结一下比较重要的两点：
1）、使用 CacheManager(默认使用的是 ConcurrentMapCacheManager) 按照名字得到 Cache（这个 cache 默认是 ConcurrentMapCache）组件
2）、key 使用 keyGenerator 生成的，默认是 SimpleKeyGenerator
```

#### 5、@cacheable 其他属性

① `cacheNames` 

用来指定缓存组件的名字，将方法的返回结果放在哪个缓存中，可以是数组的方式，支持指定多个缓存。

```java
@Cacheable(cacheNames = "emps")  // 或者这么写 @Cacheable(cacheNames = {"emps","heping"}) 
public Employee getEmployee(Integer id) {
    System.out.println("查询" + id);
    Employee employee = employeeMapper.getEmpById(id);
    return employee;
}
```

② `key` 

缓存数据时使用的 key。默认使用的是方法参数的值。可以使用 spEL 表达式去编写。

```java
@Cacheable(cacheNames = "emps", key = "#root.methodName + '[' + #id + ']'")
// 比如这里的 Key 我想用 getEmployee[2]  这样的，可以使用拼接去完成(这里用了 spEL 表达式语法)
public Employee getEmployee(Integer id) {
    System.out.println("查询" + id);
    Employee employee = employeeMapper.getEmpById(id);
    return employee;
}
```

③ keyGenerator

key 的生成器，可以自己指定 key 的生成器，通过这个生成器来生成 key。

```java
/**
 * 自定义 keyGenerator，这样就可以按照我们自定的方式去生成
 * @auther hepingfly
 * @date 2020/8/31 5:41 下午
 */
@Configuration
public class MyCacheConfig {
    @Bean("myKeyGenerator")
    public KeyGenerator keyGenerator() {
        return new KeyGenerator() {
            @Override
            public Object generate(Object o, Method method, Object... params) {
                return method.getName() + "[" + Arrays.asList(params).toString() + "]";
            }
        };
    }
}
```

```java
@Cacheable(cacheNames = "emps", keyGenerator = "myKeyGenerator")  // 这里 keyGenerator 的值写我们自定义的 keyGenerator
public Employee getEmployee(Integer id) {
    System.out.println("查询" + id);
    Employee employee = employeeMapper.getEmpById(id);
    return employee;
}
```

④ `condition`

符合条件的情况下才缓存。方法返回的数据要不要缓存，可以做一个动态判断。

```java
@Cacheable(cacheNames = "emps", condition = "#id > 1")  // 这里就表示 id 值大于 1 我才进行缓存
public Employee getEmployee(Integer id) {
    System.out.println("查询" + id);
    Employee employee = employeeMapper.getEmpById(id);
    return employee;
}
```

⑤ `unless`

否定缓存。当 unless 指定的条件为 true ，方法的返回值就不会被缓存。

```java
@Cacheable(cacheNames = "emps", unless = "#id > 1")  // 这样 id 值大于 1 的都不会被缓存
public Employee getEmployee(Integer id) {
    System.out.println("查询" + id);
    Employee employee = employeeMapper.getEmpById(id);
    return employee;
}
```

⑥ `sync`

是否使用异步模式。默认是方法执行完，以同步的方式将方法返回的结果存在缓存中。

#### 6、@CachePut 使用

标识了这个注解就会既调用方法，又更新缓存数据。主要应用场景就是：修改了数据库的某个数据，同时更新缓存。

这里需要注意 @CachePut  和 @Cacheable 这两个注解的运行时机是不同的：

> - @Cacheable 是在调用方法之前先去缓存里面看有没有指定 key 的数据，如果有直接用缓存，没有才去调用方法。
> - @CachePut  是先调用方法，调用完方法后再将结果放进缓存中。

```java
/**
     * @CachePut 注解，既调用方法，又更新缓存数据
     * 运行时机：
     * 1、先调用目标方法
     * 2、将目标方法的结果缓存起来
     * @author hepingfly
     * @date  2020/9/3 12:22 上午
     * @param employee
     * @return
     */
    @CachePut(value = "emps",key = "#employee.id") // 这里用参数的 id 作为 key
    public Employee updateEmp(Employee employee) {
        employeeMapper.updateEmp(employee);
        return employee;
    }
```

**这里需要注意的点是：**

> 你需要去指定一下 key，这样更新完数据后，才能按照你指定的 key 去覆盖数据。如果你这里不指定 key ，那么数据更新完，就会把入参当成 key 去更新缓存，这样就可能造成缓存数据未覆盖更新情况。

#### 7、@CacheEvict 注解使用

这个注解的使用场景就是：我删除了一个数据，那么同时也就需要在缓存中把这个数据同时删除。

```java
    /**
     * @CacheEvict 缓存清除
     * 1、需要通过 key 来指定要清除的数据
     * 2、可以通过指定 allEntries = true 把缓存中所有的 key 都删除
     * 3、beforeInvocation = false 表示缓存的清除是否在方法之前执行
     * 默认是缓存在方法执行之后执行清除操作，这样如果出现异常，缓存就不会清除
     * @author hepingfly
     * @date  2020/9/3 11:42 下午
     * @param id
     * @return
     */
    @CacheEvict(cacheNames = "emps", key = "#id", allEntries = true,beforeInvocation = false)  // #id 表示获取参数 id 的值
    public void deleteEmp(Integer id) {
        employeeMapper.deleteEmpById(id);
    }
```

**比较重要的两个属性总结：**

> - `allEntries`  ：可以通过指定 allEntries = true 把缓存中所有的 key 都删除。
> - `beforeInvocation` ：beforeInvocation = false 表示缓存的清除是否在方法之前执行。默认是缓存在方法执行之后执行清除操作，这样如果出现异常，缓存就不会清除。

#### 8、@Caching 和 @CacheConfig 注解使用

1）、`@Caching`  注解使用

可以使用 `@Caching` 注解来定义复杂的缓存规则

```java
/**
     * 可以用这个 @Caching 注解来定义复杂的缓存规则
     * @author hepingfly
     * @date  2020/9/5 9:07 下午
     * @param lastName
     * @return
     */
    @Caching(
          cacheable = {
                  @Cacheable(value = "emps", key = "#lastName")
          },
            put = {
                  @CachePut(value = "emps", key = "#result.id"),
                    @CachePut(value = "emps", key = "#result.email")

            }
    )
    public Employee getEmpByLastName(String lastName) {
        return employeeMapper.getEmpByLastName(lastName);
    }
```

2）、`@CacheConfig`  使用

一般我们在方法是标注缓存相关的注解时候，都需要指定 key 。这样如果方法比较多，在每个方法上都指定 key ，显得比较冗余，就可以把指定 key 的操作通过 `@CacheConfig`   来统一指定 key ，标识在类上。这样这个类中每个方法都会统一用这个 key，就不需要在每个方法上都指定。

```java
@Service
@CacheConfig(cacheNames = "emps")  // 这样这个类中所有的方法都会使用 emps 作为 key。就不用在每个方法上都指定一次了。
public class EmployeeService {
}
```

**注：**

> 上面用到的注解，默认使用的都是 ConcurrentMapCache ，将数据保存在 ConcurrentMap 中。但是我们在开发中一般都会使用一些缓存的中间件，像 redis 、memcached、ehcache

### 二、SpringBoot 与消息

#### 1、JMS 和 AMQP 简介

> - 大多数应用中，可通过消息服务中间件来提升系统异步通信、扩展解耦能力。
> - 消息服务中有两个重要的概念：消息代理（Message broker）和 目的地（destination）。当消息发送者发送消息以后，将有消息代理接管，消息代理保证消息传递到指定目的地。
> - 消息队列有两种形式的目的地：
>   - 队列（queue）：点对点消息通信
>     - 消息发送者发送消息，消息代理将其放入一个队列中，消息接收者从队列中获取消息内容，消息读取后被移除队列
>   - 主题（topic）：发布订阅消息通信
>     - 发送者发送消息到主题，多个接收者监听这个主题，那么就会在消息到达同时收到消息。

**说两个概念：**

> - JMS（Java Message Service）：Java 消息服务。基于 JVM 消息代理的规范。 ActiveMQ 是 JMS 的实现。
> - AMQP（Advanced Message Queuing Protocol）：高级消息队列协议。也是一个消息代理的规范，兼容 JMS。RabbitMQ 是 AMQP 的实现。

|              | JMS                                                          | AMQP                                                         |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 定义         | Java Api                                                     | 网络协议                                                     |
| 跨语言       | 否                                                           | 是                                                           |
| 跨平台       | 否                                                           | 是                                                           |
| Model        | 提供两种消息类型：<br />①点对点<br />②发布订阅               | 提供五种消息模型：<br />① direct exchange<br />② fanout exchage<br />③ topic chage<br />④ headers exchange<br />⑤ system exchange<br />本质上来讲，后四种和 JMS 的 pub/sub 模型没有太大差别，仅是在路由机制上做了更详细的划分 |
| 支持消息类型 | TextMessage<br />MapMessage<br />BytesMessage<br />StreamMessage<br />ObjectMessage<br />Message | byte[]<br />当实际应用时，有复杂的消息，可以将消息序列化后发送。 |
| 综合         | JMS 定义了 Java Api 层面的标准，在 Java 体系中，多个 client 均可以通过 JMS 进行交互，不需要应用修改代码，但是其对跨平台的支持性较差。 | AMQP 定义了 wire-level 层的协议标准，天然具有跨平台、跨语言特性。 |

#### 2、RabbitMQ 简介

1）、简介

RabbitMQ 是一个由 erlang 开发的 AMQP（Advanced Message Queue Protocol）的开源实现。

2）、几个重要概念

> - Message
>   - 消息。它是由消息头和消息体组成。消息体是不透明的，而消息头则是由一系列的可选属性组成，这些属性包括 routing-key（路由键）、priority（相对于其他消息的优先权）、delivery-mode（该消息需不需要持久性存储等）
> - Publisher
>   - 消息的生产者。也是一个向交换器发布消息的客户端应用程序。
> - Exchange
>   - 交换器。用来接收生产者发送的消息并叫这些消息路由给服务器中的队列。Exchage 有四种类型：direct（默认），fanout ， topic  和 headers 。不同类型的 Exchage 转发消息的策略有所区别。
> - Queue
>   - 消息队列。用来保存消息直到发送给消费者。它是消息的容器，也是消息的重点。一个消息可投入一个或多个队列。消息一直在队列里面，等待消费者连接到这个队列将其取走。
> - Binding
>   - 绑定。用于消息队列和交换器之间的关联。一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则，所以可以将交换器理解成一个由绑定构成的路由表。Exchange 和 Queue 的绑定可以是多对多的关系。
> - Connection
>   - 网络连接。比如一个 TCP 连接。
> - Channel
>   - 信道。多路复用连接中的一条独立的双向数据流通道。信道是建立在真实的 TCP 连接内的虚拟连接。AMQP 命令都是通过信道发出去的，不管是发布消息、订阅消息还是接收消息，这些动作都是通过信道完成的。因为对于操作系统来说建立和销毁 TCP 都是非常昂贵的开销，所以引入了信道的概念，以复用一条 TCP 连接。
> - Consumer
>   - 消息的消费者，表示一个消息队列中取得消息的客户端应用程序。
> - Virtual Host
>   - 虚拟主机。表示一批交换器、消息队列和相关对象。虚拟主机是共享相同的身份认证和加密环境的独立服务器域。每个 vhost 本质上就是一个 mini 版的 RabbitMQ 服务器，拥有自己的队列、交换器、绑定和权限机制。 vhost 是 AMQP 概念的基础，必须在连接时指定，RabbitMQ 默认的 vhost 是 `/`
> - Broker
>   - 表示消息队列服务器实体

上述概念之间的关系可以用图表示：

![概念之前关系](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot%E9%AB%98%E7%BA%A7/rabbitMQ%E9%87%8D%E8%A6%81%E6%A6%82%E5%BF%B5%E5%85%B3%E7%B3%BB.png)

#### 3、RabbitMQ 运行机制

**1）、AMQP 中的消息路由**

AMQP 中消息的路由过程和 Java 开发者熟悉的 JMS 存在一些差别，AMQP 中增加了 `Exchange` 和 `Binding` 的角色。生产者把消息发布到 Exchage 上，消息最终到达队列并被消费者接收，而 Binding 决定交换器的消息应该发送到哪个队列上。

**2）、Exchange 类型**

Exchage 分发消息时根据类型的不同分发策略有区别，目前共有四种类型：`direct`  、`fanout`  、`topic` 、`headers` 。`headers` 匹配 AMQP 消息的 header 而不是路由键，`headers` 交换器和 `direct` 交换器完全一致，但性能上差很多，目前几乎用不到了，所以了解其他三种类型即可。 

① Direct Exchange

消息中的路由键（routing key）如果和 Binding 中的 binding key 一致，交换器就将消息发到对应的队列中。路由键与队列名完全匹配，如果一个队列绑定到交换机要求路由键为 「Jimmy」 ，则只转发 routing key 标记为 「Jimmy 」的消息，不会转发 「JimmyA」 ，也不会转发 「JimmyB」。它是完全匹配、单播的模式。

② Fanout Exchange

每个发到 fanout 类型交换器的消息都会分到所有绑定的队列上去。fanout 交换器不处理路由键，只是简单的将队列绑定到交换器上，每个发送到交换器的消息都会被转发到与该交换器绑定的所有队列上。很像子网广播，每台子网内的主机都获得了一份复制消息。fanout 类型转发消息是最快的。（说白点就是 fanout 会给下面绑定的队列都发一份消息）

③ Topic Exchange

topic 交换器通过模糊匹配分配消息的路由键属性，将路由键和某个模式进行匹配，此时队列需要绑定到一个模式上。它将路由键和绑定键的字符串切分成单词，这些**单词之间用点隔开** 。它会识别两个通配符，「`#`」 表示匹配 0 个或多个单词，「`*`」匹配一个单词。

####

### 三、SpringBoot 与检索



### 四、SpringBoot 与定时任务



#### 2、定时任务

在项目开发中经常需要执行一些定时任务，比如需要在每天凌晨的时候，分析前一天的日志信息。 Spring 为我们提供了异步执行任务调度的方式，提供了 `TaskExecutor`、`TaskScheduler` 接口。

两个重要的注解：

> - `@Scheduled`
>   - 标注在需要开启定时任务的方法上
> - `@EnableScheduling`
>   - 标注在 springboot 的主启动类上，用于开启基于注解的定时任务

示例代码：

```java
@EnableScheduling
@SpringBootApplication  // 开启基于注解的定时任务
public class SpringbootAmqpApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootAmqpApplication.class, args);
    }
}


//-----------------------------------------------------------------------------------
/**
 * 测试 springboot 定时任务
 * @auther hepingfly
 * @date 2020/9/21 11:52 下午
 */
@Service
public class ScheduledService {

    /**
     * 秒 分 时 日 月 周几
     * 0 * * * * MON-FRI
     * 上面就表示周一到周五，每分钟的第 0 秒开始执行
     */
    @Scheduled(cron = "0 * * * * MON-FRI")  //这样每分钟都会运行一次这个方法
    public void attention() {
        System.out.println("欢迎关注 hepingfly");
    }
}
```

再说一下这个 cron 表达式可以写哪些东西：

| 字段 | 允许值                     | 允许的特殊字符                                       |
| ---- | -------------------------- | ---------------------------------------------------- |
| 秒   | 0-59                       | `,`    `-`     `*`      `/`                          |
| 分   | 0-59                       | `,`    `-`     `*`      `/`                          |
| 小时 | 0-23                       | `,`    `-`     `*`      `/`                          |
| 日期 | 1-31                       | `,`    `-`     `*`     `?`   `/`    `L`   `W`  `C`   |
| 月份 | 1-12                       | `,`    `-`     `*`      `/`                          |
| 星期 | 0-7或 SUN-SAT   0,7 是 SUN | `,`    `-`     `*`     `?`    `/`    `L`  `C`    `#` |

| 特殊字符 | 表示含义                     |
| -------- | ---------------------------- |
| `,`      | 枚举                         |
| `-`      | 区间                         |
| `*`      | 任意                         |
| `/`      | 步长                         |
| `?`      | 日/星期 冲突匹配             |
| `L`      | 最后                         |
| `W`      | 工作日                       |
| `C`      | 和 calendar 联系后计算过的值 |
| `#`      | 星期， 4#2  第二个星期四     |



cron 表达式写法示例：

```
① 0 * * * * MON-FRI     这个表示周一到周五，每分钟的第 0 秒开始执行
② 0,1,2,3 * * * * MON-FRI    这个表示周一到周五，每分钟的第 0,1,2,3 秒开始执行
③ 0-3 * * * * MON-FRI        表示含义与第 ② 种相同
④ 0/4 * * * * MON-FRI		 这个表示周一到周五,每 4 秒执行一次
⑤ 0 0/5 14,18 * * ? 		这个表示每天 14 点整和 18 点整，每隔 5 分钟执行一次
⑥ 0 15 10 ? * 1-6			这个表示每个月的周一至周六 10:15 分执行一次
⑦ 0 0 2 ? * 6 L 			每个月的最后一个周六凌晨 2 点执行一次
⑧ 0 0 2 LW * ?				每个月的最后一个工作日凌晨 2 点执行一次
⑨ 0 0 2-4 ? * 1#1			每个月的第一个周一凌晨 2 点到 4 点之间,每个整点都执行一次
```


























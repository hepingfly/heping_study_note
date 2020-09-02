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
    @CachePut(value = "emps",key = "#employee.id")
    public Employee updateEmp(Employee employee) {
        employeeMapper.updateEmp(employee);
        return employee;
    }
```

**这里需要注意的点是：**

> 你需要去指定一下 key，这样更新完数据后，才能按照你指定的 key 去覆盖数据。如果你这里不指定 key ，那么数据更新完，就会把入参当成 key 去更新缓存，这样就可能造成缓存数据未覆盖更新情况。




























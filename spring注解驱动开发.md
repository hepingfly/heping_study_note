#  Spring 注解驱动开发

## 一、容器

### 1、组件注册

#### 1、@Configuration&@Bean给容器中注册组件

先写一个之前通过 spring 配置文件，启动 ioc 容器加载 bean 的例子：

```java
// Person.java
package com.iflytek.bean;
public class Person {
    private String name;
    private Integer age;

    // getter setter 方法省略
    
    public Person() {
    }

    public Person(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

```xml
<!-- 类路径下的 beans.xml  spring 的配置文件 -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="person" class="com.iflytek.bean.Person">
        <property name="name" value="Jimmy"></property>
        <property name="age" value="44"></property>
    </bean>
</beans>
```

```java
// MainTest.java 测试文件
public class MainTest {
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("beans.xml");
        Person person = (Person) ctx.getBean("person");
        System.out.println(person);    // Person{name='Jimmy', age=44}
    }
}
```

上面的这种方式通过 spring 的配置文件注册 bean ，然后启动 ioc 容器，ioc 容器加载所有的 bean。

**下面演示通过 @Configuration  和 @Bean 这两个注解来给容器中注册 bean：**

```java
// MainConfig.java:
// 这里的配置类，就等于 spring 的配置文件
@Configuration   // 这个注解告诉 spring 这是一个配置类
public class MainConfig {

    /**
     *   <bean id="person" class="com.iflytek.bean.Person">
     *       <property name="name" value="Jimmy"></property>
     *       <property name="age" value="44"></property>
     *   </bean>
     */
    // 给容器中注册一个 bean，类型为返回值类型，id 默认用方法名作为 id
    @Bean
    public Person person() {
        return new Person("Jimmy",44);
    }
    // 上面我们说了这个 bean 的 id 默认就是方法名，那假如我想改这个bean 的名字怎么办？
    // 一种方法就是把方法名改了，另一种方法就是 @Bean("person01")  这样这个 bean 的 id 就叫 person01,不会再看你的方法名
}
```

```java
public class MainTest {
    public static void main(String[] args) {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfig.class);
        // 这里 person 这个名字，来自于上面配置文件里面的方法名
        Person person = (Person) ctx.getBean("person");
        System.out.println(person);   // Person{name='Jimmy', age=44}

    }
}
```

总结一下：

> - 你可以使用 `@Configuration` 这个注解来标识一个类，那么这个类就是一个配置类
>
>
> - 在这个配置类中你可以使用 `@Bean` 注解去注册 bean  
>   - 注册 bean 的时候，方法的返回值就是 bean 的类型，方法名就是 bean 的 id
>     - 如果想改 bean 的 id ，一种就是直接修改方法名，另一种就是在 @Bean 注解上指定 bean 的 id
> - bean 在配置类中写好了之后，你可以在测试类中通过 `AnnotationConfigApplicationContext` 去加载配置类，获取 ioc 容器，这样从 ioc 容器中就能拿到 bean 了。

#### 2、@ComponentScan 自动扫描组件

先写一下之前我们通过 spring 配置文件，去扫描组件的方式：

```xml
<!-- beans.xml spring 配置文件 -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <!--
        包扫描，会扫描标注了 @Controller @Service @Respository @Component 标识的类
        会把这些类加入到 ioc 容器中去
    -->
    <context:component-scan base-package="com.iflytek"></context:component-scan>
</beans>
```

`在 spring 的配置文件中配置了上面的包扫描器后，你在实体类上标 @Controller @Service @Respository @Component` 这几个注解，就会被自动加入到 ioc 容器中去。

下面通过 `@ComponentScan` 注解来实现：

```java
// MainConfig.java   前面我们说了这个配置类，就相当于之前 spring.xml 这个配置文件
@Configuration
@ComponentScan(value = "com.iflytek")   // 这个注解指定包扫描器，扫描哪个包
public class MainConfig {
    // 给容器中注册一个 bean，类型为返回值类型，id 默认用方法名作为 id
    @Bean
    public Person person() {
        return new Person("Jimmy",44);
    }
}
```

```java
// BookController.java
@Controller
public class BookController {
}

// ------------------------
//BookService.java
@Service
public class BookService {
}
```

```java
// 单元测试类	
	@Test
    public void test01() {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfig.class);
        // 这个方法是获取 ioc 容器中所有 bean 的名字
        String[] definitionNames = ctx.getBeanDefinitionNames();
        for (String name : definitionNames) {
            System.out.println(name);  // mainConfig   bookController  bookService  person
        }
    }
// 说明这些 bean 都被自动加入到 ioc 容器中去了
```

**指定扫描规则：**

```java
// MainConfig.java
@Configuration   
@ComponentScan(value = "com.iflytek",excludeFilters = {
        @ComponentScan.Filter(type = FilterType.ANNOTATION,value = {Controller.class})
})   // 这里就表示按照注解的方式，排除 Controller 注解标识的类
// @ComponentScan value 指定要扫描的包
// excludeFilters = Filter[]   指定扫描的时候按照什么规则排除哪些组件
//includeFilters = Filter[]   指定扫描的时候只需要包含哪些组件
// useDefaultFilters = false   禁用掉默认过滤规则，这样 include 才能生效
@ComponentScan   // 这个 @ComponentScan 可以写多次，来指定不同的扫描策略
public class MainConfig {
    // 给容器中注册一个 bean，类型为返回值类型，id 默认用方法名作为 id
    @Bean
    public Person person() {
        return new Person("Jimmy",44);
    }
}

// 注意：
//假如你要配置 includeFilters，别忘了，要先配置一下 useDefaultFilters = false  禁用掉默认的过滤规则，这样，include 才能生效
```

```java
// 单元测试类	
	@Test
    public void test01() {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfig.class);
        // 这个方法是获取 ioc 容器中所有 bean 的名字
        String[] definitionNames = ctx.getBeanDefinitionNames();
        for (String name : definitionNames) {
            System.out.println(name);  // mainConfig  bookService  person
            // 这里 就不会打印 bookController，因为这个组件被排除了
        }
    }
```

总结一下：

> - 之前我们在 spring 的配置文件中配置的包扫描器，现在我们可以在配置类上通过 @ComponentScan 注解来配置
> - excludeFilters   和  includeFilters 这些过滤规则，在注解中也是可以使用的
> - 在使用 includeFilters 的时候，记得先 `useDefaultFilters = false`  禁用掉默认的过滤规则，否则，include 不生效
> - @ComponentScan  这个注解可以写多次来指定不同的扫描策略

#### 3、自定义 FilterType 过滤规则

上面我们说了使用 @ComponentScan 这个注解可以创建一个包扫描器，在这个包扫描器中，通过 includeFilters 和 excludeFilters   可以去指定过滤规则，就是把哪些组件加到 IOC 容器中，哪些组件不加到 IOC 容器中。

```java
// MainConfig.java
@Configuration  
// 下面的意思是，按照给定的类型进行过滤，扫描 com.iflytek 包下的组件，只把 BookService 类型的组件加到 ioc 容器中
@ComponentScan(value = "com.iflytek",includeFilters = {@ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE,value = {BookService.class})},useDefaultFilters = false)
// type = FilterType.ANNOTATION  type 用来执行过滤规则
// FilterType.ANNOTATION  按照注解进行过滤
// FilterType.ASSIGNABLE_TYPE  按照给定的类型
// FilterType.REGEX  按照正则表达式
// FilterType.CUSTOM  按照自定义类型

public class MainConfig {
    // 给容器中注册一个 bean，类型为返回值类型，id 默认用方法名作为 id
    @Bean
    public Person person() {
        return new Person("Jimmy",44);
    }
}
```

下面重点来说下 `FilterType.CUSTOM`  按照自定义规则来过滤：

```java
/*
 * MainConfig.java
*/
@Configuration 
// 下面注解的意思是只包含哪些组件，过滤规则我们自定义
@ComponentScan(value = "com.iflytek",includeFilters = {
@ComponentScan.Filter(type = FilterType.CUSTOM,value =      {MyTypeFilter.class})},useDefaultFilters = false)
public class MainConfig {
    // 给容器中注册一个 bean，类型为返回值类型，id 默认用方法名作为 id
    @Bean
    public Person person() {
        return new Person("Jimmy",44);
    }
}

// @ComponentScan.Filter(type = FilterType.CUSTOM,value ={MyTypeFilter.class})  value 就是自定义的 TypeFilter 类型
```

```java
/**
 * 按照自定义规则来过滤，需要实现 TypeFilter 接口
 */
public class MyTypeFilter implements TypeFilter {
    /**
     *
     * @param metadataReader 读取到的当前正在扫描的类的信息
     * @param metadataReaderFactory 可以获取到其他任何类信息
     * @return
     * @throws IOException
     */
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) throws IOException {
        // 获取当前类注解的信息
        AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
        // 获取当前正在扫描的类的类信息
        ClassMetadata classMetadata = metadataReader.getClassMetadata();
        // 获取当前类的资源信息（类的路径）
        Resource resource = metadataReader.getResource();
        // 获取类名
        String className = classMetadata.getClassName();
        // 这样扫描到哪个类就会把哪个类的类名打出来
        System.out.println("--->" + className);
        if (className.contains("service")) {
            // 类名如果包含 service 的，就让他返回 true 匹配成功，加入到 ioc 容器中去
            return true;
        }

        // 我们写的这个自定义规则过滤，因为 @ComponentScan(value="com.iflytek") 扫描的是 com.iflytek 包下，所以这个包下所有的类都会被扫描一遍，然后看是否满足你的匹配规则，如果匹配就加到 IOC 容器中去
        return false;
    }
}
```

```java
/**
 * 单元测试类
*/
	@Test
    public void test01() {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfig.class);
        // 这个方法是获取 ioc 容器中所有 bean 的名字
        String[] definitionNames = ctx.getBeanDefinitionNames();
        for (String name : definitionNames) {
            System.out.println(name);    // mainConfig   bookService    person
        }
    }

// 这样就会把符合你自己定义的规则的组件加到 ioc 容器中去(mainConfig 是 @Configuration 注解本身就会把自己加入到 ioc 容器中，person 是在配置类中手动加的，bookService 是我自定义过滤规则加入到 ioc 容器中去的)
```

总结一下：

> - 自定义包扫描器的过滤规则，需要 把 `type = FilterType.CUSTOM` 然后 value 写你自定义的过滤类
> - 按照自定义规则来过滤，需要实现 TypeFilter 接口，然后重写抽象方法，在抽象方法里面指定过滤规则，返回 true 表示匹配，加入到 ioc 容器中，返回 false 表示不匹配

#### 4、@Scope 设置组件作用域

```java
/*
 * MainConfig2.java
*/
@Configuration
public class MainConfig2 {
    /**
     * singleton : 单实例
     *      ioc 容器启动的时候就会调用方法创建对象放到容器中，以后每次获取就直接从容器中拿
     * prototype : 多实例
     *      ioc 容器启动的时候并不会去调用方法创建对象放在容器中，每次获取的时候才会调用方法创建对象
     * @return
     */
    @Scope("prototype")
    @Bean(name = "person")
    public Person person() {
        System.out.println("person 对象创建了");
        return new Person("kimi",11);
    }
}
```

```java
/*
 * 单元测试类
*/
	@Test
    public void test02() {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfig2.class);
        // 现在 person 的作用域写的是 prototype，假如我不写下面两行获取 person 的代码，你会发现 「person 对象创建了」 并没有被打印，只有在获取的时候，提示信息才会被打印，这也就可以解释，prototype 为什么是多实例的，因为每次获取的时候都会去创建一个实例
        Object person1 = ctx.getBean("person");
        Object person2 = ctx.getBean("person");
        System.out.println(person1 == person2);   //false
    }
```

总结一下：

> - @Scope 注解可以设置组件的作用域
> - singleton 表示单例，ioc 容器启动的时候就会去创建对象，只会创建一次
> - prototype 表示多例，ioc 容器启动的时候不会去创建对象，使用对象的时候才会去创建，每使用一次创建一次对象

#### 5、@Lazy 注解设置 bean 懒加载

```java
/*
 * MainConfig2.java
*/
@Configuration
public class MainConfig2 {
    /**
     * 懒加载：
     *      单实例 bean ：默认在容器启动的时候创建对象
     *      懒加载：容器启动的时候不创建对象，第一次使用 bean 的时候创建对象，并初始化
     * @return
     */
    @Lazy
    @Bean(name = "person")
    public Person person() {
        System.out.println("person 对象创建了");
        return new Person("kimi",11);
    }
}
```

```java
/*
 * 单元测试类
*/
	@Test
    public void test02() {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfig2.class);
//        Object person1 = ctx.getBean("person");
    }

// 假如在上面配置类中我不写 @Lazy 注解，那么测试类中 ioc 容器一启动，就会创建 bean ，person 中的信息「person 对象创建了」 就会打印。
// 如果我写了 @Lazy 注解 那么在测试类中，启动 IOC 容器，如果我不获取 bean 那么 bean 就不会创建，只有在获取的时候才会创建，并初始化，但是创建也只会创建一次，下次获取的还是这个实例
```

总结一下：

> - @Lazy 注解 针对单实例 bean ，标识在 bean 上，会对该 bean 启动懒加载
> - ioc 容器启动的时候，不会创建这个 bean ，只有在使用 bean 的时候才会去创建 bean 并初始化，并且 bean 的实例也只是会创建一次

#### 6、@Conditional 注解按照条件注册 bean

```java
/*
 * MainConfig2.java
*/
@Configuration
public class MainConfig2 {
    
    @Bean(name = "person")
    public Person person() {
        return new Person("kimi",11);
    }

    @Bean(name = "durant")
    public Person person01() {
        return new Person("durant",22);
    }

    @Bean(name = "james")
    public Person person02() {
        return new Person("james",24);
    }
```

```java
@Test
    public void test03() {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfig2.class);
        // 根据类型获取所有 bean 的名字
        String[] beanNames = ctx.getBeanNamesForType(Person.class);
        for (String name : beanNames) {
            System.out.println(name);  // james durant person
        }
    }
```

上面我在配置类中创建了三个 bean，按照正常情况，ioc 容器一启动，这三个 bean 就不会被创建并加到容器中。结果也确实是，我们在测试类中可以看到，三个 bean 确实被注册到 ioc 容器中去了。

**假如我现在想根据一定条件来注册 bean 应该怎么做？** 比如说当前系统是 Mac OS X 我就把 durant 这个 bean 注册到容器中，如果当前系统是 windows 我就把 james 注册到容器中

**MainConfig2 .java**

```java
@Configuration
public class MainConfig2 {

    @Bean(name = "person")
    public Person person() {
        return new Person("kimi",11);
    }

    /**
     * @Conditional ：spring4.0 之后出现的注解，而且这个注解在 springboot 底层大量使用
     *           作用：按照一定条件进行判断，满足条件才给容器中注册 bean
     */
    @Conditional({MacCondition.class})   // @Conditional 注解里面是我自定义的条件规则，你去写这个规则的时候必须要实现 Condition 接口，写好了之后把它填到这里来
    @Bean(name = "durant")
    public Person person01() {
        return new Person("durant",22);
    }

    @Conditional({WindowsCondition.class})
    @Bean(name = "james")
    public Person person02() {
        return new Person("james",24);
    }
}
```

**MacCondition.java**

```java
/**
 * 判断是否是 Linux 系统
 * 你写的判断条件必须要去实现 Condition 接口
 */
public class MacCondition implements Condition{

    /**
     *
     * @param conditionContext 判断条件能使用的上下文环境
     * @param annotatedTypeMetadata 注释信息
     * @return
     */
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        // 能获取到 ioc 容器使用的 beanFactory
        ConfigurableListableBeanFactory beanFactory = conditionContext.getBeanFactory();

        // 获取到类加载器
        ClassLoader classLoader = conditionContext.getClassLoader();

        //获取当前环境信息
        Environment environment = conditionContext.getEnvironment();

        // 获取 bean 定义的注册类
        BeanDefinitionRegistry definitionRegistry = conditionContext.getRegistry();
        String osName = environment.getProperty("os.name");
        if ("Mac OS X".equals(osName)) {
            return true;
        }
        return false;
    }
}
```

**WindowsCondition.java**

```java
/**
 *  判断是否是 windows 系统
 */
public class WindowsCondition implements Condition {
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        Environment environment = conditionContext.getEnvironment();
        String osName = environment.getProperty("os.name");
        if ("Windows".equals(osName)) {
            return true;
        }
        return false;
    }
}
```

```java
    @Test
    public void test03() {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfig2.class);
        Environment environment = ctx.getEnvironment();
        String osName = environment.getProperty("os.name");
        System.out.println(osName);  // Mac OS X

        // 根据类型获取所有 bean 的名字
        String[] beanNames = ctx.getBeanNamesForType(Person.class);
        for (String name : beanNames) {
            // 如果你的操作系统是 Mac OS X，你会发现只有 durant 这个 bean 被注册到 ioc 容器中了
            System.out.println(name);  // durant person
        }

        // 根据类型获取容器中所有的 bean
        Map<String, Person> personsMap = ctx.getBeansOfType(Person.class);
        System.out.println(personsMap);
    }
```

上面的思路就是，我在配置类中配置 bean 的时候，使用 @Conditional 注解来指定规则，只有满足条件规则的 bean 才会被注入到 ioc 容器中。@Conditional 注解同样也可以标识在类上，标识在类上作用是：**类中组件统一设置，满足当前条件，这个类中配置的所有 bean 注册才能生效**

```java
/**
 * @Conditional 标识在类上表示，类中组件统一设置，满足当前条件，这个类中配置的所有 bean 注册才能生效
 */
@Conditional({MacCondition.class})  // 满足这个条件下面的所有 bean 才会被注册到 ioc 容器中，否则都不会被注册到容器中
@Configuration
public class MainConfig2 {

    @Bean(name = "person")
    public Person person() {
        return new Person("kimi",11);
    }
    
    @Conditional({MacCondition.class})
    @Bean(name = "durant")
    public Person person01() {
        return new Person("durant",22);
    }

    @Conditional({WindowsCondition.class})
    @Bean(name = "james")
    public Person person02() {
        return new Person("james",24);
    }
}
```

总结一下：

> - `@Conditional` 这个注解的作用是，按照条件来注册 bean
> - `@Conditional` 这个注解可以标在类上，也可以标在方法上
>   - 标在方法上表示，只有满足这个条件规则，这个 bean 才会被注册到 ioc 容器中
>   - 标在类上表示，满足这个条件，类里面的所有组件都会被注册到 ioc 容器中，否则都不注册到 ioc 容器中
> - `@Conditional` 这个注解的 value 值是一个数组，可以写多个条件
>   - 使用这个注解你需要自己去定义条件，定义的条件类需要实现 Condition 接口

**小技巧：**

> 在 eclipse 里面你可以通过配置虚拟机参数来改变虚拟机运行环境
>
> 在 eclipse 虚拟机参数里面配置 `-Dos.name=linux`    表示当前操作系统为 linux 

#### 7、@Import 导入一个组件

我们首先回忆一下给容器中注册组件有几种方式：

> - 1、包扫描 + 组件标注注解（@Controller @Service @Respository @Component）
>        但是这种方式有局限，要我们写好类，然后在类上标注注解，配置好包扫描器，这样这个组件才会被注册到容器中。 假如我引入一个第三方包，没有办法标注注解，那么就注册不到容器中了。可以采用下面第二种方法：
> - 2、@Bean 方式
>         假如你有第三方组件需要加到 IOC 容器中，你可以在配置类中通过 @Bean 方式加入到 IOC 容器中
>         但是这种方式，如果你每次添加一个简单组件，就通过 @Bean 然后写个方法创建一个实体类，这样会有点麻烦，所以也可以使下面第三种方法：
> - 3、@Import  快速给容器中添加一个组件

```java
Color.java:
public class Color {
}

//-----------------------
Red.java
public class Red {
}
```

假如我现在想把上面的两个类加入到 ioc 容器中，下面我使用 @Import 方式：

```java
@Configuration
@Import({Color.class, Red.class})     // 这样我就把这两个类加入到 IOC 容器中了,bean 的 id 默认是全类名
public class MainConfig2 {
}
```

```java
	/*
	 *  测试类
	*/
	@Test
    public void test04() {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfig2.class);
        String[] definitionNames = ctx.getBeanDefinitionNames();
        for (String name : definitionNames) {
            System.out.println(name);  // com.iflytek.bean.Color   com.iflytek.bean.Red
        }
    }
```

总结一下：

> - @Import  快速给容器中添加一个组件
> - 这样容器中就会自动注册这个组件,id 默认是全类名

**注意：**

==@Import 这个注解最好在 spring4.2 版本之后使用，否则可能会出现错误==

#### 8、使用 ImportSelector

首先说，@Import 这个注解作用就是可以快速把组件加入到 ioc 容器中，`@Import({Color.class, Red.class})`  这个意思就是把 Color 和 Red 这两个组件加入到 ioc 容器中，在 @Import 注解里面也可以写 ImportSelector，下面说下具体怎么做：

① 需要去创建一个类，这个类需要实现 ImportSelector 接口

```java
/**
 * 自定义逻辑返回需要导入的组件
 */
public class MyImportSelector implements ImportSelector {

    /**
     * 返回值就是导入到容器中组件的全类名
     * @param annotationMetadata 当期标注 @Import 注解的类的所有注解信息
     * @return
     */
    public String[] selectImports(AnnotationMetadata annotationMetadata) {

        // 这个方法尽量不要放回 Null 值，否则会发生空指针异常
        return new String[]{"com.iflytek.bean.Blue","com.iflytek.bean.Yellow"};
    }
}
```

② 在 @Import 注解中标识你自己写的 ImportSelectot

```java
@Configuration
// 下面表示的意思是把 Color Red 这两个组件加入到 ioc 容器中，然后把 MyImportSelector 中要导入的组件加入到 ioc 容器中
@Import({Color.class, Red.class, MyImportSelector.class})
public class MainConfig2 {
}

// 这个 MyImportSelector 是一个普通的类，但是它实现了 ImportSelector 接口，把 MyImportSelector 写到 @Import 注解中就表示把它的返回值加入到 ioc 容器中，而不是把这个普通类加入到 ioc 容器中
```

总结一下：

> - @Import 注解可以快速把一个组件加入到 IOC 容器中
> - 你可以写一个实现了 ImportSelector 接口的类，然后结合 @Import 注解实现批量把组件加入到 ioc 容器中
> - 实现了 ImportSelector 接口的类结合 @Import 注解，是把实现了 ImportSelector 接口的类的返回值加入到 ioc 容器中，而不是把这个类加入到 ioc 容器中

#### 9、使用 ImportBeanDefinitionRegistrar

上面我们说了 ImportSelector 可以结合 @Import 注解，往 ioc 容器中批量注册 bean ，使用 ImportBeanDefinitionRegistrar 结合 @Import 注解也能实现同样的效果。下面说下具体步骤：

① 需要去创建一个类，这个类需要实现 ImportBeanDefinitionRegistrar 接口

```java
/**
 * 自定义的 bean 定义注册类
 */
public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {
    /**
     *
     * @param annotationMetadata 当前类的注解信息
     * @param beanDefinitionRegistry  beanDefinition注册类
     * 我们可以把所有需要添加到容器中的 bean ，调用 beanDefinitionRegistry.registerBeanDefinition 手工注册进来
     */
    public void registerBeanDefinitions(AnnotationMetadata annotationMetadata, BeanDefinitionRegistry beanDefinitionRegistry) {
        boolean redExit = beanDefinitionRegistry.containsBeanDefinition("com.iflytek.bean.Red");
        boolean blueExit = beanDefinitionRegistry.containsBeanDefinition("com.iflytek.bean.Blue");
        // 如果上面这两个 bean 在 ioc 容器中存在的话，我就把 RainBow 注册到 IOC 容器
        if (redExit && blueExit) {
            // 指定 bean 的定义信息
            RootBeanDefinition rainbowBeanDefinition = new RootBeanDefinition(RainBow.class);
            // 注册一个 bean ,指定 bean 名
            beanDefinitionRegistry.registerBeanDefinition("rainbow",rainbowBeanDefinition);
        }
    }
}
```

② 在 @Import 注解中标识你自己写的 ImportBeanDefinitionRegistrar

```java
@Configuration
// 下面的注解表示把 Color Red 加入到 ioc 容器中，把 MyImportSelector 中要加入的组件加入到 ioc 容器中，把 MyImportBeanDefinitionRegistrar 中要注册的 bean 注册到 ioc 容器中
@Import({Color.class, Red.class, MyImportSelector.class, MyImportBeanDefinitionRegistrar.class})    // 在这里写上你自定义的 bean 定义注册类
public class MainConfig2 {
}
// 这个 MyImportBeanDefinitionRegistrar 是一个普通的类，但是它实现了 ImportBeanDefinitionRegistrar 接口，把 MyImportBeanDefinitionRegistrar 写到 @Import 注解中就表示把它这个类中需要注册的 bean 加入到 ioc 容器中，而不是把这个普通类加入到 ioc 容器中
```

总结一下：

> - 这个 ImportBeanDefinitionRegistrar 和上面的 ImportSelector 类似，用来结合 @Import 注解使用。
> - 作用就是把实现了这个接口的类中注册的 bean 加入到 ioc 容器中

#### 10、FactoryBean 注册组件

通过实现 FactoryBean 接口也可以往 ioc 容器中注册 bean

具体步骤：

① 创建一个实体类，这个实体类需要去实现 FactoryBean 接口

```java
/**
 * 创建一个 Spring 定义的 FactoryBean
 */
public class ColorFactoryBean implements FactoryBean<Color> {

    /**
     * 返回一个 Color 对象，这个对象会添加到容器中
     */
    public Color getObject() throws Exception {
        return new Color();
    }
    
	/**
     * 返回创建对象的类型
     */
    public Class<?> getObjectType() {
        return Color.class;
    }

    /**
     * 是否是单实例
     * true ：这个 bean 是单实例，在容器中仅保存一份
     * false ：多实例，每次获取都会创建一个新的 bean
     */
    public boolean isSingleton() {
        return true;
    }
}
```

② 将上面创建的实体类加入到 ioc 容器中

```java
@Configuration
public class MainConfig2 {
    @Bean
    public ColorFactoryBean colorFactoryBean() {
        return new ColorFactoryBean();
    }
}

// 我们都知道这种写法就是把 ColorFactoryBean 这个 bean 加入到 ioc 容器中，这个 bean 的 id 叫 colorFactoryBean(但是是实际长它会调用这个 bean 中 getObject 方法把返回的 bean 加入到 ioc 容器中)
```

③ 测试类

```java
@Test
    public void test04() {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfig2.class);
        // 工厂 bean 获取的是调用 getObject 创建的对象
        Object bean = ctx.getBean("colorFactoryBean");
        System.out.println("获取到 bean 的类型" + bean.getClass()); // 获取到 bean 的类型class com.iflytek.bean.Color
        // 假如我就像获取到 IOC 容器中注册的 colorFactoryBean 本身，可以这么获取
        Object bean2 = ctx.getBean("&colorFactoryBean");
        System.out.println("bean 类型是：" + bean2.getClass());  // bean 类型是：class com.iflytek.bean.ColorFactoryBean
    }

/*
上面代码的意思是：
1、我在配置类中把 colorFactoryBean 这个 bean 加入到 ioc 容器中了，使用 @Bean 注解，那么理论上我就可以从 ioc 容器中获取到这个 bean。测试类中我通过 bean 的 id 获取到了这个 bean ，是 Object 类型，我想知道这个 bean 具体是什么类型，打印了一下发现是 com.iflytek.bean.Color，也就是说我获取到的这个工厂 bean 实际上是调用 getObjcet 创建的对象

2、上面也说了我把 colorFactoryBean 这个 bean 加入到了 ioc 容器中，但是通过这个 bean 的名字获取到的实际上是 getObject 创建的对象，那么假如我就像获取到这个 bean 该怎么做？
答：就是在 bean 的 id 前面加一个 & 符号就能获取到本来的这个 bean。
原因是：
public interface BeanFactory {
    String FACTORY_BEAN_PREFIX = "&";      beanFactory 中定义了这个前缀
}

*/
```

总结一下：

> 使用 Spring 提供的 FactoryBean 去注册 bean 
>
> - 默认获取到的是工厂 bean 调用 getObject 创建的对象
> - 要获取工厂 bean 本身，我们需要给 bean 的 id 前面加一个 & 符号

总计一下向容器中注册 bean 的几种方式：

```java
  /**
     * 给容器中注册组件的几种方式：
     * 1、包扫描 + 组件标注注解（@Controller @Service @Respository @Component）
     * 但是这种方式有局限，要我们写好类，然后在类上标注注解，配置好包扫描器，这样这个组件才会被注册到容器中
     * 假如我引入一个第三方包，没有办法标注注解，那么就注册不到容器中了。可以采用下面第二种方法：
     *
     * 2、@Bean 方式
     * 假如你有第三方组件需要加到 IOC 容器中，你可以在配置类中通过 @Bean 方式加入到 IOC 容器中
     * 但是这种方式，如果你每次添加一个简单组件，就通过 @Bean 然后写个方法创建一个实体类，这样会有点麻烦，所以也可以使下面第三种方法：
     *
     * 3、@Import  快速给容器中添加一个组件
     *  1)、使用方法：@Import(要导入的组件)，这样容器中就会自动注册这个组件,id 默认是全类名
     *  2)、ImportSelector:返回需要导入的组件的全类名数组
     *  3)、ImportBeanDefinitionRegistrar ：手动注册 bean 到容器中
     *
     * 4、使用 Spring 提供的 FactoryBean
     *   ① 默认获取到的是工厂 bean 调用 getObject 创建的对象
     *   ② 要获取工厂 bean 本身，我们需要给 bean 的 id 前面加一个 & 符号
     *
     */
```

### 2、bean 声明周期

#### 1、@Bean 指定初始化和销毁方法

**先来说说 bean 的生命周期：**

> 1、bean 的生命周期就是指 bean 创建  ---初始化---销毁的过程
>
> 2、使用 spring 之后就是由容器来管理 bean 的生命周期
>
> 3、我们可以自定义初始化和销毁方法,容器在 bean 进行到当前生命周期的时候来调用我们自定义的初始化和销毁方法

**bean 初始化和销毁时机：**

> bean 初始化时机：
>
> 对象创建完成，并且相关属性赋值结束，调用初始化方法。
>
> bean 销毁时机：
>
> ① 单实例 bean ：容器关闭的时候会调用销毁方法销毁 bean
>
> ② 多实例 bean ：容器不会调用销毁方法

Car.java：

```java
public class Car {

    public Car() {
        System.out.println("car construct...");
    }

    public void init() {
        System.out.println("car init...");
    }

    public void destory() {
        System.out.println("car destory...");
    }
}
```

MainConfigOfLifeCycle.java：

```java
@Configuration
public class MainConfigOfLifeCycle {

    @Bean(initMethod = "init",destroyMethod = "destory")  // 在 @Bean 注解中指定初始化和销毁方法
    public Car car() {
        return new Car();
    }
}
```

测试类：

```java
@Test
    public void test01() {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfigOfLifeCycle.class);
        ctx.close();   // 容器关闭的时候会调用销毁方法
    }
```

如上，我 ioc 容器一启动的时候，就会去调用 bean 的无参构造器创建 bean ，等 bean 创建完成相关属性也赋值结束，因为我在 @Bean 注解中配置有初始化和销毁方法，所以创建完成后会调用初始化方法，等到 ioc 容器关闭的时候会调用销毁方法。以上仅针对单实例情况，多实例情况，会调用初始化方法，但是容器关闭的时候不会调用销毁方法。

#### 2、InitializingBean 和 DisposableBean

前面我们说了通过 @Bean 注解去指定初始化和销毁方法，这里我们说下通过 InitializingBean 和 DisposableBean 也能指定初始化和销毁方法。

主要思路是：

> 通过让 bean 实现 InitializingBean（定义初始化逻辑），DisposableBean（定义销毁逻辑）去实现

Cat.java ：

```java
@Component   // 实体类加入到 IOC 容器中，让 IOC 容器进行管理
public class Cat implements InitializingBean,DisposableBean {   // 实现初始化销毁接口
    public Cat() {
        System.out.println("cat constructor...");
    }

    public void destroy() throws Exception {
        System.out.println("cat destory...");

    }

    public void afterPropertiesSet() throws Exception {
        System.out.println("cat init...");
    }
}
```

MainConfigOfLifeCycle.java

```java
@ComponentScan("com.iflytek.bean")   // 包扫描器，去扫描这个包下的所有 bean 
@Configuration
public class MainConfigOfLifeCycle {

}
```

测试类：

```java
@Test
    public void test01() {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfigOfLifeCycle.class);
        ctx.close();
    }
```

这样 ioc 容器启动的时候就会去创建 bean ，创建完成并完成属性的赋值后，就会去调用初始化方法，等到 ioc 容器关闭的时候会去调用销毁方法。

#### 3、@PostConstruct 和 @PreDestroy

 上面我们介绍了两种指定初始化和销毁方法，下面介绍第三种：

> 1、通过在方法上标注 @PostConstruct  注解。标了这个注解的方法，在 bean 初始化完成并且给属性赋值完成后会自动调用这个注解标注的方法。
>
> 2、通过在方法上标注 @PreDestroy 注解。在 ioc 容器关闭的时候，会调用有这个注解标注的方法

Dog.java ：

```java
package com.iflytek.bean;
import org.springframework.stereotype.Component;
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

@Component    // 把这个 bean 加入到容器中
public class Dog {

    public Dog() {
        System.out.println("dog constructor...");
    }

    /**
     * 标注上这个注解，表示这个方法在对象创建，并属性赋值完成之后调用
     */
    @PostConstruct
    public void init() {
        System.out.println("dog init...");
    }

    /**
     * 标注上这个注解，表示这个方法在 ioc 容器关闭的时候调用
     */
    @PreDestroy
    public void destory() {
        System.out.println("dog destory");
    }
}
```

MainConfigOfLifeCycle.java ：

```java
@ComponentScan("com.iflytek.bean")   // 包扫描器，去扫描这个包下的所有 bean 
@Configuration
public class MainConfigOfLifeCycle {

}
```

测试类：

```java
@Test
    public void test01() {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfigOfLifeCycle.class);
        ctx.close();
    }
```

这样 ioc 容器在启动的时候，扫描到 Dog 这个 bean ，把这个 bean 加入到 IOC 容器中，会调用  Dog 的构造器去创建 Dog 对象，然后看 Dog 有没有属性，有属性的话，给属性赋值，赋值完成后，发现有 @PostConstruct 注解标识的方法，那么就会去调用这个方法，等到 ioc 容器关闭的时候，如果有 @PreDestroy 修饰的方法，就会去调用这个方法。

**总结一下，给 bean 指定初始化和销毁方法的三种方式：**

```java
/*
1、@Bean 指定初始化和销毁方法
2、InitializingBean 和 DisposableBean
3、@PostConstruct 和 @PreDestroy
*/
```



#### 4、BeanPostProcessor 后置处理器

> 这个 BeanPostProcessor 是一个接口，接口里面有两个方法：
>
> - postProcessBeforeInitialization
>   - 我们说在 bean 的生命周期中可以指定初始化方法，那么这个 postProcessBeforeInitialization 方法就是在初始化方法调用之前调用
> - postProcessAfterInitialization
>   - 这个方法在初始化方法调用之后调用

```java
package com.iflytek.bean;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.stereotype.Component;

/**
 * bean 的后置处理器，初始化前后进行处理工作
 */
@Component   // 后置处理器加入到容器中
public class MyBeanPostProcessor implements BeanPostProcessor {

    /**
     * IOC 容器启动的时候，会先去创建容器中的 bean，如果 bean 中定义有初始化方法，那么 bean 创建
     * 完成后会调用初始化方法，但是在调用初始化方法之前，会先调用本方法
     * @param bean ioc 容器启动后创建的 bean 实例
     * @param beanName 容器中 bean 的名字
     * @return 你可以把 ioc 容器创建的 bean 返回出去，也可以自己包装后返回包装后的 bean
     * @throws BeansException
     */
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("postProcessBeforeInitialization" + beanName);
        return bean;
    }

    /**
     * 初始化方法调用之后调用本方法
     * @param bean
     * @param beanName
     * @return
     * @throws BeansException
     */
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("postProcessAfterInitialization" + beanName);
        return bean;
    }
}
```

MainConfigOfLifeCycle.java ：

```java
@ComponentScan("com.iflytek.bean")   // 包扫描器，去扫描这个包下的所有 bean 
@Configuration
public class MainConfigOfLifeCycle {

}
```

测试类：

```java
@Test
    public void test01() {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfigOfLifeCycle.class);
        ctx.close();
    }
```

总结：

> 使用 bean 的后置处理器，你就要去实现 `BeanPostProcessor` 这个接口，然后重写这个接口里面的抽象方法，并把这个后置处理器加入到 IOC 容器中，这样在调用 bean 初始化方法前后，会先调用这个接口里面的方法。

#### 5、BeanPostProcessor 在 spring 底层的使用

**说一个在 bean 中拿到 ioc 容器的方法：**

```java
package com.iflytek.bean;

@Component    // 把这个 bean 加入到容器中
public class Dog implements ApplicationContextAware {

    private ApplicationContext applicationContext;

    public Dog() {
        System.out.println("dog constructor...");
    }

    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}

/*
 你想在 bean 中拿到 IOC 容器，那么你只需要实现 ApplicationContextAware 这个接口，然后重写它的 setApplicationContext 方法，你会发现这个方法的参数就是 ioc 容器对象，如果你想在别的方法中使用这个对象，那么你就可以把这个参数提取成一个属性，写出来，然在在这个抽象方法中，把参数赋值给属性，然后别的方法就能使用了。
*/

```

这个 BeanPostProcessor 有很多的实现类，在 spring 底层用的比较多。

### 3、属性赋值

#### 1、@Value 赋值

MainConfigOfPropertyValues.java ：

```java
@Configuration
public class MainConfigOfPropertyValues {

    @Bean
    public Person person() {
        return new Person();
    }
}
```

测试类：

```java
@Test
    public void test01() {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfigOfPropertyValues.class);
        Person person = (Person) ctx.getBean("person");
        System.out.println(person);   // Person{name='null', age=null}
    }
// 这里打印的 person 的属性值都是空，因为我没有给 person 的属性赋值
```

Person.java ：

```java
package com.iflytek.bean;

import org.springframework.beans.factory.annotation.Value;

public class Person {

    /**
     * @Value 注解中可以写：
     * 1、基本数值
     * 2、可以写 SpEL,如：#{}
     * 3、可以写 ${} 取出配置文件中的值（也就是在运行环境变量里面的值）
     */
    @Value("Jimmy")
    private String name;
    @Value("#{20-12}")
    private Integer age;

    public Person() {
    }

    public Person(String name, Integer age) {
        this.name = name;
        this.age = age;
    }
    // get set 方法省略。。
}

```

总结：

> - 使用 @Value 注解标注在类的属性上，便可以对属性进行赋值。类似于 spring 的 xml 配置文件中 property 标签中的 value 属性。

#### 2、@PropertySource 加载外部配置文件

Person.java :

```java
package com.iflytek.bean;

import org.springframework.beans.factory.annotation.Value;

public class Person {

    @Value("Jimmy")
    private String name;
    @Value("#{20-12}")
    private Integer age;

    private String nickName;    // 现在我在 bean 中新添加了一个属性，这个属性值我想从配置文件中获取

    public Person() {
    }

    public Person(String name, Integer age) {
        this.name = name;
        this.age = age;
    }
	// get set 方法省略...
}

```

person.properties ：

```properties
person.nickName=小旋风
```

现在我要是想获取配置文件中的值，按照之前 spring.xml 的方式应该这样：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

 <!-- 将配置文件加载进来 -->
<context:property-placeholder location="classpath:person.properties"></context:property-placeholder>
    <context:component-scan base-package="com.iflytek"></context:component-scan>
    <bean id="person" class="com.iflytek.bean.Person">
        <property name="name" value="Jimmy"></property>
        <property name="age" value="44"></property>
        <!-- 然后在这里获取配置文件的值-->
        <property name="nickName" value="${person.nickName}"></property>
    </bean>
</beans>
```

下面使用注解的方式来加载外部配置文件：

person.properties ：

```properties
person.nickName=小旋风
```

MainConfigOfPropertyValues.java ：

```Java
// 使用 @PropertySource 读取外部配置文件中的 key-value 保存到运行的环境变量中。加载完外部配置文件以后，使用 ${} 取出配置文件中的值
@PropertySource(value = {"classpath:person.properties"})
@Configuration
public class MainConfigOfPropertyValues {

    @Bean
    public Person person() {
        return new Person();
    }
}
```

Person.java ：

```java
package com.iflytek.bean;

import org.springframework.beans.factory.annotation.Value;

public class Person {
    @Value("Jimmy")
    private String name;
    @Value("#{20-12}")
    private Integer age;

    @Value("${person.nickName}")    // 这里我使用 ${} 取出配置文件中的值
    private String nickName;

    public Person() {
    }

    public Person(String name, Integer age) {
        this.name = name;
        this.age = age;
    }
    // get set 方法省略...
}

```

测试类：

```java
@Test
    public void test01() {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfigOfPropertyValues.class);
        Person person = (Person) ctx.getBean("person");
        System.out.println(person);   // Person{name='Jimmy', age=8, nickName='小旋风'}
    }
```

前面我们说了配置文件被加载后，文件中的 key-value 会被保存到运行的环境变量中，怎么理解这句话？

```java
@Test
    public void test01() {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfigOfPropertyValues.class);
        // 通过 ioc 容器我能够获取到这个环境变量,配置文件中的 key-value 都会被保存在这个环境变量中，所以我可以通过这个环境变量去获取属性值
        ConfigurableEnvironment environment = ctx.getEnvironment();
        String nickName = environment.getProperty("person.nickName");
        System.out.println(nickName);   // 小旋风
    }
```

#### 3、自动装配 @Autowired  @Qualifier @Primary 

这个 @Autowired 注解的作用就是完成 ioc 容器中 bean 的自动装配工作。

> - 在使用 @Autowired 注解时，ioc 容器启动后，首先在容器中查找有没有该类型的 bean（根据 bean 的类型去查）
>   - 如果查到的结果刚好就一个，那么就将该 bean 装配给 @Autowired 指定的数据
>   - 如果查询到额结果不止一个，那么会根据（属性）名称作为 bean 的名称去容器中查找，找到后将 该 bean 装配给 @Autowired 指定的数据
>   - 如果查询结果为空，抛出异常。解决方法：required=false
> - 如果这个类型的 bean 有多个，可以使用 @Qualifier("bookDao") 指定需要装配的 bean 的名字 ，而不是使用属性名
> - @Primary ：让 Spring 进行自动装配的时候，默认使用首选的 bean。当然你使用了这个注解也还是可以使用 @Qualifier 注解的

BookService.java ：

```java
@Service
public class BookService {
    @Autowired
    @Qualifier("bookDao")   // 这里使用了 @Qualifier，一旦容器中有多个 BookDao 类型的 bean ,那么就会把 bean 名字是 bookDao 的这个 bean 装配到这里来
    private BookDao bookDao;  // 要是我没有使用 @Qualifier 这个注解，那么会去容器中找 bookDao2 这个 bean 装配到这个容器里面来

    public void print() {
        System.out.println("bookDao" + bookDao2);
    }
}
```

BookDao.java ：

```Java
/**
 * @Repository 标识的类会被自动扫描进 ioc 容器， bean 的名字默认是类名首字母小写
 */
@Repository
public class BookDao {
    private String label = "1";
    // get set 省略...
}
```

MainConfOfAutowire.java :

```java
/**
 * 自动装配：
 *      spring 利用依赖注入(DI) ，完成对 ioc 容器中各个组件的依赖关系赋值
 * 1、@Autowired  自动注入
 * class BookService {
 *     @Autowired
 *     BookDao bookDao
 * }
 * 在使用 @Autowired 注解时，首先在容器中查找有没有该类型的 bean
 *      >> 如果查到的结果刚好就一个，那么就将该 bean 装配给 @Autowired 指定的数据
 *      >> 如果查询到额结果不止一个，那么会根据名称作为组件 id 去容器中查找，
 *      >>如果查询结果为空，抛出异常。解决方法：required=false
 * 如果这个类型的 bean 有多个，可以使用 @Qualifier("bookDao") 指定需要装配的 bean 的名字 ，而不是使用属性名
 *
 * @Primary ：让 Spring 进行自动装配的时候，默认使用首选的 bean
 * 当然你使用了这个注解也还是可以使用 @Qualifier 注解的
 */
@Configuration
@ComponentScan({"com.iflytek.service","com.iflytek.dao"})
public class MainConfOfAutowire {

    /**
     * @Primary 这个注解意思是：
     * 如果 ioc 容器中有多个 BookDao 这种类型的 bean，那么默认首选装配我标 @Primary 注解的 bean
     * 但是如果有 @Qualifier 指定要装配 bean 的名字，还是按照 @Qualifier 注解来
     * @return
     */
    @Primary
    @Bean("bookDao2")
    public BookDao bookDao() {
        BookDao bookDao = new BookDao();
        bookDao.setLabel("2");
        return bookDao;
    }
}
```

上面的代码总结一下：

> 我使用 @ComponentScan 这个注解往 ioc 容器中扫描进了一个叫 bookDao 的 bean ，然后又在配置类中往 ioc 容器中加了一个叫 bookDao2 的 bean ，而且这两个 bean 都是 BookDao 类型的，现在我在 BookService 中想将 BookDao 自动装配进来，这时候如果只写了一个 @Autowired 注解，那么默认是会把 bookDao 这个 bean 给装配过来（ioc 容器中如果有多个 bean ，默认根据属性名去查找），但是如果我在配置类配置的这个 bean 上加一个 @Primary 注解，那么这时候就会把 bookDao2 装配进来（因为这个注解的意思是，如果有多个这种类型的 bean 首选装配标注 @Primary 注解的 bean），假如我又标识了 @Qualifier 注解，那么一切以这个注解指定要装配哪个 bean 为准。

#### 4、@Resource 和 @Inject

这两个注解和 @Autowire 注解一样，也可以实现自动装配的功能。

> - @Resource 
>   - 和 @Autowired 注解一样，可以实现自动装配的功能。不过默认是按照组件名称去装配的，可以通过 @Resource(name="xxx") 指定 bean 的名字去装配
>   - 这个注解不支持 @Primary 功能，不支持 required=false
> - @Inject
>   - 需要导入 javax.inject 包，功能上和 @Autowired 一样，但是没有 required=false

#### 5、Aware 注入 spring 底层组件

假如我自定义组件想要使用 spring 容器底层的一些组件（ApplicationContext、BeanFactory、XXX）怎么办呢？

答案就是自定义组件实现 XXXAware 接口，这样在创建对象的时候，会调用接口中的方法注入相关组件。

Red.java ：

```java
package com.iflytek.bean;

public class Red implements ApplicationContextAware,BeanNameAware, EmbeddedValueResolverAware {

    private ApplicationContext applicationContext;
    public void setBeanName(String beanName) {
        System.out.println("当前 bean 的名字是：" + beanName);
    }

    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        System.out.println("传入的 ioc 容器：" + applicationContext);
        this.applicationContext = applicationContext;
    }

    public void setEmbeddedValueResolver(StringValueResolver stringValueResolver) {
        // 这里我就可以使用这个方法去解析字符串，得到解析后的结果
        String value = stringValueResolver.resolveStringValue("当前操作系统是 ${os.name},#{15+20}");
        System.out.println(value);
    }
}
```

MainConfOfAutowire.java ：

```java
@Configuration
@ComponentScan({"com.iflytek.service","com.iflytek.dao"})
public class MainConfOfAutowire {
    @Bean
    public Red red() {
        return new Red();
    }
}
```

上面我自定义了一个 Red 这个 bean ，在这个 bean 中，我通过实现 XXXAware 接口，可以获得 spring 底层的一些组件。

原理就是：

> 每一个 XXXAware  都对应着一个   XXXProcessor  ，这个 XXXProcessor 就是 bean 的后置处理器。
>
> 以 ApplicationContextAware 为例，它对应着 ApplicationContextAwareProcessor 这个 bean 的后置处理器，在 ioc 容器启动初始化之后，bean 的后置处理器会调用 setApplicationContext 方法，把 ioc 容器加进来，其实最庸原因就是我们实现了接口，而这个接口又有对应的后置处理器，是后置处理器把需要的组件给加入进来的。

#### 6、@Profile 环境搭建

MainConfigOfProfile.java ：

```java
package com.iflytek.config;

/**
 * Profile ：spring 为我们提供的可以根据当前环境，动态的激活和切换一系列组件的功能
 *
 */
@PropertySource("classpath:jdbc.properties")  // 加载外部配置文件
@Configuration
public class MainConfigOfProfile implements EmbeddedValueResolverAware{

    @Value("${jdbc.user}")
    private String user;

    /**
     * 值解析器
     */
    private StringValueResolver valueResolver;

    @Bean("testDataSource")
    public DataSource dataSourceTest(@Value("${jdbc.password}") String password) throws PropertyVetoException {
        ComboPooledDataSource datasource = new ComboPooledDataSource();
        datasource.setJdbcUrl("jdbc:mysql://192.168.31.128/books");
        datasource.setUser(user);
        datasource.setPassword(password);
        String driverClass = valueResolver.resolveStringValue("${jdbc.driverClass}");
        datasource.setDriverClass(driverClass);
        return datasource;
    }

    @Bean("devDataSource")
    public DataSource dataSourceTestDev(@Value("${jdbc.password}") String password) throws PropertyVetoException {
        ComboPooledDataSource datasource = new ComboPooledDataSource();
        datasource.setJdbcUrl("jdbc:mysql://192.168.31.128/books");
        datasource.setUser(user);
        datasource.setPassword(password);
        String driverClass = valueResolver.resolveStringValue("${jdbc.driverClass}");
        datasource.setDriverClass(driverClass);
        return datasource;
    }

    public void setEmbeddedValueResolver(StringValueResolver stringValueResolver) {
        this.valueResolver = stringValueResolver;
    }
}

```

解释一下：

> 上面使用了三种方式去获取配置文件中的值。首先我们使用 @PropertySource 注解去加载配置文件，那么这时候配置文件中的 key-value 就会被加载到运行的环境变量中去。第一种方式，我使用 @Value 注解标注在属性上，这样我可以通过 `${}` 获取配置文件中的值然后赋值给属性，第二种方式与第一种方式类似，我使用 @Value 注解标注在方法参数上，这样它也会获取配置文件中的值，然后赋值给方法参数。第三种方式就是我去实现 EmbeddedValueResolverAware 接口，然后重写他的抽象方法，在抽象方法中我可以获取到一个值解析器，这个值解析器会给你返回一个解析后的结果，通过这个值解析器，我可以去解析配置文件的 key 然后得到 value。

jdbc.properties ：

```properties
jdbc.user=root
jdbc.password=root
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://192.168.31.128/books
```

测试类：

```java
	@Test
    public void test01() {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfigOfProfile.class);
        String[] beanNamesForType = ctx.getBeanNamesForType(DataSource.class);
        for (String bean : beanNamesForType) {
            System.out.println(bean);  // testDataSource   devDataSource
        }
    }
```

上面的代码中我们一共向容器中注册了两个数据源，一个是开发环境的数据源一个是测试环境的数据源。现在假如我要加载指定环境的数据源，应该怎么做？下一节会说到。

#### 7、@Profile 根据环境注册 bean

> 这个 @Profile 注解用来指定组件在哪个环境的情况下才能被注册到容器中，不指定的话，任何环境下都能注册这个组件
>
> - 加了环境标识的 bean，只有这个环境被激活的时候才能注册到容器中。
>   - 意思就是说如果一个 bean 上面标注了 @Profile 注解，那么只有当 @Profile 注解标识的环境被激活的时候这个 bean 才会被加入到 ioc 容器中
> - @Profile 注解写在配置类上，只有是 @Profile 注解指定的环境，整个配置类中的 bean 才会被加入到 ioc 容器中
>   - 意思就是如果你把 @Profile 这个注解写到配置类上，那么只有当 @Profile 这个注解标识的环境被激活的时候，这个配置类里面配置的所有 bean 才会被加入到 ioc 容器中
> - 没有标注 @Profile 环境标识的 bean 在任何环境下都是加载的
>   - 意思就是如果你写的 bean 没有用 @Profile 注解标识，那么该加入的 ioc 容器还是会加入到 ioc 容器中

MainConfigOfProfile.java ：

```java
//@Profile("test")
@PropertySource("classpath:jdbc.properties")  // 加载外部配置文件
@Configuration
public class MainConfigOfProfile implements EmbeddedValueResolverAware{

    @Value("${jdbc.user}")
    private String user;

    /**
     * 值解析器
     */
    private StringValueResolver valueResolver;

    @Bean   // 我这个 Bean 没有标识 @Profile 注解，那么在切换开发测试数据源的时候，这个 bean 就不受影响，会加入到 ioc 容器中
    public Yellow yellow() {
        return new Yellow();
    }

    @Profile("test")    // 测试环境数据源
    @Bean("testDataSource")
    public DataSource dataSourceTest(@Value("${jdbc.password}") String password) throws PropertyVetoException {
        ComboPooledDataSource datasource = new ComboPooledDataSource();
        datasource.setJdbcUrl("jdbc:mysql://192.168.31.128/books");
        datasource.setUser(user);
        datasource.setPassword(password);
        String driverClass = valueResolver.resolveStringValue("${jdbc.driverClass}");
        datasource.setDriverClass(driverClass);
        return datasource;
    }

    @Profile("dev")  // 开发环境数据源
    @Bean("devDataSource")
    public DataSource dataSourceTestDev(@Value("${jdbc.password}") String password) throws PropertyVetoException {
        ComboPooledDataSource datasource = new ComboPooledDataSource();
        datasource.setJdbcUrl("jdbc:mysql://192.168.31.128/books");
        datasource.setUser(user);
        datasource.setPassword(password);
        String driverClass = valueResolver.resolveStringValue("${jdbc.driverClass}");
        datasource.setDriverClass(driverClass);
        return datasource;
    }
    public void setEmbeddedValueResolver(StringValueResolver stringValueResolver) {
        this.valueResolver = stringValueResolver;
    }
}
```

测试类:

```java
@Test
    public void test02() {
        // 1.创建一个 applicationContext
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
        // 2.设置需要激活的环境
        ctx.getEnvironment().setActiveProfiles("test");
        // 3.注册主配置类
        ctx.register(MainConfigOfProfile.class);
        // 4.启动刷新容器
        ctx.refresh();
        String[] beanNamesForType = ctx.getBeanNamesForType(DataSource.class);
        for (String bean : beanNamesForType) {
            System.out.println(bean);  // testDataSource
        }
    }

// 上面我激活的是 test 环境，那么只有 test 数据源会加入到 ioc 容器中，dev 数据源就不会被加入到 ioc 容器中

// 还有一种切换环境的方式：
// 使用命令行动态参数：在虚拟机参数位置加上 -Dspring.profiles.active=test
```

### 4、AOP

#### 1、aop 功能编写

首先先介绍一下什么是 aop ？

答：面向切面编程，指在程序运行期间动态的将某段代码切入到指定方法指定位置进行运行的编程方式。

编写 aop 的步骤：

> ```
> * 1、导入 aop 模块，spring-aspects 这个 pom 依赖
> * 2、定义一个业务逻辑类（MathCalculator），在业务逻辑运行的时候将日志进行打印（方法之前、方法运行结束、方法出现异常）
> * 3、定义一个日志切面类（LogAspects）, 切面类里面的方法需要动态感知 MathCalculator.div() 方法运行到哪一步了
> *       通知方法：
> *              前置通知(@Before)： 在目标方法运行之前运行
> *              后置通知(@After)： 在目标方法运行结束之后运行
> *              返回通知(@AfterReturning)： 在目标方法正常返回之后执行
> *              异常通知(@AfterThrowing)： 在目标方法出现异常之后执行
> *              环绕通知(@Around)： 动态代理，手动推进目标方法运行（joinPoint.procced()）
> * 4、给切面类的目标方法标注何时何地运行（通知注解）
> * 5、将切面类和业务逻辑类（目标方法所在类）都加入到容器中
> * 6、必须告诉 spring 哪个类是切面类（给切面类上加一个注解 @Aspect）
> * 7、给配置类中加 @EnableAspectJAutoProxy 开启基于注解的 aop 模式
> *
> * 精简为三步：
> * 1、将业务逻辑组件和切面类都加入到 ioc 容器中，并且告诉 Spring 哪个是切面类（@Aspect）
> * 2、在切面类上的每一个通知方法上标注通知注解，告诉 spring 何时何地运行（切入点表达式）
> * 3、开启基于注解的 aop 模式
> ```

MainConfigOfAOP.java :

```java
@EnableAspectJAutoProxy // 等同于以前在 xml 中配置的 <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
@Configuration
public class MainConfigOfAOP {

    /**
     * 业务逻辑类加入到容器中
     * @return
     */
    @Bean
    public MathCalculator mathCalculator() {
        return new MathCalculator();
    }

    /**
     * 切面类加入到容器中
     * @return
     */
    @Bean
    public LogAspects logAspects() {
        return new LogAspects();
    }
}
```

MathCalculator.java ：

```java
public class MathCalculator {
    public int div(int i, int j) {
        return i / j;
    }
}
```

LogAspects.java ：

```java
package com.iflytek.aop;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.*;

import java.util.Arrays;

/**
 * 切面类
 */
@Aspect
public class LogAspects {

    /**
     * 抽取切入点表达式
     * 1、本类引用，直接 pointCut() 即可
     * 2、别的切面类引用，需要写方法的全类名，com.iflytek.aop.LogAspects.pointCut()
     */
    @Pointcut("execution(public int com.iflytek.aop.MathCalculator.div(int,int))")
    public void pointCut(){}

    /**
     * @Before 在目标方法之前切入，切入点表达式：指定在哪个方法切入
     */
    @Before("execution(public int com.iflytek.aop.MathCalculator.div(int,int))")
    public void logStart(JoinPoint joinPoint) {
        Object[] args = joinPoint.getArgs();
        System.out.println("方法 "+joinPoint.getSignature().getName()+"运行... 参数列表是：" + Arrays.asList(args));
    }

    @After("pointCut()")
    public void logEnd(JoinPoint joinPoint) {
        Object[] args = joinPoint.getArgs();
        System.out.println("方法"+ joinPoint.getSignature().getName() +"结束... 参数列表是：" + Arrays.asList(args));
    }

    /**
     * returning = "result"
     * 表示用 result 来接收方法的返回值
     * JoinPoint 一定要写在参数的第一个
     */
    @AfterReturning(value = "pointCut()",returning = "result")
    public void logReturn(JoinPoint joinPoint, Object result) {
        System.out.println("方法 "+ joinPoint.getSignature().getName() +" 正常返回... 运行结果是：" + result);
    }

    /**
     * throwing = "exception"
     * 表示用 exception 来接收异常
     */
    @AfterThrowing(value = "pointCut()",throwing = "exception")
    public void logException(JoinPoint joinPoint, Exception exception) {
        System.out.println("方法 " + joinPoint.getSignature().getName() + " 异常... 异常信息是：" + exception);
    }
}

```

测试类：

```java
	@Test
    public void test01() {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfigOfAOP.class);
        // 这里的 bean 一定要是从 Ioc 容器中拿的，你自己 new 的是不行的，因为没有被 ioc 容器管理
        MathCalculator mathCalculator = ctx.getBean(MathCalculator.class);
        mathCalculator.div(5,1);
    }
```

#### 2、aop 原理-@EnableAspectJAutoProxy

aop 整个功能要起作用，就是通过 `@EnableAspectJAutoProxy` 这个注解控制的，只有加了这个注解，这个 aop 功能才会起作用。

> - `@EnableAspectJAutoProxy` 这个注解点进去，你会发现
>
>   - ```java
>     @Import({AspectJAutoProxyRegistrar.class})  // 标了 @Import 注解
>     public @interface EnableAspectJAutoProxy {
>         
>     }
>     ```
>
>   - 这个注解里面标注了 @Import 注解，意思就是要导入 AspectJAutoProxyRegistrar 中所声明的 bean 到容器中
>
>   - 这个 `AspectJAutoProxyRegistrar` 和我们前面说的一样，实现了 `ImportBeanDefinitionRegistrar` 这个接口，所以和 @Import 注解结合起来，会将里面自定义的 bean 注册到容器中。
>
>   - 那到底是把什么 bean 注册到容器中呢？我们发现它是把 `AnnotationAwareAspectJAutoProxyCreator` 这个 bean 注册到容器中，并且给这个bean 设置的名字是 `internalAutoProxyCreator`
>
>   - 综上，我们发现 `@EnableAspectJAutoProxy` 这个注解是给容器中注册了一个 `AnnotationAwareAspectJAutoProxyCreator`  这个 bean ，那么我们只要弄清楚这个 bean 有什么作用，就知道注解的作用了。
>
> - `AnnotationAwareAspectJAutoProxyCreator` 中文意思可以翻译成 AspectJ 自动代理创建器，下面来看看它的继承关系
>
>   - ```Java
>     AnnotationAwareAspectJAutoProxyCreator
>     	--->  AspectJAwareAdvisorAutoProxyCreator
>     			-->  AbstractAdvisorAutoProxyCreator
>     					--> AbstractAutoProxyCreator
>     						 -->  implements SmartInstantiationAwareBeanPostProcessor, BeanFactoryAware
>     ```
>
>   -   通过分析继承树，我们发现这个`AnnotationAwareAspectJAutoProxyCreator` 具有 BeanPostProcessor 的特点 和 Aware 接口的特点， 所以我们现在就关注一下 bean 的后置处理器中的方法，和自动装配 BeanFactory
>
>   - 现在就重点分析，它作为 bean 后置处理器做了哪些工作，实现 BeanFactoryAware 又做了哪些工作

**注：**

> 以后我们遇到 @EnableXXX 注解的时候，点进去看这个注解有没有给容器中注册一些组件，如果注册组件了，这些组件的功能是什么，这个功能你只要知道了，整个注解的原理你也就知道了。

#### 3、AnnotationAwareAspectJAutoProxyCreator 分析

前面我们说了这个类的继承树，下面我们从最底层往上看

> - AbstractAutoProxyCreator 这个类实现了 bean 后置处理器接口和 Aware 接口，所以重点看这几个方法
>
>   - ```java
>     public void setBeanFactory(BeanFactory beanFactory) {
>         this.beanFactory = beanFactory;
>     }
>
>     public Object postProcessBeforeInstantiation(Class<?> beanClass, String beanName) throws BeansException {
>         // ...
>     }
>
>     public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
>         // ...
>     }
>     ```
>
> - AbstractAdvisorAutoProxyCreator
>
>   - ```java
>     public void setBeanFactory(BeanFactory beanFactory) {
>             super.setBeanFactory(beanFactory);
>             if(!(beanFactory instanceof ConfigurableListableBeanFactory)) {
>                 throw new IllegalArgumentException("AdvisorAutoProxyCreator requires a ConfigurableListableBeanFactory: " + beanFactory);
>             } else {
>                 this.initBeanFactory((ConfigurableListableBeanFactory)beanFactory);
>             }
>         }
>
>         protected void initBeanFactory(ConfigurableListableBeanFactory beanFactory) {
>            // ...
>         }
>     ```
>
>   - 发现这个类里面重写了父类的 setBeanFactory 方法，并且在这个方法里面调用了 initBeanFactory 方法。
>
> - AnnotationAwareAspectJAutoProxyCreator
>
>   - ```java
>     protected void initBeanFactory(ConfigurableListableBeanFactory beanFactory) {
>         super.initBeanFactory(beanFactory);
>         if(this.aspectJAdvisorFactory == null) {
>             this.aspectJAdvisorFactory = new ReflectiveAspectJAdvisorFactory(beanFactory);
>         }
>
>         this.aspectJAdvisorsBuilder = new AnnotationAwareAspectJAutoProxyCreator.BeanFactoryAspectJAdvisorsBuilderAdapter(beanFactory, this.aspectJAdvisorFactory);
>     }
>     ```
>
>   - 发现这个类里面重写了父类的 initBeanFactory 方法。

#### 4、注册 AspectJAwareAdvisorAutoProxyCreator

```java
 @Test
    public void test01() {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfigOfAOP.class);
        MathCalculator mathCalculator = ctx.getBean(MathCalculator.class);
        mathCalculator.div(5,1);
    }
```

从测试类以 Debugger 模式运行：

> - 在测试类中我们传入配置类，创建 ioc 容器，在创建 ioc 容器内部，会对你传入的配置类进行注册，然后调用 refresh() 方法刷新容器。在 refresh() 方法中会有下面这个方法：
>
> - `registerBeanPostProcessors(beanFactory)`，这个方法就是去注册 bean 的后置处理器来方便拦截 bean 的创建
>
>   - ```java
>     public static void registerBeanPostProcessors(ConfigurableListableBeanFactory beanFactory, AbstractApplicationContext applicationContext) {
>         	// 先获取 ioc 容器中所有的已经定义的 BeanPostProcessor
>             String[] postProcessorNames = beanFactory.getBeanNamesForType(BeanPostProcessor.class, true, false);
>         int beanProcessorTargetCount = beanFactory.getBeanPostProcessorCount() + 1 + postProcessorNames.length;
>         	// 给容器中添加别的 BeanPostProcessor
>             beanFactory.addBeanPostProcessor(new PostProcessorRegistrationDelegate.BeanPostProcessorChecker(beanFactory, beanProcessorTargetCount));
>         // 下面声明的这几个 list 会有用
>         List<BeanPostProcessor> priorityOrderedPostProcessors = new ArrayList();
>             List<BeanPostProcessor> internalPostProcessors = new ArrayList();
>             List<String> orderedPostProcessorNames = new ArrayList();
>             List<String> nonOrderedPostProcessorNames = new ArrayList();
>         // 会优先注册实现了 priorityOrdered 接口的 BeanPostProcessor
>         sortPostProcessors(priorityOrderedPostProcessors, beanFactory);
>             registerBeanPostProcessors(beanFactory, (List)priorityOrderedPostProcessors);
>         // 再去注册实现了 Ordered 接口的 BeanPostProcessor
>         sortPostProcessors(orderedPostProcessors, beanFactory);
>             registerBeanPostProcessors(beanFactory, (List)orderedPostProcessors);
>         // 最后注册没有实现优先级接口的 BeanPostProcessor
>         registerBeanPostProcessors(beanFactory, (List)nonOrderedPostProcessors);
>             sortPostProcessors(internalPostProcessors, beanFactory);
>             registerBeanPostProcessors(beanFactory, (List)internalPostProcessors);
>     }
>     ```
>
>   - ​

### 5、声明式事务

##### 1、环境搭建

> 1、导入相关依赖（导入数据源、数据库驱动、spring-jdbc 模块）
>
> 2、配置数据源、JdbcTemplate(Spring 提供的简化数据库操作的工具)

TxConfig.java ：

```java
@Configuration
@ComponentScan("com.iflytek.tx")
public class TxConfig {

    // 数据源
    @Bean
    public DataSource dataSource() throws PropertyVetoException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass("com.mysql.jdbc.Driver");
        dataSource.setPassword("123");
        dataSource.setUser("root");
        dataSource.setJdbcUrl("jdbc:mysql://192.168.31.128/books");
        return dataSource;
    }

    @Bean
    public JdbcTemplate jdbcTemplate() throws PropertyVetoException {
        // spring 对 @Configuration 标注的配置类会有特殊处理，给容器中加组件的方法，多次调用都只是从容器中找组件
        // 我这里又调用了一次 dataSource() 方法，其实只是从容器中获取这个组件，并没有再把它创建一遍
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource());
        return jdbcTemplate;
    }
}
```

UserDao.java :

```java
@Repository
public class UserDao {

    @Autowired
    private JdbcTemplate jdbcTemplate;
    public void insert() {
        String sql = "insert into user(username,age) values(?,?)";
        String username = UUID.randomUUID().toString().substring(0, 5);
        jdbcTemplate.update(sql,username,19);
    }
}
```

UserService.java :

```java
@Service
public class UserService {

    @Autowired
    private UserDao userDao;

    public void insertUser() {
        userDao.insert();
        System.out.println("插入完成");
        int i = 10/0;      // 如果不加事务进行控制的话，出现错误依然可以插入成功
    }
}
```

测试类：

```java
@Test
    public void test01() {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(TxConfig.class);
        UserService userService = ctx.getBean(UserService.class);
        userService.insertUser();
    }
```

##### 2、添加事务

TxConfig.java ：

```java
/*
主要配置了@EnableTransactionManagement 开启基于注解的事务功能
以及 配置事务管理器来管理事务
*/
@EnableTransactionManagement
@Configuration
@ComponentScan("com.iflytek.tx")
public class TxConfig {

    // 数据源
    @Bean
    public DataSource dataSource() throws PropertyVetoException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass("com.mysql.jdbc.Driver");
        dataSource.setPassword("123");
        dataSource.setUser("root");
        dataSource.setJdbcUrl("jdbc:mysql://192.168.31.128/books");
        return dataSource;
    }

    @Bean
    public JdbcTemplate jdbcTemplate() throws PropertyVetoException {
        // spring 对 @Configuration 标注的配置类会有特殊处理，给容器中加组件的方法，多次调用都只是从容器中找组件
        // 我这里又调用了一次 dataSource() 方法，其实只是从容器中获取这个组件，并没有再把它创建一遍
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource());
        return jdbcTemplate;
    }

    // 注册事务管理器在容器中
    @Bean
    public PlatformTransactionManager transactionManager() throws PropertyVetoException {
        return new DataSourceTransactionManager(dataSource());
    }
}
```

UserService.java ：

```java
@Service
public class UserService {

    @Autowired
    private UserDao userDao;

    @Transactional      // 标上这个方法表示当前方法是一个事务方法
    public void insertUser() {
        userDao.insert();
        System.out.println("插入完成");
        int i = 10/0;
    }
}
```

##### 3、@EnableTransactionManagement 分析

这个 @EnableTransactionManagement  就类似于在 spring 的配置文件中配置的 `<tx:annotation-driven></tx:annotation-driven>` ，我们说一定要配置这个事务才会生效。下面说下这个注解的原理：

> 这个 @EnableTransactionManagement   注解点进去你会发现
>
> ```java
> @Import({TransactionManagementConfigurationSelector.class})
> public @interface EnableTransactionManagement {
> }
> ```
>
> 它使用了 @Import 注解，在这个注解里面是一个 Selector ，那么就意味着会把这个  Selector 所返回的 bean 导入到容器中。
>
> 再点进去 TransactionManagementConfigurationSelector 你会发现，它会返回这两个组件：
>
> ```java
> public class TransactionManagementConfigurationSelector extends AdviceModeImportSelector<EnableTransactionManagement> {
>     
>     protected String[] selectImports(AdviceMode adviceMode) {
>         switch(null.$SwitchMap$org$springframework$context$annotation$AdviceMode[adviceMode.ordinal()]) {
>         case 1:  // 因为在 EnableTransactionManagement 中会有个 adviceMode 默认值就是这个
>             // 会返回这两个组件
>             return new String[]{AutoProxyRegistrar.class.getName(), ProxyTransactionManagementConfiguration.class.getName()};
>         case 2:
>             return new String[]{"org.springframework.transaction.aspectj.AspectJTransactionManagementConfiguration"};
>         default:
>             return null;
>         }
>     }
> }
> ```
>
> `AutoProxyRegistrar`  和  `ProxyTransactionManagementConfiguration` 这两个组件，也就是说这两个组件最后会被导入到  ioc 容器中
>
> 我们会发现   `AutoProxyRegistrar`     会给容器中注册一个 `InfrastructureAdvisorAutoProxyCreator` 这个组件，这个组件的作用就是利用后置处理器在对象创建以后，包装对象，返回一个代理对象（增强器），代理对象执行方法利用拦截器链进行拦截。
>
> 然后我们再看看  `ProxyTransactionManagementConfiguration`  这个组件做了什么？
>
> ```java
> @Configuration
> public class ProxyTransactionManagementConfiguration extends AbstractTransactionManagementConfiguration {
>     @Bean(
>         name = {"org.springframework.transaction.config.internalTransactionAdvisor"}
>     )
>     @Role(2)
>     public BeanFactoryTransactionAttributeSourceAdvisor transactionAdvisor() {
>         BeanFactoryTransactionAttributeSourceAdvisor advisor = new BeanFactoryTransactionAttributeSourceAdvisor();
>         advisor.setTransactionAttributeSource(this.transactionAttributeSource());
>         advisor.setAdvice(this.transactionInterceptor());
>         advisor.setOrder(((Integer)this.enableTx.getNumber("order")).intValue());
>         return advisor;
>     }
> }
> ```
>
> 点进去之后，你会发现它是一个配置类，使用 @Bean 注解往容器中注册组件，发现它注册了一个 transactionAdvisor 事务增强器，这个事务增强器会用到事务注解的信息，所以弄了一个 AnnotationTransactionAttributeSource 来解析事务注解（上面源码中被 set 到 advidor 中了）。在这个事务增强器里面还用到了事务拦截器，对应的就是 TransactionInterceptor    ，这个 TransactionInterceptor 实现了 MethodInterceptor 接口，在目标方法执行的时候会执行拦截器链。这个拦截器链包含一个个事务拦截器，
>
> ```
> 事务拦截器会：
> 1、先获取事务相关的属性
> 2、再获取 PlatformTransactionManager，如果事务之前没有指定任何的 transactionManager ，最终它会从容器中按照类型获取一个 PlatformTransactionManager
> 3、执行目标方法
> 	如果执行异常，获取到事务管理器，利用事务管理器进行回滚操作
> 	如果执行正常，利用事务管理器进行提交操作
> ```
>
> 

### 6、扩展原理

#### 1、BeanFactoryPostProcessor

之前我们有说过 BeanPostProcessor ，这个是 bean 的后置处理器，bean 创建对象初始化前后进行拦截工作的。

BeanFactoryPostProcessor，这个是 beanFactory 的后置处理器，在 BeanFactory 标准初始化之后调用，所有的 bean 定义已经保存加载到 beanFactory, 但是 bean 的实例还未创建。






























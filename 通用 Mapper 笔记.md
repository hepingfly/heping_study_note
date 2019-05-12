# 通用 Mapper 笔记

### 一、通用 Mapper 介绍

作用：

替我们生成常用增删改查操作的 SQL 语句

代码官方发布地址：

```
https://gitee.com/free
https://gitee.com/free/Mapper/wikis/Home
```

### 二、创建实体类

```java
public class Employee {
	private Integer empId;
	private String empName;
	private Double empSalary;
	private Integer empAge;
}
```

注意：

> 考虑到基本数据类型在 Java 类中都有默认值，会导致 Mybatis 在执行相关操作时很难判断当前字段是否为 null，所以在 Mybatis 环境下使用 Java 实体类时，尽量不要使用基本数据类型，都使用其对应的包装类

### 三、集成通用 Mapper

1、添加 maven 依赖

```xml
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper</artifactId>
    <version>4.0.0-beta3</version>
</dependency>
```

2、修改 spring 整合 mybatis 的配置文件

```xml
<!-- 整合通用Mapper所需要做的配置修改： -->
	<!-- 原始全类名：org.mybatis.spring.mapper.MapperScannerConfigurer -->
	<!-- 通用Mapper使用：tk.mybatis.spring.mapper.MapperScannerConfigurer -->
	<bean class="tk.mybatis.spring.mapper.MapperScannerConfigurer">
		<!-- 这样它在去扫描 mapper 所在的接口，就能把通用 mapper 的功能加进来 -->
		<property name="basePackage" value="com.iflytek.mapper"/>
	</bean>
```

### 四、创建具体的 Mapper 接口

```java
import tk.mybatis.mapper.common.Mapper;

/**
 * 具体操作数据库的 Mapper 接口
 * 需要继承通用 Mapper 提供的核心接口 Mapper<T> 这个泛型就是与数据库表对应的实体类
 * @author Administrator
 *
 */
public interface EmployeeMapper extends Mapper<Employee> {

}
```

### **五、相关注解**

#### 1、@Table() 注解

> 作用：建立实体类和表之间的关系
>
> 默认规则：实体类类名首字母小写作为表名

#### 2、@Column() 注解

> 作用：建立实体类字段和数据库表字段之间的对应关系
>
> 默认规则：
>
> 实体类字段，驼峰式命名
>
> 数据库字段，使用 "_" 区分各个单词

#### 3、@Id 注解

> 通用 mapper 在执行 xxxByPrimaryKey(key) 方法时，有两种情况：
>
> 情况1：
>
> - 没有使用 @Id 注解明确指定主键字段
>
>   - ```sql
>     SELECT emp_id,emp_name,emp_salary,emp_age FROM tbl_emp WHERE emp_id = ? AND emp_name = ? AND emp_salary = ? AND emp_age = ? 
>     -- 上面的 sql 语句意思就是，我只想根据 id 去查询，结果他把实体类中所有的属性都当做查询条件来查了
>     ```
>
>   - 之所以出现上面的 where 子句，是因为通用 mapper  将实体类中的所有字段到拿来放在一起作为联合主键 
>
> 情况 2：
>
> - 使用 @Id 主键明确标记和数据库表中主键字段对应的实体类属性

#### 4、@GeneratedValue(strategy = GenerationType.IDENTITY)

> 作用：
>
> 让通用 mapper 在执行 insert 操作之后将数据库自动生成的主键值会写到实体类的对象中去。  
>
> - 自增主键用法：
>
>   - ```java
>     @Id
>     @GeneratedValue(strategy = GenerationType.IDENTITY)
>     private Integer empId;
>     ```
>
> - 序列主键用法：
>
>   - ```java
>     @Id
>     @GeneratedValue(strategy = GenerationType.IDENTITY,
>                     generator = "select SEQ_COUNTRY.nextval from dual")
>     private Integer id;
>     ```
>
>   ​

#### 5、@Transient 注解

> 一般情况下，实体类中的字段和数据表中的字段是一一对应的，但是也有很多情况我们会在实体类中增加一些额外属性，这种情况下，就需要使用 @Transient 注解来告诉通用 mapper 这不是表中的字段。
>
> 但是对于类中的复杂对象，像 Map 、 list  则不需要加这个注解

使用示例：

```java
@Transient
private String otherThing;     // 非数据库表中的字段
```







### 六、常用方法

#### 1、selectOne 方法

> - 你调用这个方法通用 Mapper 会替我们自动生成 SQL 语句
>
>   - ```sql
>     [DEBUG] 2019-03-12 01:45:20,506(2644) --> [main] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug(BaseJdbcLogger.java:139): ==>  Preparing: SELECT emp_id,emp_name,emp_salary,emp_age FROM tbl_emp WHERE emp_name = ? AND emp_salary = ?   
>     [DEBUG] 2019-03-12 01:45:20,529(2667) --> [main] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug(BaseJdbcLogger.java:139): ==> Parameters: kimi(String), 1000.0(Double)  
>     [DEBUG] 2019-03-12 01:45:20,548(2686) --> [main] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug(BaseJdbcLogger.java:139): <==      Total: 1 
>     ```
>
> - 实体类封装查询条件生成 where 子句的规则
>
>   - 使用非空的值生成 where 子句
>   - 在条件表达式中使用 "=" 进行比较
>
> - 要求必须返回一个结果，如果返回多个，会抛出异常

```java
@Test
    public void testSelectOne() {

        //1. 创建封装查询条件的实体类对象
        Employee employeeQueryCondition = new Employee(null,"kimi",1000.00,null);

        //2. 执行查询
        Employee employeeResult = employeeService.getOne(employeeQueryCondition);
        System.out.println(employeeResult);
    }


---------------------------------------------
 EmployeeService:
@Service
public class EmployeeService {
    @Autowired
    private EmployeeMapper employeeMapper;

    public Employee getOne(Employee employee) {
        return employeeMapper.selectOne(employee);
    }
}
```

#### 2、XXXByPrimaryKey 方法

> 在通用 mapper 中，但凡遇到 XXXByPrimaryKey  方法的，都需要使用 @Id 注解明确标记和数据库表主键字段对应的实体类字段，否则通用 mapper 会将所有实体类字段作为联合主键

#### 3、insert 方法

```java
@Test
public void testInsert() {
    Employee emp = new Employee(null,"emp02",2000.00,19);
    employeeService.saveEmployee(emp);
    // 获取保存后自增主键的值(需要在实体类对应的主键属性上标上 @GeneratedValue(strategy = GenerationType.IDENTITY))
    Integer empid = emp.getEmpId();
    System.out.println(empid);  // 5
}

---------------------------------------
EmployeeService
public void saveEmployee(Employee emp) {
        employeeMapper.insert(emp);
}
```

#### 4、XXXSelective 方法

> 非主键字段如果为空，则不加入到 sql 语句中

```java
@Test
public void testInsertSelective() {
    Employee emp = new Employee(null,"emp03",null,19);
    employeeService.saveEmployeeSelecetive(emp);
}
// 执行的 sql 语句：INSERT INTO tbl_emp ( emp_id,emp_name,emp_age ) VALUES( ?,?,? )
// Parameters: null, emp03(String), 19(Integer)    原来四个属性，只插入了三个，非主键字段如果为空，则不加入到 sql 语句中

--------------------------------------
EmployeeService
public void saveEmployeeSelecetive(Employee emp) {
   employeeMapper.insertSelective(emp);
}
```

#### 5、UpdateByPrimaryKeySelective 方法

```java
@Test
public void testUpdateByPrimaryKeySelectiveMapper() {
    Employee emp = new Employee(6,"newname",null,19);
    employeeService.UpdateByPrimaryKeySelective(emp);
}
// primarykey 根据主键去更新，where 子句之后有一个 id 条件，selective 非主键字段如果为空就不会加到更新语句中去。更新的 sql 语句为：UPDATE tbl_emp SET emp_id = emp_id,emp_name = ?,emp_age = ? WHERE emp_id = ? 
//Parameters: newname(String), 19(Integer), 6(Integer) 

-----------------------------------------
EmployeeService
public void UpdateByPrimaryKeySelective(Employee emp) {
   employeeMapper.updateByPrimaryKeySelective(emp);
}
```

#### 6、deleteByPrimaryKey 方法

```java
@Test
public void testDeleteByPrimaryKey() {
    Integer id = 6;
    employeeService.deleteByPrimarykey(id);
}
// 这个就是根据主键 id 去删除数据库记录
---------------------------------------------
EmployeeService
public void deleteByPrimarykey(Integer id) {
   employeeMapper.deleteByPrimaryKey(id);
}
```

### 七、QBC 查询

#### 1、QBC 查询简介

> Query By Criteria
>
> Criteria 意思就是查询条件
>
> QBC 查询就是将查询条件通过 Java 对象进行模块化封装。

#### 2、QBC 查询示例

```java
@Test
public void testSelectByExample() {
    // 1.创建 Example 对象
    // 目标：WHERE ( emp_salary > ? and emp_age < ? ) or ( emp_salary < ? and emp_age > ? )
    Example example = new Example(Employee.class);

    // 2. 通过 Example 对象创建 Criteria 对象
    Example.Criteria criteria01 = example.createCriteria();
    Example.Criteria criteria02 = example.createCriteria();

    // 3.在两个 Criteria 中分别设置查询条件
    //property 参数：实体类的属性   value 参数：实体类的属性值
    criteria01.andGreaterThan("empSalary",2000.01)
        .andLessThan("empAge",20);
    criteria02.andLessThan("empSalary",3000.00)
        .andGreaterThan("empAge",30);

    // 4.使用 or 关键字组装两个 Criteria 对象
    example.or(criteria02);

    // 5.执行查询
    List<Employee> list = employeeService.getEmployeeListByExample(example);
    System.out.println(list);

}

// 打印的 sql 语句：
// Preparing: SELECT emp_id,emp_name,emp_salary,emp_age FROM tbl_emp WHERE ( emp_salary > ? and emp_age < ? ) or ( emp_salary < ? and emp_age > ? )
// Parameters: 2000.01(Double), 20(Integer), 3000.0(Double), 30(Integer)

---------------------------------------------------------
EmployeeService
public List<Employee> getEmployeeListByExample(Example example) {
    return employeeMapper.selectByExample(example);
}
```

#### 3、QBC 查询的其他属性

```java
@Test
public void testSelectByExample() {
    // 1.创建 Example 对象
    // 目标：WHERE ( emp_salary > ? and emp_age < ? ) or ( emp_salary < ? and emp_age > ? )
    Example example = new Example(Employee.class);
  //************************************************
    // ①设置排序信息
    example.orderBy("empSalary").orderBy("empAge");
    // ② 设置去重
    example.setDistinct(true);
    // ③ 设置 select 字段
    example.selectProperties("empName","empSalary");
// ******************************************************
    
    // 2. 通过 Example 对象创建 Criteria 对象
    Example.Criteria criteria01 = example.createCriteria();
    Example.Criteria criteria02 = example.createCriteria();

    // 3.在两个 Criteria 中分别设置查询条件
    //property 参数：实体类的属性   value 参数：实体类的属性值
    criteria01.andGreaterThan("empSalary",2000.01)
        .andLessThan("empAge",20);
    criteria02.andLessThan("empSalary",3000.00)
            .andGreaterThan("empAge",30);

    // 4.使用 or 关键字组装两个 Criteria 对象
    example.or(criteria02);

    // 5.执行查询
    List<Employee> list = employeeService.getEmployeeListByExample(example);
    System.out.println(list);

}


// 在上一个的基础上添加了排序，去重 和 select 字段
// 打印的 sql 语句：
//Preparing: SELECT distinct emp_name , emp_salary FROM tbl_emp WHERE ( emp_salary > ? and emp_age < ? ) or ( emp_salary < ? and emp_age > ? ) order by emp_salary,emp_age
// Parameters: 2000.01(Double), 20(Integer), 3000.0(Double), 30(Integer)
```

### 八、通用 Mapper 逆向工程

#### 1、使用 Maven 执行 MBG

1）、pom 文件中 properties 配置

```xml
<properties>
    <!--
            ${basedir} ：工程的根目录
            basedir 这个变量是 maven 内置的，不需要我们去声明了
            targetJavaProject：声明存放源码的位置
        -->
    <targetJavaProject>${basedir}/src/main/java</targetJavaProject>
    <!--
            targetMapperProject：声明 MBG 生成 mapper 接口后存放的包的位置
        -->
    <targetMapperProject>com.iflytek.shop.mapper</targetMapperProject>
    <!--
            targetModelProject：声明 MBG 生成实体类后存放的包的位置
        -->
    <targetModelProject>com.iflytek.shop.entites</targetModelProject>
    <!--
            targetResourcesProject：声明存放资源文件的目录位置和 xml 配置文件的目录位置
        -->
    <targetResourcesProject>${basedir}/src/main/resources</targetResourcesProject>
    <!--
            targetXMLPackage：声明存放 XXXMapper.xml 文件的位置
        -->
    <targetXMLPackage>mapper</targetXMLPackage>
    <!--
            通用 mapper 的版本号
        -->
    <mapper.version>4.0.0-beta3</mapper.version>
    <mysql.version></mysql.version>
</properties>

<!--
	其实这些 properties 不声明也可以，你用到哪一个在具体把值写死就行了，之所以声明就是就是把它统一起来方便维护 
-->
```

2）、pom 文件中 plugin 配置

```xml
<build>
    <plugins>
        <plugin>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>${jdk.version}</source>
                <target>${jdk.version}</target>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-maven-plugin</artifactId>
            <version>1.3.6</version>
            <configuration>
                <!-- 配置 generatorConfig.xml 配置文件路径 -->
                <configurationFile>
                    ${basedir}/src/main/resources/generator/generatorConfig.xml
                </configurationFile>
                <overwrite>true</overwrite>
                <verbose>true</verbose>
            </configuration>
            <dependencies>
                <dependency>
                    <groupId>mysql</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                    <version>5.1.29</version>
                </dependency>
                <dependency>
                    <groupId>tk.mybatis</groupId>
                    <artifactId>mapper</artifactId>
                    <version>4.0.0</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
```

3）、generatorConfig.xml 配置文件配置

```Xml
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <properties resource="config.properties"/>

    <context id="Mysql" targetRuntime="MyBatis3Simple" defaultModelType="flat">
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>
		
        <!-- 配置通用 mapper MBG相关信息 -->
        <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
            <property name="mappers" value="tk.mybatis.mapper.common.Mapper"/>
            <property name="caseSensitive" value="true"/>
        </plugin>
		
        <!-- 配置连接数据库的基本信息 -->
        <jdbcConnection driverClass="${jdbc.driverClass}"
                        connectionURL="${jdbc.url}"
                        userId="${jdbc.user}"
                        password="${jdbc.password}">
        </jdbcConnection>
		
        <!-- 生成的 java 实体类存放的位置 -->
        <javaModelGenerator targetPackage="com.isea533.mybatis.model" 
                            targetProject="src/main/java"/>
		
        <!-- 生成的 mapper.xml 存放的位置 -->
        <sqlMapGenerator targetPackage="mapper" 
                         targetProject="src/main/resources"/>
		
        <!-- 生成的 mapper 接口存放的位置 -->
        <javaClientGenerator targetPackage="com.isea533.mybatis.mapper" 
                             targetProject="src/main/java"
                             type="XMLMAPPER"/>
		<!-- 根据数据库表生成 Java 文件相关规则 -->
        <!--
 				1、tableName="%" 表示数据库中所有表都参与逆向工程，此时使用默认规则
				2、默认规则：  table_dept  -----  TableDept
				3、不符合默认规则时，需要使用 tableName 和 domainObjectName 两个属性明确指定
 		-->
        <table tableName="user_info"   domainObjectName="UserInfo">
            <!-- 配置主键生成策略 -->
            <generatedKey column="id" sqlStatement="JDBC"/>
        </table>
    </context>
</generatorConfiguration>
```

最后一步：

在 pom.xml 这一级目录的命令行窗口执行 `mvn mybatis-generator:generate`即可

### 九、自定义 Mapper 接口

具体用途：就是我们可以根据开发的需要对具体的 `Mapper<T>` 接口做定制

#### 1、创建自定义的 Mapper 接口

```java
/*
 ①
*/
public interface MyMapper<T> extends SelectAllMapper<T>,SelectByExampleMapper<T> {
}

// SelectAllMapper<T>,SelectByExampleMapper<T>  这些接口都是可以自由选择的

/*
②
*/
// 自定义接口建好了以后，我们 EmployeeMapper 去继承它
public interface EmployeeMapper extends MyMapper<Employee> {
}
```

```xml
<!--③ -->
<bean class="tk.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.iflytek.mapper.mappers"/>
    <!-- 需要加上下面的配置-->
    <property name="properties">
        <!-- 这里配置你自定义 mapper 接口的全类名-->
        <value>mappers=com.iflytek.mapper.mymapper.MyMapper</value>
    </property>
</bean>
```

2、自定义 Mapper 接口注意事项

> 就是你自定义的需要继承的 Mapper 接口，和实体类对应的 Mapper 接口不要放在同一个包下，否则会报异常

![自定义 Mapper 接口注意事项](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-commonMapper/%E8%87%AA%E5%AE%9A%E4%B9%89%20mapper%20%E6%8E%A5%E5%8F%A3%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9.png?q-sign-algorithm=sha1&q-ak=AKIDIACFzSgGybM6a88LavrUQ8S5FEcc0R1G&q-sign-time=1553182034;1553183834&q-key-time=1553182034;1553183834&q-header-list=&q-url-param-list=&q-signature=d0512569359fcb050d5786a2ddf79f890eb94fd7&x-cos-security-token=9d3d8356c740b5eb224bd7f59ecca36b12c8c38610001)
























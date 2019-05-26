# MyBatis笔记

##### MyBatis简介

> - myBatis是支持定制化SQL，存储过程以及高级映射的优秀持久层框架
> - Mybatis避免了几乎所有的JDBC代码和手动设置参数以及获取结果集
> - Mybatis 可以使用简单的 XML 或注解用于配置和原始映射，将接口和 Java 的 POJO 映射成数据库中的记录

##### MyBastis历史

> - 原是 Apache 的一个开源项目 iBatis，2010 年 6 月这个项目由 Apache Software Foundation 迁移到了 Google Code，随着开发团队转投 Google Code 旗下，iBatis3.x 正式更名为 Mybatis，代码于 2013 年11 月迁移到 Github

##### 为什么要使用 MyBatis

> - MyBatis 是一个半自动化的持久层框架。
> - JDBC
>   - SQL 夹在 Java 代码块里，耦合度高导致硬编码内伤
>   - 维护不易且实际开发需求中 sql 是有变化的，频繁修改的情况多见
> - Hibernate 和 JPA
>   - 长难复杂SQL，对于 Hibernate 而言处理也不容易
>   - 内部自动产生SQL，如果想对这条 SQL 做特殊优化，做不到
>   - 基于全映射的全自动框架，大量字段的 POJO 进行部分映射时比较困难。导致数据库性能下降。
> - 所以对于开发人员来说，核心 SQL 还是需要自己优化
> - SQL 和 Java 编码分开，功能边界清晰，一个专注业务、一个专注数据

##### Mybatis HelloWorld总结

> - SqlSession 代表和数据库的一次会话，用完必须关闭
> - SqlSession 和 connection 一样他都是非线程安全的，是不能被共享的，每次都应该获取新的对象。
> - mapper接口没有实现类，但是mybatis 会为这个接口生成一个代理对象。（我们每次用接口的时候其实用的是这个代理对象）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.iflytek.mybatis.dao.EmployeeMapper">
<!-- 
mybatis 提供了一个功能就是接口可以与映射文件进行动态绑定
namespace:命名空间,指定为接口的全类名(通过这个namespace,mybatis就知道这个映射文件和哪个接口进行绑定)
resultType:返回值类型
<select id=""></select>  这里的id写的是接口里面的方法名,意思就是我的这个查询语句可以作为接口里面对应方法的一个实现
 -->
	<select id="getEmpById" resultType="com.iflytek.mybatis.Employee">
		select * from tbl_employee where id = #{id}
	</select>
</mapper>
```

##### xml 配置文件快捷键不提示问题

> - mybatis的xml配置文件有两个，一个是全局配置文件（配一些数据源等），一个是sql映射文件。那么在配置文件里面你按快捷键 “alt + /”可能会发生不出来提示的情况。**这是因为dtd文件没有关联好**
> - 解决方法：
>   - 配置文件的顶部会有一个dtd 文件的 URI 如："http://mybatis.org/dtd/mybatis-3-config.dtd"    要是在联网状态下，可以直接去下载这个 dtd 文件。下载好之后保存到本地，然后在 eclipse 里面 window → preference →  xml → xml Catalog → Add    里面的  Location 写你刚才保存到本地的dtd 文件位置，Key type 选 URI    Key 就写http://mybatis.org/dtd/mybatis-3-config.dtd这个值。配置好以后，把xml配置文件关掉，再打开就会有提示了。

##### mybatis 全局配置文件『 properties 』节点

> - 在 mybatis 中如果你想引入外部配置文件，可以使用 properties 节点，但是在之后与 Spring 整合的过程中，会统一使用 Spring 来引入配置文件，这个节点几乎用不到，了解一下即可。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 
		mybatis 可以使用properties节点来引入外部的properties配置文件的内容
			属性resource:引入类路径下的资源
			属性url:引入网络路径或磁盘路径下的资源
	-->
	<properties resource="dbconfig.properties"></properties>
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}" />
				<property name="url" value="${jdbc.url}" />
				<property name="username" value="${jdbc.user}" />
				<property name="password" value="${jdbc.password}" />
			</dataSource>
		</environment>
	</environments>

	<mappers>
		<mapper resource="EmployeeMapper.xml" />
	</mappers>
</configuration>
```

mybatis 全局配置文件『 typeAliases』节点

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 
		typeAliases:别名处理器,可以为我们的 java 类型起别名
		注:别名不区分大小写,也就是说你起了一个别名后,引用的时候大小写是不区分的
	 -->
	<typeAliases>
		<!-- 
			typeAlias 为某个java 类型起别名
				属性 type:指定要起别名的类型的全类名,默认别名就是类名第一个字母小写
				属性alias:指定你要起的别名
		 -->
		<typeAlias type="com.iflytek.mybatis.Employee" alias="employee"/><!--这里如果你不写alias,默认也是类名第一个字母小写  -->
		<!-- 
			package  为某个包下所有的类批量起别名
				属性name:指定包名(为当前包以及下面所有的后代包的每一个类都起一个默认的别名(类名第一个字母小写))
		 -->
		<package name="com.iflytek.mybatis"/>
		<!-- 
			批量起别名可能会造成一个问题就是,父包和子包下都有一个类名相同的类,这样在批量起别名的时候,这两个不同
			包下相同名称的类的别名就会一样,到时候mybatis就会报错,所以:
			可以使用 @Alias 注解为某个类型指定别名,这个注解是直接标在实体类上面的
		 -->
	</typeAliases>
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}" />
				<property name="url" value="${jdbc.url}" />
				<property name="username" value="${jdbc.user}" />
				<property name="password" value="${jdbc.password}" />
			</dataSource>
		</environment>
	</environments>

	<mappers>
		<mapper resource="EmployeeMapper.xml" />
	</mappers>
</configuration>
```

mybatis 全局配置文件『 environments』节点

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 
		environments 里面可以有多个environment标签,mybatis 可以配置多种环境
			属性default:指定具体使用哪种环境,可以达到快速切换的目的
	 -->
	<environments default="development">
		<!-- 
			environment 配置一个具体的环境信息,下面必须有两个子标签,id代表当前环境的唯一标识
		 -->
		<environment id="test">
			<!-- 
				transactionManager 事务管理器
					属性type: 事务管理器的类型,我们也可以自定义事务管理器,只需要实现 TransactionFactory接口即可,然后type写你的全类名
			 -->
			<transactionManager type=""></transactionManager>
			<!-- 
				dataSource 数据源
					属性type:数据源的类型,我们也可以定义数据源,实现DataSourceFactory接口即可,然后type写你的全类名
			 -->
			<dataSource type=""></dataSource>
		</environment>
		<environment id="development">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}" />
				<property name="url" value="${jdbc.url}" />
				<property name="username" value="${jdbc.user}" />
				<property name="password" value="${jdbc.password}" />
			</dataSource>
		</environment>
	</environments>

	<mappers>
		<mapper resource="EmployeeMapper.xml" />
	</mappers>
</configuration>
```

##### mybatis对多数据库的支持

> 假如我现在想同时支持 mysql 和 oracle 数据库
>
> 1. 在 mybatis 全局配置文件中 environments 节点下面配置两个 environment，一个mysql 的一个 oracle的，这样就可以通过environments 的属性 default 来快速切换你要使用 mysql 还是 oracle
> 2. 在 mybatis 的全局配置文件中配置 databaseIdProvider 节点
> 3. 在 sql 映射文件中指定你的sql 是给哪种数据库使用的

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<environments default="mysql">     <!--通过这里我就可以切换我要使用mysql还是oracle -->
		<environment id="oracle">
			<transactionManager type=""></transactionManager>
			<dataSource type=""></dataSource>
		</environment>
		<environment id="mysql">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}" />
				<property name="url" value="${jdbc.url}" />
				<property name="username" value="${jdbc.user}" />
				<property name="password" value="${jdbc.password}" />
			</dataSource>
		</environment>
	</environments>
	<!-- 
		databaseIdProvider 用来支持多数据库厂商
			type="DB_VENDOR"  作用就是得到数据库厂商的标识
	 -->
	<databaseIdProvider type="DB_VENDOR">
		<!-- 为不同的数据库厂商起别名,前面的name是数据库厂商标识,后面的 value值是你起的别名 -->
		<property name="MYSQL" value="mysql"/>
		<property name="ORACLE" value="oracle"/>
		<property name="SQL Server" value="sqlserver"/>
	</databaseIdProvider>

	<mappers>
		<mapper resource="EmployeeMapper.xml" />
	</mappers>
</configuration>
```

```xml
sql映射文件

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.iflytek.mybatis.dao.EmployeeMapper">
   <!--在你要执行的sql 语句上通过 databaseId 可以指定是在哪个数据库下执行 -->
	<select id="getEmpById" resultType="com.iflytek.mybatis.Employee" databaseId="mysql">
      						<!--这个databaseId的值就是你在全局配置文件中配置的数据库厂商的别名 -->
		select * from tbl_employee where id = #{id}
	</select>
  <!--上面的查询语句因为配置的databaseId是 mysql 所以只会在 mysql 数据库下执行 -->
</mapper>

```
mybatis 全局配置文件『 mappers』节点

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}" />
				<property name="url" value="${jdbc.url}" />
				<property name="username" value="${jdbc.user}" />
				<property name="password" value="${jdbc.password}" />
			</dataSource>
		</environment>
	</environments>
	
	<!-- 
		mappers 将 sql 映射文件注册到全局配置文件
	 -->
	<mappers>
		<!-- 
			mapper  注册一个 sql 映射文件
			    (注册配置文件):
					属性resource:引用类路径下的 sql 映射文件
					属性url:引用网络路径或者磁盘路径下的 sql 映射文件
				(注册接口):
					属性class:引用(注册)接口,值为接口的全类名,且有如下要求:
						1.如果你有sql映射文件,映射文件名必须和接口同名,并且放在和接口同一个包下,否则加载会出错
						2.如果你没有sql映射文件,所有的sql都是利用注解写在接口上的
					推荐:
						1.比较重要的复杂的DAO接口我们写在sql映射文件里面
						2.不重要的,简单的DAO接口为了开发快速可以直接使用注解
				
		 -->
		<mapper resource="mybatis/mapper/EmployeeMapper.xml" />
		<mapper class="com.iflytek.mybatis.dao.EmployeeMapperAnnotation"/>
	</mappers>
</configuration>
```

##### mybatis 增删改

```java
接口:
public interface EmployeeMapper {
	/*
	*  mybatis 允许增删改直接定义一下类型返回值：
	*            Integer、Long、Boolean
    *  其实你在使用 mybatis 对数据库进行增删改的时候,它会给你返回一个"多少行受影响",其实这个你就可以用
    *  Integer或者 Long 来接收,至于Boolean 如果你"受影响的行"大于0就返回true,要是0行受影响就返回fasle
	*/
	public boolean addEmp(Employee employee);
	
}
sql映射文件:
	<!-- parameterType,可以省略 -->
	<insert id="addEmp" parameterType="com.iflytek.mybatis.Employee">
		INSERT INTO tbl_employee(last_name,email,gender)
		VALUES(#{lastName},#{email},#{gender})
	</insert>
```

##### mybatis 获取自增主键的值

```java
接口:
public interface EmployeeMapper {
	public boolean addEmp(Employee employee);
}
sql 映射文件:
<!-- 
		mysql 支持自增主键,自增主键的获取方式:
		配置useGeneratedKeys="true",意思是使用自增主键获取主键值策略
		keyProperty="",指定对应的主键属性,也就是 mybatis 获取到主键值以后,将这个值封装给 javaBean的哪个属性
	 -->
	<insert id="addEmp" parameterType="com.iflytek.mybatis.Employee" useGeneratedKeys="true" keyProperty="id">
		INSERT INTO tbl_employee(last_name,email,gender)
		VALUES(#{lastName},#{email},#{gender})
	</insert>
 
测试类:
@Test
	public void test4() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sqlSessionFactory.openSession();
		EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
		Employee employee = new Employee(null, "aa", "bb", "0");
		mapper.addEmp(employee);
		System.out.println(employee.getId());     //添加完之后,在这里就能直接获取你添加到数据库后这条记录的id(这样的需求还是存在的,比如你在保存完成后需要把这条记录的id传给前台,那么就可以通过这种方式来拿)
		sqlSession.commit();
		sqlSession.close();
	}
```

##### mybatis 获取非自增主键的值

```xml
sql 映射文件：
<!-- 
		Oracle 不支持自增,Oracle 使用序列来模拟自增,
		每次插入的数据的主键是从序列中拿到的值,那我们如何获取到这个值
	 -->
	<insert id="addEmp" databaseId="oracle">
		<!-- 
			keyProperty:把查出的主键值封装给 javaBean 的哪个属性
			order="BEFORE":当前 sql 在下面的插入 sql 之前运行
			resultType:返回值类型
			BEFORE运行顺序:
				先运行selectKey查询id的sql,查出id值封装给javaBean的id属性
				再运行插入的sql,就可以取出id属性对应的值
		  -->
		<selectKey keyProperty="id" order="BEFORE" resultType="Integer">
			<!-- 在这里编写查询主键的sql语句 -->
			select EMPLOYEES_SEQ.nextval from dual
		</selectKey>
		INSERT INTO tbl_employee(id,last_name,email,gender)
		VALUES(#{id},#{lastName},#{email},#{gender})
	</insert>
```



mybatis 对于参数的处理

> - 如果你 mapper 接口中传递的是单个参数，那么 mybatis 不会做特殊处理，你可以直接使用 『#{参数名}』来取出参数值
> - 如果你 mapper 接口中传递的是多个参数，那么 mybatis 会做特殊处理，多个参数会被封装成一个 map 。你使用 『#{} 』其实就是从 map 中取值，而这个 map 的 key 默认是『param1......paramN』 value 是我们传入的参数值。
>   - 所以假如你的接口的方法有两个参数，你在sql映射文件中想要获取值，就应该这么获取：#{param1}获取传入的第一个参数的值，#{param2} 获取第二个参数的值。或者也可以通过索引来获取值，使用 #{0}获取第一个参数的值，#{1} 获取传入的第二个参数的值
> - 命名参数
>   - 明确指定封装参数时 map 的 key         例：@Param("id")
>   - 同样的，多个参数会被封装成一个 map ，但这时候 map 的 key 就是使用 @Param 注解指定的值，vaule 是你传入的参数值
> - POJO
>   - 如果多个参数正好是我们普通 Java 类的属性，那我们就可以直接传入 pojo 然后通过 #{属性名}取出传入的 pojo 的属性值
> - Map
>   - 如果传入的多个参数不是我们普通 Java 类的属性，没有对应的 pojo ，那么我们就可以传入 Map 通过#{key} 取出 map 中对应的值

```java
//mapper接口:
public interface EmployeeMapper {
    //mybatis 对单个参数不会做特殊处理,你在映射文件里面可以通过#{参数名}来取出参数值,也可以#{任意写一个东西}来取出参数值,因为你传过去的参数只有一个,所以 mybatis 不会做特殊处理
	public Employee getEmpById(Integer id);	   //这个方法的参数只有一个
	public Employee getEmpByIdAndLastName(Integer id, String lastName);
    //public Employee getEmpByIdAndLastName(@Param("id") Integer id, @Param("lastName")String lastName);       这样封装Map的时候key就是你使用@Param注解指定的值
  
  public Employee getEmpByMap(Map<String,Object> map);   //sql映射文件中的#{}直接写你往map里面放的key值就可以了,因为mybatis 本质上也是会把多个参数封装成一个 Map 的
}


```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.iflytek.mybatis.dao.EmployeeMapper">
	<select id="getEmpById" resultType="com.iflytek.mybatis.Employee" databaseId="mysql">
		select * from tbl_employee where id = #{id}  <!--在这里你可以通过#{参数名}来取出参数值,也可以通过 #{aabbcc}随便写一个东西来取出参数值,因为你传过来的参数只有一个 -->
	</select>
	
	<select id="getEmpByIdAndLastName" resultType="com.iflytek.mybatis.Employee">
		select * from tbl_employee where id = #{param1} and last_name=#{1}
      							<!--可以使用#{param1}或#{0} 来获取第一个参数的值 -->
	</select>
</mapper>
```
##### **mybatis 参数封装 map 的过程**

```java
//DAO 接口
public interface EmployeeMapper {
	public Employee getEmpByIdAndLastName(@Param("id")Integer id, @Param("lastName")String lastName);
}
//测试类
@Test
	public void test5() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sqlSessionFactory.openSession();
		//这个mapper接口,mybatis会为这个接口创建一个代理对象,最终都是代理对象在调用方法
		EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
        Employee employee = mapper.getEmpByIdAndLastName(1, "tom");  //切个断点停在这一行,看一下它参数是怎么封装的
		System.out.println(employee);
    }
```

```java
/**
  * 开启debug模式,按F5进入到getEmpByIdAndLastName这个方法里面,你会发现:
  * 它会先进入到 MapperProxy 这个类里面,而这个类就是 mapper接口的一个代理对象,然后执行这个方法：
*/
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
			    if (Object.class.equals(method.getDeclaringClass())) {    //这个method就是我们调用的getEmpByIdAndLastName,这里的意思就是通过反射获取这个方法的Class类对象看是不是Object类,说直白点就是看下你这个方法是不是Object类里面的方法(像toString、hashCode等等)如果是Object里面的方法我就直接放行,不是再进行下一步操作
			      try {
			        return method.invoke(this, args);
			      } catch (Throwable t) {
			        throw ExceptionUtil.unwrapThrowable(t);
			      }
			    }
			    final MapperMethod mapperMethod = cachedMapperMethod(method);
			    return mapperMethod.execute(sqlSession, args);     //这里就是把参数带上去执行,args值为[1, tom]
	 }
```

```java
/**
	 * 然后会去执行下面这段代码:
	 * 其实本质上还是去执行sqlSession的一些增删改查的 API
*/
public Object execute(SqlSession sqlSession, Object[] args) {
			    Object result;
			    switch (command.getType()) {
			      case INSERT: {
			    	Object param = method.convertArgsToSqlCommandParam(args);
			        result = rowCountResult(sqlSession.insert(command.getName(), param));
			        break;
			      }
			      case UPDATE: {
			        Object param = method.convertArgsToSqlCommandParam(args);
			        result = rowCountResult(sqlSession.update(command.getName(), param));
			        break;
			      }
			      case DELETE: {
			        Object param = method.convertArgsToSqlCommandParam(args);
			        result = rowCountResult(sqlSession.delete(command.getName(), param));
			        break;
			      }
			      case SELECT:
			        if (method.returnsVoid() && method.hasResultHandler()) {
			          executeWithResultHandler(sqlSession, args);
			          result = null;
			        } else if (method.returnsMany()) {
			          result = executeForMany(sqlSession, args);
			        } else if (method.returnsMap()) {
			          result = executeForMap(sqlSession, args);
			        } else if (method.returnsCursor()) {
			          result = executeForCursor(sqlSession, args);
			        } else {
			          Object param = method.convertArgsToSqlCommandParam(args);    //在这一步就是对我们传进来参数进行封装了,按F5进入会执行如下代码
			          result = sqlSession.selectOne(command.getName(), param);
			        }
			        break;
			      case FLUSH:
			        result = sqlSession.flushStatements();
			        break;
			      default:
			        throw new BindingException("Unknown execution method for: " + command.getName());
}
  

		 //这个方法也是在MapperMethod中的:
		  public Object convertArgsToSqlCommandParam(Object[] args) {
		      return paramNameResolver.getNamedParams(args);    //这里是调用参数名称解析器去解析我们传入的参数
		    }
```

```java
/**
 * 下面的代码比较重要,在下面的代码中完成参数封装到 map 中
*/
public Object getNamedParams(Object[] args) {       //args值为[1, tom]      假设我们传了三个参数,但是第三个参数没用 @Param注解修饰,args值为[1,tom,abc],  names的值也应该是 {0=id,1=lastName,2=2}
			    final int paramCount = names.size();    //这个names是个 map 在这里值是 {0=id,1=lastName},下面说一下这个names的值是怎么来的(见下面一段代码)：
			    if (args == null || paramCount == 0) {
			      return null;
			    } else if (!hasParamAnnotation && paramCount == 1) {    //如果你没用 @Param 注解标识,且只有一个参数,直接返回参数名
			      return args[names.firstKey()];                       //要是你用 @Param 注解标识了,且只有一个参数,它会封装 map 的
			    } else {
			      final Map<String, Object> param = new ParamMap<Object>();
			      int i = 0;
			      for (Map.Entry<Integer, String> entry : names.entrySet()) {
			        param.put(entry.getValue(), args[entry.getKey()]);       //所以这里就会变成   {id=args[0],lastName=args[1]}   即  {id=1,lastName=tom}    假设有三个参数,第三个参数没用  @Param 注解修饰,那么这里就是   {id=1,lastName=tom,2=abc}
			        // add generic param names (param1, param2, ...)
			        final String genericParamName = GENERIC_NAME_PREFIX + String.valueOf(i + 1);
			        // ensure not to overwrite parameter named with @Param
			        if (!names.containsValue(genericParamName)) {
			          //这里就是它额外的将每一个参数也保存到 map 中,使用新的 key param1.....paramN
			          //效果就是:你使用 @Param 注解修饰了,可以用 #{指定的key}  或者直接使用 #{param1}也可以拿到值
			          param.put(genericParamName, args[entry.getKey()]);
			        }
			        i++;
			      }
			      return param;
			    }
			  }
```

```java
/**
* 下面代码就是把传入的args参数封装到 names 中
*/

 
		   //ParamNameResolver这个类
		   private final SortedMap<Integer, String> names;
		  
		   public ParamNameResolver(Configuration config, Method method) {
			    final Class<?>[] paramTypes = method.getParameterTypes();
			    final Annotation[][] paramAnnotations = method.getParameterAnnotations();
			    final SortedMap<Integer, String> map = new TreeMap<Integer, String>();
			    int paramCount = paramAnnotations.length;
			    // get names from @Param annotations
			    for (int paramIndex = 0; paramIndex < paramCount; paramIndex++) {
			      if (isSpecialParameter(paramTypes[paramIndex])) {
			        // skip special parameters
			        continue;
			      }
			      String name = null;
			      for (Annotation annotation : paramAnnotations[paramIndex]) {
			        if (annotation instanceof Param) {      //首先它会判断一下这个参数它有没有用 @Param 注解标注
			          hasParamAnnotation = true;
			          name = ((Param) annotation).value();    //如果有用@Param 注解标注,这个获取注解的 value值,并把这个value值赋给name
			          break;
			        }
			      }
			      if (name == null) {
			        // @Param was not specified.
			        if (config.isUseActualParamName()) {
			          name = getActualParamName(method, paramIndex);
			        }
			        if (name == null) {     //这里的name 为空就说明上面的 name 没有被赋值,也就是说没有用 @Param注解标识
			          // use the parameter index as the name ("0", "1", ...)
			          // gcode issue #71
			          name = String.valueOf(map.size());     //那我就直接用参数的索引当 name 值
			        }
			      }
			      map.put(paramIndex, name);     //最终把参数的索引作为key,name值作为 value 放到 map 里面去
			    }
			    names = Collections.unmodifiableSortedMap(map);   //所以我们最终看到的 names 是这样的{0=id,1=lastName},那假如再有一个参数,而这个参数没有用 @Param注解标识,那么 names 应该是这样的 {0=id,1=lastName,2=2}
			  }
```
**对于上面参数封装 map 的过程总结一下：**

> 1. 参数多时会封装 map ，为了不混乱，我们可以使用 @Param 来指定封装时使用的 key，使用 #{key} 就可以取出 map 中的值
> 2. 如果你使用 @Param 标识了参数，然后你在映射文件中使用 #{0},#{1} 等通过索引去拿值是拿不到的，不过通过 #{param1}  #{param2} 去拿值是可以拿到的。具体看上面源码，都有说明

##### 参数值的获取

> 1. **#{} 的方式：可以获取 map 中的值或者 pojo 对象属性的值**
> 2. ${}  的方式：取值效果跟上面那种方式都是一样的，也是可以获取 map 中的值或者 pojo 对象属性的值

```mysql
#下面说一下使用 #{} 和使用 ${} 取值的区别:
select * from tbl_employee where id = ${id} and last_name=#{lastName}   #mapper中编写的 sql 语句
select * from tbl_employee where id = 2 and last_name=?      #执行时控制台打印的日志

#所以我们可以发现:
		1.#{}  是以预编译的形式(占位符的方式)，将参数设置到 sql 语句中
		2.${}  取出的值会直接拼装在 sql 语句中
#故:
	大多数情况下我们取参数的值都应该去使用 #{} ,在某些情况下（原生 jdbc 不支持占位符的情况,我们就可以使用${}来进行取值）
	例如分表(按照年份分表拆分):
			select * from ${year}_salary        #总的来说就是使用${}可以把值直接取出来拼在sql上 
	
```

**#{} 更丰富的用法**

> - 在 #{}  里面你可以写一些属性，支持以下的属性：
>   -  javaType、jdbcType、mode、numericScale、resultMap、typeHandler、jdbcTypeName
> - 重点关注一下 jdbcType，通常需要在某种特定条件下被设置：
>   - 在我们传入的数据为 null 的时候，有些数据库可能不能识别 mybatis 对 null 的默认处理，比如说 Oracle，你传一个 null 类型的值，mybatis会把 null 类型映射成 数据库的 other类型，而Oracle 不认识 other 类型，所以会报错

```xml
<insert id="addEmp" parameterType="com.iflytek.mybatis.Employee">
		INSERT INTO tbl_employee(last_name,email,gender)   <!-- 假设这里的email可能为空-->
		VALUES(#{lastName},#{email,jdbcType=varchar},#{gender})   <!-- 在这里指定一下它的 jdbcType就可以了,这样 mybatis 就不会把它映射成 other 类型了-->
</insert>
```
##### select 元素

> select 元素用来定义查询操作
>
> - id    唯一标识符
>   - 需要和接口方法名保持一致
> - parameterType      参数类型
>   - 可以不传，mybatis 会根据 TypeHandler 自动推断
> - resultType       返回值类型
>   - 可以是别名或者全类名，如果返回的是集合，resultType 的值应该写集合中元素的类型。注意：不能和 resultMap 同时使用

**select 元素返回一个集合**

```java
//mapper 接口
public interface EmployeeMapper {
    //这里我返回了一个集合
	public List<Employee> getEmpsByLastNameLike(String lastName);
}
//测试类
	@Test
	public void test6() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sqlSessionFactory.openSession();
		EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
		List<Employee> emps = mapper.getEmpsByLastNameLike("%o%");
		System.out.println(emps);    //这里可以正常得到emps集合的值
	}
```

```xml
mapper 映射文件：
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.iflytek.mybatis.dao.EmployeeMapper">
	<!-- resultType:如果返回的是一个集合,要写集合中元素的类型 -->
	<select id="getEmpsByLastNameLike" resultType="com.iflytek.mybatis.Employee">
		<!-- mybatis 会把每一条数据封装为一个 Employee 并把它放在集合中,最终返回 -->
		select *from tbl_employee where last_name like #{lastName}
	</select>
</mapper>
```

**select 元素返回一个 Map**

```java
//mapper 接口
public interface EmployeeMapper {
    //返回一条记录的 map, key 就是列名,值就是对应的值
	public Map<String, Object> getEmpsByIdReturnMap(Integer id);
	
	//多条记录封装一个Map,key 是这条记录的主键,值是记录封装后的 javaBean
	@MapKey("id")   //告诉 mybatis 封装这个 Map的时候 key 用哪个属性
	public Map<Integer,Employee> getEmpsByLastNameReturnMap(String lastName);   //根据名字模糊查询,查出来的结果封装成 map ,key为每一条记录的主键,值为每一个Employee对象
}

    //返回一条记录 map 的测试类
	@Test
	public void test7() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sqlSessionFactory.openSession();
		EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
		Map<String, Object> map = mapper.getEmpsByIdReturnMap(1);
		System.out.println(map);  //{gender=0, last_name=tom, id=1, email=123@qq.com}
	}
    
	//返回多条记录 map 的测试类
	@Test
	public void test8() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sqlSessionFactory.openSession();
		EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
		Map<Integer, Employee> map = mapper.getEmpsByLastNameReturnMap("%o%");
		System.out.println(map);  //{1=Employee [id=1, lastName=null, email=123@qq.com, gender=0]}
	}
```

```xml
mapper 映射文件：
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.iflytek.mybatis.dao.EmployeeMapper">
	<select id="getEmpsByIdReturnMap" resultType="map">
		<!-- 这里我想把返回出来的记录封装成 Map -->
		select *from tbl_employee where id = #{id}
	</select>
	
	<select id="getEmpsByLastNameReturnMap" resultType="com.iflytek.mybatis.Employee">
		<!-- 这里我还是想把查出来的每一条记录封装成一个Emplyee对象,所以 resultType写的还是 Employee -->
		select *from tbl_employee where last_name like #{lastName}
	</select>
</mapper>
```

##### 自定义ResultMap，实现高级结果集映射

```xml
mapper 映射文件：
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.iflytek.mybatis.dao.EmployeeMapper">
  <!-- 
      自定义某个 javaBean 的封装规则
      type:自定义规则的 java 类型
      id: 方便下面引用
   -->
  <resultMap type="com.iflytek.mybatis.Employee" id="employeeResult">
      <!-- 
          指定主键列的封装规则
          column:指定哪一列
          property:指定对应的 javaBean 属性
       -->
      <id column="id" property="id"/>
      <result column="last_name" property="lastName"/><!-- 之所以写result标签就是因为你列名和属性名不一样,mybatis 封装不上 -->
      <!-- 其他不指定的列会自动封装(就是说我下面的result标签可以都不写,因为列名和属性名都一样,mybatis会自动封装),但是我们建议只要写 resultMap 就把全部的映射规则写上 -->
      <result column="email" property="email"/>
      <result column="gender" property="gender"/>
  </resultMap>
</mapper>

	<select id="getEmpsByLastNameReturnMap" resultMap="employeeResult">  <!--这里可以引用上面定义的resultMap -->
		select *from tbl_employee where last_name like #{lastName}
	</select>
```

resultMap 联合查询

```java
//mapper接口
public interface EmployeeMapper {
	//查询员工和部门信息
	public Employee getEmpAndDept(Integer id);
    //使用assocation进行分步查询
	public Employee getEmpByIdStep(Integer id);
}
//测试类
@Test
	public void test9() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sqlSessionFactory.openSession();
		EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
		Employee employee = mapper.getEmpAndDept(2);
		System.out.println(employee);
		System.out.println(employee.getDept());
	}
```

```xml
mapper 映射文件：
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.iflytek.mybatis.dao.EmployeeMapper">
	<!-- 1.联合查询『级联属性』:级联属性封装结果集 -->
	<resultMap type="com.iflytek.mybatis.Employee" id="empResult">
		<id column="id" property="id"/>
		<result column="last_name" property="lastName"/>
		<result column="email" property="email"/>
		<result column="gender" property="gender"/>
		<!-- 
			column:  写下面sql语句你命名的别名(要是你没起别名,就写原表的列名)
			property:  写级联属性
		 -->
		<result column="deptId" property="dept.id"/>
		<result column="departmentName" property="dept.departmentName"/>
	</resultMap>
	
	<!-- 2.联合查询『嵌套结果集』:使用 association 来定义单个对象的封装规则 -->
	<resultMap type="com.iflytek.mybatis.Employee" id="empResult2">
		<id column="id" property="id"/>
		<!-- 注意:如果你 sql 语句中起了别名的话,这里column的值就应该写别名而不是数据表列名,要是没起别名就是数据表列名 -->
		<result column="lastName" property="lastName"/>
		<result column="email" property="email"/>
		<result column="gender" property="gender"/>
		<!-- 
			association: 可以指定联合的javaBean对象
			property: 指定哪个属性是联合的对象
			javaType: 指定这个属性对象的类型(不能省略)
		 -->
		<association property="dept" javaType="com.iflytek.mybatis.Department">
			<id column="deptId" property="id"/>
			<result column="departmentName" property="departmentName"/>
		</association>
	</resultMap>
	<select id="getEmpAndDept" resultMap="empResult2">
		SELECT e.id id,e.last_name lastName,e.gender gender,e.email email,t.id deptId,t.dept_name departmentName FROM tbl_employee e, tbl_dept t WHERE e.d_id= t.id AND e.id=#{id}
	</select>
  
  <!-- 
		3.联合查询『分段查询』:使用 association 进行分步查询
				1.先按照员工 id 查询出员工信息(员工信息里面会包含部门id)
				2.根据查询出的员工信息中的部门id值去查出部门信息
				3.部门设置到员工中
	-->
	<resultMap type="com.iflytek.mybatis.Employee" id="empSteptResult">
		<id column="id" property="id"/>
		<result column="last_name" property="lastName"/>
		<result column="email" property="email"/>
		<result column="gender" property="gender"/>
		<!-- 
			association定义关联对象的封装规则
				property:同样是指定哪个属性是联合的对象
				select:表名当前 dept 属性是调用 select 指定的方法查出的结果
				column:指定将哪一列的值传给这个方法
				总的流程就是: 使用 select 指定的方法(传入column指定的这列参数的值)查出对象,并封装给 property 指定的属性
		 -->
		<association property="dept" <!-- 在这里我们希望dept的值是通过d_id(我会先去查员工表信息,一条员工信息会包含d_id字段)查出来的,所以后面写 select 关键字 -->
					select="com.iflytek.mybatis.dao.DepartmentMapper.getDeptById" column="d_id"><!-- 你调用 getDeptById方法查询,它是要传参数的,那这个参数怎么弄？通过 column来指定-->
		
		</association>
	</resultMap>
	
	<select id="getEmpByIdStep" resultMap="empSteptResult">
		select *from tbl_employee where id = #{id}
	</select>
</mapper>
```

注：

> - 上面联合查询第 3 点，使用 association 进行分步查询，是先查出 employee 记录信息，然后在根据 employee 记录中的部门id 去查部门信息。所以，每次我们在查 employee 对象的时候，都会将 department 信息一起查出来，我们希望部门信息可以在我们使用的时候再去查询，就可以使用 **懒加载**
>
> - 我们只需要在分步查询的基础上加上两个配置即可：
>
>   - 在 mybatis 的全局配置文件中加上以下两个配置:
>
>     - ​
>
>       ```xml
>       <settings>	
>       	<setting name="lazyLoadingEnabled" value="true"/>
>       	<setting name="aggressiveLazyLoading" value="false"/>
>       </settings>
>       ```

##### resultMap关联查询~collection定义关联查询封装规则

```java
//Department类
public class Department {

	private Integer id;
	private String departmentName;
	private List<Employee> emps;
}
//mapper接口
public interface DepartmentMapper {
	//根据id查部门信息,会顺便把这个部门里面所有的员工也一起查出来
	public Department getDeptByIdPlus(Integer id);
}

//测试类
	@Test
	public void test10() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sqlSessionFactory.openSession();
		DepartmentMapper mapper = sqlSession.getMapper(DepartmentMapper.class);
		Department department = mapper.getDeptByIdPlus(2);
		System.out.println(department);   //Department [id=2, departmentName=测试部]
		System.out.println(department.getEmps());   //[Employee [id=2, lastName=kelly, email=bb, gender=0], Employee [id=3, lastName=kimi, email=bb, gender=0]]
	}
```

```xml
<!-- mapper 映射文件 -->
<mapper namespace="com.iflytek.mybatis.dao.DepartmentMapper">
  	<!--『嵌套结果集』的方式,使用 collection 标签定义关联的集合类型的属性封装规则 -->
	<resultMap type="com.iflytek.mybatis.Department" id="deptResult">
		<id column="did" property="id"/>
		<result column="dept_name" property="departmentName"/>
		<!-- 上面查的都是部门的字段,下面查的是员工的字段,因为一个部门里面会有多个员工所以用 collection -->
		<!-- 
			collection:定义关联集合类型的属性的封装规则(Department类里面有一个emps集合属性,所以这里写emps)
			ofType: 指定集合里面元素的类型
		 -->
		<collection property="emps" ofType="com.iflytek.mybatis.Employee">
			<!-- 定义这个集合中元素的封装规则(集合中都是一个个的employee对象) -->
			<id column="id" property="id"/>
			<result column="last_name" property="lastName"/>
			<result column="email" property="email"/>
			<result column="gender" property="gender"/>
		</collection>
	</resultMap>
	<select id="getDeptByIdPlus" resultMap="deptResult">
		SELECT
			d.id did,
			d.dept_name,
			e.id,
			e.last_name,
			e.email,
			e.gender
		FROM
			tbl_dept d
		LEFT JOIN tbl_employee e ON d.id = e.d_id
		WHERE
			d.id = #{id}
	</select>
</mapper>
```

承接上面代码，改用分段查询的方式

```java
//DepartmentMapper
public interface DepartmentMapper {
	//查询部门信息,顺便把部门中的员工查询出来(使用分步查询)
	public Department getDeptByIdStep(Integer deptId);
}
//EmployeeMapper
public interface EmployeeMapper {
	//根据部门id查询员工信息
	public List<Employee> getEmpsByDeptId(Integer deptId);
}
//测试类
@Test
	public void test11() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sqlSessionFactory.openSession();
		DepartmentMapper mapper = sqlSession.getMapper(DepartmentMapper.class);
		Department department = mapper.getDeptByIdStep(2);
		System.out.println(department);  //[id=2, departmentName=测试部]
		System.out.println(department.getEmps()); //[Employee [id=2, lastName=null, email=bb, gender=0], Employee [id=3, lastName=null, email=bb, gender=0]]
	}
```

```xml
<!-- mapper 映射文件 -->
<mapper namespace="com.iflytek.mybatis.dao.DepartmentMapper">
  		<!--『分段查询的方式』 -->
  		<resultMap type="com.iflytek.mybatis.Department" id="deptStepResult">
		<id column="id" property="id"/>
		<result column="dept_name" property="departmentName"/>
		<association property="emps" 
			select="com.iflytek.mybatis.dao.EmployeeMapper.getEmpsByDeptId" 
             <!--这里我就先把部门信息查出来,然后根据部门id去查所有员工的信息 -->
									column="id" fetchType="eager">
          			<!--上面column值的意思就是我把部门id列作为参数传递给 select 里面的方法 -->
			<id column="id" property="id"/>
			<result column="last_name" property="lastName"/>
			<result column="email" property="email"/>
			<result column="gender" property="gender"/>
		</association>
	</resultMap>
	<select id="getDeptByIdStep" resultMap="deptStepResult">
		select id,dept_name from tbl_dept where id = #{deptId}
	</select>
	
	<!-- 
		扩展:我们说了分段查询里面 association 的属性 column 的值的意思就是,我将哪一列的值
		传递给 select 属性,那假如有多列怎么办呢？
		答:
		可以将多列的值封装进 map传递,格式:column="{key1=column1,key2=column2}"
		fetchType="lazy":表示使用延迟加载
					--lazy:延迟
					--eager:立即
	 -->
	 
	 <!-- 
	 	接口中的方法:public Department getDeptByIdStep(Integer deptId);
	 	所以 resultMap 我还可以这么写:
	 	<resultMap type="com.iflytek.mybatis.Department" id="deptStepResult">
		<id column="id" property="id"/>
		<result column="dept_name" property="departmentName"/>
		<association property="emps" 
			select="com.iflytek.mybatis.dao.EmployeeMapper.getEmpsByDeptId" 
									column="{deptId=id}">
			这里因为接口中方法的参数名是deptId,所以你key写deptId,value就是你 要传哪一列的值
			<id column="id" property="id"/>
			<result column="last_name" property="lastName"/>
			<result column="email" property="email"/>
			<result column="gender" property="gender"/>
		</association>
	</resultMap>
	  -->
</mapper>
```

##### mybatis 鉴别器（用的不多，了解即可）

```xml
<mapper>
  	 <!-- 
	  	<discriminator javaType=""></discriminator>
	  	鉴别器:mybatis可以使用discriminator判断某列的值,然后根据某列的值改变封装行为
	  	案例:
	  		封装Employee
	  			如果查出的是女生,就把部门信息查询出来,否则不查询
	  			如果是男生,把last_name这一列的值赋值给 email
	   -->
	   <resultMap type="com.iflytek.mybatis.Department" id="deptDisResult">
	   		<id column="id" property="id"/>
			<result column="last_name" property="lastName"/>
			<result column="email" property="email"/>
			<result column="gender" property="gender"/>
			<!-- 
				column:指定判定的列名
				javaType:列值对应的java类型
			 -->
			<discriminator javaType="string" column="gender"><!--我要判定gender这一列是男生还是女生 -->
				<!-- 0代表女生, resultType指定封装的结果类型-->
				<case value="0" resultType="com.iflytek.mybatis.Employee">
					<!-- 这里就表示如果是女生,我才去查部门信息 -->
					<association property="dept" 
						select="com.iflytek.mybatis.dao.DepartmentMapper.getDeptById" column="d_id">
					</association>
				</case>
				<!-- 1代表男生 -->
				<case value="1">
					<!-- 如果是男生我们要求不查部门信息,而且email属性值是last_name这一列的值 -->
					<id column="id" property="id"/>
					<result column="last_name" property="lastName"/>
					<result column="last_name" property="email"/>
					<result column="gender" property="gender"/>
				</case>
			</discriminator>
	   </resultMap>
</mapper>
```
### Mybatis 动态查询

##### 动态 sql ------ if 判断

```java
//EmployeeMapperDynamicSQL类
public interface EmployeeMapperDynamicSQL {
	//映射文件中使用动态 sql
	public List<Employee> getEmpsByConditionIf(Employee employee);
}
//测试类
	@Test
	public void test12() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sqlSessionFactory.openSession();
		EmployeeMapperDynamicSQL employeeMapper = 		sqlSession.getMapper(EmployeeMapperDynamicSQL.class);
		Employee employee = new Employee(1, null, null, null); 
		List<Employee> list = employeeMapper.getEmpsByConditionIf(employee);
		System.out.println(list);//[Employee [id=1, lastName=tom, email=123@qq.com, gender=0]]
      
      //注：
      //使用动态 sql ---if判断，查询的时候如果某些条件没带可能 sql 拼装会有问题
      /*
      	解决方法有两种：
      		1.给 where 后面加上1=1,以后的条件都加上  and 
      		2.mybatis 推荐使用 where 标签将所有的查询条件包括在内。这样 mybatis 就会将 where 标签中拼装的 sql ,多出来的 and 或者 or 去掉(但是这个 where 标签只会去除第一个多出来的 and 或 or)
      */
      
	}
```

```xml
<mapper namespace="com.iflytek.mybatis.dao.EmployeeMapperDynamicSQL">
		<!-- 查询员工信息,要求:携带了哪个字段查询条件就带上这个字段的值 -->
	<select id="getEmpsByConditionIf" resultType="com.iflytek.mybatis.Employee">
		select id,last_name lastName,email,gender from tbl_employee where
		<!-- 
			test:判断表达式(OGNL)
			OGNL可以参照官方文档
			遇见特殊符号我们应该去写转义字符(比如你双引号里面再写双引号)
		 -->
		<if test="id != null">
			id = #{id}
		</if>
		<if test="lastName != null and lastName != &quot;&quot;"><!-- 这里我要判断一下lastName不等于空串,空串用转义字符代替 -->
			and last_name like #{lastName}
		</if>
		<if test="email != null and email.trim()!= &quot;&quot;">
			and email=#{email}
		</if>
		<!-- ognl 会进行字符串与数字的转换判断 "0"==0 -->
		<if test="gender ==0 or gender ==1">
			and gender=#{gender}
		</if>
  </select>
      
      <!--上面那种方式如果我第一个 id 没传,那么拼接 sql 就有问题了,就变成 where and last_name like ...
	解决方案上面也说了，第一种就是使用 where 1=1 然后后面所有拼接的条件前面都加 and。第二种就是 mybatis 推荐的 where 标签,不过这个 where 标签只会去除第一个多出来的 and 或者 or
-->
      <!--使用 where 标签的方式 -->
  <select id="getEmpsByConditionIf" resultType="com.iflytek.mybatis.Employee">
		select id,last_name lastName,email,gender from tbl_employee
		<where>
			<if test="id != null">
				id = #{id}
			</if>
			<if test="lastName != null and lastName != &quot;&quot;">
				and last_name like #{lastName}  假如传来的 id 为空,where 标签会自动帮你把第一个and去掉
			</if>
			<if test="email != null and email.trim()!= &quot;&quot;">
				and email=#{email}      假如你要是把 and 写在后面就不行了
			</if>
			<if test="gender ==0 or gender ==1">
				and gender=#{gender}
			</if>
		</where>
	</select>
</mapper>
```

动态 sql ---- choose

```java
//EmployeeMapperDynamicSQL接口
public interface EmployeeMapperDynamicSQL {
	public List<Employee> getEmpsByConditionChoose(Employee employee);
}
//测试类
@Test
	public void test12() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sqlSessionFactory.openSession();
		EmployeeMapperDynamicSQL employeeMapper = sqlSession.getMapper(EmployeeMapperDynamicSQL.class);
		Employee employee = new Employee(1, null, null, null);
		List<Employee> list = employeeMapper.getEmpsByConditionChoose(employee);
		System.out.println(list);
	}
```

```xml
<mapper>
		<select id="getEmpsByConditionChoose" resultType="com.iflytek.mybatis.Employee">
		select id,last_name lastName,email,gender from tbl_employee
		<where>
			<!-- 如果传了 id 就用 id 查,如果传了 last_name 就用 last_name 去查,只会进入其中一个 -->
			<choose>
				<when test="id != null">
					id = #{id}
				</when>
				<when test="last_name != null">
					last_name like #{lastName}
				</when>
				<when test="email != null">
					email = #{email}
				</when>
				<otherwise>
					1=1         <!-- 否则我就查所有,因为外面已经用 where 标签了,所以这里就相当于 where 1=1 -->
				</otherwise>
			</choose>
		</where>
	</select>
</mapper>
```

动态 sql -------foreach遍历集合

```java
//mapper接口
public interface EmployeeMapperDynamicSQL {
    
	public List<Employee> getEmpsByConditionForeach(@Param("ids") List<Integer> ids);//注意参数最好加个@Param注解修饰,不然到时候它可能找不到这个参数报错
}
//测试类
	@Test
	public void test14() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sqlSessionFactory.openSession();
		EmployeeMapperDynamicSQL employeeMapper = sqlSession.getMapper(EmployeeMapperDynamicSQL.class);
		List<Employee> list = employeeMapper.getEmpsByConditionForeach(Arrays.asList(1,2,3));
		System.out.println(list);//[Employee [id=1, lastName=null, email=123@qq.com, gender=0], Employee [id=2, lastName=null, email=bb, gender=0], Employee [id=3, lastName=null, email=bb, gender=0]]
	}
```

```xml
<mapper>
	<select id="getEmpsByConditionForeach" resultType="com.iflytek.mybatis.Employee">
		select * from tbl_employee where id in
		<!-- 
			collection:指要遍历的集合,list类型的参数会特殊处理,封装在Map中,map的key就叫list
			item:将当前遍历出的元素赋值给指定的变量
			separator：每个元素之间的分隔符
			open:遍历出所有结果拼接一个开始的字符
			close：遍历出所有结果拼接一个结束的字符
			index:索引,遍历list的时候index就是索引,item就是当前值;遍历map的时候index表示的就是 map 的key,item就是 map 的值
		 -->
		<foreach collection="ids" item="item_id" separator="," open="(" close=")">
			#{item_id}     <!-- 通过使用#{变量名}就能取出变量的值也就是当前遍历出的元素 -->
		</foreach>
	</select>
</mapper>
```

##### mysql 下 foreach 批量插入的两种方法

```java
//mapper接口
public interface EmployeeMapperDynamicSQL {
	//批量保存
	public int insertEmps(@Param("emps") List<Employee> emps);
}
//测试类
	@Test
	public void test15() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sqlSessionFactory.openSession();
		EmployeeMapperDynamicSQL employeeMapper = sqlSession.getMapper(EmployeeMapperDynamicSQL.class);
		List<Employee> list = new ArrayList<>();
		list.add(new Employee( "aa", "aa@qq.com", "0",new Department(1,"开发部")));
		list.add(new Employee("bb", "bb@qq.com", "0",new Department(1,"开发部")));
		int i = employeeMapper.insertEmps(list);
		System.out.println(i);  //打印2,两行受影响
		sqlSession.commit();
		sqlSession.close();
	}
```

```xml
<mapper>
		<!-- 
		批量保存sql语句其实可以这么写:
		insert into tbl_employees(last_name,email,gender,d_id)
		values ('aa','aa@qq.com','0',1),('bb','bb@qq.com','0',1))
		    values后面的内容其实就是需要我们遍历的,动态追加在后面
		mysql下批量保存,之所以能够使用foreach遍历,是因为 mysql 支持 values(),(),()的语法
	 -->
	<insert id="insertEmps">
		insert into tbl_employee(last_name,email,gender,d_id)
		values
		<foreach collection="emps" item="emp" separator=",">
			(#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})
		</foreach>
	</insert>
	
	<!-- 
		mysql批量保存第二种方式,这种方式需要数据库连接属性allowMultiQueries=true(就是在你连接数据库配置文件jdbcUrl后面加上这个即可)
	 -->
	<insert id="insertEmps">
		<!-- 这种就是让 mysql 执行多条用分号隔开的sql语句 -->
		<foreach collection="emps" item="emp" separator=";">
			insert into tbl_employee(last_name,email,gender,d_id) values (#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})
		</foreach>
	</insert>
</mapper>
```

##### oracle 下 foreach 批量插入的两种方法

```xml
	<!-- 
		Oracle 数据库批量保存
		Oracle 不支持 values(),(),()的方式
		下面说下Oracle支持批量保存的方式:
		1.多个 insert 放在 begin - end 中间
			begin
				insert into tbl_employee(last_name,email,gender,d_id) 
				values (#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})
				insert into tbl_employee(last_name,email,gender,d_id) 
				values (#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})
			end;
		2.利用中间表
			insert into tbl_employee(employee_id,last_name,email)
				select employees_seq.nextval,last_name,email from(
					select 'aa' lastName,'aa@qq.com' email from dual
					union
					select 'bb' lastName,'bb@qq.com' email from dual
					)
	 -->
	 
	 <insert id="insertEmps" databaseId="oracle">
	 	<!-- begin     这里的begin 和下面的 end 可以直接写在 foreach 中-->
	 		<foreach collection="emps" item="emp" open="begin" close="end;"><!--end后面有个分号要记得  -->
	 			insert into employees(employee_id,last_name,email) 
				values (employees_seq.nextval,#{emp.lastName},#{emp.email});
	 		</foreach>
	 	<!-- end -->
	 </insert>
	 <!-- oracle 第二种批量保存方式 -->
	 <insert id="insertEmps" databaseId="oracle">
	 	insert into tbl_employee(employee_id,last_name,email)
	 		select employees_seq.nextval,last_name,email from(
	 			<foreach collection="emps" item="emp" separator="union">
	 				select #{emp.lastName} lastName,#{emp.email} email from dual
	 			</foreach>
	 		)
	 </insert>
```

##### mybatis 两个内置参数

```java
//mapper接口
public interface EmployeeMapperDynamicSQL {
	//测试mybatis两个内置参数_parameter和_databaseId
	public List<Employee> getEmpsTestInnerParameter(Employee employee);
}
```

```xml
<mapper>
		<!-- 
		两个内置参数:
			不只是方法传递过来的参数可以被用来判断、取值
			mybatis 默认还有两个内置参数:
				①_parameter:代表整个参数
					单个参数:_parameter就是这个参数
					多个参数:参数会被封装为一个 map,_parameter就代表这个map
				②_databaseId:如果配置了databaseIdProvider标签
					_databaseId就是代表当前数据库的别名
					
	 -->
	
	<select id="getEmpsTestInnerParameter" resultType="com.iflytek.mybatis.Employee">
		<if test="_databaseId=='mysql'">     <!--前提是我在配置文件中配置了databaseIdProvider标签 -->
			select * from tbl_employee
			<if test="_parameter!=null"><!-- 因为我参数只传了一个值,所以_parameter就代表Employee -->
				where last_name = #{lastName}  <!--这里其实也可以写 where last_name = #{_parameter.lastName} 因为_parameter代表的就是Employee -->
			</if>
		</if>
		<if test="_databaseId=='oracle'">
			select * from employees 
			<if test="_parameter!=null">
				where last_name = #{lastName}
			</if>
		</if>
	</select>
</mapper>
```

##### mybatis 抽取可重用的 sql 片段

```xml
<mapper>
		<!-- 
		抽取可重用的sql片段,方便后面引用
			1.sql抽取,将经常要查询的列名,或者插入用的列名抽取出来方便引用
			2.使用include标签来引用已经抽取的sql
			3.include还可以自定义一些property,sql标签内部就能使用自定义的属性
						include-property:取值的正确方式${prop}
										#{不能使用这种方式}   
	 -->
	<sql id="insertColumn">
		last_name,email,gender,d_id,${testabc}  <!-- 这里通过${}可以取出testabc的属性值 -->
	</sql>
	
	<insert id="insertEmps">
		insert into tbl_employee(
			<include refid="insertColumn">   <!-- 正常情况下,在这里引用上面抽取的sql就可以了 -->
				<property name="testabc" value="abc"/>  <!-- 但是我们也可以在这里定义一个属性,然后在上面抽取出来的sql片段中可以通过${}拿到这个属性值 -->
			</include>
		)
		values
		<foreach collection="emps" item="emp" separator=",">
			(#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})
		</foreach>
	</insert>
</mapper>
```

mybatis 一级缓存

```java
	/**
	 * 两级缓存
	 * 一级缓存(本地缓存):sqlSession级别的缓存,一级缓存是一直开启的
	 * 		与数据库同一次会话期间查询到的数据会放在本地缓存中
	 * 		以后如果需要获取相同的数据,直接从缓存中拿,没必要再去查询数据库
	 * 
	 * 		一级缓存失效的情况(没有使用当前一级缓存的情况,效果就是还需要向数据库发出查询):
	 * 			1.sqlSession不同(一级缓存又叫sqlSession级别缓存,你sqlSession不同一级缓存里面东西肯定不同)
	 * 			2.sqlSession相同,查询条件不同(一级缓存中还没有这个数据)
	 * 			3.sqlSession相同,两次查询之间执行了增删改操作(因为这次增删改可能会对当前数据有影响,所以会再发一次sql语句)
	 * 			4.sqlSession相同,手动清除了一级缓存
	 * @throws IOException 
	 */
	
	@Test
	public void testFirstLevalCache() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sqlSessionFactory.openSession();
		try {
			EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
			Employee employee = mapper.getEmpById(1);
			System.out.println(employee);
			//手动清除一级缓存
//			sqlSession.clearCache();        假如这里我把注释打开,把一级缓存清了,再查询它就还会发送一条sql语句 
			//这里又查了一次id为1的对象,但是sql语句只会发送一次
			Employee employee2 = mapper.getEmpById(1);
			System.out.println(employee2);
		} finally {
			sqlSession.close();
		}
	}
```

##### mybatis 二级缓存

```java
//Employee类
public class Employee implements Serializable {  //使用二级缓存的时候POJO需要序列化
  	private Integer id;
	private String lastName;
	private String email;
	private String gender;
	private Department dept;
}
	
	/**
	 * 
	 * 二级缓存(全局缓存):基于namespace级别的缓存,一个namespace对应一个二级缓存
	 * 			工作机制:
	 * 					1.一个会话,查询一条数据,这个数据会被放在当前会话的一级缓存中
	 * 					2.如果会话关闭,一级缓存中的数据会被保存到二级缓存中,新的会话查询信息,就可以参照二级缓存中的内容
	 * 					3.不同 namespace 查出的数据会放在自己对应的缓存中
	 * 			使用:
	 * 					1.开启全局二级缓存配置<setting name="cacheEnabled" value="true"/>
	 * 					2.去映射文件中配置使用二级缓存<cache></cache>
	 * 					3.我们POJO需要实现序列化接口
	 * 			注意:
	 * 				查出的数据都会默认先放在一级缓存中,只有会话提交或者关闭以后,一级缓存中的数据才会转移到二级缓存中
	 * @throws IOException 
	 */
	@Test
	public void testSecondLevalCache() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession1 = sqlSessionFactory.openSession();
		SqlSession sqlSession2 = sqlSessionFactory.openSession();
		try {
			EmployeeMapper mapper1 = sqlSession1.getMapper(EmployeeMapper.class);
			EmployeeMapper mapper2 = sqlSession2.getMapper(EmployeeMapper.class);
			Employee employee1 = mapper1.getEmpById(1);
			//这里我去查employee对象,查完之后会把这个对象放到一级缓存中,然后我把session关闭之后就会把这个对象放到二级缓存中(前提是你开启了二级缓存)
			sqlSession1.close();
			//这里我再去查询的时候,就会直接从二级缓存中查
			Employee employee2 = mapper2.getEmpById(1);
			sqlSession2.close();
			//注意:要是第一次我查完之后没有把session关闭,我第二次在去查还是会发sql语句不会去二级缓存中拿,因为只有当你session关了数据才会往二级缓存里面放
		} finally {
		}
	}
```

```xml
<mapper>
	<cache></cache>  <!--开启二级缓存,只要在mapper标签下面加一个 cache 标签即可 -->
</mapper>
```

##### 和缓存相关的属性/设置

```java
/*
	1).『<setting name="cacheEnabled" value="true"/>』当值为false的时候,是关闭缓存(但关闭的是二级缓存,一级缓存会一直开启)
	2).每个select标签都有『useCache="true"』这个属性,当值为false的时候不使用二级缓存(一级缓存依然使用)
	3).每个增删改标签都会有个『flushCache="true"』属性(select标签里面这个属性默认是false),所以增删改执行完成后会清除缓存
而且『flushCache="true"』属性,一级缓存和二级缓存都会被清除
	4).『sqlSession.clearCache()』,只是清除当前session的一级缓存
*/
```

##### mybatis 整合第三方缓存

> - 关于缓存 mybatis 提供了一个『Cache』接口，你可以通过实现这个接口来自定义缓存。mybatis 缓存使用的是 PerpetualCache 这个类，你进去之后发现这个类其实就是一个 Map，所以 mybatis 关于缓存这块做的比较简陋，因此才提供一个 Cache 接口出来，让第三方来实现。关于缓存什么时候使用，这是由 mybatis 来决定的，比如说：等 sqlSession 关闭了我才会去使用二级缓存。这种策略还是按照 mybatis 规定的来。

```java
//以mybatis PerpetualCache为例
public class PerpetualCache implements Cache {
  private String id;
  private Map<Object, Object> cache = new HashMap<Object, Object>();
  @Override
  public void putObject(Object key, Object value) {    //比如说我现在用redis 做缓存,那我就在 put 的时														候,把数据放到 redis里面
    cache.put(key, value);
  }

  @Override
  public Object getObject(Object key) {    			//get的时候,我就从 redis 里面拿缓存,这样就实现了自													定义缓存
    return cache.get(key);
  }
}
```

mybatis 去整合 Ehcache

> - 现在我想去整合 Ehcache，那么按照思路我应该去实现 mybatis 提供的 Cache 接口，然后我自己来写这些 putObject、getObject方法，把数据放到 Ehcache中和从 Ehcache中取数据。至于什么时候把数据放到 Ehcache 中，这是由 mybatis 规定的，比如说 mybatis 是在会话关闭的时候把数据放到 Ehcache 中。
>
> 整合的步骤：
>
> - 首先导入 Ehcache 相关的包 （ehcache-core-2.6.8.jar、slf4j-api-1.6.1.jar、slf4j-log4j12-1.6.2.jar）Ehcache依赖 slf4j相关的包
>
> - 第二步我们就要来写 Cache 接口的实现（使用 Ehcache），这样就需要我们去手写各种方法，但是 mybatis 其实已经为我们提供好了实现，你只需要导入 jar 包即可（mybatis-ehcache-1.0.3.jar）
>
> - ```xml
>   <mapper>
>     	<!--在sql映射文件中,配置自定义缓存 -->		
>   	<cache type="org.mybatis.caches.ehcache.EhcacheCache"></cache>
>   </mapper>
>   ```
>
> - Ehcache 要能正常运行，需要我们在类路径下有一个 ehcache.xml文件，这个文件直接考过来放在类路径下即可。
>
> 以上步骤做完，便完成了 Ehcache的整合。mybatis现在变使用 Ehcache来做二级缓存。

##### mybatis 逆向工程

> - **MyBatis Generator**，简称 『MBG』，是一个专门为 MyBatis 框架使用者定制的代码生成器，可以快速的根据表生成对应的映射文件，接口，以及 bean 类。 

```xml
<!--主要是根据这个配置文件,来指定逆向生成的一些策略,然后通过代码执行这个配置文件即可 -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
	<!-- <classPathEntry location="/Program Files/IBM/SQLLIB/java/db2java.zip" /> -->

	<context id="DB2Tables" targetRuntime="MyBatis3Simple">
		<!-- jdbcConnection:指定如何连接到指定数据库 -->
		<jdbcConnection driverClass="com.mysql.jdbc.Driver"
			connectionURL="jdbc:mysql://localhost:3306/" userId="root" password="123">
		</jdbcConnection>
		
		<!-- java 类型解析器,使用默认就好 -->
		<javaTypeResolver>
			<property name="forceBigDecimals" value="false" />
		</javaTypeResolver>
		
		<!-- 
			javaModelGenerator:指定 javabean 的生成策略
			targetPackage:目标包名
			targetProject:目标工程
		 -->
		<javaModelGenerator targetPackage="com.iflytek.mybatis.bean"
			targetProject=".\src">
			<property name="enableSubPackages" value="true" />
			<property name="trimStrings" value="true" />
		</javaModelGenerator>
		
		<!-- 
			sqlMapGenerator:指定 sql 映射文件的生成策略
		 -->
		<sqlMapGenerator targetPackage="com.iflytek.mybatis.mapper"
			targetProject=".\conf">
			<property name="enableSubPackages" value="true" />
		</sqlMapGenerator>
		
		<!-- 
			javaClientGenerator:指定 mapper接口所在的位置
		 -->
		<javaClientGenerator type="XMLMAPPER"
			targetPackage="com.iflytek.mybaits.dao" targetProject=".\src">
			<property name="enableSubPackages" value="true" />
		</javaClientGenerator>
		
		<!-- 
			table:指定要逆向分析哪些表
			domainObjectName:数据表对应哪个javaBean
		 -->
		<table tableName="tbl_employee" domainObjectName="Employee"></table>
		<table tableName="tbl_dept" domainObjectName="Department"></table>


	</context>
</generatorConfiguration>
```

```java
	//官方文档提供的执行方法,配置好上面的配置文件,运行这个方法就可以生成你需要的一些类、接口、映射文件
	@Test
	public void testMBG() throws Exception {
		List<String> warnings = new ArrayList<String>();
		boolean overwrite = true;
		File configFile = new File("mbg.xml");    //配置文件的名字
		ConfigurationParser cp = new ConfigurationParser(warnings);
		Configuration config = cp.parseConfiguration(configFile);
		DefaultShellCallback callback = new DefaultShellCallback(overwrite);
		MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
		myBatisGenerator.generate(null);
	}
```



##### MyBatis 运行原理

- SessionFactory的初始化

首先是通过 new 创建了一个 SessionFactoryBuilder 对象，然后调用这个对象的 build 方法，传入的是全局xml配置文件的流对象

```java
@Test
	public void test2() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new 		SqlSessionFactoryBuilder().build(inputStream);     //这里传入全局配置文件的流对象
		SqlSession sqlSession = sqlSessionFactory.openSession();
		EmployeeMapper employeeMapper = sqlSession.getMapper(EmployeeMapper.class);
		Employee employee = employeeMapper.getEmpById(1);
		System.out.println(employee);
	}
```

然后来看这个 build 方法，这个 build 方法主要用来创建一个 xml 解析器，解析器调用 parse方法去解析这个xml

```java
  public SqlSessionFactory build(InputStream inputStream, String environment, Properties properties) {
    try {
      //创建xml解析器(其实这个parser就是XPathParser)
      XMLConfigBuilder parser = new XMLConfigBuilder(inputStream, environment, properties);
      return build(parser.parse());
    } catch (Exception e) {
      throw ExceptionFactory.wrapException("Error building SqlSession.", e);
    } finally {
      ErrorContext.instance().reset();
      try {
        inputStream.close();
      } catch (IOException e) {
        // Intentionally ignore. Prefer previous error.
      }
    }
  }

//XMLConfigBuilder类
 public Configuration parse() {
    if (parsed) {
      throw new BuilderException("Each XMLConfigBuilder can only be used once.");
    }
    parsed = true;
    parseConfiguration(parser.evalNode("/configuration"));//这里通过xml解析器获取到全局配置文件的跟标签(在mybatis全局配置文件中这个configuration是根标签)
    return configuration;
  }
```

获取到全局配置文件的根标签后，然后根据这个根标签获取下面各个子标签

```java
//这个方法就是获取configuration下面的各个节点(你会发现它获取了像 settings节点、typeAliases节点、databaseIdProvider等我们熟悉的节点),然后去解析各个节点的详细信息  
private void parseConfiguration(XNode root) {
    try {
      Properties settings = settingsAsPropertiess(root.evalNode("settings"));
      //issue #117 read properties first
      propertiesElement(root.evalNode("properties"));
      loadCustomVfs(settings);
      typeAliasesElement(root.evalNode("typeAliases"));
      pluginElement(root.evalNode("plugins"));
      objectFactoryElement(root.evalNode("objectFactory"));
      objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
      reflectorFactoryElement(root.evalNode("reflectorFactory"));
      settingsElement(settings);
      // read it after objectFactory and objectWrapperFactory issue #631
      environmentsElement(root.evalNode("environments"));
      databaseIdProviderElement(root.evalNode("databaseIdProvider"));
      typeHandlerElement(root.evalNode("typeHandlers"));
      mapperElement(root.evalNode("mappers"));
    } catch (Exception e) {
      throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + e, e);
    }
  }


//获取这些节点的同时,它会去解析这个节点的详细信息并把它设置到 Configuration对象中
//以settings节点为例
  private void settingsElement(Properties props) throws Exception {
    configuration.setAutoMappingBehavior(AutoMappingBehavior.valueOf(props.getProperty("autoMappingBehavior", "PARTIAL")));
    configuration.setAutoMappingUnknownColumnBehavior(AutoMappingUnknownColumnBehavior.valueOf(props.getProperty("autoMappingUnknownColumnBehavior", "NONE")));  //你会发现它会获取settings节点的各个属性信息然后把它设置到Configuration对象中
    configuration.setCacheEnabled(booleanValueOf(props.getProperty("cacheEnabled"), true));
    configuration.setProxyFactory((ProxyFactory) createInstance(props.getProperty("proxyFactory")));
    configuration.setLazyLoadingEnabled(booleanValueOf(props.getProperty("lazyLoadingEnabled"), false));      //中间删了一点,太多了。
    configuration.setLogPrefix(props.getProperty("logPrefix"));
    @SuppressWarnings("unchecked")
    Class<? extends Log> logImpl = (Class<? extends Log>)resolveClass(props.getProperty("logImpl"));
    configuration.setLogImpl(logImpl);
    configuration.setConfigurationFactory(resolveClass(props.getProperty("configurationFactory")));
  }

//mappers节点也说一下
/*
上面解析节点的方法：发现有一个这个方法 mapperElement(root.evalNode("mappers"));
我们都知道,mapper映射文件写好之后是需要注册到全局配置文件中的,那么它在解析节点的时候,解析到 mappers 节点,看这个节点下面的一个个子节点,就可以解析到你注册的映射文件(具体是那个映射文件如：Employee.xml),拿到这个值以后它又会把这个xml弄成一个流,然后去解析这个 xml 下面的信息,把Employee.xml中每一个元素信息解析出来并写到 Configuration 类中
*/

//所以说这个Configuration对象保存了所有配置文件的详细信息,包括全局配置文件和sql映射文件

```

上面我们是得到封装所有信息的 Configuration 对象

```java
 public SqlSessionFactory build(InputStream inputStream, String environment, Properties properties) {
    try {
      XMLConfigBuilder parser = new XMLConfigBuilder(inputStream, environment, properties);
      return build(parser.parse());   //parser.parse()返回一个 Configuration 对象
    } catch (Exception e) {
      throw ExceptionFactory.wrapException("Error building SqlSession.", e);
    } finally {
      ErrorContext.instance().reset();
      try {
        inputStream.close();
      } catch (IOException e) {
        // Intentionally ignore. Prefer previous error.
      }
    }
  }
    
  public SqlSessionFactory build(Configuration config) {
    return new DefaultSqlSessionFactory(config);    //你传给我带所有信息的Configuration对象,我返回给你一个默认的SqlSessionFactory对象
  }
```

所以关于 SqlSessionFactory初始化总结一下就是：

`把配置文件的信息解析并保存在 Configuration 对象中，返回包含了 Configuration 的 DefaultSqlSessionFactory 对象`

- openSession 获取 SqlSession 对象

这行代码 SqlSession sqlSession = sqlSessionFactory.openSession() ，其实就是上面一步返回的 DefaultSqlSessionFactory 对象调用它的 openSession 方法

```java
//DefaultSqlSessionFactory 类 
@Override
  public SqlSession openSession() {
    //实际上调用的是 openSessionFromDataSource 方法
    return openSessionFromDataSource(configuration.getDefaultExecutorType(), null, false);
  }
//上面方法的入参会从 configuration 中拿到一个默认的执行器类型(configuration 中有一个属性是 defaultExecutorType) 默认值是 『SIMPLE』
```

下面我们就进入到 openSessionFromDataSource 方法

```java
//DefaultSqlSessionFactory 类  
private SqlSession openSessionFromDataSource(ExecutorType execType, TransactionIsolationLevel level, boolean autoCommit) {
    Transaction tx = null;
    try {
      final Environment environment = configuration.getEnvironment();
      final TransactionFactory transactionFactory = getTransactionFactoryFromEnvironment(environment);
      //创建一个事务对象
      tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);
      //这里它要去创建一个 Executor 对象
      final Executor executor = configuration.newExecutor(tx, execType);  //这里的参数一个是我们刚创建的事务，一个是上一步从 Configuration 里面获取的执行器类型,默认是 『SIMPLE』,下面看下这个方法
      return new DefaultSqlSession(configuration, executor, autoCommit);
    } catch (Exception e) {
      closeTransaction(tx); // may have fetched a connection so lets call close()
      throw ExceptionFactory.wrapException("Error opening session.  Cause: " + e, e);
    } finally {
      ErrorContext.instance().reset();
    }
  }

//Configuration 类
  public Executor newExecutor(Transaction transaction, ExecutorType executorType) {
    executorType = executorType == null ? defaultExecutorType : executorType;
    executorType = executorType == null ? ExecutorType.SIMPLE : executorType;
    Executor executor;
    //下面就是根据Executor的类型(其实这个Executor的类型来源于全局配置文件,你要是没配就用默认值)取创建Executor 不同的实现
    if (ExecutorType.BATCH == executorType) {
      executor = new BatchExecutor(this, transaction);
    } else if (ExecutorType.REUSE == executorType) {
      executor = new ReuseExecutor(this, transaction);
    } else {
      executor = new SimpleExecutor(this, transaction);
    }
    //这里就是看是否配置了二级缓存
    if (cacheEnabled) {
      executor = new CachingExecutor(executor);//如果配置了二级缓存就 new 一个 CachingExecutor
    }
    //这一步很重要。意思是使用每一个拦截器重新包装 executor 并返回
    executor = (Executor) interceptorChain.pluginAll(executor);
    return executor;
  }

//这个 executor 创建好了以后，看上一步把它当成参数就可以 new 一个 DefaultSqlSession，并返回,整个流程就结束了,我们拿到的也就是 DefaultSqlSession。而这个对象它里面包含了 Executor 和 Configuration 对象
```

所以关于 SqlSession 初始化总结一下就是：

返回一个 DefaultSQLSession 对象,这个对象包含Executor和Configuration。而且注意一下，在这一步创建了 Executor对象

- getMapper 获取接口的代理对象

下面来看源码分析：

```java
//这里调用sqlSession的getMapper方法,其实是调用DefaultSqlSession的getMapper方法
EmployeeMapper employeeMapper = sqlSession.getMapper(EmployeeMapper.class);

//进入到这个方法里面,DefaultSqlSession类
public <T> T getMapper(Class<T> type) {
    return configuration.<T>getMapper(type, this);//发现调用的是configuration里面的getMapper方法
  }


//再往方法里面进,Configuration类
public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
    return mapperRegistry.getMapper(type, sqlSession);//发现调用的是mapperRegistry的getMapper方法
  }


//再往方法里面进,MapperRegistry类
  public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
    //这里得到一个工厂
    final MapperProxyFactory<T> mapperProxyFactory = (MapperProxyFactory<T>) knownMappers.get(type);
    if (mapperProxyFactory == null) {
      throw new BindingException("Type " + type + " is not known to the MapperRegistry.");
    }
    try {
      //用工厂去创建实例
      return mapperProxyFactory.newInstance(sqlSession);
    } catch (Exception e) {
      throw new BindingException("Error getting mapper instance. Cause: " + e, e);
    }
  }


//下面进入到newInstance方法,MapperProxyFactory类
 @SuppressWarnings("unchecked")
  protected T newInstance(MapperProxy<T> mapperProxy) {
    //使用动态代理创建MapperProxy 对象
    return (T) Proxy.newProxyInstance(mapperInterface.getClassLoader(), new Class[] { mapperInterface }, mapperProxy);
  }

  public T newInstance(SqlSession sqlSession) {
    //在这里去new 了一个MapperProxy对象
    final MapperProxy<T> mapperProxy = new MapperProxy<T>(sqlSession, mapperInterface, methodCache);
    return newInstance(mapperProxy);//调用上面的 newInstance 方法
  }

```

所以关于getMapper 获取接口的代理对象总结一下就是：

getMapper方法,使用 MapperProxyFactory 创建一个MapperProxy 的代理对象,这个代理对象里面包含了DefaultSqlSession,DefaultSqlSession 里面又包含了 Executor

关于 Mybatis 整个运行流程总结一下就是：

> - 我们知道 Mybatis 执行增删改查是通过代理对象来执行的，而代理对象里面包含了『DefaultSqlSession』，实际上是通过 DefaultSqlSession 来执行的，而 DefaultSqlSession 里面又包含了 『Executor 』，所以实际上又是 Executor 来执行增删改查的。Executor 在执行的时候，又会创建一个 『StatementHandler』对象，这个对象就是用来处理 sql 预编译，设置参数等相关工作。而 StatementHandler 下面又有两个对象，ParameterHandler（这个用来设置预编译参数） 和 ResultSetHandler （这个是用来处理结果的），也就是说我们 StatementHandler 也不是直接来处理 sql 而是通过 ParameterHandler 来设置预编译参数，执行完后通过 ResultSetHandler 来处理结果。 而这两个对象又都是通过 『TypeHandler』 来做的。ParameterHandler 通过 TypeHandler 来设置参数，ResultSetHandler 通过 TypeHandler 来获取结果集。其实 TypeHandler 在整个过程中的作用就是进行数据库类型和 javaBean 类型的映射。

Mybatis 插件原理

```java
/**
	 * 插件原理
	 * 在四大对象创建的时候
	 * 1、每个创建出来的对象不是直接返回的,而是 interceptorChain.pluginAll(parameterHandler);
	 * 2、获取到所有的 Interceptor(这个 Interceptor 就是插件需要实现的接口 ) 
	 * 		public Object pluginAll(Object target) {
			    for (Interceptor interceptor : interceptors) {
			      target = interceptor.plugin(target);     //返回 target 包装后的对象
			    }
			    return target;
			}
			
		3、插件机制,我们可以使用插件为目标对象创建一个代理对象
					我们的插件可以为四大对象创建出代理对象
					代理对象就可以拦截到四大对象的每一个执行方法
	 */
```

编写第一个 mybatis 插件

1、首先你要去实现 Interceptor 接口，然后重写里面的抽象方法

```java
public class MyFirstPlugin implements Interceptor {
	
	/**
	 * intercept方法:
	 * 			拦截目标对象的目标方法执行
	 */
	@Override
	public Object intercept(Invocation invocation) throws Throwable {
		
		System.out.println("MyFirstPlugin...的intercept方法" + invocation.getMethod());  //可以拿到你拦截的是哪个方法
		//执行目标方法
		Object proceed = invocation.proceed();//你只要显示的调用invocation.proceed()方法,目标方法就会被执行(放行)
		//返回执行后的返回值
		return proceed;
	}
	
	/**
	 * plugin方法:
	 * 			用来包装目标对象的,所谓的包装就是为目标对象创建一个代理对象
	 */
	@Override
	public Object plugin(Object target) {
		System.out.println("MyFirstPlugin...的plugin方法,mybatis将要包装的对象是" + target);
		//mybatis为了我们使用方便,给我们提供了一个 Plugin 对象,我们可以借助 Plugin 的 Wrap 方法来使用当前的拦截器来包装我们的目标对象
		Object wrap = Plugin.wrap(target, this);  //第一个参数:要包装的目标对象,第二个参数:用哪个拦截器包装。这样就会给我们返回一个包装后的对象
		//返回为当前target创建的动态代理
		return wrap;
	}
	
	/**
	 * setProperties方法:
	 * 		将插件注册的 property 属性设置进来
	 */
	@Override
	public void setProperties(Properties properties) {
		System.out.println("插件配置的信息..." + properties);
	}
	/**
	 * 注:
	 * 你写好一个Interceptor的实现类,是不够的。因为这个拦截器是能够拦截每一个对象的每一个方法。
	 * 所以接下来你应该告诉 mybatis 你编写的这个插件是用来拦截哪个对象的哪个方法。
	 * 因此写好实现类后下一步你就应该在实现类上写注解(@Intercepts)
	 */
}
```

2、在你写好的插件上面标识一个注解，告诉 myBatis 当前插件用来拦截哪个对象的哪个方法

```java
/**
 * 完成插件签名:
 * 			目的就是告诉 Mybatis 当前插件用来拦截哪个对象的哪个方法
 * 说一下@Signature这个注解三个参数的意思:
 * 					type:指定你要拦截四大对象中的哪个对象
 * 					method:指定你拦截这个对象的哪个方法
 * 					args:指定当前方法的参数列表(因为有的方法可能会有重载)
 *
 */
@Intercepts({
	@Signature(type=StatementHandler.class,method="parameterize",args=java.sql.Statement.class)
})
public class MyFirstPlugin implements Interceptor {
  		...........       //中间的内容和上面一步一样
}
```

3、把你写好的插件注册到 MyBatis 全局配置文件中

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
		<plugins>
            <plugin interceptor="com.iflytek.mybatis.dao.MyFirstPlugin">
                <property name="username" value="root"/>   <!--你可以在这里设置一些初始属性,然后在插件的setProperties()都可以拿到这些属性 -->
                <property name="password" value="123456"/>
            </plugin>
	</plugins> 
</configuration>
```

**总结一下编写插件的步骤：**

> 1. 编写 Interceptor 的实现类
> 2. 使用 @Intercepts 注解完成插件签名
> 3. 将写好的插件注册到全局配置文件中

**注：要是我有多个插件，那么多个插件的运行流程是怎么样的？**

> - 首先我们说 『plugin』方法和 『setProperties』方法是按照你在全局配置文件中注册插件的顺序来执行的，关键是看 『intercept』方法。我们知道 调用 『plugin』方法的时候会给你创建一个代理对象，但是当你有多个插件的时候，创建动态代理，是按照插件配置的顺序创建层层代理对象，所以调用 『intercept』方法去执行目标方法时，肯定是从最外层先执行，然后再向里层执行。所以 『intercept』方法 是按照逆向顺序执行的

**总结一下插件的执行流程：**

> - 你写一个插件，就是为我们的目标对象（@Signature注解的type属性值）产生一个动态代理对象（plugin方法会产生），接下来目标对象的目标方法要执行，都会来到我们代理对象的 『intercept』 方法，而只有在这个方法里我调用了『invocation.proceed()』方法给你放行了，你的目标方法才能执行，所以我们可以在目标方法放行的前后做很多事情。

##### mybatis 批量操作BatchExecutor

> 先说一下之前我们批量操作都是怎么做的，拿 mybatis 批量保存来说，有这么两种方法：

```xml
	<!-- 
		批量保存sql语句其实可以这么写:
		insert into tbl_employees(last_name,email,gender,d_id)
		values ('aa','aa@qq.com','0',1),('bb','bb@qq.com','0',1))
		    values后面的内容其实就是需要我们遍历的,动态追加在后面
		mysql下批量保存,之所以能够使用foreach遍历,是因为 mysql 支持 values(),(),()的语法
	 -->
	<insert id="insertEmps">
		insert into tbl_employee(last_name,email,gender,d_id)
		values
		<foreach collection="emps" item="emp" separator=",">
			(#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})
		</foreach>
	</insert>
	
	<!-- 
		mysql批量保存第二种方式,这种方式需要数据库连接属性allowMultiQueries=true(就是在你连接数据库配置文件jdbcUrl后面加上这个即可)
	 -->
	<!-- <insert id="insertEmps">
		这种就是让 mysql 执行多条用分号隔开的sql语句
		<foreach collection="emps" item="emp" separator=";">
			insert into tbl_employee(last_name,email,gender,d_id) values (#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})
		</foreach>
	</insert> -->
```

> - 从本质上来说，上面的两种方法都是在拼接一个非常长 sql 语句然后交给 mysql 服务器去执行，而 mysql 服务器对这个长度其实是有限制，如果你插入的很长是可能会报错的。比如说你一下子拼几千条然后插入，这样就可能会报错。
> - 那我就是想批量执行怎么办呢？其实 mybatis 有批量执行器

```java
//mapper接口
public interface EmployeeMapper {
	public Long addEmp(Employee employee);
}
//测试类
	@Test
	public void testSave() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		//可以执行批量操作的 sqlSession
		SqlSession sqlSession = sqlSessionFactory.openSession(ExecutorType.BATCH);
		try {
			EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
			//模拟插入10000条,真实场景中你可以遍历list,然后保存
			for (int i = 0; i < 10000; i++) {
				mapper.addEmp(new Employee(UUID.randomUUID().toString(), "a", "b", new Department()));
			}
			sqlSession.commit();
		} finally {
			sqlSession.close();
		}
	}
```

```xml
<mapper>
		<insert id="addEmp">
	   		insert into tbl_employee(last_name,email,gender) values(#{lastName},#{email},#                      {gender})
	   </insert>
</mapper>
<!-- 
	以上就是批量操作的方法,关键就是获取可以执行批量操作的sqlSession。那假如我 mybatis 和 spring 整合之后该怎么做呢？看下面
-->
```

##### 在 spring 中配置批量 sqlSession

```xml
<!--spirng-mybatis的配置文件 -->
<!--我们一般会配置sqlSessionFactory -->
<bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource"></property>
		<!--mapperLocations: 指定mapper文件的位置-->
		<property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"></property>
</bean>  <!--在这个bean 下面我们可以配置一个批量执行的sqlSession -->

<!--配置一个可以进行批量执行的sqlSession  -->
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
    <!-- 通过sqlSessionFactory 来创建 sqlSession -->
    <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactoryBean"></constructor-arg>
    <!-- 指定 executor 的类型是 BATCH-->
    <constructor-arg name="executorType" value="BATCH"></constructor-arg>
</bean>

<!-- 
	上面的做法是你在spring整合mybatis的配置文件中指定了一个 sqlSession,这个 sqlSession 是可以指定批量操作的 sqlSession,下次你要执行批量操作就可以直接用这个 sqlSession
-->

```

**下面说一下怎么在 Service 中使用这个 sqlSession**

```java
@Service
public class EmployeeService {
	
	@Autowired
	private EmployeeMapper employeeMapper;
	
	/**
	 * 用的时候你直接使用@Autowired注解把你在配置文件中配置的sqlSession加进来即可
	 * 这个sqlSession就是一个批量的sqlSession
	 */
	@Autowired
	private SqlSession sqlSession;
	
	public List<Employee> getEmps() {
		//在这里你就可以通过sqlSession拿到这个EmployeeMapper对象,拿到后你就可以调用这个接口里面的任意方法了
		EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
		return null;
	}
}
```
**mybatis 自定义类型处理器**

```java
/**
 * 自定义类型处理器
 * 实现 TypeHandler 接口,或者继承 BaseTypeHandler
 *
 */
public class MyEnumEmpStatusTypeHandler implements TypeHandler<EmpStatus> {

	@Override
	public EmpStatus getResult(ResultSet rs, String columnName) throws SQLException {
		//需要根据从数据库中拿到的枚举状态码返回一个枚举对象
		int code = rs.getInt(columnName);
		System.out.println("从数据库中获取到的状态码为：" + code);
		EmpStatus empStatus = EmpStatus.getEmpStatusByCode(code);
		return empStatus;
	}

	@Override
	public EmpStatus getResult(ResultSet rs, int columnIndex) throws SQLException {
		//跟上面的区别就是一个通过列名去拿,一个通过索引去拿
		int code = rs.getInt(columnIndex);
		System.out.println("从数据库中获取到的状态码为：" + code);
		EmpStatus empStatus = EmpStatus.getEmpStatusByCode(code);
		return empStatus;
	}

	@Override
	public EmpStatus getResult(CallableStatement cs, int columnIndex) throws SQLException {
		//存储过程
		int code = cs.getInt(columnIndex);
		System.out.println("从数据库中获取到的状态码为：" + code);
		EmpStatus empStatus = EmpStatus.getEmpStatusByCode(code);
		return empStatus;
	}
	
	/**
	 * 定义当前数据如何保存到数据库中
	 */
	@Override
	public void setParameter(PreparedStatement ps, int i, EmpStatus parameter, JdbcType jdbcType) throws SQLException {
		//设置参数
		ps.setString(i, parameter.getCode().toString());
	}

}
```

```java
public enum EmpStatus {
	LOGIN(100, "用户登录"), LOGOUT(200, "用户登出"), REMOVE(300, "用户不存在");

	private Integer code;
	private String msg;

	private EmpStatus(Integer code, String msg) {
		this.code = code;
		this.msg = msg;
	}
    ..........getter setter方法
	
	//按照状态码返回枚举对象
	public static EmpStatus getEmpStatusByCode(Integer code) {
		switch (code) {
		case 100:
			return LOGIN;
		case 200:
			return LOGOUT;
		case 300:
			return REMOVE;
		default:
			return LOGOUT;
		}
	}
}

public class Employee implements Serializable {
	private Integer id;
	private String lastName;
	private String email;
	private String gender;
    //枚举类对象
	private EmpStatus empStatus;
}
```

```xml
<!--在全局配置文件中注册你的类型转换器 -->
<configuration>
    <typeHandlers>
		<!-- 1、配置我们自定义的 TypeHandler -->
		<!-- 后面的javaType表示,在处理EmpStatus这个对象的时候用我们定义的MyEnumEmpStatusTypeHandler处理器 -->
		<typeHandler handler="com.iflytek.mybatis.dao.MyEnumEmpStatusTypeHandler" javaType="com.iflytek.mybatis.EmpStatus"/>
		<!-- 
			2、也可以在处理某个「字段」的时候告诉 mybatis 用什么类型处理器
					保存：#{empStatus,typeHandler=你自定义的类型处理器}
					查询:
						<resultMap type="com.iflytek.mybatis.Employee" id="empMap">
							<id column="id" property="id"/>
							<result column="empStatus" property="empStatus" typeHandler="你自定义的类型处理器"/>
						</resultMap>
					注意：如果在参数位置修改 TypeHandler,应该保证保存数据和查询数据用的 TypeHandler 是一样的
		 -->
	</typeHandlers>
</configuration>

<!--
	具体也可以参看一个博客,关于mybatis 类型处理器的使用
 	https://blog.csdn.net/feelwing1314/article/details/24303943
-->
```




# Java8 笔记2

##### Java8 新特性简介

> - 速度更快
>   - 在底层主要是对 HashMap 做了改动，我们先回顾一下原来的 HashMap 是怎么样的？我们知道往 Map里面添加元素是不能重复的，如果 Map 不用哈希算法的话，那就要用 equals 去比，往 Map 里面每添加一个元素都要和 Map 里面所有的元素都比较一遍。假如我 Map 里面有 1w 个元素，那么我新添加一个元素就要 equals 一万次。因此，Map 使用哈希算法，在底层哈希表其实就是一个数组，数组里面存放的是一个一个的 Entry。我们说既然是数组就会有索引值，当我想要往 Map 里面添加元素的时候，会调用你这个元素的 hashCode 方法得到一个哈希code值，然后根据哈希算法对这个哈希code值进行运算，运算之后就生成了一个数组的索引值，然后就根据这个数组的索引值去找到这个索引值的位置，看下这个位置有没有元素，要是没有就直接存储，如果要是有元素在，就需要通过 equals 去比较两个元素的内容，如果内容一样，后面的元素覆盖前面的元素；如果 equals 后发现两个元素内容也不一样，那就形成链表，这种情况称之为 **“碰撞”**。我们应该尽可能的去避免"碰撞"，因为如果你链表的长度过长，当我再新添加一个元素到 Map 中，算出的 数组索引值也是这个位置的话，那我就要用 equals 方法和这个位置的链表所有元素再比一次，效率就低下了。我们要避免“碰撞”的话，我们能做的就是把 hashCode 方法和 equals 方法重写的时候尽量写的严谨一些，并且让 hashCode 方法和 equals 方法尽量保持一致。但是，不管你 hashCode 方法和 equals 方法写的多严谨，“碰撞”还是避免不了的。因为你新添加元素的时候，调用 hashCode 方法，最终运算出来一个数组的索引值，但是数组的长度是有限的，你算出来的索引值肯定是在有限的长度内，难免就会有重复的。所以 hashMap 里面会有一个“**加载因子**”的概念，默认是 0.75。当元素数量达到数组的 75%的时候数组就进行扩容，形成链表的元素就会被打断，然后重新通过哈希算法，算出一个新的数组索引值，存放到对应的位置。
>   - 下面说 JDK1.8 做的改变，JDK1.7 的时候是数组+链表，到了JDK1.8 的时候变成了数组 + 链表 + 红黑树，这个红黑树是二叉树的一种，说什么时候这个链表变红黑树呢？当你碰撞形成的链表的元素个数大于 8 时并且数组的总容量大于 64 的时候，链表就会被转换成红黑树。转成红黑树好在哪里呢？除了添加以外，其他的操作都比链表快。
> - 代码更少（增加了新的语法 Lambda 表达式）
> - 强大的 Stream API
> - 便于并行
> - 最大化减少空指针异常

` 其中最为核心的为 Lambda 表达式与 Stream API`



#### Lambda表达式

> - 为什么要使用 Lambda 表达式？
>   - Lambda 是一个匿名函数，我们可以把 Lambda 表达式理解为时一段可以传递的代码（可以像数据一样进行传递）。可以写出更简洁、更灵活的代码。

##### Lambda 表达式初探

- 我想比较两个数的大小，我可以使用匿名内部类的方式

```java
	// 匿名内部类的方式
	@Test
	public void test1() {
		Comparator<Integer> com = new Comparator<Integer>() {
			@Override
			public int compare(Integer o1, Integer o2) {
				return Integer.compare(o1, o2);
			}
		};
		TreeSet<Integer> treeSet = new TreeSet<Integer>(com);
	}

	// Lambda表达式的方式

	@Test
	public void test2() {
		Comparator<Integer> com = (x, y) -> Integer.compare(x, y);
		TreeSet<Integer> treeSet = new TreeSet<Integer>(com);
	}
```

- 假设我现在有个需求，想获取当前公司中员工年龄大于35的员工信息，传统的方法我们应该是这么做的：

```java
List<Employee> employees = Arrays.asList(new Employee("张三", 24, 3200),
                                         new Employee("李四", 32, 1300),
										new Employee("王五", 60, 7000),
                                         new Employee("赵六", 74, 8500), 
                                         new Employee("田七", 18, 2700));

	public List<Employee> filterEmployee(List<Employee> list) {
		List<Employee> emps = new ArrayList<Employee>();
		for (Employee employee : list) {
			if (employee.getAge() > 35) {
				emps.add(employee);
			}
		}
		return emps;
	}
//假设我现在又有一个需求:获取当期公司中员工工资大于5000的员工信息
    public List<Employee> filterEmployee2(List<Employee> list) {
            List<Employee> emps = new ArrayList<Employee>();
            for (Employee employee : list) {
                if (employee.getSalary() > 5000) { // 这部分代码发现跟上面的代码就一行代码不同,存在大量代码冗余
                    emps.add(employee);
                }
            }
            return emps;
        }
```

- 既然出现这么多冗余代码,我们就要想到对代码进行优化，优化方式一：

```java
//策略模式
//我写一个接口
public interface MyPredict<T> {
	public boolean test(T t);
}

	public List<Employee> filterEmployee(List<Employee> emps, MyPredict<Employee> mp) {
		// 我遍历emps集合,按照MyPredict接口的实现类的规则进行过滤
		List<Employee> emplist = new ArrayList<Employee>();
		for (Employee employee : emps) {
			if (mp.test(employee)) {
				emplist.add(employee);
			}
		}
		return emps;
	}
	@Test
	public void test4() {
		// 这样做的好处就是你设计好的代码filterEmployee不用动,当你有一个新需求的时候,只需要新写一个类去实现MyPredict即可
		List<Employee> list = filterEmployee(employees, new FilterEmployeeByAge());
		// 这样就得到过滤后的list了
		System.out.println("-----------------");
		// 假如我现在想要按工资过滤,那么我只需要新写一个实现类去实现MyPredict接口即可
		List<Employee> list2 = filterEmployee(employees, new FilterEmployeeBySalary());
	}
```

- 优化方式一种，你会发现如果你有很多需求的话，你就要新建很多的实现类，而这些实现类里面就一个方法，这样比较不划算，所以我们就考虑到使用匿名内部类的方式，优化方式二:

```java
	@Test
	public void test5() {
		List<Employee> list = filterEmployee(employees, new MyPredict<Employee>() {
			@Override
			public boolean test(Employee t) {
				return t.getSalary() < 6000;
			}
		});
	}
```

- 在匿名内部类的基础上，我们可以进一步使用 Lambda 表达式，优化方式三：

```java
	@Test
	public void test6() {
		List<Employee> list = filterEmployee(employees, (e) -> e.getSalary() > 5000);
	}
```
##### Lambda表达式语法

> - Java8 中引入了一个新的操作符 "->"，该操作符称为箭头操作符或Lambda操作符，箭头操作符将 Lambda 表达式拆分成两部分：
>   - **左侧：** 指定了 Lambda 表达式所需要的所有参数
>   - **右侧:**  指定了 Lambda 体，即 Lambda 表达式要执行的功能
> - 所谓的 Lambda 表达式其实就是对接口的实现，之前我们用匿名内部类去实现接口，现在我们就可以使用 Lambda 表达式去实现接口。关于左侧和右侧到底写什么在说下: 接口中它是有一个抽象方法的，所谓的 Lambda 表达式左侧的参数列表对应的就是你接口中抽象方法的参数列表，相应的箭头操作符的右侧，就是你对接口中抽象方法的实现。
> - 那假设接口中有多个抽象方法，Lambda 表达式到底实现哪一个呢？答：实际上 Lambda 表达式需要函数式接口的支持。所谓的函数式接口就是你这个接口中只有一个抽象方法的接口。

```java
/*
	 * 语法格式一：无参数,无返回值
	 * 		() -> System.out.println("hello world");
	 */
	@Test
	public void test1() {
		Runnable runnable = new Runnable() {
			@Override
			public void run() {
				System.out.println("hello world");
			}
		};
		runnable.run();
		
		Runnable run = () -> System.out.println("hello");
		run.run();
	}
```

```java
/*
	 * 语法格式二:有一个参数,并且无返回值
	 * 		(x) -> System.out.println(x);
	 * 语法格式三:若只有一个参数,小括号可以省略不写
	 * 		x -> System.out.println(x);
	 */
	@Test
	public void test2() {
		Consumer<String> con = (x) -> System.out.println(x);
		con.accept("大服务层v5");
	}
```

```java
/*
	 * 语法格式四:有两个以上的参数,有返回值,并且 Lambda 体中有多条语句
	 * Comparator<Integer> com = (x,y) -> {
			System.out.println("函数式接口");
			return Integer.compare(x, y);
		};
	 */
	@Test
	public void test3() {
		//如果 Lambda 体有多条语句,那么必须用大括号括起来
		Comparator<Integer> com = (x,y) -> {
			System.out.println("函数式接口");
			return Integer.compare(x, y);
		};
	}
```

```java
/*
	 * 语法格式五:若 Lambda 体中只有一条语句,return 和大括号都可以省略不写
	 * 		Comparator<Integer> com = (x,y) -> Integer.compare(x, y);
	 * 
	 * 语法格式六: Lambda 表达式的参数列表的数据类型可以省略不写,因为 JVM 编译器通过上下文推断出数据类型,即 "类型推断"
	 * 		(Integer x, Integer y) -> Integer.compare(x, y);
	 * 因为我左边参数列表写的是接口中抽象方法的参数列表,像上面Comparator接口,我在泛型中已经指定参数是Integer类型的,
	 * 所以接口的抽象方法中参数列表肯定是Integer类型的
	 */
	@Test
	public void test4() {
		Comparator<Integer> com = (x,y) -> Integer.compare(x, y);
	}
```

**注：**

> - Lambda 表达式需要函数时接口的支持
>   - 函数式接口: 接口中只有一个抽象方法的接口，称为函数式接口。可以使用注解 @FunctionalInterface修饰，检查是否是函数式接口，如果不是会编译不过。

**Java8 内置的四大函数式接口**

> - Consumer<T>     消费型接口
>   - void accept(T t)
> - Supplier<T>        供给型接口
>   - T get()
> - Function<T,R>      函数型接口
>   - R apply(T t)
> - Predicate<T>         断言型接口
>   - boolean test(T t)

```java
	//消费型接口
	@Test
	public void test1() {
		happy(10000, (m) -> System.out.println("消费了" + m));
	}
	
	public void happy(double money,Consumer<Double> d) {
		d.accept(money);
	}
	
	//供给型接口
	@Test
	public void test2() {
		//每次都产生一个随机数,一共产生10个
		List<Integer> numList = getNumList(10, () -> (int)(Math.random() *100));
		
	}
	//需求:产生指定个数的整数,并放入集合中
	public List<Integer> getNumList(Integer num, Supplier<Integer> sup) {
		List<Integer> list = new ArrayList<>();
		
		for(int i = 0; i < list.size(); i++) {
			Integer n = sup.get();
			list.add(n);
		}
		return list;
	}
	
	
	//函数型接口
	@Test
	public void test3() {
		String str = strHandler("\t\t\t你好   ", (m) -> m.trim());
		System.out.println(str);
	}
	//需求:用于处理字符串
	public String strHandler(String str, Function<String, String> fun) {
		return fun.apply(str);
	}
	
	//断言型接口
	@Test
	public void test4() {
		List<String> strList = Arrays.asList("aab","abcd","a");
		System.out.println(filterStr(strList, (t) -> t.length() > 2));
	}
	//需求:将满足条件的字符串,放入到集合中
	public List<String> filterStr(List<String> list, Predicate<String> pre) {
		List<String>newList = new ArrayList<>();
		for (String str : list) {
			if (pre.test(str)) {
				newList.add(str);
			}
		}
		return newList;
	}
```
##### 方法引用

> - 若 Lambda 体中的内容有方法已经实现了，我们就可以使用"方法引用"（可以理解为方法引用是 Lambda 表达式的另一种表现形式）
> - 主要有三种语法格式：
>   - 对象::实例方法名
>   - 类::静态方法名
>   - 类::实例方法名
> - 注意
>   1. Lambda 体中调用方法的参数列表与返回值类型，要与函数式接口中抽象方法的参数列表和返回值类型保持一致
>   2. 若 Lambda 参数列表中第一个参数是实例方法的调用者，而第二个参数是实例方法的参数时，就可以使用ClassName::method

```java
	/*
	 * 对象::实例方法名
	 */
	@Test
	public void test1() {
		PrintStream ps = System.out;
		//这里我可以用 Lambda 表达式去实现这个接口
		Consumer<String> con = (s) -> ps.println(s);   //如果你 Lambda 体中的功能已经有方法实现的话,我们就可以使用方法引用的方式
		//等同于
		Consumer<String> con1 = System.out::println;   //println这个方法也是一个参数,无返回值
		//用这种方式有个要求就是你接口中的参数列表和返回值要和方法引用的参数列表和返回值保持一致
	}
	
	@Test
	public void test2() {
		Employee emp = new Employee();
		Supplier<String> sup = () -> emp.getName();
		String str = sup.get();
		
		Supplier<String> sup2 =emp::getName;//注意这里连方法的括号都不要写,因为你方法的参数列表跟Supplier接口是一样的,通过上下文可以推断出来
		String str1 = sup2.get();
	}
	
	/*
	 * 类::静态方法
	 */
	@Test
	public void test3() {
		Comparator<Integer> com = (x,y) -> Integer.compare(x, y);
		
		Comparator<Integer> com1 = Integer::compare;   //这个compare方法是Integer类中的静态方法,所以可以使用『类名::方法名』的方式
	}
	
	@Test
	public void test4() {
		BiPredicate<String, String> bp = (x,y) -> x.equals(y);   //这里我 Lambda 中第一个参数是实例方法的调用者,第二个参数是实例方法的参数,所以这里我也可以使用方法引用,用类名::方法名
		boolean flag = bp.test("er", "er");
		System.out.println(flag);   //打印true
		
		//等同于这种写法
		BiPredicate<String, String> bp1 = String::equals;
		boolean flag2 = bp1.test("ert", "ery");  
		System.out.println(flag2);   //打印false
	}
```

##### 构造方法引用

> - 格式：   ClassName::new
> - 注意：
>   - 需要调用的构造器的参数列表要与函数式接口中抽象方法的参数列表保持一致

```java
	@Test
	public void test5() {
		//Lambda表达式的写法
		Supplier<Employee> sup = () -> new Employee();
		Employee employee = sup.get();
		
		//构造器引用方式
		Supplier<Employee> sup2 = Employee::new;   //这里调用的其实是无参的构造器
		//因为构造器参数列表要与接口中抽象方法的参数列表一致,接口中抽象方法是无参的,所以调用的是无参的构造器
	}
	
	@Test
	public void test6() {
		//Lambda 表达式的方式
		Function<String, Employee> fun = (x) -> new Employee(x);
		Employee employee = fun.apply("tom");
		
		//构造器引用的方式
		Function<String, Employee> fun1 = Employee::new;  //这里调用的其实是一个参数的构造器,因为Function接口中抽象方法的的参数列表是一个参数
		Employee employee2 = fun1.apply("tom");
		
	}
```

##### 数组引用

> - 格式   type[]::new

```java
	/*
	 * 数组引用
	 */
	@Test
	public void test7() {
		//Lambda 表达式的写法
		Function<Integer, String[]> fun = (x) -> new String[x];  //传进去一个参数,返回一个数组
		String[] strArray = fun.apply(10);
		System.out.println(strArray.length);   //打印10
		
		//数组引用的方式
		Function<Integer, String[]> fun2 = String[]::new;
		String[] strArray2 = fun2.apply(8);
		System.out.println(strArray2.length);   //打印8
	}
```
#### Stream API

##### 了解 Stream

> - Java8 中有两大最为重要的改变。第一个是 Lambda 表达式，另外一个就是 Stream API（java.util.stream.*）
> - Stream 是 Java8 中处理集合的关键抽象概念，它可以指定你希望对集合进行的操作，可以执行非常复杂的查找、过滤和映射数据等操作。使用 Stream API 对集合数据进行操作，就类似于使用 SQL 执行数据库查询。简言之，Stream API 提供了一种高效且易于处理数据的方式。

##### 什么是 Stream

> 流（Stream）到底是什么？
>
> - 它是数据渠道，用于操作数据源（集合、数组）所生成的元素序列。
> - “集合讲的是数据，流讲的是运算”（就是把这些数据经过一系列的流水线式的中间操作，最终产生一个新流）
>
> 注意：
>
> 1. Stream 自己不会存储元素
> 2. Stream 不会改变源对象。相反，他们会返回一个持有结果的新 Stream
> 3. Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才会执行

##### Stream 操作的三个步骤

> - 创建 Stream
>   - 一个数据源（如：集合，数组），获取一个流
> - 中间操作
>   - 一个中间操作链，对数据源的数据进行处理
> - 终止操作
>   - 一个终止操作，执行中间操作链，并产生结果 

##### 创建 Stream 

```java
	//创建Stream
	@Test
	public void test1() {
		//1.可以通过 Collection 系列集合提供的 Stream() 或 paralleStream()来获取一个流
		List<String> list = new ArrayList<>();
		Stream<String> stream = list.stream();
		
		//2.通过Arrays 中的静态方法 stream() 获取数组流
		String[] strArray = new String[10];
		Stream<String> stream2 = Arrays.stream(strArray);
		
		//3.通过 Stream 类中的静态方法 of()
		Stream<String> stream3 = Stream.of("aa","bb","cc");
		
		//4.创建无限流
		//①通过迭代的方式去创建
		Stream<Integer> stream4 = Stream.iterate(0, (x) -> x+2);  //这就是按照我一元运算的规律产生一个无限流
		stream4.forEach(System.out::println);
		//②通过生成的方式去创建
		Stream<Double> stream5 = Stream.generate(() -> Math.random());   //这里的参数是一个供给型接口,可以不断的产生对象
		stream5.limit(5)
				.forEach(System.out::println);
	}
```

##### Stream 的中间操作

多个中间操作可以连接起来形成一个流水线，除非流水线上触发终止操作，否则中间操作不会执行任何的处理。而在终止操作时一次性全部处理，称为 “惰性求职”



##### 筛选与切片

> - filter(Predicate p) ----接收 Lambda，从流中排除某些元素
> - limit(long maxsize) -----截断流，使其元素不超过给定数量
> - skip(n) ----- 跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流。与 limit(n) 互补。
> - distinct --- 筛选，通过流所生成的元素的 hashCode() 和 equals() 去除重复元素

```java

public class TestStreamAPI2 {
	
	List<Employee> emps = Arrays.asList(new Employee("张三", 24, 3200)
												,new Employee("李四", 32, 1300)
												,new Employee("王五", 60, 7000)
												, new Employee("赵六", 74, 8500)
												, new Employee("田七", 18, 2700));
	
	//内部迭代:迭代操作由 Stream API 完成
	@Test
	public void test1() {
		Stream<Employee> stream = emps.stream();
		stream.filter((e) -> e.getAge() > 30)   //这一步称为中间操作,不会执行任何操作。
				.forEach(System.out::println);  //这一步称为终止操作,一次性执行全部内容。要是你不写这一步的话,上面中间操作你是看不见结果的
		//中间操作不执行任何操作,而在终止操作时一次性执行全部内容,称为 "惰性求值"
		//其实上面filter的过程是有一个迭代的过程的,你在过滤的时候,会把集合中每个元素都拿出来然后看年龄是不是大于30,这其中就有迭代的操作,只不过这个
		//迭代不是我们自己做的,是 Stream API 帮我们做的。
	}
	//外部迭代
	@Test
	public void test2() {
		//外部迭代就是需要我们自己去写循环迭代操作
		Iterator<Employee> iterator = emps.iterator();
		while (iterator.hasNext()) {
			System.out.println(iterator.next());
		}
	}
	
	@Test
	public void test3() {
		emps.stream()
			.limit(3)    //只要满足条件的前3个元素
			.forEach(System.out::println);;
	}
	
	@Test
	public void test4() {
		emps.stream()
			.skip(2)     //跳过前两个元素
			.forEach(System.out::println);
	}
	
	@Test
	public void test5() {
		emps.stream()
			.distinct()   //这个去重是根据 hashCode 和 equals 方法去重的,你实体类要是没有重写这两个方法是去不了的
			.forEach(System.out::println);
	}

}
```

##### 映射

> - map
>   - 接收 Lambda ，将元素转换成其它形式或提取信息。接收一个函数作为参数，该函数或应用到每个元素上，并将其映射成一个新的元素。
> - flatMap
>   - 一般接收一个函数作为参数，将流中每个值都换成另一个流，然后把所有流连成一个流。

```java
@Test
	public void test6() {
		List<String> strList = Arrays.asList("aa","bb","cc");
		strList.stream()
				.map((s) -> s.toUpperCase())   //这里会把strList中每个元素都应用到 map 里面的函数中
				.forEach((x) -> System.out.println(x));
		
		System.out.println("----------------");
		//map和flatMap都返回一个流
		Stream<Stream<Character>> stream = strList.stream()
				.map((str) -> TestStreamAPI2.filterCharacter(str));  //流中嵌套流,因为我们说map会把数据源中的每个元素都应用到括号里面的函数上,括号里面的函数返回一个流,而map本身也会返回一个流,所以就是一个大流中嵌套这一个一个的小流
		stream.forEach((sm) -> {   //第一次遍历会返回一个流,再次遍历才会拿到流中的字符
			sm.forEach(System.out::println);
		});
		System.out.println("--------------");
		//map和flatMap的区别,说白了就是map是把一个个的流放入到流中,flatMap是把一个个流中的元素放入到流中
		Stream<Character> stream2 = strList.stream()
				.flatMap(TestStreamAPI2::filterCharacter);  //map是不不同的流放到一个流中,flatMap会把流中的元素都拿出来放到一个流中.可类比集合中的add()和addAll()方法。我add方法里面也可以加入一个集合,但和addAll区别就是一个直接把集合加入到另一个集合中去,另一个是把集合中的元素加入到另一个集合中去
		stream2.forEach(System.out::println);
				
	}

public static Stream<Character> filterCharacter(String str) {
		List<Character> list = new ArrayList<Character>();
		for (Character ch : str.toCharArray()) {
			list.add(ch);
		}
		return list.stream();
	}
```

##### 排序

> - sorted
>   - 自然排序
> - sorted(Comparator comp)
>   - 定制排序

```java
	@Test
	public void test7() {
		List<String> list = Arrays.asList("aa","cc","bb");
		//自然排序
		list.stream()
			.sorted()
			.forEach(System.out::println);
		
		//定制排序
		emps.stream()
			.sorted((x,y) -> {
				if (x.getAge() == y.getAge()) {
					return x.getName().compareTo(y.getName());
				} else {
					return Integer.compare(x.getAge(), y.getAge());
				}
			}).forEach(System.out::println);;
	}
```

##### Stream 的终止操作

**查找与匹配**

> - allMatch          检查是否匹配所有的元素
> - anyMatch        检查是否至少匹配一个元素
> - noneMatch       检查是否没有匹配所有的元素
> - findFirst            返回第一个元素
> - findAny              返回当前流中的任意元素
> - count                  返回流中元素的个数
> - max                     返回流中的最大值
> - min                      返回流中的最小值 

```java
	List<Employee> emps = Arrays.asList(new Employee("张三", 24, 3200,Status.BUSY)
			,new Employee("李四", 32, 1300,Status.FREE)
			,new Employee("王五", 60, 7000,Status.VACATION)
			, new Employee("赵六", 74, 8500,Status.FREE)
			, new Employee("田七", 18, 2700,Status.VACATION));
	
	@Test
	public void test1() {
		//allMatch,检查是否匹配所有的元素,就是看emps中是否所有元素的状态都是BUSY
		boolean b1 = emps.stream()
			.allMatch((s) -> s.getStatus().equals(Status.BUSY));
		System.out.println(b1);   //打印false
		
		//anyMatch 检查是否至少匹配一个元素,就是看emps中是否至少有一个元素的状态是BUSY
		boolean b2 = emps.stream()
				.anyMatch((s) -> s.getStatus().equals(Status.BUSY));
		System.out.println(b2);   //打印 true
		
		//noneMatch 检查是否没有匹配所有的元素,打印false就是表示emps种不是没有匹配所有的元素,就是有匹配的元素
		boolean b3 = emps.stream()
				.noneMatch((s) -> s.getStatus().equals(Status.BUSY));
		System.out.println(b3);   //打印 false
		
		//findFirst 返回第一个元素,首先对员工的工资进行排序,然后获取第一个元素就能得到工资最低或者最高的那个员工
		Optional<Employee> optional = emps.stream()
			.sorted((e1,e2) -> Double.compare(e1.getSalary(), e2.getSalary()))
			.findFirst();
		System.out.println(optional.get());
		
		//findAny 返回流中的任意元素,我先把集合中状态为空闲的元素都过滤出来,然后从空闲的元素里面返回任意一个元素
		Optional<Employee> optional2 = emps.stream()
			.filter((e) -> e.getStatus().equals(Status.VACATION))
			.findAny();
		System.out.println(optional2.get());
	}
	
	@Test
	public void test2() {
		//count 返回流中元素的个数
		long count = emps.stream()
			.count();
		System.out.println(count);
		
		//返回流中的最大值,我根据工资去比较,返回流中工资最大的那个员工信息
		Optional<Employee> op1 = emps.stream()
			.max((e1,e2) -> Double.compare(e1.getSalary(), e2.getSalary()));
		System.out.println(op1.get());
		
		//我想返回所有员工中工资最低的那个员工的工资
		Optional<Double> min = emps.stream()
			.map((e) -> e.getSalary())   //先用 map 提取出所有的工资信息
			.min(Double::compare);       //然后找出最小的那个工资
		System.out.println(min.get());
	}
```

**归约**

> - reduce(T iden, BinaryOperator b)
>   - 可以将流中的元素反复结合起来，得到一个值。返回 T
> - reduce(BinaryOperator b)
>   - 可以将流中的元素反复结合起来，得到一个值。返回 Optional<T>

**注：**

map 和 reduce 的连接通常被称为 map-reduce 模式，因 Google 用它来进行网络搜索而出名

```java
	@Test
	public void test3() {
		List<Integer> list = Arrays.asList(1,2,3,4,5,6,7,8,9,10);
		Integer sum = list.stream()
			.reduce(0, (x,y) -> Integer.compare(x, y));
		System.out.println(sum);
		//算出所有员工的工资总数
		Optional<Double> op = emps.stream()
			.map(Employee::getSalary)
			.reduce(Double::sum);
		System.out.println(op.get());
	}
```

**收集**

> - collect(Collector c)
>   - 将流转换为其他形式。接收一个 Collector 接口的实现，用于给 Stream 中元素做汇总的方法
>   - Collector 接口中方法的实现决定了如何对流执行收集操作（如收集到 List、Set、Map）。但是 Collectors 工具类提供了很多静态的方法，可以方便的创建常见的收集器实例 

```java
	@Test
	public void test4() {
		//现在我想把集合中所有员工的名字都提取出来,然后放到一个集合中去
		List<String> list = emps.stream()
			.map((e) -> e.getName())   //先把所有的名字取出来,然后给他收集到List中去
			.collect(Collectors.toList());   //需要传一个collector,这个collector被称为是收集器,它的作用就是你想按照什么方式进行结果的收集操作
		list.forEach(System.out::println);
		
		System.out.println("------------");
		//上面的api可以满足你把数据收集到list、set、map中,但是如果我想收集到特殊的一些集中中,比如LinkedHashSet中,应该怎么做呢
		LinkedHashSet<String> linkedHashSet = emps.stream()
			.map(Employee::getName)
			.collect(Collectors.toCollection(LinkedHashSet::new));  //toCollection方法里面可以传你要收集到那个集合中去
		linkedHashSet.forEach(System.out::println);
	}
	
	@Test
	public void test5() {
		//总数
		Long count = emps.stream()
			.collect(Collectors.counting());
		System.out.println(count);
		
		//平均值
		Double avg = emps.stream()
			.collect(Collectors.averagingDouble((e) -> e.getSalary()));
		System.out.println(avg);
		
		//总和
		Double sum = emps.stream()
			.collect(Collectors.summingDouble((e) -> e.getSalary()));
		System.out.println(sum);
		
		//最大值
		Optional<Employee> max = emps.stream()
			.collect(Collectors.maxBy((e1,e2) -> Double.compare(e1.getSalary(), e2.getSalary())));
		System.out.println(max.get());
		
		//最小值
		Optional<Double> min = emps.stream()
			.map(Employee::getSalary)
			.collect(Collectors.minBy(Double::compare));
		System.out.println(min.get());
	}
	
	@Test
	public void test6() {
		//分组
		//把员工信息按照状态进行分组
		Map<Status, List<Employee>> group = emps.stream()
			.collect(Collectors.groupingBy((e) -> e.getStatus())); //分组,按照员工的状态进行分组,所以返回结果是一个Map,key就是状态,value是一个集合
		System.out.println(group);
		/*
		 * 在sql语句中我们会遇到这种情况,就是先按照部门分组,然后在按照年龄分组,这样的多级分组
		 */
		//多级分组
		Map<Status, Map<String, List<Employee>>> manyGroup = emps.stream()
				//选两个参数的方法,第一个参数传一个函数,第二个参数又传一个collector这样你就可以无限的分组下去
			.collect(Collectors.groupingBy((e) -> e.getStatus(), Collectors.groupingBy((e) -> {
				if (((Employee) e).getAge() > 18) {
					return "成年了";
				} else {
					return "未成年";
				}
			})));
		System.out.println(manyGroup);
	}
	
	@Test
	public void test7() {
		//分区,分true和false两个区。满足条件的一个区,不满足条件的一个区
		Map<Boolean, List<Employee>> block = emps.stream()
			.collect(Collectors.partitioningBy((e) -> e.getSalary() > 4000));  //大于4000的为true时一个区,小于4000的为false是一个区
		System.out.println(block);
	}
	
	@Test
	public void test8() {
		//连接字符串
		String join = emps.stream()
			.map((e) -> e.getName())
			.collect(Collectors.joining(","));   //把名字连接起来中间用逗号分隔
		System.out.println(join);   //打印张三,李四,王五,赵六,田七
	}
```
##### 并行流

> - 并行流就是把一个内容分成多个数据块，并用不同的线程分别处理每个数据块的流
> - Java8 中将并行进行了优化，我们可以很容易的对数据进行并行操作。Stream API 可以声明性的通过 paraller() 与 sequential() 在并行流与顺序流之间切换

##### Fork/Join 框架

> - Fork/Join 框架，就是在必要的情况下，将一个大任务，进行拆分（fork）成若干个小任务（拆到不能再拆为止），再将一个一个的小任务运算的结果进行 join 汇总

##### Fork/Join框架与传统线程池的区别

> - 所谓的多线程就是把我们的任务分配到 CPU 不同的核上，以四核 CPU	 为例，说白了就是四个线程，如果是传统的线程，就是把线程任务分配到一个个的 CPU 线程核上执行。这样就存在一个问题，就是每一个任务它有可能会阻塞，这就会造成一个这样的情况，假设 CPU 一个A线程核上有一个线程任务阻塞了，那么它后面的线程任务就不能执行了，就一直处于等待状态，与此同时，要是别的B线程核上的线程任务都执行完了，那么这个线程核就处于空闲状态，可是线程核A还有任务没执行完，线程核B处于空闲状态，这样就会造成 CPU 资源浪费
> - 而 Fork/Join 框架会采用“工作窃取模式”，就是当执行新的任务时，它可以将其拆分成更小的任务执行，并将小任务加到线程队列中，然后再从随机的一个线程队列中偷一个任务并把它放到自己的队列中。还是上面的例子，四核 CPU,假设 B 线程核在获取线程任务获取不到了（很大可能就是 B 线程核上的任务全都执行完了）那么它就会随机的从别的线程队列中偷一个任务到自己的线程队列中来执行。这样就提高了 CPU 利用率。

##### Optional 类

Optional<T> 类（java.util.Optional）是一个容器类，代表一个值存在或不存在，原来用 null 表示一个值不存在，现在 Optional可以更好的表达这个概念。并且可以避免空指针异常。

> 常用方法:
>
> - Optional.of(T t)      创建一个 Optional 实例
> - Optional.empty()     创建一个空的 Optional 实例
> - Optional.ofNullable(T t)      若 t 不为 null，创建 Optional 实例，否则创建空实例
> - isPresent()      判断是否包含空值
> - orElse(T t)        如果 Optional 中包含对象，则返回该对象，否则返回 t
> - orElseGet(Supplier s)   如果 Optional 中包含对象，则返回该对象，否则返回 s 获取到的值

```java
	@Test
	public void test() {
		//Optional 是一个容器类,专门用来封装对象的
		Optional<Employee> optional = Optional.of(new Employee());
		/*
		 * 我们说Optional这个类是用来预防空指针异常的,但是它也可能发生空指针异常
		 * 但是它设计的思想就是,传进来一个Null我在构造Optional类的时候就会发生空指针异常,这样异常的位置就很明显,不像之前
		 * 我们需要一步一步debug去找出空指针异常的位置,这样我们就能以最快的速度锁定空指针异常的位置
		 */
		Optional<Employee> optional2 = Optional.of(null);     //空指针异常发生的位置
		System.out.println(optional2.get());
	}
	
	@Test
	public void test2() {
		//构建一个空的Optional 实例
		Optional<Employee> optional = Optional.empty();
		//这里会发生空指针异常,因为我构建的是一个空的Optional
		System.out.println(optional.get());   //空指针异常的位置
	}
	
	@Test
	public void test3() {
		//ofNullable可以传空也可以传对象
		Optional<Employee> optional = Optional.ofNullable(null);
		if (optional.isPresent()) {    //判断一下Optional里面有没有对象,要是有对象我才get()一下
			System.out.println(optional.get());
		}
		//orElse方法,如果Optional里面有对象,我就返回该对象,否则我就返回参数里面写的对象
		Employee employee = optional.orElse(new Employee("Jimmy"));
		System.out.println(employee);
		
		//orElseGet方法,如果Optional中有对象,返回该对象,否则返回供给型接口返回的对象
		Employee emp = optional.orElseGet(() -> new Employee());
		System.out.println(emp);
	}
```

##### 接口中的默认方法

> - Java8 中允许接口中包含具有具体实现的方法，该方法称为 “默认方法” ，默认方法使用 default 关键字修饰 

```java
public interface MyFun {
	
	//接口中可以包含有具体实现的方法,该方法称为默认方法,默认方法用default 关键字修饰
	default String getName() {
		return "哈哈";
	}
}
```

##### 接口中类优先原则

```java
public interface MyFun {
	
	//接口中可以包含有具体实现的方法,该方法称为默认方法,默认方法用default 关键字修饰
	default String getName() {
		return "哈哈";
	}
}

public class MyClass {
	
	public String getName() {
		return "嘿嘿";
	}

}

public class SubClass extends MyClass implements MyFun {

}

public class TestInterface {
	public static void main(String[] args) {
		/*
		 * 现在我有一个接口MyFun,接口里面有一个默认方法getName
		 * 我还有一个类MyClass,类中也有一个方法,名字也叫getName
		 * 我有一个子类,现在去继承MyClass实现MyFun接口
		 * 然后我创建子类对象,调用getName方法,发现打印出来的是『类中』getName方法的返回值,这就是接口默认方法的“类优先原则”
		 */
		SubClass sub = new SubClass();
		System.out.println(sub.getName());
	}

}
```

**还有一个注意事项**

```java
public interface MyFun {
	
	//接口中可以包含有具体实现的方法,该方法称为默认方法,默认方法用default 关键字修饰
	default String getName() {
		return "哈哈";
	}
}

public interface MyInterface {
	default String getName() {
		return "啦啦";
	}

}

public class SubClass implements MyFun,MyInterface {

	@Override
	public String getName() {
		return MyFun.super.getName();     //这里我就指定一下调用MyFun里面的getName方法,或者你也可以自己去实现
	}

}

public class TestInterface {
	public static void main(String[] args) {
		/*
		 * 现在我有两个接口MyFun和MyInterface
		 * 这两个接口里面都有一个默认的叫getName的方法
		 * 现在我有一个子类去实现这两个接口,这时,我在子类中就必须要去重写这个方法,或者指定调用哪个接口的getName方法
		 */
		SubClass sub = new SubClass();
		System.out.println(sub.getName());
	}
}
```



##### 新时间日期 API

Java8 出了一套全新的时间日期 API 替代之前的旧的时间日期 API。此外，Java8 之前的时间日期 API 有一个最大的特点就是 ：它们都是可变的，存在线程安全问题。而全新的时间日期 API 是不可变的，这个不可变的意思就是，你像 String 这个类，我们称 String 也是不可变的，也就是说不管对这个类做怎样的改变，它都将会产生一个新的实例。同理我们新时间日期的 API 也是不可变的，你不管对它做怎样的改变，它都将产生一个新的实例。所以它是线程安全的。

##### 旧时间日期 API 线程安全问题

```java
	public static void main(String[] args) throws InterruptedException, ExecutionException {
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
		/*
		 * 编写多线程一般有三种方法,Thread、Runnable、Callable
		 * Callable规定的方法是call(),Runnable规定的方法是run()
		 * Callable和Runnable相比,多了一个返回值
		 */
		Callable<Date> task = new Callable<Date>() {

			@Override
			public Date call() throws Exception {
				return sdf.parse("2018-01-25");
			}
		};
		List<Future<Date>> result = new ArrayList<>();
		ExecutorService pool = Executors.newFixedThreadPool(10);
		for (int i = 0; i < 10; i++) {
			Future<Date> future = pool.submit(task);  //把线程任务提交到线程池里面去执行
			result.add(future);
		}
		
		//遍历集合
		for (Future<Date> fu : result) {
			System.out.println(fu.get());
		}
		pool.shutdown();
	}
//上述代码跑起来会报错,因为存在线程安全问题,多个线程一起跑就报错了
```

##### 同样的代码改用新时间日期API

```java
	public static void main(String[] args) throws InterruptedException, ExecutionException {
		DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd");
		/*
		 * 编写多线程一般有三种方法,Thread、Runnable、Callable
		 * Callable规定的方法是call(),Runnable规定的方法是run()
		 * Callable和Runnable相比,多了一个返回值
		 */
		Callable<LocalDate> task = new Callable<LocalDate>() {

			@Override
			public LocalDate call() throws Exception {
				return LocalDate.parse("2018-01-25", dtf);
			}
		};
		List<Future<LocalDate>> result = new ArrayList<>();
		ExecutorService pool = Executors.newFixedThreadPool(10);
		for (int i = 0; i < 10; i++) {
			Future<LocalDate> future = pool.submit(task);  //把线程任务提交到线程池里面去执行
			result.add(future);
		}
		
		//遍历集合
		for (Future<LocalDate> fu : result) {
			System.out.println(fu.get());
		}
		pool.shutdown();
	}
//上述代码执行的时候就不会报错,是线程安全的,会打印10次2018-01-25
```

##### LocalDate、LocalTime、LocalDateTime

> - LocalDate、LocalTime、LocalDateTime 类的实例是不可变的对象，分别表示使用 ISO-8601 日历系统的日期、时间、日期和时间。他们提供了简单的日期或时间，并不包含当前的时间信息。也不包含时区相关的信息。

```java
	@Test
	public void test1() {
		//获取当前时间
		LocalDateTime dt = LocalDateTime.now();
		System.out.println(dt);    //打印2018-01-25T22:31:28.419
		
		//这样也可以得到一个LocalDateTime实例
		LocalDateTime dt2 = LocalDateTime.of(2018, 01, 25, 22, 32);
		System.out.println(dt2);    //打印2018-01-25T22:32
		//现在我想对时间做一些操作
		LocalDateTime plusYears = dt.plusYears(2);    //我在当前时间的基础上加了2年,注意:我不管对时间做怎样的改变,它都会产生一个新的实例
		System.out.println(plusYears);   //打印2020-01-25T22:36:54.776
		LocalDateTime minusMonths = dt.minusMonths(2);  //减2个月,同样的不管做怎样的操作都会得到一个新的实例
		System.out.println(minusMonths);   //打印2017-11-25T22:38:37.690
		
		//获取年、月、日、时、分、秒
		System.out.println(dt.getYear());   
		System.out.println(dt.getMonthValue());
		System.out.println(dt.getDayOfMonth());
		System.out.println(dt.getHour());
		System.out.println(dt.getMinute());
		System.out.println(dt.getSecond());
	}
```

##### 时间戳

> - Instant：时间戳（以Unix 元年：1970年1月1日00:00:00到某个时间的毫秒值）

```java
	@Test
	public void test2() {
		Instant now = Instant.now();   //默认获取的是 UTC 时区时间,晚八小时
		System.out.println(now);    //打印2018-01-25T14:56:42.481Z
		
		OffsetDateTime odt = now.atOffset(ZoneOffset.ofHours(8));  //可以通过这个方法去修改偏移量
		System.out.println(odt);  //打印2018-01-25T23:06:16.592+08:00   带偏移量的时间
		//打印时间戳的毫秒数
		System.out.println(now.toEpochMilli());    //打印1516892877030
		//可以操作秒或者毫秒数
		Instant ins = Instant.ofEpochSecond(60);   //意思就是在1970年1月1日00:00:00基础上加60秒
		System.out.println(ins);  //打印1970-01-01T00:01:00Z
	}
```

##### 计算时间/日期间隔

> Duration 和 Period
>
> - Duration   用于计算两个时间间隔
> - Period       用于计算两个日期间隔

```java
	//获取两个时间之间的间隔
	@Test
	public void test3() {
		Instant ins1 = Instant.now();
		try {
			Thread.sleep(1000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		Instant ins2 = Instant.now();
		Duration duration = Duration.between(ins1, ins2);
		System.out.println(duration.toMillis());   //这里注意下,获取秒或纳秒用的是get...的API,获取像毫秒、分钟用的是to...的API
		System.out.println("----------------");
		
		LocalTime lt = LocalTime.now();
		try {
			Thread.sleep(1000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		LocalTime lt2 = LocalTime.now();
		Duration duration2 = Duration.between(lt, lt2);
		System.out.println(duration.toMillis());   //打印1000
	}
	
	//获取两个日期之间的间隔
	@Test
	public void test4() {
		//通过now获取一个LocalDate实例
		LocalDate ld1 = LocalDate.now();
		//通过传入具体的年、月、日得到一个LocalDate实例
		LocalDate ld2 = LocalDate.of(1996, 02, 21);
		//计算两个日期之间的间隔
		Period period = Period.between(ld2, ld1);
		System.out.println(period.getYears());   //打印21
		System.out.println(period.getMonths());  //打印11
		System.out.println(period.getDays());     //打印6 
	}
```

##### 时间校正器

> - TemporalAdjuster ：时间校正器。有时候我们可能需要获取像 “下个周日” 这样的日期。就可以使用时间校正器。
> - TemporalAdjusters： 上面那个接口的工具类。里面通过静态方法提供了很多常用的TemporalAdjuster 的实现。

```java
	//TemporalAdjusters,里面封装了很多TemporalAdjuster的实现
	@Test
	public void test5() {
		LocalDateTime ldt = LocalDateTime.now();
		System.out.println(ldt);  //打印2018-01-27T16:50:33.402
		
		LocalDateTime ldt2 = ldt.withDayOfMonth(9);  //通过with...方法你可以把年月日指定成你需要的值
		System.out.println(ldt2);   //打印2018-01-09T16:50:33.402
		//里面传一个参数,就是时间校正器,通过时间校正器,你就可以得到一个特殊的时间,比如说下个周日
		LocalDateTime ldt3 = ldt.with(TemporalAdjusters.next(DayOfWeek.SUNDAY));   //下个周日
		System.out.println(ldt3);
		
		//自定义,去实现TemporalAdjuster这个接口
		//现在我想获取下个工作日
		LocalDateTime ldt5 = ldt.with((l) -> {   //TemporalAdjuster是一个函数型接口,我可以用 Lambda 表达式去实现它
			LocalDateTime ldt4 = (LocalDateTime) l;
			DayOfWeek dow = ldt4.getDayOfWeek();
			if (dow.equals(DayOfWeek.FRIDAY)) {
				return ldt4.plusDays(3);
			} else if (dow.equals(DayOfWeek.SATURDAY)) {
				return ldt4.plusDays(2);
			} else{
				return ldt4.plusDays(1);
			}
		});
		System.out.println("---" + ldt5);
	}
```

##### 时间格式化

> java.time.format.DateTimeFormatter 类：该类提供了三种格式化方法：
>
> - 预定义的标准格式
> - 语言环境相关的格式
> - 自定义的格式

```java
	@Test
	public void test6() {
		DateTimeFormatter dtf = DateTimeFormatter.ISO_DATE;  //默认提供的格式化格式
		LocalDateTime ldt = LocalDateTime.now();
		//格式化的方式有很多种,你调用谁的方法都行
		String ldtStr = ldt.format(dtf);
		System.out.println(ldtStr);      //2018-01-28
		//自定义的方式来格式化
		DateTimeFormatter dtf2 = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm:ss");
		String format = ldt.format(dtf2);   //按照什么格式来进行格式化
		System.out.println(format);   //打印2018年01月28日 10:46:07
		
		//或者也可以这么写
		String format2 = dtf2.format(ldt);   //去格式化哪个时间日期
		System.out.println(format2);   //打印2018年01月28日 10:47:57
		
		//把时间日期字符串解析回时间日期
		LocalDateTime ldt3 = LocalDateTime.parse(format, dtf2);
		System.out.println(ldt3);   //打印2018-01-28T22:59:27
	}
```

##### 时区相关

> - Java8 中加入了对时区的支持，带时区的时间分别为：   ZonedDate、ZonedTime、ZonedDateTime 每个时区都对应一个时区 ID
> - ZoneId          该类中包含了所有的时区信息
>   - getAvailableZoneIds()           获取所有时区的时区信息
>   - of(id)      用指定的时区信息获取 ZoneId对象

```java
	@Test
	public void test7() {
		//获取所有的时区
		Set<String> set = ZoneId.getAvailableZoneIds();
		set.forEach(System.out::println);
	}
	
	@Test
	public void test8() {
		LocalDateTime ldt = LocalDateTime.now(ZoneId.of("America/Edmonton"));
		System.out.println(ldt);  //打印2018-01-28T08:51:22.374
		
		LocalDateTime ldt2 = LocalDateTime.now(ZoneId.of("Asia/Shanghai"));
		ZonedDateTime zdt = ldt2.atZone(ZoneId.of("Asia/Shanghai"));   //带时区的时间日期
		System.out.println(zdt);  //打印 2018-01-28T23:56:16.249+08:00[Asia/Shanghai]
	}
```








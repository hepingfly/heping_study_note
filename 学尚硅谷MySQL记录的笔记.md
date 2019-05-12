# MySQL 笔记

##### 数据库的好处

> - 持久化数据到本地
> - 可以实现结构化查询，方便管理

数据库的相关概念

> - DB
>   - 数据库（database）：存储数据的“仓库”。它保存了一系列有组织的数据。
> - DBMS
>   - 数据库管理系统（Database Management System）数据库是通过 DBMS 创建和操作的容器
>   - 常见的数据库管理系统：MYSQL、 Oracle(甲骨文公司)、 DB2（IBM公司）、 SqlServer（微软，不支持跨平台，只能在windows上运行）。我们是在我们电脑上装上数据库管理系统，从而去管理数据库中的数据
> - SQL
>   - 结构化查询语言（Structure Query Language）：专门用来与数据库通信的语言

##### MYSQL 产品的特点

> - MySQL 数据库隶属于 MySQL AB公司，总部位于瑞典。08年的时候被 SUN 公司收购了，但是 SUN 公司又被 Oracle 公司收购了，所以现在 MySQL 属于 Oracle 公司。
> - 优点：
>   - 成本低：开放源代码，可以免费试用
>   - 性能高，执行速度很快
>   - 简单：很容易按照和使用

##### MySQL 配置文件介绍

> - MySQL 的按照目录下有一个  my.ini 文件，这个文件就是 MySQL 的配置文件。
> - 这个配置文件里面有个 [mysqld]，主要是来配置服务端的一些配置。
>   - port    端口号
>   - basedir   MySQL 数据库安装的目录
>   - datadir    MySQL 数据存储的位置。虽然说  MySQL 是数据库，我们平时说都说把数据存在数据库里面，但是 MySQL 它仅仅是对这些数据进行管理，本质上这些数据还是存储在文件中的。
>   - character-set-server    字符集。你要是想改字符集，改这字段的值就可以了。
>   - default-storage-engine    数据库存储引擎。引擎是用来执行 SQL 语句的。

##### MySQL 服务启动和停止

> 方法一：
>
> - 右击『计算机』，点击『服务』，找到你 MySQL当时安装时设置的服务名，点击启动或者停止即可。
>
> 方法二：
>
> - 以管理员身份运行windows 命令行，输入命令 『net start 服务名』 来启动 MySQL 服务。『net stop 服务名』来关闭服务

##### MySQL 服务的登录和退出

> - 登录
>   - 方式一
>     - 通过 mysql 自带的客户端 『Command Line Client』可以登录 mysql 但是这种方式不够灵活，只适用于 root 用户
>   - 方式二
>     - mysql -h 主机名 -P 端口号 -u 用户名 -p             敲回车会提示你输入密码,密码输入正确即可进入
>     - mysql -u 主机名 -p        如果你登录的是本机的mysql 就可以这么简写
> - 退出
>   - exit 或 ctrl + c

**注：**

> 你进入 mysql 以后会看到系统自带的四张表 
>
> - information_schema     这个数据库是用于保存一些元数据信息的
> - mysql      这个数据库专门用于保存用户信息的
> - performance_schema    这个数据库用于保存一些性能信息、性能参数
> - test    这个数据库是一个测试数据库，这个数据库是空的，你可以直接操作这个数据库。注意：这个数据库你可以随意操作，但是上面三个你是不能去操作的 



MySQL 常用命令介绍

> - show databases;   查看所有数据库
> - use 数据库名;         打开指定的数据库
> - show tables;            查看当前库的所有表
> - show tables from 库名;        查看其它库的所有表
> - desc 表名;           查看表结构
> - 查看数据库版本
>   - 如果你已经登录到 mysql 服务端 你可以使用命令     select version();  去查看mysql 版本
>   - 如果你没有登录到 mysql 服务端  你可以使用  mysql -V      查看数据库版本      或者使用 mysql --version去查看

##### MySQL 语法规范

> - 不区分大小写，但建议关键字大写，表名、列名小写
> - 每条命令最好是用分号结尾
> - 每条命令根据需要可以缩进或换行
> - 注释
>   - 单行注释     #注释文字
>   - 单行注释      -- 注释文件          这里两个横杠和注释文件之间要有一个空格
>   - 多行注释      /*   注释文字  */

##### 基础查询

> - 语法：  select 查询列表 from 表名
> - 特点：
>   - 查询列表可以是： 表中的字段、常量值、表达式、函数
>   - 查询结果是一个虚拟的表格

```mysql
#1.查询单个字段
SELECT last_name from employees
#2.查询表中多个字段
SELECT last_name,email from employees
#3.查询表中所有字段
SELECT * from employees
#4.查询常量值
SELECT 100
SELECT 'kimi'     #MySQL中是不区分字符和字符串的，一律用单引号括起来
#5.查询表达式
SELECT 100 * 98
#6.查询函数
SELECT VERSION()
#7.起别名
#方式一:使用 as
/*
 *  ①便于理解
 *  ②如果要查询的字段有重名的情况,使用别名可以区分开来
*/
SELECT last_name AS 姓 from employees
#方式二:使用空格
SELECT last_name  姓 from employees
#8.去重
SELECT DISTINCT department_id  from departments
#9.mysql 中 + 的作用
```

关于起别名的一个小案例:

```mysql
#查询 salary 字段,显示结果为 out put
SELECT salary AS out put from employees     #这种写法是错误的,因为你 out 和 put 中间有个空格
SELECT salary AS "out put" from employees    #应该这么写,用个双引号把它引起来
```

承接上面第9点，mysql 中 + 的作用

```mysql
/*
mysql 中的 + 号：
仅仅只有一个功能：运算符
SELECT 100 + 90;  两个操作符都是数值型,则做加法运算      (这个查询结果是190)
SELECT '123' + 90;    如果其中一方为字符型,它会试图将字符串型转化为数值型,如果转换成功,则继续做加法运算,如果转换失败,则将字符型转换成0     (这个查询结果是90)
SELECT NULL + 10;    只要其中一方为null,则结果肯定为null
*/
```

mysql 中 拼接

```mysql
#我现在想把last_name和first_name查询结果拼接在一起
SELECT CONCAT(last_name,first_name) 姓名 FROM employees  #使用CONCAT函数,中间可以传要拼接的参数
```

IFNULL 函数

```mysql
#IFNULL(expr1,expr2)函数,第一个参数传你可能为空的那个字段,第二个参数传要是第一个字段为空了,我用什么值代替
SELECT IFNULL(commission_pct,0) 奖金率 FROM employees  #commission_pct要是为空,我就用0代替
```

**注：**

`一般你自动生成一个sql脚本的时候,你会发现它在每个字段上面都加了一个着重号,说一下这个着重号的作用：`

```mysql
例如,有的sql语句可能会这么写：
SELECT `last_name` from employees     #这里的last_name字段上面有个着重号,这个着重号不是必须的,可写可不写
#那什么时候写呢？假如你查询的字段要是一个关键字,那就需要用着重号来区分下
SELECT `NAME` from employees      # NAME 在sql语句里面是一个关键字,那如果你的字段名字就叫 NAME 这时候就要用着重号来区分下
```



##### 条件查询

> - 语法
>
> select  查询列表  from 表名  where  筛选条件
>
> 筛选条件分类
>
> - 按条件表达式筛选
>   - mysql 中条件运算符有 > 、<、=、!=、<>、>=、<=
> - 按逻辑表达式筛选
>   - mysql 中逻辑运算符有  and   or   not
> - 模糊查询
>   - ​     like    between...and     in     is null

```mysql
#一、按条件表达式筛选
  #案例1：查询工资大于12000的员工信息
  SELECT * FROM employees WHERE salary > 12000
  #案例2：查询部门编号不等于90的员工名和部门编号
  SELECT last_name,department_id FROM employees WHERE department_id <> 90
#二、按逻辑表达式筛选
  #案例1：查询工资在 10000 到 20000 之间的员工名、工资以及奖金
  SELECT last_name,salary,commission_pct FROM employees WHERE salary > 10000 AND salary < 20000
  #案例2:查询部门编号不是在90到110之间,或者工资高于15000的员工信息
  SELECT * FROM employees WHERE NOT(department_id > 90 AND department_id < 110) OR salary > 15000
#三、模糊查询
  like    
	   特点：一般和通配符搭配使用
			通配符：  
					%   任意多个字符,包含0个字符
				    _   任意单个字符
  #案例1:查询员工名中包含字符 a 的员工信息
  SELECT * FROM employees WHERE last_name LIKE '%a%';
  #案例2：查询员工姓名中第三个字符为 e 第五个字符为 a 的员工姓名和工资
  SELECT last_name,salary FROM employees WHERE last_name LIKE '__e_a%';
  #案例3：查询员工名中第二个字符为_ 的员工名
  SELECT last_name FROM employees WHERE last_name LIKE '_\_%';  #mysql 中支持直接使用 反斜杠来进行转义
  SELECT last_name FROM employees WHERE last_name LIKE '_$_%' ESCAPE '$';  #mysql中推荐你先随意指定一个转义字符,然后在后面用ESCAPE关键字说明下你用的哪个转义字符

 between and
	   1、使用它可以提高语句的简洁度
       2、查出来的结果包含临界值
       3、两个临界值不要调换顺序
 #案例1:查询员工编号在100到200之间的员工信息
 SELECT * FROM employees WHERE employee_id BETWEEN 100 AND 200

in
   含义：判断某字段的值是否属于in列表中的某一项
   特点：
		1.使用in提高语句简洁度
		2.in列表里面值的类型必须一致
#案例：查询员工的工种编号在 IT_PROG、AD_VP、AD_PRES中的员工名和员工编号
SELECT last_name,job_id from employees WHERE job_id in('IT_PROT','AD_VP','AD_PRES')

is NULL
	1. = 或 <> 不能用于判断字段 null 值
	2. is NULL 或 is NOT NULL 可以判断字段 NULL 值
#案例1:查询没有奖金的员工名和奖金率
SELECT last_name,commission_pct FROM employees WHERE commission_pct is NULL;
#案例2:查询有奖金的员工名和奖金率
SELECT last_name,commission_pct FROM employees WHERE commission_pct is NOT NULL;
```


##### 排序查询

> - 语法
>
> select 查询列表 from 表 where 筛选条件 order by 排序列表 asc|desc
>
> - 特点
>   - asc 代表的是升序，desc代表的是降序，如果两个都不写，默认是按照升序排列
>   - order by 子句中可以支持单个字段、多个字段、表达式、函数、别名
>   - order by 子句一般是放在查询语句的最后面，limit 子句除外

```mysql
#案例1:查询员工信息,要求工资从高到低排序
SELECT * FROM employees ORDER BY salary DESC
#案例2:查询部门编号大于等于90的员工信息,并按照入职时间进行降序排列
SELECT * FROM employees WHERE department_id > 90 ORDER BY hiredate desc;
#案例3:按年薪的高低显示员工的信息和年薪（按表达式排序）
SELECT *,salary*12*(1 + IFNULL(commission_pct,0)) FROM employees ORDER BY salary*12*(1 + IFNULL(commission_pct,0)) DESC;
#案例4:按年薪的高低显示员工的信息和年薪（按别名排序）
SELECT *,salary*12*(1 + IFNULL(commission_pct,0)) 年薪 FROM employees ORDER BY 年薪 DESC;
#案例5：按姓名的长度显示员工的姓名和工资（按函数排序）
SELECT LENGTH(last_name) 长度,salary FROM employees ORDER BY LENGTH(last_name) DESC;
#案例6：查询员工信息，要求先按照工资进行升序，再按照员工编号进行降序（按多个字段排序）
SELECT * FROM employees ORDER BY salary ASC,department_id DESC;
```


常见函数：

> - 概念：类似于 Java 的方法，将一组逻辑语句封装在方法体中，对外暴露方法名
> - 好处：1、隐藏了实现细节    2、提高代码的重用性
> - 调用：select 函数名(实参列表) from 表名
> - 特点：
>   - 叫什么(函数名)
>   - 干什么（函数功能）
> - 分类：
>   - 单行函数      如：concat、length、ifnull等
>   - 分组函数
>     - 功能：做统计使用，又称为统计函数、聚合函数、组函数

##### 字符函数

```mysql
#一、字符函数
#1.length  获取参数值的字节个数
SELECT LENGTH('heping')     #长度为6
SELECT LENGTH('和平haha')   #长度为10   如果字符集为utf8一个汉字占三个字节,一个英文字母占一个字节,如果字符集为gbk一个汉字占两个字节

#2.concat 拼接字符串
SELECT CONCAT(last_name,'_',first_name) FROM employees

#3.upper  lower   大小写
SELECT UPPER('heping')
SELECT LOWER('hePinG')
#示例:将姓变大写，名变小写，然后拼接
SELECT CONCAT(UPPER(last_name),'_',LOWER(first_name)) FROM employees

#4.substr、substring   截取字符(用这两个都行,前面一个是简写)
#截取从指定索引处后面所有的字符
SELECT SUBSTR('林志颖是我的偶像',7)
#截取从指定索引处指定字符长度的字符
SELECT SUBSTR('林志颖是我的偶像',1,3)
#案例：姓名中首字符大写，其他字符小写，然后用"_"拼接显示出来
SELECT CONCAT(UPPER(SUBSTR(last_name,1,1)),'_',LOWER(SUBSTR(last_name,2))) FROM employees

#5.instr  返回子串第一次出现的索引,如果找不到返回 0
SELECT INSTR('林志颖是我的偶像','偶像')

#6.trim   取出前后空格
SELECT TRIM('  林志颖 ')
SELECT TRIM('a' FROM 'aaaa林志颖aaa')  #去除前后指定字符,结果为   林志颖

#7.lpad   用指定的字符实现左填充指定长度
SELECT LPAD('林志颖',6,'*')    #用"*"去左填充林志颖,实现它的长度是6个    结果为:  ***林志颖

#8.rpad     用指定的字符实现右填充指定长度
SELECT RPAD('林志颖',6,'*')      #用"*"去右填充林志颖,实现它的长度是6个    结果为:  林志颖***

#9.replace  替换
SELECT REPLACE('林志颖是我偶像','林志颖','林志玲')     #结果是把林志颖替换成了林志玲


```

##### 数学函数

```mysql
#二、数学函数
#1.round 四舍五入
SELECT ROUND(1.76)  #结果是2
SELECT ROUND(1.435,2)   #表示小数点后保留2位,结果是1.44

#2.ceil 向上取整,返回大于等于该参数的最小整数
SELECT CEIL(1.02)   #结果是2
SELECT CEIL(1.00)   #结果是1
SELECT CEIL(-1.02)  #结果是-1

#3.floor 向下取整,返回小于等于该参数的最大整数
SELECT FLOOR(9.93)   #结果是9
SELECT FLOOR(-9.93)  #结果是-10

#4.truncate 截断
SELECT TRUNCATE(1.69999,1)   #小数点后保留1位,结果是1.6

#5.mod 取余
SELECT MOD(10,3)   #结果是1
SELECT MOD(-10,3)  #结果是-1  
SELECT MOD(10,-3)  #结果是1   正负和被除数有关,被除数是正数结果就是正数,反之为负数
```
##### 日期函数

```mysql
#三、日期函数
#1.now 返回当前系统日期+时间
SELECT NOW()

#2.curdate 返回当前系统日期,不包含时间
SELECT CURDATE()

#3.curtime  返回当前系统的时间,不包含日期
SELECT CURTIME()

#4.可以获取指定部分,年、月、日、时、分、秒
SELECT YEAR(NOW())   #结果是2018
SELECT YEAR('2010-1-1')   #结果是2010,YEAR()里面可以传一个时间值
SELECT MONTH(NOW())   #结果是2

#5.str_to_date  将日期格式的字符转换成指定格式的日期
SELECT STR_TO_DATE('2-21 1996','%c-%d %Y')    #结果是1996-02-21

#6.date_format  
SELECT DATE_FORMAT(NOW(),'%Y年%c月%d日')   #2018年2月26日
#案例:查询有奖金的员工名和入职日期(xx月/xx日 xx年)
SELECT last_name,DATE_FORMAT(hiredate,'%m月/%d日 %Y年') FROM employees
```

##### 其他函数

```mysql
#四、其他函数
SELECT VERSION()   #查看mysql 版本号
SELECT DATABASE()  #查看当前是哪个数据库
SELECT USER()      #查看当前是哪个用户
```

##### 流程控制函数

```mysql
#五、流程控制函数
#1.if函数
SELECT IF(3>4,'大于','小于')  #结果是小于,条件成立返回第一个值,否则返回第二个值,有点类似三元表达式的意思
SELECT last_name,commission_pct,IF(commission_pct is NULL,'没奖金','有奖金') FROM employees

#2.case函数  类似于 switch case 的效果
/*
 语法格式1:
			case 要判断的字段或表达式
			when 常量1 then 要显示的值1或语句1
			when 常量2 then 要显示的值2或语句2
			...
			else 要显示的值n或语句n
			end
*/
/*
		案例1:
    查询员工的工资,要求
		部门号=30,显示的工资为1.1倍
		部门号=40,显示的工资为1.2倍
		部门号=50,显示的工资为1.3倍
		其他部门,显示的工资为原工资

*/
SELECT salary 原始工资,department_id,
CASE department_id
WHEN 30 THEN salary * 1.1
WHEN 40 THEN salary * 1.2
WHEN 50 THEN salary * 1.3
ELSE salary
END 新工资 FROM employees;

/*
 语法格式2:
			case 
			when 条件1 then 要显示的值1或语句1
			when 条件2 then 要显示的值2或语句2
			...
			else 要显示的值n或语句n
			end
*/
/*
		案例2:
				查询员工工资情况,
				如果工资 > 20000,显示 A 级别
				如果工资 > 15000,显示 B 级别
				如果工资 > 10000,显示 C 级别
*/

SELECT salary, 
				CASE
				WHEN salary > 20000 THEN 'A'
				WHEN salary > 15000 THEN 'B'
				WHEN salary > 10000 THEN 'C'
				ELSE 'D'
				END 工资级别
FROM employees
```

##### 分组函数

> - 功能：用作统计使用，又称为聚合函数或统计函数或组函数
> - 分类：
>   - sum 求和 、  avg  平均值  、  max  最大值 、  min  最小值 、  count   计算个数
> - 特点：
>   - sum、avg一般用于处理数值型，max、min、count 可以处理任何类型
>   - 以上分组函数都忽略 null 值

```mysql
#分组函数

#1.简单使用
SELECT SUM(salary) 和,AVG(salary) 平均,MAX(salary) 最高,MIN(salary) 最低,COUNT(salary) 数量 from employees

#2.是否忽略 null 值
SELECT COUNT(last_name) FROM employees   #count计算的是非空值的个数
SELECT SUM(commission_pct),AVG(commission_pct) from employees
SELECT MAX(commission_pct),MIN(commission_pct) from employees
#以上函数在做运算时,都忽略了 null 值

#3.分组函数和 DISTINCT 搭配使用
SELECT SUM(DISTINCT salary),SUM(salary) from employees   #先把相同的工资去重后再求和
SELECT COUNT(DISTINCT salary),COUNT(salary) FROM employees  #把工资不一样的个数算出来

#count 函数的详细介绍
SELECT COUNT(*) FROM employees   #使用count(*)来统计行数,count(列名)统计的是这一列不为空的个数
SELECT COUNT(1) FROM employees   #这个也是用来统计行数,这就相当于在你数据表中新增加一列,这一列里面每一行的都是1,然后统计1的个数。当然这里的1也可以换成别的常量值
```
**分组函数案例**

```mysql
#和分组函数一同查询的字段有限制
SELECT AVG(salary),employee_id from employees
#分组函数查询案例
#查询员工表中的最大入职时间和最小入职时间的相差天数
SELECT DATEDIFF(MAX(hiredate),MIN(hiredate)) FROM employees  #结果是8735
```

##### 分组查询

```mysql
#分组查询
/*
语法:
		SELECT 分组函数,列(要求出现在group by的后面)
		FROM 表名
		WHERE 筛选条件
		GROUP BY 分组的列表
		ORDER BY 子句
注意：查询列表比较特殊,要求是分组函数和 group by 后出现的字段
*/
```

```mysql
#简单的分组查询
#案例1：查询每个工种的最高工资
SELECT MAX(salary),job_id FROM employees GROUP BY job_id
#案例2：查询每个位置上的部门个数
SELECT COUNT(*),location_id FROM departments GROUP BY location_id

#添加筛选条件
#案例1:查询邮箱中包含 a 字符的,每个部门的平均工资
SELECT AVG(salary),department_id FROM employees WHERE email LIKE '%a%' GROUP BY department_id
#案例2:查询有奖金的每个领导手下员工的最高工资
SELECT MAX(salary),manager_id FROM employees  WHERE commission_pct is NOT NULL GROUP BY manager_id

#添加分组后的筛选
#案例1:查询哪个部门的员工个数>2
SELECT COUNT(*),department_id FROM employees GROUP BY department_id HAVING COUNT(*) >2
#案例2：查询每个工种有奖金的员工的最高工资>12000的工种编号和最高工资
#①查询每个工种有奖金的员工的最高工资
	SELECT MAX(salary),job_id FROM employees 
	WHERE commission_pct IS NOT NULL 
	GROUP BY job_id
#②根据①的结果继续筛选,最高工资>12000
	SELECT MAX(salary),job_id FROM employees 
	WHERE commission_pct IS NOT NULL 
	GROUP BY job_id
	HAVING MAX(salary) >12000
#案例3：查询领导编号>102的每个领导手下的最低工资>5000的领导编号是哪个
#①查询领导编号>102的每个领导手下的最低工资
    SELECT MIN(salary),manager_id FROM employees
    WHERE manager_id > 102
    GROUP BY manager_id
#②在①的基础上添加筛选条件,最低工资 > 5000
    SELECT MIN(salary),manager_id FROM employees
    WHERE manager_id > 102
    GROUP BY manager_id
    HAVING MIN(salary) > 5000   #为什么要用having,因为我要筛选的字段在原始表中找不到,要筛选的条件是根据分组后来的,在分组后的基础上我才能进行筛选

```
**关于分组查询总结一下**

> - 分组查询中的筛选条件分为两类
>
> |       | 筛选的数据源  |      位置      |  关键字   |
> | :---: | :-----: | :----------: | :----: |
> | 分组前筛选 |   原始表   | group by子句前面 | where  |
> | 分组后筛选 | 分组后的结果集 | group by子句后面 | having |
>
> - group by 子句支持单个字段分组，多个字段分组（多个字段之间用逗号隔开没有顺序要求），表达式分组
> - 也可以添加排序（排序放在整个分组查询的最后）

##### 连接查询

```mysql
#连接查询
/*
含义:又称多表查询,当查询的字段来自多个表时,就会用到连接查询
笛卡尔乘积现象：表1有m行,表2有n行,结果是m*n行
发生原因:没有有效的连接条件
如何避免：添加有效的连接条件
*/
```

##### 连接查询的分类

```mysql
/*
	连接查询分类
			按年代分类:
					sql92标准:仅仅支持内连接
					sql99标准【推荐】：支持内连接 + 外连接（左外和右外） + 交叉连接
			按功能分类：
					内连接：
								等值连接
								非等值连接
								自连接
					外连接
								左外连接
								右外连接
								全外连接
					交叉连接
*/
```

##### 等值连接

```mysql
#一、sql92标准
#1.等值查询
/*
	①多表等值连接的结果为多表的交集部分
	②n表连接，至少需要 n-1 个连接条件
	③多表的顺序没有要求
	④可以搭配我们之前的所有子句使用，比如：排序、分组、筛选
*/
#案例1：查询女神名和对应男神名
SELECT NAME,boyName FROM boys,beauty 
WHERE beauty.boyfriend_id=boys.id;   #说一下等值连接是怎么实现的,它是拿表1的每一行去匹配表2的每一行,根据你的关联条件筛选出需要的数据
#案例2:查询员工名和对应的部门名
SELECT last_name,department_name FROM employees e,departments d WHERE e.department_id = d.department_id
#案例3；查询员工名、工种号、工种名
SELECT last_name,e.job_id,job_title FROM employees e, jobs j WHERE e.job_id = j.job_id

#等值查询加筛选条件
#案例1：查询有奖金的员工名、部门名
SELECT last_name,department_name FROM employees e,departments d WHERE e.department_id = d.department_id AND e.commission_pct IS NOT NULL 
#案例2：查询城市名中第二个字符为o的部门名和城市名
SELECT department_name,city FROM departments d,locations l WHERE d.location_id = l.location_id AND city LIKE '_o%'

#等值连接加分组
#案例1：查询每个城市的部门个数
SELECT COUNT(*),city FROM departments d,locations l WHERE d.location_id = l.location_id  GROUP BY city
#案例2：查询有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资
SELECT department_name,e.manager_id,MIN(salary) FROM employees e,departments d WHERE e.department_id = d.department_id AND e.commission_pct IS NOT NULL GROUP BY department_name

#等值连接加排序
#案例1：查询每个工种的工种名和员工的个数，并且按照员工个数降序
SELECT j.job_title,COUNT(*) FROM employees e,jobs j WHERE e.job_id = j.job_id GROUP BY j.job_title ORDER BY COUNT(*) DESC

#等值连接三表查询
#案例:查询员工名、部门名和所在的城市
SELECT last_name,department_name,city FROM employees e,departments d,locations l WHERE e.department_id = d.department_id AND d.location_id = l.location_id
```

##### SQL99语法等值连接

```mysql
#二、sql199语法(指的就是1999年推出的sql标准)
/*
	语法：
				SELECT 查询列表
				FROM 表1 别名 【连接类型】
				JOIN 表2 别名
				ON 连接条件
				【WHERE 筛选条件】
				【group by 分组】
				【having 筛选条件】
				【order by 排序列表】

分类：
		内连接：inner
		外连接
					左外：left 【outer】
					右外：right【outer】
					全外：full【outer】
		交叉连接：cross

*/

#1).内连接
/*
语法：
		SELECT 查询列表
		FROM 表1 别名
		INNER JOIN 表2 别名
		ON 连接条件

分类：
		等值连接
		非等值连接
		自连接

特点：
①可以添加排序、分组、筛选
②inner可以省略
③筛选条件放在 where 后面，连接条件放在 on 后面，提高分离性，便于阅读
④inner join 连接和 sql92 语法中的等值连接效果是一样的，都是查询多表的交集
*/

#等值连接
#案例1：查询员工名、部门名
SELECT e.last_name,d.department_name FROM employees e INNER JOIN departments d ON e.department_id = d.department_id
#案例2：查询名字中包含e的员工名和工种名(添加筛选)
SELECT e.last_name,j.job_title FROM employees e INNER JOIN jobs j ON e.job_id = j.job_id WHERE e.last_name LIKE '%a%'
#案例3：查询部门个数>3的城市名和部门个数（添加分组+筛选）
SELECT COUNT(*),city FROM departments d INNER JOIN locations l ON d.location_id=l.location_id GROUP BY l.city HAVING COUNT(*) > 3
#案例4：查询哪个部门的员工个数大于3的部门名和员工个数,并按个数降序（添加排序）
SELECT COUNT(*),department_name FROM employees e INNER JOIN departments d ON e.department_id = d.department_id GROUP BY department_name HAVING COUNT(*) > 3 ORDER BY COUNT(*) DESC
#案例5：查询员工名、部门名、工种名,并按照部门名降序（添加三表连接）
SELECT last_name,department_name,job_title FROM employees e LEFT JOIN departments d ON e.department_id = d.department_id LEFT JOIN jobs j ON e.job_id = j.job_id ORDER BY d.department_name DESC
```

##### SQL99语法非等值连接和自连接

```mysql
#非等值连接
#查询员工的工资级别
SELECT e.salary,g.grade_level FROM employees e INNER JOIN job_grades g ON e.salary BETWEEN g.lowest_sal AND g.highest_sal

#查询每个工资级别的个数,并且按工资级别降序
SELECT COUNT(*),e.salary,g.grade_level FROM employees e INNER JOIN job_grades g ON e.salary BETWEEN g.lowest_sal AND g.highest_sal GROUP BY g.grade_level ORDER BY g.grade_level DESC

#自连接
#查询员工的名字、上级的名字
SELECT e.last_name,m.last_name FROM employees e INNER JOIN employees m ON e.manager_id = m.employee_id
```

##### SQL99语法外连接

```mysql
#二、外连接
/*
	应用场景：用于查询一个表中有，另一个表中没有的记录
	特点:
				1、外连接的查询结果为主表中的所有记录
							如果从表中有和它匹配的,则显示匹配的值
							如果从表中没有和它匹配的,则显示 null
							外连接的查询结果=内连接结果 + 主表中有而从表中没有的记录
				2、左外连接, 『left join』 左边的是主表
						右外连接, 『right join』 右边的是主表
				3、左外和右外交换两个表的顺序,可以实现同样的效果
*/
#引入：查询男朋友不在男神表的女神名
SELECT b.name FROM beauty b LEFT JOIN boys bo ON b.boyfriend_id = bo.id WHERE bo.id IS NULL #where 后面的意思就是查boys表主键列为空

#案例1：查询哪个部门没有员工
#左外
SELECT d.*,e.employee_id FROM departments d LEFT JOIN employees e ON e.department_id=d.department_id WHERE e.employee_id IS NULL
#右外
SELECT d.*,e.employee_id FROM employees e RIGHT JOIN departments d ON e.department_id=d.department_id WHERE e.employee_id IS NULL



#使用内连接实际上相当于在查询两个表的交集部分
#说一下外连接是怎么实现的,外连接是区分主从表的,假如我拿表1去匹配表2,那么表1就是主表,表2就是从表,查询的时候它会将主表中的记录都显示
#出来（主表的每一行会根据连接条件去匹配从表的每一行,匹配到就显示出来,匹配不到用null填充）
```


##### 子查询

```mysql
#子查询
/*
	含义：
			出现在其他语句中的 select 语句,称为子查询或内查询
			外部的查询语句,称为主查询或外查询

	分类:
				按子查询出现的位置：
									select 后面:
													仅仅支持标量子查询
									from 后面
													支持表子查询
									where 或 having 后面
													标量子查询、列子查询、行子查询
									exists 后面（相关子查询）
													表子查询
	按结果集的行列数不同：
				标量子查询（结果集只有一行一列）
				列子查询（结果集有一列多行）
				表子查询（结果集一般为多行多列）
*/
```

**where 后面标量子查询的使用**

```mysql
#一、子查询位置在 where 或 having 后面
/*
	1.标量子查询(单行子查询)
	2.列子查询（多行子查询）
	3.行子查询（多列多行）

特点：
		①子查询放在小括号内
		②子查询一般放在条件的右侧
		③标量子查询,一般搭配着单行操作符使用,如：> < >= <= = <>
			列子查询,一般搭配着多行操作符使用,如：in any/some、all
		④子查询的执行优先于主查询,主查询用到了子查询的结果
*/
#案例1：谁的工资比 Abel 高？
#①查询 Abel 的工资
SELECT salary FROM employees
WHERE last_name = 'Abel'
#②查询员工信息,满足 salary 打印①的结果
SELECT * FROM employees WHERE salary > (SELECT salary FROM employees
WHERE last_name = 'Abel')

#案例2：返回 job_id 与 141 号员工相同,salary 比 143 号员工多的员工姓名,job_id 和工资
#①查询141号员工的 job_id
SELECT job_id FROM employees WHERE employee_id = 141
#②查询143号员工的 salary
SELECT salary FROM employees WHERE employee_id = 143
#③查询员工的姓名、job_id、工资,要求 job_id=①并且salary > ②
SELECT last_name,job_id,salary FROM employees WHERE job_id = (SELECT job_id FROM employees WHERE employee_id = 141) AND salary > (SELECT salary FROM employees WHERE employee_id = 143)

#案例3：返回公司工资最少的员工的 last_name,job_id,和 salary
SELECT last_name,job_id,salary FROM employees WHERE salary = (SELECT MIN(salary) FROM employees)

#案例4：查询最低工资大于 50 号部门最低工资的部门id和其最低工资
#①查询50号部门的最低工资
SELECT MIN(salary) FROM employees WHERE department_id=50
#②查询每个部门的最低工资
SELECT MIN(salary),department_id FROM employees GROUP BY department_id
#③在②的基础上筛选,满足min(salary) > ①
SELECT MIN(salary),department_id FROM employees GROUP BY department_id HAVING MIN(salary) > (SELECT MIN(salary) FROM employees WHERE department_id=50) 
```

where 后面多行子查询的使用

```mysql
#列子查询（多行子查询）
#案例1：返回 location_id 是 1400 或 1700 的部门中的所有员工姓名、
#①查询location_id是1400或1700的部门编号
SELECT department_id FROM departments WHERE location_id in(1400,1700)
#②查询员工姓名,要求部门号是①列表中的某一个
SELECT last_name FROM employees WHERE department_id in(SELECT department_id FROM departments WHERE location_id in(1400,1700))

#案例2：返回其他工种中比 job_id 为 'IT_PROG'部门任意工资低的员工的工号、姓名、job_id以及 salary
#①查询job_id为'IT_PROG'部门任意工资
SELECT DISTINCT salary FROM employees WHERE job_id = 'IT_PROG'
#②查询员工号、姓名、job_id以及salary
SELECT employee_id,last_name,job_id,salary FROM employees WHERE salary < ANY(SELECT DISTINCT salary FROM employees WHERE job_id = 'IT_PROG') AND job_id <> 'IT_PROG'

#案例3：返回其他工种中比 job_id 为 'IT_PROG'部门所有工资低的员工的工号、姓名、job_id以及 salary
SELECT employee_id,last_name,job_id,salary FROM employees WHERE salary < ALL(SELECT DISTINCT salary FROM employees WHERE job_id = 'IT_PROG') AND job_id <> 'IT_PROG'
```

**select 后面子查询的使用**

```mysql
#二、select 后面
#案例1:查询每个部门的员工个数
SELECT COUNT(*),department_id FROM employees GROUP BY department_id    #方法一
SELECT d.*,(SELECT COUNT(*) FROM employees e WHERE e.department_id = d.department_id) FROM departments d  #方法二
```

**from 后面的子查询**

```mysql
#三、from 后面
/*
	将子查询结果充当一张表,要求必须起别名
*/
#案例:查询每个部门的平均工资的工资等级
#①查询每个部门的平均工资
SELECT AVG(salary),department_id FROM employees GROUP BY department_id
#②  连接①的结果集和 job_grades 表
SELECT ag_dep.*,j.grade_level
FROM (SELECT AVG(salary) ag,department_id FROM employees GROUP BY department_id) ag_dep INNER JOIN job_grades j ON ag_dep.ag BETWEEN j.lowest_sal AND j.highest_sal 
```

**exists 后面的子查询(相关子查询)**

```mysql
#四、exists 后面(相关子查询)
/*
语法：
exists(完整的查询语句)
结果：
1或0
*/
SELECT EXISTS(SELECT employee_id FROM employees WHERE salary > 2000000)  #结果是0,exists的结果就两个0和1
#案例1：查询有员工的部门名 
SELECT department_name FROM departments d WHERE EXISTS(SELECT * FROM employees e WHERE e.department_id=d.department_id) #方法一
SELECT department_name FROM departments d WHERE d.department_id in (SELECT department_id FROM employees)		#方法二

#案例2：查询没有女朋友的男神信息
SELECT bo.* FROM boys bo WHERE bo.id NOT in (SELECT boyfriend_id FROM beauty)   #方法一,使用in
SELECT bo.* FROM boys bo WHERE NOT EXISTS(SELECT * FROM beauty b WHERE b.boyfriend_id=b.id)  #方法二,使用exists
```

**练习**

```mysql
 #查询和 Zlotkey 相同部门的员工姓名和工资
SELECT last_name,salary FROM employees WHERE department_id = (SELECT department_id FROM employees WHERE last_name = 'Zlotkey')

#查询工资比公司平均工资高的员工的员工号，姓名和工资
SELECT employee_id,last_name,salary FROM employees WHERE salary > (SELECT AVG(salary) FROM employees)

#查询各部门中工资比本部门平均工资高的员工的员工号，姓名和工资
#①先查询各部门的平均工资
SELECT AVG(salary),department_id FROM employees GROUP BY department_id
#②连接①结果集和 employees 表,进行筛选 
SELECT e.employee_id,e.last_name,e.salary FROM employees e INNER JOIN (SELECT AVG(salary) ag,department_id FROM employees GROUP BY department_id) s ON e.department_id = s.department_id

#查询在姓名中包含字母 u 的员工在相同部门的员工的员工号和姓名
#①查询姓名中包含字母 u 的员工的部门
SELECT department_id FROM employees WHERE last_name LIKE '%u%'
#②查询部门号=①中的任意一个的员工号和姓名
SELECT last_name,employee_id FROM employees WHERE department_id in (SELECT department_id FROM employees WHERE last_name LIKE '%u%')

#查询在部门的 location_id 为1700的部门工作的员工的员工号
#①查询location_id为1700的部门
SELECT department_id FROM departments WHERE location_id = 1700
#②查询部门号=①中的任意一个员工号
SELECT employee_id FROM employees WHERE department_id = ANY(SELECT department_id FROM departments WHERE location_id = 1700)

#查询管理者是King的员工姓名和工资
#①查询姓名为King的员工编号
SELECT employee_id FROM employees WHERE last_name='K_ing'
#查询哪个员工的 manager_id = ①
SELECT last_name,salary FROM employees WHERE manager_id in (SELECT employee_id FROM employees WHERE last_name='K_ing')
```

##### 分页查询

```mysql
#分页查询
/*
应用场景：当要显示的数据一页显示不全,需要分页提交 sql 请求
语法：
		SELECT 查询列表
		FROM 表
		LIMIT offset,size

		offset 要显示条目的起始索引
		size 要显示条目的个数
特点：
		①limit 语句放在查询语句的最后
		②公式
				SELECT 查询列表
				FROM 表
				LIMIT (pageNum -1) * pageSize

*/
#案例1：查询前五条员工信息
SELECT * FROM employees LIMIT 0,5    #这个意思就是起始索引是0,然后查5条记录
SELECT * FROM employees LIMIT 5

#案例2：查询第11条到第25条
SELECT * FROM employees LIMIT 10,15

#案例3：查询有奖金的员工信息,并且工资较高的前10名显示出来
SELECT * FROM employees WHERE commission_pct IS NOT NULL ORDER BY salary DESC LIMIT 10
```

**子查询练习**

```mysql
#1、查询工资最低的员工信息
#①查询最低工资
SELECT MIN(salary) FROM employees
#②查询员工信息,要求 salary = ①
SELECT * FROM employees WHERE salary = (SELECT MIN(salary) FROM employees)

#2、查询平均工资最低的部门信息
#①查询各部门的平均工资
SELECT AVG(salary),department_id FROM employees GROUP BY department_id
#②求出最低平均工资的部门编号
SELECT A.d_id FROM (SELECT AVG(salary) ag,department_id d_id FROM employees GROUP BY department_id) A ORDER BY A.ag LIMIT 1
#③查询部门信息
SELECT t.* FROM departments t WHERE t.department_id = (SELECT A.d_id FROM (SELECT AVG(salary) ag,department_id d_id FROM employees GROUP BY department_id) A ORDER BY A.ag LIMIT 1)

#3、查询平均工资最低的部门信息和该部门的平均工资
#①查询各部门的平均工资
SELECT AVG(salary),department_id FROM employees GROUP BY department_id
#②查询出最低平均工资的部门编号和平均工资
SELECT A.d_id a,A.ag b FROM (SELECT AVG(salary) ag,department_id d_id FROM employees GROUP BY department_id) A ORDER BY A.ag LIMIT 1
#③查询部门信息
SELECT * FROM departments INNER JOIN (SELECT A.d_id a,A.ag b FROM (SELECT AVG(salary) ag,department_id d_id FROM employees GROUP BY department_id) A ORDER BY A.ag LIMIT 1) C
ON departments.department_id = C.a

#4、查询平均工资最高的job信息
#①查询最高的job的平均工资
SELECT AVG(salary),job_id FROM employees GROUP BY job_id
ORDER BY AVG(salary) DESC LIMIT 1
#②查询job信息
SELECT * FROM jobs WHERE job_id = (SELECT job_id FROM employees GROUP BY job_id
ORDER BY AVG(salary) DESC LIMIT 1)

#5、查询平均工资高于公司平均工资的部门有哪些
#①查询公司平均工资
SELECT AVG(salary) FROM employees
#②查询各部门的平均工资
SELECT AVG(salary),department_id FROM employees GROUP BY department_id
#③筛选②的结果集,满足平均工资大于①
SELECT AVG(salary),department_id FROM employees GROUP BY department_id
HAVING AVG(salary) > (SELECT AVG(salary) FROM employees)

#6、查询公司中所有 manager 的详细信息
#①查询出所有 manager_id
SELECT DISTINCT manager_id FROM employees
#②查询详细信息,满足employee_id等于①
SELECT * FROM employees WHERE employee_id in (SELECT DISTINCT manager_id FROM employees)

#7、各个部门中，最高工资中最低的那个部门的最低工资是多少
#①查询各个部门的最高工资
SELECT MAX(salary),department_id FROM employees GROUP BY department_id
#②查询①中工资最低的那个部门和工资
SELECT MAX(salary) sa,department_id d_id FROM employees GROUP BY department_id ORDER BY sa LIMIT 1
#③查询最低工资
SELECT MIN(salary) FROM employees WHERE department_id = (SELECT department_id d_id FROM employees GROUP BY department_id ORDER BY MAX(salary) LIMIT 1)

#8、查询平均工资最高的部门的 manager 的详细信息
#①查询各部门的平均工资
SELECT AVG(salary),department_id FROM employees GROUP BY department_id
#②查询平均工资最高的部门
SELECT AVG(salary),department_id FROM employees GROUP BY department_id ORDER BY AVG(salary) DESC LIMIT 1
#③将employees和departments连接查询,筛选条件是①
SELECT last_name,d.department_id,email,salary FROM employees e INNER JOIN departments d ON d.manager_id = e.employee_id
WHERE d.department_id = (SELECT department_id FROM employees GROUP BY department_id ORDER BY AVG(salary) DESC LIMIT 1)
```

##### 联合查询

```mysql
#联合查询
/*
	union 联合查询,将多条查询语句的结果合并成一个结果
	语法：
			查询语句1
			UNION
			查询语句2
			UNION
			查询语句3
	应用场景：要查询的结果来自多张表，且多个表没有直接的连接关系，但查询的信息一致时
	特点：
			1、要求多条查询语句的查询列数是一致的
			2、要求多条查询语句的查询的每一列的类型和顺序最好一致
			3、union 关键字默认去重，如果使用 union all 可以包含重复项
*/
#案例引入：查询部门编号大于90或邮箱包含 a 的员工信息
SELECT * FROM employees WHERE email LIKE '%a%' OR department_id > 90 #方式一
SELECT * FROM employees WHERE email LIKE '%a%' UNION SELECT * FROM employees WHERE department_id > 90
```

##### 插入语句

```mysql
#DML语言
/*
	数据操作语言
	插入：insert
	修改：update
	删除：delete
*/

#一、插入语句
/*
	语法：
	insert into 表名(列名...) values(值1....)
*/
#1、插入的值的类型要与列的类型一致或兼容
INSERT INTO beauty(id,name,sex,borndate,phone,photo,boyfriend_id) VALUES(111,'Jimmy','男','1996/02/21','110',null,12)

#2、不可以为 null 的列必须插入值,那么可以为 null 的列怎么插入值？
#方式一：
INSERT INTO beauty(id,name,sex,borndate,phone,photo,boyfriend_id) VALUES(112,'Jimmy','男','1996/02/21','110',null,12) #把列名写上,值写个null
#方式二：
INSERT INTO beauty(id,name,sex,borndate,phone,boyfriend_id) VALUES(111,'Jimmy','男','1996/02/21','110',12)  #直接不写这个字段

#3、列的顺序是否可以调换
INSERT INTO beauty(name,sex,id,phone) VALUES('aa','女',11122,'111233')  #列的顺序是可以调换的,只要和值对应上就行

#4、可以省略列名,默认是所有列,而且列的顺序和表中的顺序是一致的
INSERT beauty VALUES(90,'bb','男',NULL,'119',NULL,NULL)

#方式二
/*
	语法：
			insert into 表名 set 列名=值,列名=值....
*/
#案例：
INSERT INTO beauty SET id=19,NAME='jimmy',phone='999'

#两种方式 PK
#1、方式一支持插入多行,方式二不支持
INSERT INTO beauty VALUES(95,'bb','男',NULL,'119',NULL,NULL),(96,'bb','男',NULL,'119',NULL,NULL)
#2、方式一支持子查询,方式二不支持
INSERT INTO beauty(id,name,phone) SELECT 26,'aa','3333'     #后面的 select 是子查询
```

##### 修改语句

```mysql
#二、修改语句
/*
	1、修改单表的记录
		语法：
				update 表名 set 列=新值,列=新值 where 筛选条件
	2、修改多表记录
		语法：
				update 表1 别名 inner|left|right| join 表2 别名 on 连接条件 set 列=值,列=值 where 筛选条件
		
*/

#1、修改单表记录
#案例1：修改 beauty 表中 周芷若的电话为 110
UPDATE beauty SET phone = '110' WHERE `name`='周芷若'
#案例2：修改 boys 表中 id 为2号的名称为张飞，魅力值为10
UPDATE boys SET boyName='张飞',usercp=10 WHERE id = 2

#2.修改多表记录
#案例1：修改张无忌的女朋友的手机号为114
UPDATE boys bo INNER JOIN beauty b on bo.id=b.boyfriend_id
SET b.phone = '114' WHERE bo.boyName='张无忌'
```

##### 删除语句

```mysql
#三、删除语句
/*
	方式一、delete 
	语法：
		1、单表的删除
				delete from 表名 where 筛选条件
		2、多表的删除
			sql92语法：
					delete 表1的别名,表2的别名 from 表1 别名,表2 别名
					where 连接条件
					and 筛选条件
			sql99语法：
					delete 表1的别名,表2的别名 from 表1 别名
					inner|left|right join 表2 别名 on 连接条件
					where 筛选条件
				
	方式二、truncate
	语法：
			truncate table 表名
*/

#多表删除
#案例1：删除张无忌女朋友的信息
DELETE b FROM beauty b INNER JOIN boys bo ON b.boyfriend_id=bo.id WHERE bo.boyName='张无忌'

#案例2：删除黄晓明的信息以及他女朋友的信息
DELETE b,bo FROM beauty b INNER JOIN boys bo ON b.boyfriend_id=bo.id WHERE bo.boyName='黄晓明'

#方式二：truncate 语句
TRUNCATE TABLE boys           #truncate后面不能跟 where 语句,truncate 意为清空的意思,就是把表里面的数据都清掉

/*
说下 delete 和 truncate 的区别：
	1、使用 delete 可以加 where 筛选条件,truncate 不能加
	2、truncate删除,效率会高一点
	3、假如要删除的表中有自增长列,如果用 delete 删除后,再插入数据,自增长列的值从断点开始;而 truncate 删除后,再插入数据,自增长列的值从 1 开始
	4、truncate 删除没有返回值,delete 删除有返回值
	5、truncate 删除不能回滚, delete 删除可以回滚
*/
```

##### 库的管理

```mysql
#DDL
/*
	数据定义语言
	库和表的管理

一、库的管理
		创建、修改、删除
二、表的管理
		创建、修改、删除
创建：create
修改：alter
删除：drop
*/

#一、库的管理
#1、库的创建
/*
语法：
	create database [if not exists] 库名
*/

#1、创建库
#案例：创建库 Books 
CREATE DATABASE IF NOT EXISTS books;

#2、库的修改
#更改库的字符集
ALTER DATABASE books CHARACTER SET GBK;

#3、库的删除
DROP DATABASE IF EXISTS books;
```

##### 表的管理

```mysql
#DDL
/*
	数据定义语言
	库和表的管理

一、库的管理
		创建、修改、删除
二、表的管理
		创建、修改、删除
创建：create
修改：alter
删除：drop
*/

#一、库的管理
#1、库的创建
/*
语法：
	create database [if not exists] 库名
*/

#1、创建库
#案例：创建库 Books 
CREATE DATABASE IF NOT EXISTS books;

#2、库的修改
#更改库的字符集
ALTER DATABASE books CHARACTER SET GBK;

#3、库的删除
DROP DATABASE IF EXISTS books;

#二、表的管理
#1.表的创建
/*
	语法：
	create table 表名(
					列名 列的类型【(长度) 约束】
					列名 列的类型【(长度) 约束】
					.....
					列名 列的类型【(长度) 约束】

)
*/
#案例：创建表 Book
CREATE TABLE book(
		id INT,
		bname VARCHAR(20),
		price DOUBLE,
		autherId INT,
		publishDate DATETIME

)

#创建表 auther
CREATE TABLE auther(
				id INT,
				au_name VARCHAR(20),
				nation VARCHAR(10)
)

DESC book;
#2、表的修改
/*
	语法：
	alter table 表名 add|drop|modify|change colume 列名 【列类型 约束】
*/
#①修改列名
ALTER TABLE book CHANGE COLUMN publishDate pubDate DATETIME;
#②修改列的类型或约束
ALTER TABLE book MODIFY COLUMN pubDate TIMESTAMP;
#③添加新列
ALTER TABLE book ADD COLUMN annual DOUBLE;
#④删除列
ALTER TABLE book DROP COLUMN annual;
#⑤修改表名
ALTER TABLE auther RENAME TO book_author

#3.表的删除
DROP TABLE book_author

#4.表的复制

#①、仅仅复制表的结构
CREATE TABLE book_copy LIKE book

#②、复制表的结构及数据
CREATE TABLE book_copy2 SELECT * FROM book

#③、只复制部分数据
CREATE TABLE book_copy3 SELECT id,bname,price FROM book

#④、仅仅复制某些字段
CREATE TABLE book_copy4 SELECT id,bname FROM book WHERE 1=2    #我让它where条件都不满足,它就只会复制表的结构不会复制数据了
```

##### 库和表的管理案例：

```mysql
use test
CREATE TABLE dept1(
	id INT(7),
	NAME VARCHAR(25)
)

#案例：将 departments 中的数据插入新表 dept2 中
CREATE TABLE dept2 
SELECT department_id,department_name FROM myemployees.departments      #这里我是在 test库下面创建一个新表，用 myemployees 库里面表的数据，这里跨库操作要把库名加上,如果你只写"departments",它就会在当前库中找，当前库是找不到的,所以把库名加上

#案例：创建表 emp5
CREATE TABLE emp5(
	id INT(7),
	first_name VARCHAR(25),
	last_name VARCHAR(25),
	dept_id INT(7)
)

#案例：将 last_name 的长度增加到50
ALTER TABLE emp5 MODIFY COLUMN last_name VARCHAR(50);

#案例：根据表 employees 创建 employees2
CREATE TABLE employees2 LIKE myemployees.employees

#案例：删除表 emp5
DROP TABLE IF EXISTS emp5

#案例：将表 employees2 重命名为 emp5
ALTER TABLE employees2 RENAME TO emp5

#案例：在表 emp5 中添加新列 test_column int
ALTER TABLE emp5 ADD COLUMN test_column INT
```

##### MYSQL 中常见的数据类型

```mysql
#常见的数据类型
/*
	数值型：
			整型
			小数：
					定点数
					浮点数
	字符型：
				较短的文本：char varchar 
				较长的文本：text blob
	日期型
*/

#一、整型
/*
	分类：
			tinyint smallint mediumint int bigint
	特点：
			①如果不设置有符号还是无符号,默认是有符号,如果想设置无符号,需要添加 unsigned 关键字
			②如果插入的数据超过了整型的范围,会报 out of range 异常,并且插入临界值
			③如果不设置长度,会有默认的长度。这个长度代表了显示的最大宽度,如果不够会用0在左边填充,但是必须搭配 zerofill 使用
*/

#二、小数
/*
	分类：
		1.浮点型
			  float(M,D)
				double(M,D)
		2.定点型
				dec(M,D)
				decimal(M,D)
	特点：
		①
			M:整数部位 + 小数部位
			D:小数部位
			如果超出范围，插入临界值
		②
			M 和 D 都可以省略
			如果是 decimal 则 M 默认是 10, D 默认是 0
			如果是 float 和 double ，则会根据插入数值的精度来决定精度
		③
			定点型的精确度较高，如果要求插入数值的精度较高如货币运算则考虑使用
*/

#三、字符型
/*
	较短的文本：
	char 
	varchar
	
	较长的文本：
	text
	blob
	
	特点：
						写法  				M的意思 							 									特点							空间耗费   效率
	char 			char(M)				最大的字符数,可以省略,默认是1        固定长度的字符				耗空间     高
	varchar   varchar(M)     最大的字符数 ,不能省略								可变长度的字符				比较节省   低
*/

#四、日期型
/*
datetime 和 timestamp 的区别：
	1.Timestamp 支持的时间范围较小,取值范围：19700101080001~2038年的某个时间;datetime 的取值范围：1000-1-1~9999-12-31
	2.timestamp 和实际时区有关,更能反映实际的日期。而datetime 只能反映插入时的当地时区
	3.timestamp 的属性受 mysql 版本和 sql 语法的影响较大
	
*/
```

##### 常见约束

```mysql
#常见约束

/*
	含义：一种限制，用于限制表中的数据，为了保证表中的数据的准确和可靠性
	分类：六大约束
			NOT NULL ： 非空，用于保证该字段的值不能为空
			DEFAULT ： 默认，用于保证该字段有默认值
			PRIMARY KEY ：主键，用于保证该字段的值具有唯一性，并且非空
			UNIQUE ： 唯一，用于保证该字段的值具有唯一性，可以为空
			CHECK : 检查约束（mysql 中不支持），这个是为了做兼容性的,比如说你在 sqlserver 中添加了检查约束，然后移植到 mysql 上,这个检查约束不会起作用,也不会报错
			FOREIGN KEY：外键，在从表添加外键约束，用于引用主表中某列的值

	添加约束的时机：
			1.创建表时
			2.修改表时

	约束的添加分类：
				列级约束：
								六大约束语法上都支持，但是外键约束没有效果
				表级约束：
								除了非空、默认，其他都支持
*/
```

##### 创建表时添加列级约束

```mysql
CREATE TABLE stuinfo(
	id INT PRIMARY KEY, #主键约束
	stuName VARCHAR(20) NOT NULL, #非空约束
	gender CHAR(1) CHECK(gender='男' or gender='女'),  #检查约束,不起作用
	seat INT UNIQUE,#唯一约束
	age INT DEFAULT 18#默认约束
)

```

##### 创建表是添加表级约束

```mysql
#添加表级约束
/*
	语法：
			在各个字段的最下面
			【constraint 约束名】 约束类型(字段名)
*/
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo(
		id INT,
		stuName VARIANCE(20),
		gender CHAR(1),
		seat INT,
		age INT,
		majorid, INT,
		CONSTRAINT pk PRIMARY KEY(id), #主键
		CONSTRAINT uq UNIQUE(seat), #唯一
)
```

##### 主键和唯一的区别

|          | 能否保证唯一性 | 是否允许为空 | 一个表中能否有多个 | 是否允许组合 |
| -------- | -------------- | ------------ | ------------------ | ------------ |
| 主键约束 | 能             | 否           | 否                 | 是，但不推荐 |
| 唯一约束 | 能             | 是           | 能                 | 是，但不推荐 |

下面来说一下是否允许组合：

```mysql
CREATE TABLE IF NOT EXISTS stuinfo(
	id INT,
	stuName VARCHAR(20),
	gender CHAR(1),
	seat INT,
	CONSTRAINT pk PRIMARY KEY(id,stuName),  #这里其实就是把 id 和 stuName 组合在一起设置成主键
	CONSTRAINT uq UNIQUE(seat)

)
INSERT INTO stuinfo VALUES(1,'aa','m',1),
INSERT INTO stuinfo VALUES(1,'bb','m',2)#这里因为 id 和 stuName 跟上面的不一样,所以尽管 id 一样也是可以插入成功的
INSERT INTO stuinfo VALUES(1,'aa','m',1),
INSERT INTO stuinfo VALUES(1,'aa','m',2)#这种情况,两个都一样就插入不成功了
```

##### 修改表时添加约束

```mysql
#二、修改表时添加约束
/*
	1、添加列级约束
	alter table 表名 modify column 字段名 字段类型 新约束
	2、添加表级约束
	alter table 表名 add 【constraint 约束名】约束类型(字段名)

*/
#1、添加非空约束
ALTER TABLE stuinfo MODIFY COLUMN stuName VARCHAR(20) NOT NULL
#2、添加默认约束
ALTER TABLE stuinfo MODIFY COLUMN age INT DEFAULT 10
#3、添加主键约束
#①列级约束
ALTER TABLE stuinfo MODIFY COLUMN id INT PRIMARY KEY;
#②表级约束
ALTER TABLE stuinfo ADD PRIMARY KEY(id)
#4、添加唯一约束
#①列级约束
ALTER TABLE stuinfo MODIFY COLUMN seat INT UNIQUE
#②表级约束
ALTER TABLE stuinfo ADD UNIQUE(id)
```

##### 标识列

```mysql
#标识列
/*
 又称自增长列
	含义：可以不用手动插入值，系统提供默认的序列值
	特点：
		1、标识列必须和主键搭配吗？答：不一定，但要求是一个 key 例如主键是 primary key ,或者也可以是 unique 等等,是一个 key 就行
		2、一个表可以有几个标识列？ 答：至多一个
		3、标识列的类型只能是数值型（int float double 都可以，但是 varchar 不行）
		4、标识列可以通过 SET auto_increment_increment=3,设置步长（就是你每次自增几个）
*/
#一、创建表时设置标识列
CREATE TABLE stuinfo(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(20)
)
#二、修改表时设置标识列
ALTER TABLE stuinfo MODIFY COLUMN id INT PRIMARY KEY AUTO_INCREMENT
#三、修改表时删除标识列
ALTER TABLE stuinfo MODIFY COLUMN id INT
```



##### MySQL  中的存储引擎

> 1、概念：在 mysql 中的数据用各种不同的技术存储在文件（或内存）中
>
> 2、可以通过 show engines 来查看 mysql 支持的存储引擎
>
> 3、mysql 中用的最多的存储引擎有：innodb、myisam、memory。其中 innodb 支持事务，而 myisam 、memory等不支持事务

##### 事务

```mysql
#TCL
/*
Transaction Control Language  事务控制语言
事务：
一个或一组 sql 语句组成一个执行单元，这个执行单元要么全部执行，要么全部不执行

事务的创建：
隐式事务:事务没有明显的开启和结束的标记。比如 insert update delete 语句
显示事务：事务具有明显的开启和结束的标记。但是前提是：必须先设置自动提交功能为禁用,使用 set autocommit=0
下面说下显示事务的步骤：
步骤1：开启事务
set autocommit=0
start transaction  这个可选,因为你写了上面那行代码就表示事务默认已经开了
步骤2：编写事务中的 sql 语句（select insert update delete）
步骤3：结束事务
commit 提交事务
rollback 回滚事务

*/

#演示事务的使用步骤
#开启事务
SET autocommit=0
START TRANSACTION;

#编写一组事务语句
UPDATE account set balance = 1500 where username='张无忌'
UPDATE account set balance = 1500 where username='赵敏'

#结束事务
COMMIT;
```

##### 视图的介绍

```mysql
#视图
/*
	含义：一种虚拟的表,和普通表一样使用
	mysql5.1 版本出现的新特性，是通过表动态生成的数据

	应用场景：
			1.多个地方用到同样的查询结果
			2.该查询结果使用的 sql 语句较复杂
	满足上面的应用场景,我们就不妨把 sql 语句封装到视图中

*/
#案例：查询姓张的学生名和专业名
SELECT stuname, majorname FROM stuinfo s INNER JOIN major m
ON s.majorId=m.id
WHERE s.stuname LIKE '张%'

CREATE VIEW v1
AS
SELECT stuname, majorname FROM stuinfo s INNER JOIN major m
ON s.majorId=m.id

-- 使用视图的查询语句就可以这么写 
SELECT * FROM v1 WHERE s.stuname LIKE '张%'

#一、创建视图
/*
	语法：
	create view 视图名
	as
	查询语句

*/
#视图的好处：
/*
 1.重用 sql 语句
 2.简化复杂的 sql 操作,不必知道它的查询细节
 3.保护数据,提高安全性

*/
```

##### 修改视图

```mysql
#二、视图的修改
#方式一：
/*
	create or replace view 视图名
	as
	查询语句
*/

CREATE OR REPLACE VIEW myv3
AS
SELECT AVG(salary),job_id
FROM employees
GROUP BY job_id

#方式二
/*
	alter view 视图名
	as
	查询语句
*/

ALTER view myv3
AS
SELECT job_id
FROM employees
```

##### 删除视图

```mysql
/*
语法：drop view 视图名1,视图名2
*/
DROP VIEW myv3
```

##### 视图中数据的更新

```mysql
#1.插入
INSERT INTO myv1 VALUES('张飞','zf@qq.com')   #注意：在往视图里面插入数据的时候，主表也会插入数据
#2.修改
UPDATE myv1 SET last_name='关羽' WHERE last_name='张飞'   #注意：在修改视图中的数据时，也会把原始表中数据给修改掉
#3.删除
DELETE FROM myv1 WHERE last_name='关羽'

#具备以下特点的视图不允许更新
#①包含以下关键字的 sql 语句：分组函数、distinct、group by、having、union 或者 union all
CREATE OR REPLACE VIEW myv2
AS
SELECT MAX(salary) m,department_id
FROM employees
GROUP BY department_id


#现在我想要去更新上面视图的数据
UPDATE myv2 SET m = 9000 WHERE department_id=10   #按道理说 sql 语句写的没问题,应该是可以更新成功的,但是视图 sql 语句中出现了分组函数和 group by 等关键字,所以更新是不成功的

#②常量视图
CREATE OR REPLACE VIEW myv2
AS
SELECT 'jone' NAME

#现在我想要去更新上面视图的数据
UPDATE myv2 SET NAME = 'lili'              #发现是更新不成功的

#③select 中包含子查询
CREATE OR REPLACE VIEW myv2
AS
SELECT(SELECT MAX(salary) FROM employees) max_salary

#现在我想要去更新上面视图的数据
UPDATE myv2 SET max_salary = 2000     #发现是更新不成功的

#④连接查询 join
CREATE OR REPLACE VIEW myv2
AS
SELECT last_name,department_name
FROM employees e
LEFT JOIN departments d
ON e.department_id = d.department_id

#现在我想要去更新上面视图的数据
UPDATE myv2 SET last_name = '张飞' WHERE last_name='Whalen'   #发现是可以更新成功的,但是你执行 insert 语句是不成功的，所以我就统一称为不能更新了

#⑤ from 一个不能更新的视图
#⑥ where 子句的子查询引用了 from 子句中的表

```

##### delete 和 truncate 在事务使用时的区别

```mysql
#delete
SET autocommit=0;
START TRANSACTION
DELETE FROM account;   #delete 支持回滚
ROLLBACK

#truncate 
SET autocommit=0;
START TRANSACTION;
TRUNCATE TABLE account;     #truncate 执行完之后,就会把执行结果写入到磁盘文件,不支持回滚
ROLLBACK;
```

##### 变量

```mysql
#变量
/*
系统变量：
			全局变量
			会话变量
自定义变量：
			用户变量
			局部变量
*/

#一、系统变量
/*
	说明：变量由系统提供，不是用户定义，属于服务器层面
	系统变量如果是全局级别需要加 GLOBAL , 如果是会话级别，则需要加 SESSION ，如果不写，则默认是 SESSION
*/

#1、查看所有的系统变量
SHOW VARIABLES         #查看全局变量使用 SHOW GLOBAL_VARIABLES    查看会话变量使用 SHOW SESSION_VARIABLES
#2、查看满足条件的部分系统变量
SHOW VARIABLES LIKE '%char%'
#3、查看指定的某个系统变量的值
SELECT @@系统变量名;      #你如果查看全局变量的值就是 SELECT @@global.系统变量名    如果查看会话变量就是  SELECT @@session.系统变量名
#4、为某个系统变量赋值
#方式一：
SET GLOBAL | SESSION 系统变量名 = 值
#方式二：
SET @@global | session.系统变量名 = 值
```

##### 全局变量

```mysql
#1、全局变量
/**
	作用域：服务器每次启动将为所有的全局变量赋初始值，针对所有的会话（连接）有效,就是说你新建一个连接这些全局变量也是有效的，但是不能
					跨重启,比如说你把一个全局变量改了,当前连接或者你新建一个连接是有效的,但是服务器重启之后失效
*/
#①查看所有的全局变量
SHOW GLOBAL VARIABLES
#②查看部分全局变量
SHOW GLOBAL VARIABLES LIKE '%char%'
#③查看指定的全局变量的值
SELECT @@global.autocommit       #就是使用 select 加两个艾特符号然后后面跟你要查的全局变量名
SELECT @@tx_isolation
#④为某个指定的全局变量赋值
SET @@global.autocommit=0
```

##### 会话变量

```mysql
#2、会话变量
/*
	作用域：仅仅针对当前会话（连接）有效
*/
#①查看所有的会话变量
SHOW SESSION VARIABLES    #这个 session 可以省略,如果不写默认就是查会话变量
#②查看部分会话变量
SHOW SESSION VARIABLES LIKE '%char%'
#③查看指定的某个会话变量
SELECT @@tx_isolation
#④为某个会话变量赋值
#方式一：
SET @@tx_isolation='read-uncommitted'
#方式二：
SET SESSION tx_isolation = 'read-uncommitted'
```

##### 自定义变量-用户变量

```mysql
#二、自定义变量
/*
	说明：变量是用户自定义的，不是由系统提供的
	使用步骤：
			声明
			赋值
			使用（查看、比较、运算）
*/
#1、用户变量
/*
	作用域：针对当前会话（连接）有效，跟会话变量的作用域一样
*/

#①声明并初始化
SET @用户变量名 = 值      #注意：这里要求用户变量名前面必须要加上一个 @ 符号
SET @用户变量名 := 值
SELECT @用户变量名 := 值   
#注意：上面三种方式都是可以用来声明并初始化的，你如果用 SET 来声明并初始化,赋值操作符可以使用 '=' 或 ':=' 但如果你用 SELECT 来初始化赋值操作符只能使用 ':='

#②赋值（更新用户变量的值）
#方式一：通过 SET 或 SELECT    也就是说这种方式既可以用于在声明的时候初始化,也可以用于来更新值
SET @用户变量名 = 值
SET @用户变量名 := 值
SELECT @用户变量名 := 值 
#方式二：通过 SELECT INTO
#SELECT 字段 INTO @变量名 FROM 表名      这里就要求你查询出来的字段只能是一个值,然后这个值才能赋值给这个变量

#③使用（查看用户变量的值）
SELECT @用户变量名


#案例
SET @name = 'John'  #这里我声明一个用户变量,并把 John 赋值给这个变量
SET @name = 100  #上面我赋值的是一个字符串给声明的变量,这里我更想变量的值,直接赋一个 int 型也是可以的,不会报错

SET @count = 1    #声明一个用户变量它的值是 1
SELECT COUNT(*) INTO @count FROM employees      #这里意思就是把 employees 表中总数查出来然后赋值给 @count 这个变量

SELECT @count   #查看用户变量的值
```

##### 自定义变量-局部变量

```mysql
#2、局部变量
/*
	作用域：仅仅在定义它的 begin end 中有效
	应用在 begin end 中的第一句话
*/
#①声明
DECLARE 变量名 类型
DECLARE 变量名 类型 DEFAULT 值    #声明的同时初始化,这里要求你写的默认值和你声明的类型是一致的

#②赋值
#方式一：通过 SET 或 SELECT
SET 局部变量名 = 值
SET 局部变量名 := 值
SELECT @局部变量名 := 值 
#方式二：通过 SELECT INTO
#SELECT 字段 INTO 局部变量名 FROM 表名      这里就要求你查询出来的字段只能是一个值,然后这个值才能赋值给这个变量

#③使用
SELECT 局部变量名
```

##### 用户变量和局部变量比较

|          | 作用域       | 定义和使用的位置                  | 语法                            |
| -------- | ------------ | --------------------------------- | ------------------------------- |
| 用户变量 | 当前会话     | 会话中的任何地方                  | 必须加 @ 符号，不用限定类型     |
| 局部变量 | BEGIN END 中 | 只能在 BEGIN END 中，且为第一句话 | 一般不用加 @ 符号，需要限定类型 |

##### 存储过程

```go
#存储过程和函数
/*
	存储过程和函数：类似于 java 中的方法
	好处：
	1、提高代码的重用性
	2、简化操作
*/

#存储过程
/*
	含义：一组预先编译好的 SQL 语句的集合,可以理解成批处理语句
	1、提高代码的重用性
	2、简化操作
	3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率
*/

#一、创建语法
CREATE PROCEDURE 存储过程名(参数列表)
BEGIN
		存储过程体（一组合法的 SQL 语句）

END

#注意：
/*
1、参数列表包含三部分
参数模式 参数名 参数类型
举例：
IN stuname VARCHAR(20)

参数模式：
IN：该参数可以作为输入，也就是说该参数需要调用方传入值
OUT：该参数可以作为输出，也就是该参数可以作为返回值
INOUT：该参数既可以作为输入也可以作为输出，也就是说该参数既需要传入值，又可以返回值

2、如果存储过程体仅仅只有一句话，BEGIN END 可以省略
	存储过程体中的每条 SQL 语句的结尾要求必须加分号
	存储过程的结尾可以使用 DELIMITER 重写设置
语法：
DELIMITER 结束标记
*/

#二、调用语法
CALL 存储过程名(实参列表)
```

##### 空参存储过程

```mysql
#1.空参列表
#案例：插入到 admin 表中五条记录
DELIMITER $
CREATE PROCEDURE myp1()
BEGIN
				INSERT INTO admin(username,'password') 
				VALUES('aa','123'),('bb','234'),('cc','456'),('dd','789'),('ee','890')
END $

#调用
CALL myp1()$
```

##### 带 IN 模式的存储过程

```mysql
#2.创建带 in 模式参数的存储过程
#案例1 ：创建存储过程实现 根据女神名，查询对应的男神信息
DELIMITER $
CREATE PROCEDURE myp2(IN beautyName VARCHAR(20))
BEGIN
				SELECT bo.* FROM boys bo
				RIGHT JOIN beauty b ON b.boyfriend_id = bo.id
				WHERE b.`name` = beautyName
END $

#调用
CALL myp2('范冰冰')$

#案例2：创建存储过程实现用户是否登录成功
DELIMITER $
CREATE PROCEDURE myp3(IN username VARCHAR(20), IN password VARCHAR(20))
BEGIN
			DECLARE result INT DEFAULT 0  #声明并初始化
			SELECT COUNT(*) INTO result   #赋值
			FROM admin WHERE admin.username = username AND admin.`password` = password;
			SELECT `IF`(result > 0,'成功','失败')
END $

#调用
CALL myp3('张飞','8888')$
```

##### 带 OUT 模式的存储过程

```mysql
#3.创建带 out 模式的存储过程
#案例：根据女神名，返回对应的男神名
CREATE PROCEDURE myp4(IN beautyName VARCHAR(20) OUT boyName VARCHAR(20))
BEGIN
				SELECT bo.boyName	INTO boyName																		#需要注意的是带 out 模式的存储过程，存储过程体里面也不用加 return 语句，因为上面 boyName 你如果在存储过程体里面给它赋上值以后,你调用的时候会自动返回给你
				FROM boys bo
				INNER JOIN beauty b WHERE b.`name` = beautyName

END $

#调用
SET @bname   #定义一个用户变量,这个用户变量用来接收存储过程返回的值
CALL myp4('范冰冰',@bname)$
SELECT @bname$

#案例2：根据女神名，返回对应的男神名和男神魅力值
DELIMITER $
CREATE PROCEDURE myp5(IN beautyName VARCHAR(20), OUT boyName VARCHAR(20), OUT userCP VARCHAR(20))
BEGIN
				SELECT bo.boyName,bo.userCP INTO boyName,userCP 
				FROM boys bo
				INNER JOIN beauty b ON b.boyfriend_id = bo.id
				WHERE b.name = beautyName
END $

#调用
CALL myp5('范冰冰', @bname,@usercp)
SELECT @bname,@usercp$
```

##### INOUT 模式参数的存储过程

```mysql
#4.创建带 inout 模式参数的存储过程
#案例1：传入 a 和 b 两个值，最终 a 和 b 都翻倍并返回
CREATE PROCEDURE myp7(INOUT a int, INOUT b INT )
BEGIN
			SET a = a * 2
			SET b = b * 2
END $
#调用
SET @m = 10$
SET @n = 20$
CALL myp7(@m,@n)$
SELECT @m,@n$
```

##### 删除和查看存储过程

```mysql
#二、删除存储过程
#语法：drop procedure 存储过程名
DROP PROCEDURE p1;

#三、查看存储过程信息
SHOW CREATE PROCEDURE myp2;
```

##### 函数的创建和调用语法

```mysql
#函数
/*
	含义：一组预先编译好的 SQL 语句的集合，理解成批处理语句

函数和存储过程的区别：
存储过程：可以有 0 个返回，也可以有多个返回，适合做批量插入、批量更新
函数：有且仅有 1 个返回，适合做处理数据后返回一个结果
*/

#一、创建语法
CREATE FUNCTION 函数名(参数列表) RETURNS 返回类型
BEGIN
		函数体
END
/*
注意：
1.参数列表包括两部分：
参数名 和 参数类型

2.函数体：肯定会有 return 语句，如果没有会报错
如果 return 语句没有放在函数体的最后也不会报错，但是不建议

3.函数体中仅有一句话，则可以省略 begin end

4.使用 delimiter 语句作为设置结束的标记
*/

#二、调用语法
SELECT 函数名(参数列表)
```

**案例：**

```mysql
#1.无参有返回值
#案例：返回公司的员工个数
delimiter $
CREATE FUNCTION myf1() RETURNS INT
BEGIN
	DECLARE c INT DEFAULT 0    #定义一个变量,这个变量主要用来存放返回值的
	SELECT COUNT(*) INTO c     #给变量赋值 
	FROM employees

END $

SELECT myf1()$

#2.有参有返回
#案例1：根据员工名，返回它的工资
CREATE FUNCTION myf2(empName VARCHAR(20)) RETURNS DOUBLE
BEGIN
			SET @sal = 0;   #定义一个用户变量
			SELECT salary INTO @sal    #给用户变量赋值
			FROM employees
			WHERE last_name = empName;
END $

SELECT myf2('k_ing')$

#案例2：根据部门名，返回该部门的平均工资
CREATE FUNCTION myf3(deptName VARCHAR(20)) RETURNS DOUBLE
BEGIN
			DECLARE sal DOUBLE;
			SELECT AVG(salary)
			FROM employees e
			INNER JOIN departments d ON e.departmentid = d.departmentid
			WHERE d.departmen_name = deptName;
			RETURN sal;
END $
SELECT myf3('销售部')$
```

##### 查看函数和删除函数

```mysql
#三、查看函数
SHOW CREATE FUNCTION myf3;

#四、删除函数
DROP FUNCTION myf3;
```
















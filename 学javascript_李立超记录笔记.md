# JavaScript学习笔记

`什么是JavaScript?它跟Java有什么关系？`

答:JavaScript是一门编程语言，它跟Java之间的关系不大。就像“雷锋”和"雷锋塔"，“周杰”和"周杰伦"他们之间关系并不大。

#### 起源

> - *JavaScript*诞生于1995年，它的出现主要用于处理网页中的前端验证。
> - 所谓的前端认证，就是指检查用户输入的内容是否符合一定的规则。
> - 但是到今天*JavaScript*已经不仅仅用来处理前端验证了，网页中所有的动态效果都离不开*JavaScript*

#### 简史

> - *JavaScript* 是由网景公司发明，起初命名为*LiveScript*，后来由于SUN公司的介入更名为了*JavaScript*。
> - 1996 年微软在其最新的IE3浏览器中引入了自己对*JavaScript*的实现*JScript*。
> - 于是市面上就存在了两个版本的JavaScript，一个网景公司的*JavaScript*和微软的JScript。
> - 为了确保不同的浏览器上运行的JavaScript标准一致，所以几个公司共同定制了*JS*的标准命名为*ECMAScript*。

#### 实现

> - ECMAScript 是一个标准，而这个标准需要由各个厂商去实现。例如：就比如说我现在规定“世界上所有的杯子高度都是20厘米”那么我说的这个就是一个标准。至于如何实现肯定由各个杯子的制造商来决定。
> - 不同浏览器厂商对该标准会有不同的实现。
> - 我们已经知道*ECMAScript* 是 *JavaScript* 标准，所以一般情况下这两个词我们认为是一个意思。
> - 概括的说就是 *ECMAScript* 是标准，*JavaScript* 是实现。
> - 一个完整的*JavaScript* 实现应该由以下三个部分构成：
>   1. *ECMAScript* 
>   2. *DOM*
>   3. *BOM*

------



***HelloWorld*程序**

```javascript
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<script type="text/javascript">
			alert("helloWorld");   //弹框
			//向body中输出一个内容
			document.write("hello,你真帅");
			//向控制台输出一个内容
			console.log("你猜我出现在哪里");
		</script>
	</head>
	<body>
	</body>
</html>
```
***JS*代码编写位置**

> - 方式一：可以把*js*写在标签的属性中,但是这样结构和行为耦合,不方便维护,不推荐使用
> - 方式二：就是将*js*代码编写到*script*标签中
> - 方式三：可以将*js*代码编写到外部*js*文件中,然后通过*script*标签引入。写到外部文件中可以在不同的页面中同时引用,也可以利用到浏览器的缓存机制。*这是我们推荐使用的方式*。

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<!--
			方式三:可以将js代码编写到外部js文件中,然后通过script标签引入
		-->
		<script type="text/javascript" src="js/script.js">
			alert("无效代码")     //这个script已经用于引用外部文件了,再写内部代码,内部代码也不会起作用
		</script>
		<script type="text/javascript">
			//方式二:就是将js代码编写到script标签中
			alert("hello");
		</script>
	</head>
	<body>
		<!--可以将js代码编写到标签的onclick属性中,当我们点击按钮时,js代码才会执行-->
		<button onclick="alert('弹出咯')"></button>
		<!--可以将js代码写在超链接的href属性中,这样当点击超链接时,会执行js代码-->
		<a href="javascript:alert('点我吧')">点我一下</a>   <!--格式就是要在js代码前面写上javascript: -->
		<a href="javascript:;">超链接点上去后没有反应</a>   <!--这种写法我们也会经常遇到 -->
		<!--
			方式一:
			虽然可以把js写在标签的属性中,但是这样结构和行为耦合,不方便维护,不推荐使用
		-->
	</body>
</html>
```

**注意**：*script*标签一旦用于引用外部文件了,就不能在里面编写代码了,即使编写了浏览器也会忽略,如果需要可以再创建一个*script*标签用于编写内部代码



***JS*基本语法**

> 1. *JS*中严格区分大小写 。
> 2. *JS*中每一条语句都以分号结尾。如果你不写分号,浏览器会自动帮你添加,但是会消耗一些系统资源,而且有时候浏览器会加错分号,所以在开发中分号必须写。
> 3. JS中会忽略多个空格和换行,所以我们可以利用空格和换行对代码进行格式化。



**字面量和变量**

> 字面量：都是一些不可改变的值。比如 1 2 3 4 5 字面量都是可以直接使用,但是我们一般都不会直接使用字面量
>
> 变量：变量可以用来保存字面量,而且变量的值是可以任意改变的,变量更加方便我们使用,所以在开发中都是通过变量去保存。一个字面量,而很少直接使用字面量。



**标识符**

> - 在*JS*中所有的可以由我们自主命名的都可以称为是标识符。如:变量名、函数名、属性名都属于标识符
> - 命名一个标识符时需要遵守如下的规则：
>   1. 标识符中可以含有字母、数字、下划线、美元符
>   2. 标识符不能以数字开头
>   3. 标识符不能是*JS*中的关键字或保留字
>   4. 标识符一般采用驼峰命令法
> - *JS*底层保存标识符时实际上是采用的*Unicode*编码。所以理论上讲,所有的*utf-8*中含有的内容都可以作为标识符。（意思就是我变量名用中文也可以）

### 数据类型

> 在JS中一共有六种数据类型
>
> - String       字符串
> - Number    数值
> - Boolean     布尔值
> - Null             空值
> - Undefined    未定义
> - Object           对象
>
> 其中String     Number    Boolean   Null    Undefined  属于基本数据类型,而Object 属于引用数据类型



**字符串(*String*)**

> - 在*JS*中字符串需要使用引号引起来
> - 使用双引号或单引号都可以

```javascript
		<script type="text/javascript">
			/**
			 * 在字符串中我们可以使用\作为转义字符,当表示一些特殊符号时可以使用\进行转义
			 */
			var str = "我说:\"今天天气很不错\"";
			console.log(str);
		</script>
```

**数值类型(*Number*)**

> - 在*JS*中所有的数值都是Number类型，包括整数和浮点数
> - JS中可以表示的数字的最大值     
>   - Number.MAX_VALUE          值为1.7976931348623157e+308
> - 如果使用Number表示的数字超过了最大值，则会返回一个Infinity表示正无穷
>   - 使用typeof 检查Infinity也会返回Number
>   - Infinity 表示正无穷，-Infinity表示负无穷
> - NaN 是一个特殊的数字,表示Not A Number
>   - 使用typeof检查一个 NaN 也会返回 number
> - JS中大于0的最小值
>   - Number.MIN_VALUE          值为5e-324

```javascript
		<script type="text/javascript">
			var a = 123;
			var b = "123";
			
			/**
			 * 可以使用一个运算符 typeof,来检查一个变量的类型
			 * 语法:typeof 变量
			 */
			console.log(a);
			console.log(b);
			console.log(typeof b);    //打印string   
			console.log(Number.MAX_VALUE);    //打印1.7976931348623157e+308
			var c = Infinity;    //Infinity本身就是一个字面量
			console.log(c);     //打印Infinity
			console.log(typeof Infinity);   //打印一个number
			var d = "ab" * "bc";
			console.log(d);    //打印NaN
			console.log(typeof d);    //打印number
			console.log(Number.MIN_VALUE)   //打印5e-324
			/**
			 * 在JS中整数的运算基本可以保证精确
			 */
			var e = 12345 + 23432;
			console.log(d);
			/**
			 * 在JS中对浮点数进行运算,可能得到一个不精确的结果
			 * 所以千万不要使用JS进行对精确度要求比较高的运算
			 */
			var f = 0.1 + 0.2;    //打印0.30000000000000004  结果不精确
			console.log(f);
		</script>
```

**布尔值(boolean)**

> - 布尔值只有两个，主要用来做逻辑判断
>   - true      表示真
>   - false     表示假

```javascript
		<script type="text/javascript">
			var bool = true;
			console.log(bool);
			console.log(typeof bool);   //打印boolean 
		</script>
```

**Null 和 Undefined**

> - null 这个值专门用来表示一个为空的对象
> - 使用typeof 检查一个null 值时，会返回一个Object
> - 当声明一个变量，但并不给变量赋值时，它的值就是 undefined
> - 使用typeof 检查一个 undefined 时也会返回 undefined

```javascript
		<script type="text/javascript">
			var a = null;
			console.log(a);   //打印null
			console.log(typeof a);   //打印Object
			var b;
			console.log(b);    //打印出undefined,表示这个变量声明了但是没有赋值
		</script>
```

**强制类型转换**

> - 指将一个数据类型强制转换为其他数据类型
> - 类型转换主要指，将其他的数据类型转换为 String   Number    Boolean

**其他数据类型转换为 String**

> - 方式一
>   - 调用被转换数据类型的toString() 方法，它会将转换结果返回
>   - 但是注意，null 和 undefined 这两个值没有 toString() 方法，调用就会报错
> - 方式二
>   - 调用String() 函数，并将被转换的数据作为参数传递给函数
>   - 使用String()函数做强制类型转换时，对于Number 和 Boolean 实际上就是调用他们的toString()方法，但是对于null 和 undefined 就不会调用toString()方法，它会将 null 直接转换为 “null” 将 undefined 直接转换为 “undefined”

**其他数据类型转换为 Number**

> - 方式一
>   - 使用Number()函数
>     - 字符串  ---->   数字
>       1. 如果是纯数字的字符串，则直接将其转换为数字
>       2. 如果字符串中有非数字的内容，则转换为NaN
>       3. 如果字符串是一个空串或是是一个全是空格的字符串，则转换为0
>     - 布尔值    ----->  数字
>       1. true  转成 1
>       2. false  转成  0
>     - null   ------>   数字
>       1. 结果就是0
>     - undefined  ------>   数字
>       1.  结果就是NaN
> - 方式二
>   - 这种方式专门用来对付字符串
>     - parseInt()     把一个字符串转换成一个整数
>     - parseFloat() 把一个字符串转换为一个浮点数

```javascript
	<script type="text/javascript">
		var a = "123";
		console.log(typeof a);
		console.log(a);
		//调用Number()函数来将a转换为Number类型
		a = Number(a);
		console.log(typeof a);   //打印string
		var b = "";
		console.log(Number(b));   //空字符串转换成0
		var c = true;
		console.log(Number(c))     //true转成1,false转成0
		var d = undefined;
		console.log(Number(d));    //undefined 转成 NaN
		/**
		 * parseInt()可以将一个字符串中的有效整数内容取出来,然后转换为Number
		 * 它是从左往右解析,遇到非数字就停止解析
		 */
		var e = "123abc";
		console.log(parseInt(e));   //打印123
		e = "12s3abc";
		console.log(parseInt(e));   //打印12
		/**
		 * parseFloat()作用和parseInt()类似,不同的是它可以获得有效的小数位
		 */
		var f = "123.11dd";
		console.log(parseFloat(f));    //打印的是123.11
		
		/**
		 * 注:
		 * 如果对非String 使用parseInt()或parseFloat()
		 * 它会先将其转换为String,然后再操作
		 */
		var h = true;
		console.log(parseInt(h));   //打印的是NaN   相当与console.log(parseInt("true")) 这个打印的肯定是NaN  
	</script>
```

**其他数据类型转换为 Boolean**

> - 使用Boolean函数
>   - 数字   ------>   布尔        除了 0 和 NaN，其余的都是 true
>   - 字符串   ------->  布尔     除了空串，其余的都是true
>   - null 和 undefined  ------->    结果都为false
>   - 对象也会转换为true

```javascript
		<script type="text/javascript">
			var a = 123;   //打印true
			a = -123;        //打印true
			a = Infinity;   //打印true
			a = "abc";    //打印true
			a = 0;      //打印false
			a = null;    //打印false
			a = undefined;  //打印false
			a = NaN;       //打印false
			a = "";    //打印false
			a = " ";   //打印true
			console.log(Boolean(a));    
		</script>
```
**运算符**

> - 运算符也叫操作符，通过运算符可以对一个或多个值进行运算，并获取运算结果。
>
> 比如typeof就是运算符，可以来获得一个值的类型，他会将该值的类型以字符串的形式返回

**算术运算符**

> - 当对非Number类型的值进行运算时，会将这些值转换为Number然后进行运算
>
> - 任何值和NaN做运算都得NaN
>
> - 任何值和字符串做加法操作，都会先转换为字符串，然后再和i字符串做拼接操作
>
>   `我们可以利用这一特点，来将一个任意的数据类型转换为String ，只需要为任意的数据类型后面加上一个空串即可将其转换为String ，这是一种隐式的类型转换，由浏览器自动完成，实际上也是调用的String()函数`
>
> - 任何值做减法"-"  乘法 "*" 除法 "/" 运算时都会自动转换为Number
>
>   `我们可以利用这一特点做隐式类型转换，可以通过为一个值减0乘1除1将其转换为Number  原理和Number()函数一样，只是使用起来会更加简单（了解下就行了）`

```javascript
		<script type="text/javascript">
			var num = 123;
			var result = typeof num;
			console.log(result);    //打印number,这里是使用typeof来获取一个值的类型,会以字符串的形式返回这个类型
			console.log(typeof result);   //打印String
			//算术运算符
			var a = 234;
			a = a + 1;
			console.log(a);   //打印235
			a = true + 1;   
			console.log(a);   //打印2    true转换为数字是1
			a = false + 1;
			console.log(a);   //打印1    fasle转换为数字是0
			a = null + 1;
			console.log(a);    //打印1   null 转换为数字是0
			a = 1 + NaN;
			console.log(a);    //打印NaN    任何值和NaN做运算都得NaN
			var c = 123;
			c = c + "";       //本质上和  c = String(c)一样
			console.log(typeof c);    //打印String
			//减法
			var d = 100 - "1";   //这里会先把字符串1转换为number 1然后再做运算
			console.log(d);    //打印99
			//乘法
			var e = 2 * "3";     //这里会先把字符串3转换为number 3然后再做运算
			console.log(e);     //打印6
			e = 2 * undefined;    //这里会先把undefined转换为Number类型,结果是NaN然后再做运算
			console.log(e);      //打印NaN   因为我们说任何数和NaN做运算,结果都是NaN
			//除法
			var f = 4 / 2;
			console.log(f);    //打印2
 		</script>
```

**一元运算符**

> - 一元运算符，只需要一个操作数
>
>   - 正号（"+"）
>
>     正号不会对数字产生任何影响
>
>   - 负号（"-"）
>
>     负号可以对数字进行负号的取反
>
> - 对于非Number类型的值，它会先转换为Number，然后在运算
>
>   可以对一个其他的数据类型使用+，来将其转换为number，它的原理和Number()函数一样

```javascript
		<script type="text/javascript">
			var a = "12";
			a = +a;
			console.log(a);   //打印12
			console.log(typeof a); //打印number
			var result = 1 + + "2" + 3;   //这里+"2"就把字符串2转换为了number 2
			console.log(result);  //打印结果是6
		</script>
```
**逻辑运算符**

> - JS中为我们提供了三种逻辑运算符
>   - ！   非
>     - 可以用来对一个值进行非运算
>     - 所谓非运算就是指对一个布尔值进行取反操作，true 变 false，false 变 true
>     - 如果对一个值进行两次取反，它不会变化
>     - 如果对非布尔值进行运算，则会先将其转换为布尔值，然后再取反
>     - 所以我们可以利用该特点，来将一个其他数据类型转换为布尔值，我们可以为一个任意数据类型取两次反，来将其转换为布尔值。原理和Bollean()函数类似
>   - &&  与
>     - && 可以对符号两侧的值进行与运算并返回结果
>     - 运算规则：
>       - 两个值中只要有一个值为false 就返回 false ，只有两个值都为true时，才会返回true
>       - JS 中的 “与” 属于短路 “与”，如果第一个值为false 第二个值就不会再去看了
>   - ||    或
>     - || 可以对符号两侧的值进行或运算并返回结果
>     - 运算规则：
>       - 两个值中只要有一个true,就返回true，如果两个值都为false 就返回false
>       - JS中的 "或" 属于短路 "或"，如果第一个值为true 就不会去看第二个值了

```javascript
		<script type="text/javascript">
			var a = true;
			a = !a;
			console.log(a);    //打印false
			var b = 10;     
			b = !!b     //如果是一个非布尔值的元素就先把它转换为布尔值然后再做运算
			console.log(b);    //打印true
			var c = "hello";
			c = !!c;                  //把其他类型元素转换为Boolean(直接取两次反原值不变)
			console.log(typeof c);
			
			//只要有一个false,结果就是false
			var d = true && false;
			console.log(d);
			var e = false || true;   //只要有一个为true结果就是true
			console.log(e);
		</script>
```
**非布尔值的与或运算**

> - 对于非布尔值进行运算时，会先将其转换为布尔值，然后再运算，并且返回原值
> - 与运算（短路）
>   - 如果第一个值为true，则必然返回第二个值
>   - 如果第一个值为 false，则直接返回第一个值
> - 或运算（短路）
>   - 如果第一个值为true,则直接返回第一个值
>   - 如果第一个值为false，则直接返回第二个值

```javascript
		<script type="text/javascript">
			
			//相当于true && true 如果两个值都为true,则返回后面的
			var result = 1 && 2;     //会把这两个值都转换为boolean类型然后再做运算
			console.log(result); //打印2
			result = 2 && 1;
			console.log(result);   //打印1
			
			//相当于false && true 如果有一个值为false,则返回false对应的值
			result = 0 && 2;
			console.log(result);  //打印0
			result = 2 && 0;
			console.log(result);  //打印0
			
			//相当于false && false 如果两个值都为false,则返回靠前的
			result = NaN && 0;
			console.log(result);   //打印NaN
			result = 0 && NaN;
			console.log(result);   //打印0
			
			//相当于 true || true
			result = 2 || 1;
			console.log(result);   //打印2
			
			result = "" || "hello";   //相当于false || true
			console.log(result);      //打印"hello"
		</script>
```
**关系运算符**

> - 非数值的情况
>   - 对于非数值进行比较时，会将其转换为数字然后在比较
>   - 如果符号两侧的值都是字符串时，不会将其转换为数字进行比较，而会分别比较字符串中字符的Unicode编码

```javascript
		<script type="text/javascript">
			console.log(1 > true);     //打印false,true转换成数字是1
			console.log(1 >= true);    //打印true
			console.log(1 > "0");      //打印true,字符串"0"转换为数字就是0
			console.log(10 > null);    //打印true,null转换成数字就是0
			//注意任何值和NaN做比较都是false
			console.log(10 >= "hello");   //打印false,字符串"hello"转换成数字是NaN
			console.log(true > false);    //打印true, true转换为数字是1,false转换为数字是0
			
			//比较两个字符串时,比较的是两个字符编码(ASCII码)
			console.log("a" < "b");  //true
			//比较字符编码是一位一位比较,如果两位一样,则比较下一位(如果第一位就比较出结果了,后面就不比了),所以借用它来对英文进行排序
			console.log("abc" < "bcd");    //打印true
			
			//如果比较的是两个字符串类型的数字,可能会得到不可预期的结果
			console.log("1223213" > "2");   //打印false
			//注意:在比较两个字符串类型的数字时,一定要记得强转
			console.log("1223213" > +"2");   //打印true,这里把字符串2强转成了数字2 
		</script>
```

**Unicode编码**

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>Unicode编码</title>
		<script type="text/javascript">
			//下面是JS中的使用方法
			//在字符串中使用转义字符输入Unicode编码:\u四位编码
			console.log("\u2620");   //2620是一个骷髅图案的unicode编码
		</script>
	</head>
	<body>
		<!--
			在网页中使用Unicode编码 ,方式:
			&#编码;   这里的编码需要的是10进制
		-->
		<h1>&#9760;</h1>     <!--输出骷髅头图案 -->
	</body>
</html>
```

**相等运算符**

> - 相等运算符用来比较两个值是否相等，如果相等会返回true，否则返回fasle
> - 使用 == 来做相等运算
>   - 当使用 == 来比较两个值时，如果值的类型不同，则会自动进行类型转换，将其转换为相同的类型然后再比较。
> - 使用 != 来做不相等运算
>   - 不相等两个值是否不相等，如果不相等返回true,否则返回false
>   - 不相等也会对变量进行自动的类型转换，如果转换后相等它也会返回false
> - 全等(===)
>   - 用来判断两个值是否全等，它和相等类似，不同的是它不会做自动类型转换。如果两个值的类型不同，直接返回false 
> - 不全等(!==)
>   - 用来判断两个值是否不全等，它和不等类似，不同的是它不会做自动类型转换。如果两个值的类型不同，直接返回true



```javascript
		<script type="text/javascript">
			console.log(1 == 1);   //打印true
			console.log("1" == 1);  //打印true
			console.log(true == "1");  //打印true
			console.log(null == 0);    //这是个特例不会做自动类型转换,打印false
			//undefined 衍生自null所以这两个值做相等判断时,会返回true
			console.log(undefined == null);   //打印true
			
			//NaN不和任何值相等,包括他本身
			console.log(NaN == undefined)   //打印false
			console.log(NaN == NaN);    //打印false
			
			var b = NaN;
			//判断b的值是否是NaN
			console.log(b == NaN);     //很显然这种方式是行不同的
			//可以通过isNaN()函数来判断一个值是否是NaN,如果该值是NaN则返回true,否则返回false
			console.log(isNaN(b));    //打印true
			
			//全等
			console.log("123" === 123);  //打印false   全等,类型不一样直接返回false
			console.log(null === undefined)//打印false  上面我们用这两个判断相等是true,这里全等因为类型不一样直接返回false 
			
			//不全等
			console.log("1" == 1) //打印true   类型转换后相等
			console.log("1" !== 1)  //打印true  不全等,类型不一样
		</script>
```

**运算符优先级**

> - 和数学中的一样，在JS中运算符也有优先级，比如先乘除，后加减
> - 在JS中有一个*运算符优先级*的表，在表中越靠上优先级越高，优先级越高越优先计算。如果优先级一样，则从左往右计算。
> - 这个运算符优先级的表我们并不需要记忆，如果遇到优先级不清楚，可以使用 () 来改变优先级

```javascript
		<script type="text/javascript">
			/**
			 * 逗号运算符
			 * 使用逗号可以分割多个语句,一般可以在声明多个变量时使用
			 */
			var a,b,c;    //可以使用逗号运算符同时声明多个变量
			var a = 1,b = 2,c = 3;    //可以同时声明多个变量并赋值
			alert(a);
			
			//运算符优先级
			/**
			 * 如果||的优先级高,或者两个一样高(从左往右算),则应该返回3
			 * 如果&&的优先级高,则应该返回1
			 */
			var result = 1 || 2 && 3;
			console.log(result);  //   打印1,  所以说明&&的优先级比较高
			//假设我现在不知道||和&&的优先级谁高,我们可以通过()来改变优先级
			result = (1 || 2) && 3;   //这里我想让1 和 2 先做运算
			console.log(result);   //打印3
		</script>
```

**代码块**

> - 我们的程序是由一条一条的语句构成的，语句是按照自上而下的属性一条一条的执行的
> - 在JS中使用 {} 来为语句进行分组，同一个 {} 中的语句我们称为是一组语句，他们要么都执行，要么都不执行。一个 {} 中的语句我们也称为一个代码块。
> - JS中的代码块，只具有分组的作用，没有其他的用途。代码块内部的内容，在外部是完全可见的



```javascript
		<script type="text/javascript">
			{
				var a = "hello";
				console.log("你好")
			}
			//JS中的代码块,只具有分组的作用,没有其他用途,代码块中的内容在外部是完全可见的
			console.log(a);  //打印hello
		</script>
```

**条件判断语句**

> - 使用条件判断语句可以在执行某个语句之前进行判断。如果条件成立才会执行，条件不成立则语句不执行

```javascript
		<script type="text/javascript">
			/**
			 * if语句
			 * 语法一:
			 *    if (条件表达式){
			 * 	      语句...
			 * }
			 * if语句在执行时,会先对条件表达式进行求值判断,如果条件表达式的值为true,则执行if
			 * 后的语句,如果条件表达式的值为false,则不会执行if后的语句
			 * 		if语句只能控制紧随其后的那个语句,如果希望if语句可以控制多条语句
			 * 		可以将这些语句统一放到大括号中,if语句后的大括号不是必须的,但是在开发中尽量写上
			 * 		大括号,即使if后只有一条语句
			 */
			var a = 10;
			if (a < 12){
				console.log(a);
			}
		</script>
```
**prompt()函数**

> - prompt()可以弹出一个提示框，该提示框中会带有一个文本框，用户可以在文本框中输入一段内容，该函数需要一个字符串作为参数，该字符串将作为提示框的提示文字。
> - 用户输入的内容将作为函数的返回值返回，可以定义一个变量来接收该内容
> - prompt()这个函数的返回值是 String 类型的

```javascript
		<script type="text/javascript">
			var score = prompt("请输入一个数");
			console.log(score);   //控制台打印你输入的数
		</script>
```

**条件分支语句**

> - 条件分支语句也叫switch语句
> - 执行流程
>   - switch...case....语句在执行时会依次将 case 后表达式的值和 switch 后的条件表达式的值进行全等比较，如果比较结果为 true ，则从当前 case 处开始执行代码，当前 case 后的所有代码都会执行，我们可以在 case 的后边跟着一个 break 这样就确保只会执行当前 case 后的语句而不会执行其它的case语句，如果比较结果为 fasle，则继续往下比较。如果所有的比较结果都为 false，则只执行 default 后的语句。

```javascript
		<script type="text/javascript">
			/**
			 * 语法:
			 * 		switch(条件表达式){
			 * 			case 表达式:
			 * 				语句...
			 * 				break;
			 * 			case 表达式:
			 * 				语句...
			 * 				break;
			 * 			default:
			 * 				语句...
			 * 				break;
			 * 		}
			 */
			var num = 1;
			switch (num) {
				case 1:
					console.log("1");
					break;
				case 2:
					console.log("2");
					break;
				default:
					console.log("哈哈");
					break;
			}
			//JS里面独有的一种使用switch方式,其他语言都不能像下面这么用
			var score = 70;
			switch (true) {
				case score > 60:
					console.log("及格");
					break;
				default:
					console.log("不及格");
					break;
			}
		</script>
```

**for循环**

> - for 循环中，为我们提供了专门位置用来放三个表达式
>   1. 初始化表达式
>   2. 条件表达式
>   3. 更新表达式

```javascript
		<script type="text/javascript">
			/**
			 * for循环语法:
			 * 		for(①初始化表达式 ;②条件表达式; ④更新表达式) {
			 * 				③语句...
			 * }
			 * for循环的执行流程:
			 * 		①执行初始化表达式,初始化变量
			 * 		②执行条件表达式,判断是否执行循环
			 * 			  如果为true,则执行循环③
			 * 			 如果为false,终止循环
			 * 		④执行更新表达式,更新表达式执行完毕继续重复②
			 */
			for (var i = 0; i < 10; i++) {
				alert(i);
			}

			//因为for循环初始化表达式只会执行一次,所以我在for循环括号里面去初始化多个变量也是可以的
			for (var i = 0,sum = 0; i < 10; i++) {
				alert(i);
			}
			
			/**
			 * for 循环中的三个部分都可以省略,也可以写在外部
			 * 如果for循环中不写任何表达式,只写两个分号,这个循环就是一个死循环
			 */
			for (;;) {
				alert(i);
			}
		</script>
```

**break关键字**

```javascript
		<script type="text/javascript">
			/**
			 * break关键字,会立即终止离他最近的那个循环语句
			 */
			for (var i = 0; i < 5; i++) {
				console.log(i);
				if (i == 2) {
					break;
				}
			}
			
			/**
			 * 可以为循环语句创建一个label,来标识当前的循环
			 * label:循环语句      (lable表示一个标识字段)
			 * 使用break语句时,可以在break后跟一个label,这样break将会结束指定的循环,而不是离它最近的循环
			 * continue使用同上
			 */
			hello:for (var i = 0; i < 5; i++) {        //加了一个hello以后,表示我这个循环的名字叫hello
				console.log("我是外层循环");
				for (var j = 0; j < 5; i++) {
					console.log("我是内层循环");
					break hello;    //在这里直接终止外层循环
				}
			}
		</script>
```

**计时器**

> - console.time("")可以用来开启一个计时器。它需要一个字符串作为参数,这个字符串将会作为计时器的标识
> - console.timeEnd("")   用来停止计时器,需要一个计时器的名字作为参数

```javascript
<script type="text/javascript">
			/**
			 * console.time("")可以用来开启一个计时器
			 * 它需要一个字符串作为参数,这个字符串将会作为计时器的标识
			 */
			console.time("test");      //传一个参数作为这个计时器的名字
			for (var i = 0; i < 100; i++) {
				console.log("--------" + i);
			}
			//终止计时器
			console.timeEnd("test");  //用来停止计时器,需要一个计时器的名字作为参数
</script>
```

**对象**

> - JS种一共有六种数据类型，5种基本数据类型，如果我们看到的值不是5中基本数据类型，就一定是对象
> - 基本数据类型都是单一的值如 "hello" 123 true 值和值之间没有任何联系。如果使用基本数据类型的数据，我们所创建的变量都是独立的，不能成为一个整体。
> - 对象属于一种复合的数据类型，在对象中可以保存多个不同数据类型的属性。
> - 对象的分类
>   1. 内建对象
>      - 由ES标准中定义的对象，在任何的ES实现中都可以使用
>      - 比如：Math  String  Number  Boolean
>   2. 宿主对象
>      - 由JS运行环境提供的对象，目前来讲主要是由浏览器提供的对象
>      - 比如 BOM  DOM
>   3. 自定义对象
>      - 由开发人员自己创建的对象

```javascript
	<script type="text/javascript">
			//创建对象
			var obj = new Object();
			console.log(typeof obj);   //使用typeof 检查一个对象时,会返回object
			
			/**
			 * 向对象添加属性
			 * 		语法: 对象.属性名 = 属性值
			 */
			//向obj中添加一个name属性
			obj.name = "Jimmy";
			//向obj中添加一个gender属性
			obj.gender = "man";
			//向obj中添加一个age属性
			obj.age = 18;
			console.log(obj);   //打印{name: "Jimmy", gender: "man", age: 18}
			
			/**
			 * 读取对象属性
			 * 		语法:对象.属性名
			 * 如果读取对象中没有的属性,不会报错而是会返回undefined
			 */
			console.log(obj.name);
			
			/**
			 * 修改对象的属性值
			 * 		语法:对象.属性名 = 新值
			 */
			obj.name = "kimi";
			
			/**
			 * 删除对象的属性
			 * 		语法: delete 对象.属性名
			 */
			//删除name属性
			delete obj.name;
			console.log(obj);    //打印{gender: "man", age: 18}
		</script>
```

**对象的属性名和属性值**

> - 属性名
>   - 对象的属性名不强制要求遵守标识符的规范，什么乱七八糟的名字都可以使用
>   - 但是我们尽量还是要按照规范去命名
> - 属性值
>   - JS对象的属性值，可以是任意的数据类型。甚至也可以是一个对象。

```javascript
		<script type="text/javascript">
			var obj = new Object();
			obj.name = "Jimmy";
			//属性名
			obj.var = "aa";         //我属性名时一个关键字也可以
			console.log(obj.var);   //打印aa
			
			/**
			 * 如果要使用特殊的属性名,不能采用点"."的方式来操作
			 * 需要使用另一种方式:
			 * 		语法:  对象["属性名"] = 属性值
			 * 读取时也需要采用这种方式
			 * 
			 * 使用[]这种形式去操作属性,更加的灵活
			 * 在[]中可以直接传递一个变量,这样变量值是多少就会读取那个属性
			 */
//			obj.123 = "bb";
//			console.log(obj.123);    //这样是会报错的
			obj["123"] = "bb";
			obj["nihao"] = "你好";
			console.log(obj["123"]);   //打印bb
			var n = "123";
			console.log(obj[n]);    //这里打印的是bb,但是如果我把n的值换成"nihao"打印的就是你好,更加灵活
			
			//属性值       可以是任意数据类型,甚至是一个对象
			obj.test = true;
			obj.test = null;
			obj.test = undefined;
			console.log(obj);
			
			var obj2 = new Object();
			obj2.name = "kelly";
			obj.test = obj2;
			console.log(obj.test);   //打印obj2这个对象
			
			/**
			 * in 运算符
			 * 		通过该运算符可以检查一个对象中是否含有指定的属性，如果有则返回true ，没有则返回 false
			 * 		语法:
			 * 			"属性名" in 对象
			 */
			//检查 obj 中是否含有 test 属性
			console.log("test" in obj);   //打印true
		</script>
```

**in 运算符**

> - 通过该运算符可以检查一个对象中是否含有指定的属性，如果有则返回true ，没有则返回 false
> - 语法
>   - "属性名"  in 对象

**对象字面量**

> - 使用对象字面量,可以在创建对象时,直接指定对象中的属性
> - 语法:
>   - {属性名:属性值,属性名:属性值....}
> - 对象字面量的属性名可以加引号也可以不加,建议不加
> - 如果要使用一些特殊的名字,则必须加引号
> - 属性名和属性值是一组一组的key-value结构，key和value之间使用冒号":"连接,多个key-value对之间使用逗号","隔开

```javascript
		<script type="text/javascript">
			//创建一个对象
			var obje = new Object();
			
			/**
			 * 使用对象字面量来创建一个对象,使用这种方式和上面的方式本质上是一样
			 */
			var obj = {};
			console.log(obj);  //打印[object Object]
			obj.name = "Jimmy";
			console.log(obj.name);   //打印Jimmy
			
			/**
			 * 使用对象字面量,可以在创建对象时,直接指定对象中的属性
			 * 语法:
			 * 		{属性名:属性值,属性名:属性值....}
			 * 对象字面量的属性名可以加引号也可以不加,建议不加
			 * 如果要使用一些特殊的名字,则必须加引号
			 * 
			 * 属性名和属性值是一组一组的key-value结构
			 * key和value之间使用冒号":"连接,多个key-value对之间使用逗号","隔开
			 */
			var obj2 = {
				name:"kimi",
				"age":"12",
				sex:"male",
				test:{name:"kelly"}     //test的属性值是一个对象
				};
			console.log(obj2);
		</script>
```

**函数**

> - 函数也是一个对象
> - 函数中可以封装一些功能(代码),在需要时可以执行这些功能(代码)
> - 使用 typeof 检查一个函数对象时,会返回function
> - 我们可以使用函数声明来创建一个函数
> - 我们可以是会用函数表达式来创建一个函数

```javascript
		<script type="text/javascript">
			//创建一个函数对象
			var fun = new Function();
			console.log(typeof fun);    //打印function
			
			//可以将要封装的代码以字符串的形式传递给构造函数(这种方式了解下即可)
			var fun1 = new Function("console.log('这是第一个函数')")
			
			/**
			 * 封装到函数中的代码不会立即执行
			 * 函数中的代码会在函数调用的时候执行
			 * 调用函数语法:
			 * 			函数对象()
			 * 当调用函数时,函数中封装的代码会按照顺序执行
			 */
			fun1();
			//我们说函数也是一个对象,它具有所有普通的对象的功能,只不过它比普通对象更强大,在函数里面封装的是一些可执行的代码
			fun1.hello = "你好";
			console.log(fun1.hello);   //打印你好
			
			/*
			 * 在开发中我们一般都使用函数声明来创建一个函数
			 * 	语法:
			 * 		function 函数名 ([形参1,形参2...形参N]) {   中括号表示这些形参可选
			 * 			语句....
			 * 		}
			 */
			function fun2() {
				console.log("这是我的第二个函数");
			}
			console.log(fun2);    //会把整个函数都给它打出来
			//调用fun2()
			fun2();
			
			/*
			 * 使用函数表达式来创建一个函数
			 * var 函数名 = function([形参1,形参2...形参N]) {
			 * 		语句....
			 * }
			 * 所谓的函数表达式就是创建一个匿名函数,然后将这个匿名函数的对象赋值给一个变量
			 */
			var fun3 = function() {
				console.log("我是匿名函数中的语句");
			};
		</script>
```

**函数参数**

```javascript
		<script type="text/javascript">
			function add(a,b) {
				console.log(a + b);
			}
			/*
			 * 调用函数时,解析器不会检查实参的类型,所以要注意是否可能会接收到非法的参数,如果有可能则需要对参数进行类型的检查
			 * 函数的实参可以是任意的数据类型
			 */
			add(2,3);   //打印5
			/**
			 * 调用函数时,解析器也不会检查实参的数量
			 * 多余的实参不会被赋值
			 * 如果实参的数量少于形参的数量,则没有对应实参的形参将是undefined
			 */
			add(3,2,1,"hello"); //打印5,后面的参数不会被赋值
			add(1);    //打印NaN,就相当于a=1,b=undefined  a+b即为NaN
		</script>
```

**函数返回值**

```javascript
		<script type="text/javascript">
			/*
			 * 创建一个函数,用来计算三个数的和
			 * 可以使用 return 来设置函数的返回值
			 * 语法:
			 * 		return 值
			 * return 后的值将会作为函数的执行结果返回,可以定义一个变量,来接收该结果
			 * 在函数中return 后的语句都不会执行
			 * 如果 return 语句后不跟任何值就相当于返回一个undefined
			 * 如果函数不写 return(即没有返回值),则也会返回 undefined
			 */
			function sum (a,b,c) {
				var d =  a + b + c;
				return d;
			}
			var result = sum(1,2,3);
			console.log(result);   //打印6   假设sum函数中我没有写return,这里打印的就是undefined
		</script>
```

**函数的实参可以是任何值**

```javascript
		<script type="text/javascript">
			function isou(num) {
				/*if (num % 2 == 0) {
					return true;
				} else {
					return false;
				}*/
				//上面这样写有点多余了,直接返回就可以了
				return num % 2 == 0;
			}
			var result = isou(6);
			console.log(result)
			
			var obj = {
				name : "孙悟空",
				age : "18",
				gender : "男",
				address : "花果山"
			};
			
			function myFun(fun) {
				console.log(fun);
			}
			//函数的实参也可以是一个函数
			myFun(isou);    //这里我可以传一个函数对象过去
			
			myFun(function () {    //我也可以传一个匿名函数
				console.log("匿名函数作为一个实参传递给一个函数");
			});  
		</script>
```

**返回值类型**

> - 返回值可以是任意的数据类型
> - 返回值可以是一个对象，也可以是一个函数（强调）

```javascript
		<script type="text/javascript">
			function fun2() {
				return {name:"Jimmy"};   //返回值类型可以是一个对象
			}
			function fun3() {
				//在函数内部再声明一个函数
				function fun4() {
					console.log("我是fun4");
				}
				//fun4();    在函数里面调用fun4
				return fun4;     //返回fun4这个函数对象
			}
			var resu = fun3();   
			console.log(resu);   //打印的就是fun4这个函数
		</script>
```

**立即执行函数**

> - 函数定义完，立即被调用，这种函数叫做立即执行函数
> - 立即执行函数往往只会执行一次(因为没有变量去接收它，执行完以后你就找不到了)

```javascript
		<script type="text/javascript">
			/*
			 * 1、如果我直接声明一个匿名函数,它是会报错的。因为它把大括号当成一个代码块,大括号前面的东西它就不认识了
			 * 所以我们应该在匿名函数上加一个括号,来表示这个函数是一个整体,这样它就不会报错了
			 * 2、如果我要是想去调用匿名函数应该怎么去调用呢？
			 * 答:我们说调用一个函数,直接用    函数对象()
			 */
			/*(function() {
				console.log("我是一个匿名函数");
			})*/
			
			//调用匿名函数
			/*
			 * 这就是一个立即执行函数,函数定义完,立即被调用
			 * 立即执行函数往往只会执行一次,因为这个函数没有变量去接收它,执行完以后你就找不到它了
			 */
			/*(function() {
				console.log("我是一个匿名函数,啦啦啦");
			})()*/
			
			//也可以这么写
			(function(a,b) {
				console.log("a = " + a);
				console.log("b = " + b);
			})(2,3)     //这也是一个立即执行函数,带参数的
		</script>
```

**方法**

> - 函数也可以成为对象的属性,如果一个函数作为一个对象的属性保存,那么我们称这个函数是这个对象的方法
> - 调用函数就说调用对象的方法
> - 调函数和调方法本质上是一样的,只是叫法,名称不同而已

```javascript
		<script type="text/javascript">
			var obj = new Object();
			//像对象中添加属性
			obj.name = "Jimmy";
			obj.age = 18;
			//对象的属性值可以是任何数据类型,也可以是个函数
			obj.sayName = function() {
				console.log(obj.name);
			}
			console.log(obj.sayName)      //打印sayName这个函数
			obj.sayName();    //调用方法
			
			function fun() {
				console.log("aa");
			}
			fun();  //调用函数
			
			/*
			 * 函数也可以成为对象的属性,
			 * 如果一个函数作为一个对象的属性保存,那么我们称这个函数是这个对象的方法
			 * 调用函数就说调用对象的方法
			 * 
			 * 调函数和调方法本质上是一样的,只是叫法,名称不同而已。
			 */
			
			var obj2 = {
				name : "kelly",
				age : 18,
				sayName : function() {
					console.log(obj2.name);
				}
			}
			obj2.sayName();     //这种方式也可以
		</script>
```

**枚举对象中的属性**

```javascript
		<script type="text/javascript">
			var obj = {
				name : "kimi",
				age : 18,
				gender : "男",
				address : "Taibei"
			}
			//枚举对象中的属性
			//使用 for ... in 语句
			/*
			 * 语法:
			 * 	for(var 变量 in 对象) {
			 * 	
			 * 	}
			 * for...in语句 对象中有几个属性,循环体就会执行几次
			 * 每次执行会将对象中的一个属性的名字赋值给变量
			 */
			for (var n in obj) {
				console.log(n + ":" + obj[n]);
			}
		</script>
```

**作用域**

> - 作用域指一个变量的作用范围
> - 在JS中一共有两种作用域
>   - 全局作用域
>     - 直接编写在script标签中的JS代码，都在全局作用域
>     - 全局作用域在页面打开时创建，在页面关闭时销毁
>     - 在全局作用域中有一个全局对象window，它代表的是浏览器窗口，它由浏览器创建我们可以直接使用
>     - 在全局作用域中，创建的变量都会作为window对象的属性保存，创建的函数都会作为window对象的方法保存
>     - 全局作用域中的变量都是全局变量，在页面的任意部分都可以访问的到
>   - 函数作用域
>     - 调用函数时创建函数作用域，函数执行完毕后，函数作用域销毁
>     - 每调用一次函数就会创建一个新的函数作用域，它们之间是互相独立的
>     - 在函数作用域中可以访问到全局作用域的变量，在全局作用域中无法访问到函数作用域中的变量
>     - 当在函数作用域操作一个变量时，它会先在自身作用域中寻找，如果有就直接使用，如果没有则向上一级作用域中寻找，直到找到全局作用域，如果全局作用域中依然没有找到，则会报错
>     - 在函数中要访问全局变量可以使用window对象

```javascript
		<script type="text/javascript">
			//直接写在script标签中的JS代码,都在全局作用域,在全局作用域中,创建的变量都会作为window对象的属性保存
			var a = 10;
			console.log(window.a);   //创建的a变量,会作为window对象的属性
			
			function fun() {        //创建的这个fun函数,会作为window对象的方法
				console.log("我是一个方法");
			}
			//全局作用域中的函数会作为window对象的方法保存
			window.fun();     //我们所谓的函数其实就是window对象的方法
		</script>
```

**变量/函数的声明提前**

> - 变量的声明提前
>   - 使用var关键字声明的变量,会在所有的代码执行之前被声明(但是不会被赋值)
>   - 但是如果声明变量时不使用var关键字,则变量不会被声明提前
> - 函数的声明提前
>   - 使用函数声明形式创建的函数 function 函数名(){} 它会在所有代码执行之前就被创建,所以我们可以在函数声明前来调用函数
>   - 使用函数表达式创建的函数,不会被声明提前,所以不能在声明前调用

```javascript
		<script type="text/javascript">
			var a = 10;
			console.log(a);
			b = 15;          //这里我定义变量的时候不写var,发现也没有报错。不写var就相当于是window.b=15,b作为window对象的一个属性
			console.log(b);   //打印15
			
			console.log(c);   //打印undefined
			var c = 11;        //这行代码就相当于,我在最开始声明一个var c;然后在这行给c赋值c=11 
			
//			console.log(d);
			d = 13;             //这样的话就会报错,因为我声明变量的时候没有使用var关键字,变量就不会被声明提前,变量还没声明,我在上一行就打印肯定就报错了
			
			
			fun();     //我在函数声明之前调用函数,是不会报错的
			
			function fun() {        //它会在所有代码执行之前被调用
				console.log("我是fun函数");
			}
			
			fun2();    //在用函数表达式创建的函数之前调用会报错,用var声明的变量会被声明提前,所以就相当于在最前面声明一个var fun2; 因为没有赋值所以这个fun2肯定是undefined,所以你在这行就相当于调用undefined(),肯定报错
			var fun2 = function() {   
				console.log("我是fun2函数");
			}
		</script>
```

**函数作用域小案例**

```javascript
		<script type="text/javascript">
			var a = 10;
			
			function fun() {
				var a = 3;
				console.log(window.a);   //打印10,在函数里面我如果想访问全局变量,可以借助window对象
			}
			
			/*
			 * 在函数作用域中也有声明提前的特性
			 * 使用var关键字声明的变量,会在函数中所有的代码执行之前被声明
			 */
			function fun2() {
				console.log(b);   //打印undefined,这行代码它会现在fun2函数作用域里面找b,看有没有b,发现是有b的,因为b这个变量用var声明了,它会被声明提前,找到了但是没有被赋值,所以打印undefined
				var b = 1;         //这行代码就相当于我在fun2函数里面的第一行代码声明一个变量var b; 然后在这行给b赋值b=1
			}
//			fun2();
			
			var c = 33;
			function fun3() {
				console.log(c);   //打印33,这行代码它会现在fun3函数作用域里面找看有没有c,发现没有,因为下面的c没有用var关键字声明,就不会被声明提前,所以去它上一级作用域找,找到了就打印
				c = 10;   //这个c实际上是个全局变量
				//这个d没有使用var关键字,则会设置为全局变量
				d = 100;  //这个d也是全局变量
			}
			fun3();
			/*
			 * 在函数中,不使用var声明的变量都会成为全局变量
			 */
			//在全局输出c
			console.log(c); //打印10
			console.log(d);  //打印100
			
			/*
			 * 定义形参就相当于在函数作用域中声明了变量
			 */
			function fun4(e) {
				console.log(e);  //打印undefined,我实参没有传值,但是函数定义了形参,就相当于在函数作用域中声明了变量,声明了变量但是没有赋值,所以打印undefined
			}
			fun4();
		</script>
```
**工厂方法创建对象**

```javascript
		<script type="text/javascript">
			var obj = {
				name : "Jimmy",
				age : 18,
				gender : "男",
				sayName: function() {
					console.log(this.name)
				}
			}
			var obj2 = {
				name : "kimi",
				age : 8,
				gender : "男",
				sayName: function() {
					console.log(this.name)
				}
			}
			
			/*
			 * 使用工厂方法创建对象
			 */
			function createPerson(name, age, gender) {
				//创建新的对象
				var obj = new Object();
				//向对象中添加属性
				obj.name = name;
				obj.age = age;
				obj.gender = gender;
				obj.sayName = function() {
					console.log(this.name);
				};
				return obj;
			}
			var p1 = createPerson("cc",18,"男");
			console.log(p1);

			function createDog(name, age) {
				var obj = new Object();
				obj.name = name;
				obj.age = age;
				obj.sayHello = function() {
					console.log("汪汪");
				}
				return obj;
			}
			/*
			 * 工厂方法创建的对象,使用的构造函数都是Object
			 * 所以创建的对象都是Object这个类型,就导致我们无法区分出多种不同类型的对象
			 */
			var dog = createDog("旺财",12);
		</script>
```

**构造函数**

> - 构造函数就是一个普通的函数，创建方式和普通函数没有区别，不同的是构造函数习惯上首字母大写。
> - 构造函数和普通函数的区别就是调用方式的不同，普通函数是直接调用，而构造函数需要使用 new 关键字来调用
> - 构造函数的执行流程
>   1. 立刻创建一个新的对象
>   2. 将新建的对象设置为函数中的this，在构造函数中可以使用 this 来引用新建的对象
>   3. 逐行执行函数中的代码
>   4. 将新建的对象作为返回值返回
> - 使用同一个构造函数创建的对象，我们称为一类对象，也将一个构造函数称为一个类。我们通过一个构造函数创建的对象，称为是该类的实例。

```javascript
		<script type="text/javascript">
			/*
			 * 构造函数习惯首字母大写
			 */
			function Person(name, age, gender) {
				this.name = name;
				this.age = age;
				this.gender = gender;
			}
			var per = new Person("Jimmy",12,"男");
			console.log(per);   //打印Person {name: "Jimmy", age: 12, gender: "男"}
			
			/*
			 * 使用instanceof 可以检查一个对象是否是一个类的实例
			 */
			console.log(per instanceof Person);  //打印true
			//所有对象都是Object的后代
			console.log(per instanceof Object);  //打印true
		</script>
```

**关于构造函数里面的方法**

```javascript
		<script type="text/javascript">
			//声明一个构造函数
			function Person(name, age, gender) {
				this.name = name;
				this.age = age;
				this.gender = gender;
				//向对象中添加一个方法
				this.sayName = fun;    //为了不是每调用一次构造函数,我就要新调用一次这个方法,我把方法拿到外面去写
			}
			/*
			 * 将函数拿到外面来写又会带来一个问题:
			 * 将函数定义在全局作用域,污染了全局作用域的命名空间(如果有两个名字一样的函数,后一个会覆盖前面的)
			 * 而且定义在全局作用域中也很不安全(别人定义的函数名和你定义的函数名可能会重掉,这样就会出问题)
			 */
			function fun() {            
				console.log(this.name);
			}
			//js中可以定义两个函数名相同的函数,后一个函数会把前一个函数给覆盖掉
			function fun() {
				console.log(this.age);
			}
			//这样我不同对象调用sayName方法,调用的都是同一个方法。要是在构造函数里面写,每创建一个对象,就会创建一个方法造成资源浪费
			var p1 = new Person("Jimmy",44,"男");
			p1.sayName();
		</script>
```

**原型对象**

> - 我们所创建的每一个函数，解析器都会向函数中添加一个属性 prototype
> - 如果函数作为普通函数调用，这个 prototype 属性没有任何作用
> - 当函数以构造函数的形式调用时，它所创建的对象中都会有一个隐含的属性，指向该构造函数的原型对象，我们可以通过 _ _proto_ _ _来访问该属性
> - 原型对象就相当于一个公共的区域，所有同一个类的实例都可以访问到这个原型对象，我们可以将对象中共有的内容，统一设置到原型对象中
> - 当我们访问对象的一个属性或方法时，它会先在对象自身中寻找，如果有则直接使用，如果没有则会去原型对象中寻找，如果找到则直接使用
> - 以后我们创建构造函数时，可以将这些对象共有的属性和方法，统一添加到构造函数的原型对象中，这样就不用分别为每一个对象添加，也不会影响到全局作用域，就可以使每个对象都具有这些属性和方法了

```javascript
		<script type="text/javascript">
			/*
			 * 原型 prototype
			 * 	我们所创建的每一个函数,解析器都会向函数中添加一个属性prototype
			 * 	如果函数作为普通函数调用,这个prototype属性没有任何作用
			 *  当函数以构造函数的形式调用时,它所创建的对象中都会有一个隐含的属性,
			 * 	指向该构造函数的原型对象,我们可以通过__proto__来访问该属性
			 */
			function Person() {
				
			}
			function MyClass() {
				
			}
			var mv = new MyClass();
			console.log(MyClass.prototype);   //这个prototype属性值是一个对象
			//同一个类的实例都指向这个原型对象
			console.log(mv.__proto__ == MyClass.prototype);  //打印true
			
			//向MyClass的原型中添加属性a
			MyClass.prototype.a = 123;     //MyClass的prototype属性的属性值是一个对象,现在我给这个对象添加一个属性a
			console.log(mv.a);    //打印123,因为同一类的实例都指向这个原型对象
			mv.a = 456;
			console.log(mv.a);  //打印456,找的时候是先在对象实例中找,找不到后再去原型里面找
			
			//向MyClass原型中添加一个方法
			MyClass.prototype.sayHello = function() {
				console.log("我是原型对象中的方法");
			}
			
			mv.sayHello();   //在mv对象中我并没有写sayHello方法,在原型对象中写了,所以我这里可以调
		</script>
        
        //像上面关于构造函数里面的方法,我们就可以把它写到原型对象里面去
        <script type="text/javascript">
			//声明一个构造函数
			function Person(name, age, gender) {
				this.name = name;
				this.age = age;
				this.gender = gender;
			}
			Person.prototype.sayName = function() {
				console.log(this.name);
			}
			var p1 = new Person("Jimmy",44,"男");
			p1.sayName();    //在原型对象里面写方法,我这个对象的实例都可以访问到
		</script>
        
```

```javascript
		<script type="text/javascript">
			//创建一个构造函数
			function MyClass() {
				
			}
			//向MyClass的原型中添加一个name属性
			MyClass.prototype.name = "我是原型中的名字";
			var mv = new MyClass();
			console.log(mv.name);   //打印"我是原型中的名字"
			//使用 in 检查对象中是否含有某个属性时,如果对象中没有但是原型中有,也会返回true
			console.log("name" in MyClass)   //打印true
			
			//可以使用对象的hasOwnProperty()来检查对象自身中是否含有该属性
			console.log(mv.hasOwnProperty("name"));    //打印false
			
			/*
			 * 原型对象也是对象,所以它也有原型
			 * 当我们使用一个对象的属性或方法时,会现在自身中寻找,自身中如果有,则直接使用
			 * 如果没有则去原型对象中寻找,如果原型对象中有,则使用
			 * 如果没有则去原型的原型中寻找,直到找到Object对象的原型
			 * Object对象的原型没有原型,如果在Object 中依然没有找到,则返回undefined
			 */
			console.log(mv.__proto__.hasOwnProperty("hasOwnProperty"))   //看下原型里面有没有hasOwnProperty这个属性,发现打印fasle
			console.log(mv.__proto__.__proto__.hasOwnProperty("hasOwnProperty"));  //打印true,发现这个属性是在原型的原型里面
		</script>
        
        
        //关于原型
        /*当系统为Person构造器创建原型对象的时候，会执行这样的一条语句：Person.prototype = new Object();也就是说，这个原型对象是Object的一个实例，那么，Object类下面的所有属性和方法会被这个原型对象所拥有，Person下的实例就可以通过这个原型对象使用这些属性和方法。所以说Object是所有类的父类*/
```

**toString()方法**

```javascript
		<script type="text/javascript">
			function Person(name, age, gender) {
				this.name = name;
				this.age = age;
				this.gender = gender;
			}
			var per = new Person("Jimmy",12,"男");
			//当我们直接在页面中打印一个对象时,实际上是输出这个对象的toString()方法的返回值
			//如果我们希望在输出对象时不输出[Object,Object],可以为对象添加一个toString()方法
			Person.prototype.toString = function() {
				return this.name + "," + this.age + "," + this.gender
			}
			console.log(per.toString()); 
		</script>
```

**垃圾回收**

> - 就像人生活时间长了会产生垃圾一样，程序运行过程也会产生垃圾，这些垃圾积攒过多以后，会导致程序运行的速度过慢，所以我们需要一个垃圾回收机制，来处理程序运行过程中的垃圾。
> - 当一个对象没有任何的变量或属性对它进行引用，此时我们将永远无法操作该对象，此时这种对象就是一个垃圾，这种对象过多会占用大量的内存空间，导致程序运行变慢，所以这种垃圾必须进行清理。
> - 在JS中拥有自动的垃圾回收机制，会自动将这些垃圾对象从内存中销毁，我们不需要也不能进行垃圾回收操作
> - 我们需要做的就是将不再使用的对象设置为 null 即可

**数组**

> - 数组也是一个对象
> - 它是用来存储一些值的

```javascript
		<script type="text/javascript">
			//创建一个数组对象
			var arr = new Array();
			//使用typeof检查一个数组时,会返回object
			console.log(typeof arr);
			
			/*
			 * 向数组中添加元素
			 * 语法:
			 * 		数组[索引] = 值
			 */
			arr[0] = 1;
			arr[1] = 2;
			/*
			 * 读取数组中的元素
			 * 语法:
			 * 		数组[索引]
			 * 如果读取不存在的索引,它不会报错而是返回undefined
			 */
			console.log(arr[1]);    //如果读取不存在的索引,返回undefined
			/**
			 * 获取数组的长度
			 * 可以使用length属性来获取数组长度
			 * 语法:   数组.length
			 * 对于连续的数组,使用length可以获取到数组的长度(元素的个数)
			 * 对于非连续的数组,使用length会获取到数组的最大索引+1
			 */
			console.log(arr.length);   //打印2
			
			/*
			 * 修改length
			 * 		如果修改的length大于原长度,则多出部分会空出来
			 * 		如果修改的length小于原长度,则多出的元素会被删除(所以我们可以通过修改length来删除一些元素)
			 */
			arr.length = 5;   //原来length长度是2,现在我给它改成5
			console.log(arr.length);    //打印5
		</script>
```

**数组字面量**

```javascript
		<script type="text/javascript">
			//创建一个数组
			var arr = new Array();
			/*
			 * 使用字面量来创建数组
			 * 语法:[]
			 */
			var arr1 = [];
			console.log(arr1);
			//使用字面量创建数组时,可以在创建时就指定数组中的元素
			var arr2 = [1,2,3,4];
			console.log(arr2.length);   //打印4
			
			//使用构造函数创建数组时,也可以同时添加元素,将要添加的元素作为构造函数的参数传递,元素之间使用逗号隔开
			var arr3 = new Array(3,4,5,6);
			console.log(arr3.length);     //打印4
			
			//创建一个数组数组中只有一个元素10
			var arr4 = [10];
			console.log(arr4.length);    //打印1
			//创建一个长度为10的数组
			var arr5 = new Array(10);
			console.log(arr5.length);    //打印10
			
			//数组中的元素可以是任意的数据类型
			var arr6 = ["hello",1,true,null,undefined];
			
			//可以是对象
			var arr7 = [{name:"Jimmy"},{name:"kimi"},{name:"kelly"}]
			console.log(arr7.length);   //打印3
			
			//也可以是函数
			var arr8 = [function(){console.log(1)},function(){console.log(2)}];
			console.log(arr8.length);  //打印2
			//调用
			arr8[0]();
			
			//数组中也可以放数组,如下这种数组我们称为二维数组
			var arr9 = [[1,2,3],[2,3,4],[1,3,8]];
			console.log(arr9.length);   //打印3
		</script>
```

**数组的四个方法**

> - push()方法
>   - 该方法可以向数组的末尾添加一个或多个元素,并返回数组的新长度
>   - 可以将要添加的元素作为方法的参数传递,这样这些元素将会自动添加到数组的末尾
>   - 该方法会将数组新的长度作为返回值返回
> - pop()方法
>   - 该方法可以删除数组的最后一个元素,并将被删除的元素作为返回值返回
> - unshift()方法
>   - 向数组开头添加一个或多个元素,并返回新的数组长度
>   - 向前边插入元素以后,其他元素的索引会依次调整
> - shift()方法
>   - 可以删除数组的第一个元素,并将删除的元素作为返回值返回

```javascript
		<script type="text/javascript">
			//创建一个数组
			var arr = ["Jimmy","kelly","kimi"];
			/*
			 * push()
			 * 		该方法可以向数组的末尾添加一个或多个元素,并返回数组的新的长度
			 * 		可以将要添加的元素作为方法的参数传递,这样这些元素将会自动添加到数组的末尾
			 */
			arr.push("kyson");   //添加一个
			console.log(arr);    //打印"Jimmy,kelly,kimi,kyson"
			var len = arr.push("shp","heping");   //添加多个
			console.log(len);   //打印len,返回值是数组的长度
			
			/*
			 * pop()
			 * 		该方法可以删除数组的最后一个元素,并将被删除的元素作为返回值返回
			 */
			console.log(arr);   //打印"Jimmy,kelly,kimi,kyson,shp,heping"
			var result = arr.pop();
			console.log(arr);   //打印"Jimmy,kelly,kimi,kyson,shp"
			console.log(result);    //打印heping
			
			/*
			 * unshift()
			 * 		向数组开头添加一个或多个元素,并返回新的数组长度
			 * 		向前边插入元素以后,其他元素的索引会依次调整
			 */
			var len2 = arr.unshift("hi","hello");
			console.log(arr);  //打印"hi,hello,Jimmy,kelly,kimi,kyson,shp"
			console.log(len2);  //打印7
			
			/*
			 * shift()
			 * 		可以删除数组的第一个元素,并将删除的元素作为返回值返回
			 */
			result = arr.shift();
			console.log(arr);   //打印"hello,Jimmy,kelly,kimi,kyson,shp"
			console.log(result);  //打印"hi",这个为删除的第一个元素
		</script>
```

**数组小练习**

```javascript
		<script type="text/javascript">
			function Person(name,age,gender) {
				this.name = name;
				this.age = age;
				this.gender = gender;
			}
			//修改Person原型的toString方法
			Person.prototype.toString = function() {
				return "Person[name="+this.name+",age="+this.age+",gender="+this.gender+"]";
			}
			//创建Person对象
			var per1 = new Person("Jimmy",43,"男");
			var per2 = new Person("kelly",33,"女");
			var per3 = new Person("kimi",11,"男");
			//将这些Person放到一个数组里
			var perArr = [per1,per2,per3];
			
			/*
			 * 创建一个函数,可以将perArr中大于18岁的Person提取出来,
			 * 然后封装到一个新的数组中并返回
			 */
			function getAdult(arr) {
				var newArr = [];
				for (var i = 0; i < arr.length; i++) {
					if (arr[i].age > 18) {
						newArr.push(arr[i]);
					}
				}
				return newArr;
			}
			console.log(getAdult(perArr));   //打印Jimmy和kelly两个数组对象
		</script>
```

**forEach()方法**

> - 一般我们都是使用for循环去遍历数组，JS中还为我们提供了一个方法，用来遍历数组
> - forEach()方法
>   - 这个方法只支持IE8以上浏览器，IE8及以下的浏览器均不支持该方法
> - forEach()方法需要一个函数作为参数
> - 像这种，由我们创建但是不由我们调用的，我们称为回调函数
> - 数组中有几个元素，函数就执行几次，每次执行时浏览器会将遍历到的元素，以实参的形式传递进来，我们可以定义形参来读取这些内容
> - 浏览器会在回调函数中传递三个参数
>   1. 第一个参数，就是当前正在遍历的元素
>   2. 第二个参数，就是当前正在遍历元素的索引
>   3. 第三个参数，就是正在遍历的数组

```javascript
		<script type="text/javascript">
			//创建一个数组
			var arr = ["Jimmy","kelly","kimi","kyson","jkson"];
			/*
			 * forEach()方法需要一个函数作为参数
			 * 		像这种函数,由我们创建但是不由我们调用的,我们称为回调函数
			 * 		数组中有几个元素函数执行几次,每次执行时,浏览器会将遍历到的元素以实参的形式传递进来,我们可以定义形参,来读取这些内容
			 */
			arr.forEach(function(value,index,obj) {
				console.log(value);   //打印数组中的各个元素
				console.log(index);   //打印正在遍历元素的索引
				console.log(arr);     //打印这个数组对象
			})
		</script>
```

**数组的方法**

> - slice()方法
>   - 可以从数组中提取指定的元素
>   - 该方法不会改变数组，而是将截取到的元素封装到一个新数组中返回
>   - 参数
>     - start        从何处开始选取(包含开始)
>     - end          从何处结束选取(不包含结束)
>   - 参数可以传一个负数，如果传的是负数，那么就从数组尾部开始算起。即，-1指最后一个元素，-2指倒数第二个元素
> - splice()方法
>   - 可以用于删除数组中的元素
>   - 使用splice()会影响到原数组,会将指定元素从原数组中删除,并将被删除的元素作为返回值返回
>   - 参数
>     - 第一个参数,表示开始位置的索引
>     - 第二个参数,表示删除的数量
>     - 第三个及以后,可以传递一些新的元素,这些元素将会自动插入到开始位置的索引前边

```javascript
		<script type="text/javascript">
			//创建一个数组
			var arr = ["Jimmy","kelly","kimi","kyson","jkson"];
			//使用slice截取数组中的元素
			var newArr = arr.slice(0,2);   //截取是前包括后不包括截取
			console.log(newArr);    //打印Jimmy,kelly
			
			/*
			 * splice()
			 * 可以用于删除数组中的元素
			 * 使用splice()会影响到原数组,会将指定元素从原数组中删除,并将被删除的元素作为返回值返回
			 * 参数
			 * 		第一个参数,表示开始位置的索引
			 * 		第二个参数,表示删除的数量
			 * 		第三个及以后,可以传递一些新的元素,这些元素将会自动插入到开始位置的索引前边
			 */
			var result = arr.splice(0,2);
			console.log(result);    //打印"Jimmy,kelly"
		</script>
```

**数组剩余方法**

> - concat()方法
>   - 可以连接两个或多个数组，并将新的数组返回
>   - 该方法不会对原数组产生影响
> - join()方法
>   - 该方法可以将数组转换为一个字符串
>   - 该方法不会对原数组产生影响，而是将转换后的字符串作为结果返回
>   - 在join()中可以指定一个字符串作为参数，这个字符串将会成为数组中元素的连接符。如果不指定连接符，则默认使用逗号“，”作为连接符
> - reverse()方法
>   - 该方法用来反转数组
>   - 该方法会直接修改原数组
> - sort()方法
>   - 可以用来对数组进行排序
>   - 会影响原数组，默认会按照Unicode编码进行排序

```javascript
		<script type="text/javascript">
			var arr = ["Jimmy","kimi","kelly"];
			var arr2 = ["durant","kobe","James"];
			var arr3 = ["aa","bb","cc"];
			/**
			 * concat()可以连接两个或多个数组,并将新的数组返回
			 */
			var newArr = arr.concat(arr2,arr3);
			console.log(newArr);    //打印三个数组所有的元素
			/*
			 * join方法
			 */
			var result = arr.join();
			console.log(result);   //打印Jimmy,kimi,kelly
			console.log(typeof result);   //打印string
			//在join()中指定一个字符串作为参数,这个字符串会成为数组中元素的连接符
			result = arr.join("-");
			console.log(result);    //打印Jimmy-kimi-kelly
			/*
			 * reverse()
			 */
			newArr = arr.reverse();
			console.log(newArr);    //打印kelly,kimi,Jimmy
			/**
			 * sort()
			 */
			var arr3 = ["a","c","d","b"];
			console.log(arr3.sort());    //打印a,b,c,d
			
			/*
			 * 对于纯数字的数组,使用sort()排序时,也会按照unicode编码来排序
			 * 所以对数字进行排序可能会得到错误的结果
			 * 
			 * 所以我们可以自己指定排序的规则,我们可以在sort()中添加一个回调函数,来指定排序规则
			 * 回调函数中需要定义两个形参,浏览器将会分别使用数组中的元素作为实参去调用回调函数
			 * 使用哪个元素不确定,但是肯定的是在数组中a一定在b的前边
			 * 浏览器会根据回调函数的返回值来决定元素的顺序,
			 * 如果返回一个大于0的值,则元素会交换位置
			 * 如果返回一个小于0的值,则元素位置不变
			 * 如果返回一个0,则认为两个元素相等,也不交换位置
			 * 
			 * 结论:
			 * 如果需要升序排列,则返回a-b
			 * 如果需要降序排列,则返回b-a
			 */
			var arr4 = [3,4,6,5,11];
			console.log(arr4.sort());  //打印11,3,4,5,6, 是按照unicode编码来排序的
			
			var arr5 = [5,3];
			arr5.sort(function(a,b) {
				//前面的大
				if (a > b) {
					return 1;     //如果前面的比后面的大就换位置
				} else if (a < b) {    //说明小的数在前面
					return -1;     //如果前面的比后面的小就不换位置
				} else {
					return 0;
				}
				//比较简单一点可以这么写,直接return 两个值相减
//				return a-b;
			})
			console.log(arr5);     //如果返回一个正数,这里打印3,5。如果返回一个负数,这里打印5,3
		</script>
```

**函数的方法**

> - call()和apply()方法
>   - 这两个方法都是函数对象的方法,需要通过函数对象来调用
>   - 当函数对象调用call()和apply()都会调用函数执行
> - call()方法可以将实参在对象之后依次传递
> - apply()方法需要将实参封装到一个数组中统一传递

```javascript
		<script type="text/javascript">
			function fun() {
				console.log("我是fun函数");
			}
			fun.call();   //打印我是fun函数
			fun.apply();  //打印我是fun函数
			fun();		  //打印我是fun函数
			
			function fun2() {
				console.log(this);
			}
			fun2();   //打印[object Window],this代表当前window对象
			var obj = {};
			fun2.call(obj);  //打印[object Object],this代表当前对象,你传过来哪个对象,this就代表哪个对象
			
			function fun3(a,b) {
				console.log(a);
				console.log(b);
			}
			var obj2 = {};
			fun3.call(obj2,5,6);   //打印a是5,b是6
			fun3.apply(obj2,[2,3]);  //打印a是2,b是3
			//这两个方法不同的是,call()方法实参需要一个一个的传,apply()方法实参需要封装到一个数组中一起传
		</script>
```

**函数的arguments对象**

> 在调用函数时,浏览器每次都会传递进两个隐含的参数:
>
> 1. this
> 2. 封装实参的对象 arguments
>    - arguments是一个类数组对象,它也可以通过索引来操作数据,也可以获取长度
>    - 在调用函数时,我们所传的实参都会封装到arguments中
>    - arguments.length可以用来获取实参的长度
>    - 我们即使不定义形参,也可以通过arguments来使用实参,只不过比较麻烦
>    - arguments[0]   表示第一个实参  arguments[1]   表示第二个实参
>    - 它里面有一个属性叫做callee,这个属性的值对应一个函数对象,就是当前正在执行的函数对象

```javascript
		<script type="text/javascript">
			function fun() {
				console.log(Array.isArray(arguments));   //打印false,判断这个arguments对象是不是一个数组对象
				console.log(arguments.length);   //打印3
				console.log(arguments[0]);  //打印2
				console.log(arguments.callee == fun);  //打印true
			}
			fun(2,"hello",true);   //这里的三个参数都会封装到arguments对象中
		</script>
```

**Date对象**

```javascript
		<script type="text/javascript">
			/*
			 * Date对象
			 * 		在JS中使用Date对象来表示一个时间
			 */
			//创建一个Date对象
			var d = new Date();
			
			/*
			 * 创建一个指定的时间对象
			 * 需要在构造函数中传递一个表示时间的字符串作为参数
			 * 日期格式  月份/日/年 时:分:秒
			 */
			var d2 = new Date("12/4/2017 12:30:10");
			console.log(d2);
			
			/*
			 * getDate()
			 * 		获取当前日期对象是几日
			 */
			var date = d2.getDate();
			console.log(date);    //打印3,表示当前日期对象是3日
			
			/*
			 * 获取当前日期对象是周几
			 * 0表示周日
			 * 1表示周一
			 */
			var day = d2.getDay();
			console.log(day);   //打印1,表示当前日期对象是周一
			
			/*
			 * 获取当前对象的月份,返回一个0-11的值
			 * 0表示1月
			 * 1表示2月
			 * 11表示12月
			 * 
			 */
			var month = d2.getMonth();
			console.log(month);   //打印11
			
			/*
			 * 获取当前日期对象的年份
			 */
			var year = d2.getFullYear();
			console.log(year);
			
			/*
			 * 获取当前日期对象的时间戳
			 * 计算机底层在保存时间时,使用的都是时间戳
			 */
			var time = d2.getTime();
			console.log(time);      //打印1512361810000
			
			/*
			 * 获取当前时间的时间戳
			 */
			var timeNow = Date.now();
			console.log(timeNow);   //打印1511441225359
		</script>
```

**Math对象**

> - Math和其他对象不同，它不是一个构造函数，它属于一个工具类不用创建对象，它里面封装了数学运算相关的属性和方法

```javascript
		<script type="text/javascript">
			console.log(Math.PI);    //打印圆周率
			//abs()可以用来计算一个数的绝对值
			console.log(Math.abs(-1)); 
			/*
			 * Math.ceil()
			 * 可以对一个数进行向上取整,小数位只要有值就自动进1
			 * Math.floor()
			 * 	可以对一个数进行向下取整,小数部分会被舍掉
			 * Math.round()
			 * 	可以对数进行四舍五入取整
			 */
			console.log(Math.ceil(1.4));   //打印2
			
			console.log(Math.floor(1.9));   //打印1
			console.log(Math.round(2.4));  //打印2
			/*
			 * Math.random()
			 * 		可以用来生成一个0-1之间的随机数
			 * 	生成一个0-x之间的随机数
			 * 		Math.round(Math.random() * x)
			 *  生成一个x-y之间的随机数
			 * 		Math.round(Math.random() * (y-x) + x)
			 */
			console.log(Math.random());   //打印0.07515802048146725
			console.log(Math.round(Math.random() * 10));  //生成一个0-10之间的随机数
			/*
			 * Math.random() 是0-1之间的随机数,乘以9就是0-9之间的随机数,加1就是1-10之间的随机数
			 */
			console.log(Math.round(Math.random() * 9) + 1);  //生成一个1-10之间的随机数
			
			/*
			 * max() 可以获取多个数中的最大值
			 * min() 可以获取多个数中的最小值
			 */
			console.log(Math.max(12,23,56,34,18));   //打印56
			console.log(Math.min(12,56,22,34,9));    //打印9
			/*
			 * Math.pow(x,y)
			 * 返回x的y次幂
			 */
			console.log(Math.pow(2,3));    //打印8,即2的3次方
			
			/*
			 * Math.sqrt()
			 * 		用于对一个数进行开方运算
			 */
			console.log(Math.sqrt(4));   //打印2
		</script>
```
**包装类**

> - 基本数据类型
>   - String Number Boolean Null Undefined
> - 引用数据类型
>   - Object
> - 在 JS 中为我们提供了三个包装类，通过这三个包装类可以将基本数据类型转换为对象
>   - String()
>     - 可以将基本数据类型字符串转换为 String 对象
>   - Number()
>     - 可以将基本数据类型的数字转换为 Number 对象
>   - Boolean()
>     - 可以将基本数据类型的布尔值转换为 Boolean 对象
> - 注意：我们在实际开发中不会使用基本数据类型的对象，如果使用基本数据类型的对象，在做一些比较时可能会带来一些不可预期的结果 

```javascript
		<script type="text/javascript">
			//创建一个Number类型的对象
			var num = new Number(3);
			console.log(num);    //打印3
			console.log(typeof num);  //打印object
			
			//创建一个String 类型的对象
			var str = new String("aa");
			console.log(str);     //打印aa
			console.log(typeof str);    //打印object
			
			//创建一个Boolean类型的对象
			var bol = new Boolean(true);
			console.log(bol);     //打印true
			console.log(typeof bol);   //打印object
			
			/*
			 * 开发中我们不会使用基本数据类型的对象
			 */
			var b = new Boolean(false);
			if (b) {    //因为这个b是一个对象,你把一个对象放在这,它会把这个对象转换成一个布尔值然后在判断,我们知道对象转换成布尔值都是true
				console.log("你好呀");
			}
			
			/*
			 * 方法和属性只能添加给对象,不能添加给基本数据类型
			 * 当我们对一些基本数据类型的值去调用属性和方法时,
			 * 		浏览器会临时使用包装类将其转换为对象,然后调用对象的属性和方法
			 * 		调用完以后,再将其转换为基本数据类型
			 */
			var s = 123;
			s = s.toString();  //这里其实调用的是String类的toString方法,代码执行到这一步的时候,浏览器发现这个s是这个基本数据类型,基本数据类型是没有属性和方法的,所以浏览器会临时把这个基本数据类型转换成包装类,然后调用包装类的方法,调用完之后销毁
			console.log(s);   //打印123
			console.log(typeof s);   //打印string
			
			var s1 = 345;
			s1.name = "hello";   //这个s1是个基本数据类型,我给基本数据类型设置一个属性,保存之后没有报错
			console.log(s1.name);    //打印undefined
			/*
			 * 原因:
			 * 我给s1设置一个name属性,但是这个s1是个基本数据类型,所以浏览器会临时把s1转成包装类,然后给这个包装类设置name属性
			 * 当我在下面打印s1.name的时候,浏览器发现这个s1是个基本数据类型,所以临时把这个s1转成一个包装类,这个包装类是一个新的类,所以你在里面找name属性,肯定找不到undefined(重点就在这两个包装类不是同一个类)
			 */
		</script>
```

**字符串相关的方法**

```javascript
		<script type="text/javascript">
			//创建一个字符串
			var str = "hello Jimmy";
			/*
			 * 字符串在底层是以字符数组的形式保存的
			 */
			//获取字符串的长度
			console.log(str.length);   //打印11
			console.log(str[0]);    //打印h
			
			/*
			 * charAt()
			 * 返回字符串中指定位置的字符
			 */
			var result = str.charAt(1);
			console.log(result);   //打印e
			
			/*
			 * charCodeAt()
			 * 获取指定位置字符的字符编码(Unicode编码)
			 */
			result = str.charCodeAt(1);
			console.log(result);     //打印101
			
			/*
			 * String.fromCharCode()
			 * 可以根据字符编码去获取字符
			 */
			result = String.fromCharCode(74);  //打印J
			console.log(result);
			
			/*
			 * concat()
			 * 可以用来连接两个或多个字符串
			 */
			result = str.concat("love","you");
			console.log(result);    //打印hello Jimmyloveyou
			
			/*
			 * indexOf()
			 * 返回字符串中字符的索引位置
			 * 如果该字符串中含有该内容,则会返回其第一次出现的索引位置
			 * 如果没有找到指定的内容,则返回-1
			 * 可以指定一个第二个参数,指定开始查找的位置
			 */
			result = str.indexOf("e");
			console.log(result);     //打印1
			result = str.indexOf("m",2);   //打印8,表示从索引为2开始查找,返回"m"第一次出现的索引位置
			console.log(result);
			
			/*
			 * slice()
			 * 可以从字符串中截取指定的内容
			 * 不会影响原字符串,而是将截取到的内容返回
			 * 参数
			 * 		第一个,开始位置的索引
			 * 		第二个,结束位置的索引(前包括,后不包括)
			 * 		也可以传递一个负数作为参数,负数的话将会从后边开始计算
			 */
			result = str.slice(0,2);
			console.log(result);   //打印he
			
			/*
			 * substring()
			 * 		可以用来截取一个字符串,和slice()类似
			 * 参数
			 * 		第一个,开始截取位置的索引
			 * 		第二个,结束位置的索引(前包括后不包括)
			 * 和slice()不同的是,这个方法不能接受负值作为参数,如果传递一个负值,则默认使用0
			 * 而且它还能自动调整参数位置,如果第二个参数小于第一个,则自动交换
			 * 
			 */
			str = "abcde";
			result = str.substring(3,0);
			console.log(result);     //打印abc  第二个参数小于第一个,自动交换位置
			
			/*
			 * substr()
			 * 用来截取字符串
			 * 参数
			 * 		第一个,截取开始位置的索引
			 * 		第二个,截取的长度
			 */
			result = str.substr(3,2);
			console.log(result);    //打印de
		</script>
```

**正则表达式**

> - 正则表达式用于定义一些字符串的规则，计算机可以根据正则表达式来检查一个字符串是否符合规则，或者将字符串中符合规则的内容提取出来

```javascript
		<script type="text/javascript">
			/*
			 * 创建正则表达式的对象
			 * 语法:
			 * 		var 变量 = new RegExp("正则表达式","匹配模式")
			 * 使用typeof检查正则对象,会返回object
			 * var reg = new RegExp("a") 这个正则表达式可以检查一个字符中是否含有a
			 * 在构造函数中可以传递一个匹配模式作为第二个参数
			 * 		这个参数可以是
			 * 					i   忽略大小写
			 * 					g   全局匹配模式
			 */
			var reg = new RegExp("a");
			console.log(typeof reg);
			var str = "a";
			//现在我想用reg这个正则来检查str这个字符串是否符合规则
			/*
			 * 正则表达式的方法：
			 * test()
			 * 		使用这个方法可以用来检查一个字符串是否符合正则表达式的规则,如果符合返回true,否则返回false
			 */
			var result = reg.test(str);
			console.log(result);   //打印true
			console.log(reg.test("dfdadd"));   //打印true
			console.log(reg.test("A"));   //打印false
			
			reg = new RegExp("a","i");      //忽略大小写
			console.log(reg.test("Ab"));   //打印true
		</script>
```

**正则语法**

```javascript
		<script type="text/javascript">
			/*
			 * 使用字面量来创建正则表达式
			 * 语法:
			 * 		var 变量 = /正则表达式/匹配模式
			 * 使用字面量的方式创建更加简单
			 * 使用构造函数创建更加灵活
			 */
			//创建一个正则表达式对象
			var reg = new RegExp("a","i");   //构造函数方式,我可以传一个正则表达式变量进来
			console.log(reg.test("A"));   //打印true
			//使用字面量的方式来创建
			var reg2 = /a/i;
			console.log(reg2.test("A"));   //打印true
			
			//创建一个正则表达式,检查一个字符串中是否含有a或b
			/*
			 * 使用 | 表示或者的意思
			 */
			var reg3 = /a|b/;
			console.log(reg3.test("anc"));  //打印true
			
			//创建一个正则表达式检查一个字符串中是否有字母
			/*
			 * []里的内容也是或的关系
			 * [ab] == a|b
			 * [a-z] 任意小写字母
			 * [A-Z] 任意大写字母
			 * [A-z] 任意字母
			 * [0-9] 任意数字
			 */
			var reg4 = /[a-z]/;
			console.log(reg4.test("t"));  //打印true
			reg4 = /[A-z]/;
			console.log(reg4.test("R"));   //打印true
			
			//检查一个字符串中是否含有abc 或 adc 或 aec
			var reg5 = /a[bde]c/;
			console.log(reg5.test("aec"));   //打印true
			
			/*
			 *[^ ]除了 
			 */
			var reg6 = /[^ab]/;
			console.log(reg6.test("ab"));    //打印fasle,找除了ab以外的
		</script>
```

**字符串和正则相关方法**

```javascript
		<script type="text/javascript">
			var str = "1a2b3c4d5e6f";
			/*
			 * split()方法
			 * 可以传递一个正则表达式作为参数,这样方法会根据正则表达式去拆分字符串
			 */
			var result = str.split(/[a-z]/);
			console.log(result[6]);  //打印[1,2,3,4,5,6,]
			
			/*
			 * search()方法
			 * 可以搜索字符串中是否含有指定内容
			 * 如果搜索到指定内容,则会返回第一次出现的索引,如果没有搜索到返回-1
			 * 它可以接收一个正则表达式作为参数,然后会根据正则表达式去检索字符串
			 * search()只会查找第一个,即使设置全局配置也没用
			 */
			str = "hello abc hello aec afc";
			result = str.search("abc");
			console.log(result);   //打印6,第一次出现abc时的索引位置
			
			/*
			 * 搜索字符串中是否含有abc 或 aec 或afc
			 */
			result = str.search(/a[bef]c/);
			console.log(result);  //打印6
			
			/*
			 * match()
			 * 可以根据正则表达式,从一个字符串中将符合条件的内容提取出来
			 * 默认情况下我们match只会找到第一个符合要求的内容,找到以后就停止检索
			 * 我们可以设置正则表达式为全局匹配模式,这样就会匹配到所有的内容
			 * 可以为一个正则表达式设置多个匹配模式,且顺序无所谓
			 * match()会将匹配到的内容封装到一个数组中返回,即使只查询到一个结果
			 */
			str = "1a2b3c4d5e6f";
			result = str.match(/[A-z]/);   //这里我想把str中所有的字母都提取出来(却发现只提取出了一个a)
			console.log(result);   //打印一个a
			result = str.match(/[A-z]/g);    //设置正则表达式为全局匹配模式
			console.log(result);   //打印a,b,c,d,e,f
			result = str.match(/[a-z]/gi); //设置正则表达式为全局匹配加忽略大小写
			console.log(result);     //打印a,b,c,d,e,f   如果有大写字母,大写字母也会被提前出来
			console.log(Array.isArray(result));  //打印true  发现这个结果是一个数组
			
			/*
			 * replace()
			 * 可以将字符串中指定内容替换为新的内容
			 * 参数:
			 * 		第一个,被替换的内容,可以接受一个正则表达式作为参数
			 * 		第二个,新的内容
			 */
			str = "1a2b3c4d5e6f";
			result = str.replace("a","@");  //我想把字符串中的a替换为@,如果字符串中有多个a,那么默认只会替换第一个位置的a
			console.log(result);  //打印1@2b3c4d5e6f
			str = "1a2a3a4b";
			result = str.replace(/a/g,"@");   //第一个参数传正则表达式,指定匹配模式为全局
			console.log(result);   //打印1@2@3@4b
		</script>
```

**正则表达式语法2**

```javascript
		<script type="text/javascript">
			//创建一个正则表达式检查一个字符串中是否含有aaa
			var reg = /aaa/;
			var str = "aaa";
			console.log(reg.test(str));   //打印true
			
			/*
			 * 量词
			 *   通过量词可以设置一个内容出现的次数
			 *   量词只对它前面的一个内容起作用
			 * 	 {n}  正好出现n次
			 *   {m,n}  出现m-n次
			 *   {m,}   出现m次以上
			 *   +    至少出现1次,相当于{1,}
			 *   *    出现0次或多次,相当于{0,}
			 *   ?    出现0次或1次,相当于{0,1}
			 */
			var reg1 = /a{3}/;
			console.log(reg1.test("aaab"));   //打印true
			reg1 = /ab{3}/;
			console.log(reg1.test("abbb"));   //打印true
			console.log(reg1.test("ababab"))  //打印false
			
			reg1 = /(ab){3}/;     //加个小括号表示ab是一个整体,一起出现3次
			console.log(reg1.test("ababab"));  //打印true
			
			reg1 = /ab{1,3}c/;    //表示匹配ab(出现1-3次都行)c
			console.log(reg1.test("abc"));  //打印true,中间的b出现1次
			console.log(reg1.test("abbc")); //打印true,中间的b出现2次,中间的b出现3次也是true,出现4次就是false了
			
			reg1 = /ab{2,}c/;   //表示中间的b需要出现2次以上
			console.log(reg1.test("abbbbbc"));    //打印true
			
			reg1 = /ab+c/;
			console.log(reg1.test("abc"));  //打印true
			console.log(reg1.test("ac"));   //打印fasle, b至少需要出现一次
			
			reg1 = /ab*c/;
			console.log(reg1.test("ac"));   //打印true
			console.log(reg1.test("abbbc"));  //打印true, b出现0次或多次
			
			reg1 = /ab?c/;
			console.log(reg1.test("abc"));   //打印true
			console.log(reg1.test("ac"));  //打印true    b可以出现0次或1次
			
			/*
			 * 检查一个字符串中是否以a开头
			 * ^ 表示开头
			 * $ 表示结尾
			 */
			reg1 = /^a/;
			console.log(reg1.test("bcd"));   //打印fasle,这个字符串不是以a开头的
			console.log(reg1.test("abc"));  //打印true
			
			reg1 = /a$/;
			console.log(reg1.test("ab"));  //打印fasle,不是以a结尾的
			console.log(reg1.test("ba"));  //打印true
			
			/*
			 * 如果在正则表达式中同时使用^ $则要求字符串必须完全符合正则表达式
			 * 如果想用以a开头或以a结尾,可以使用 /^a|a$/
			 */
			reg1 = /^a$/;
			console.log(reg1.test("a"))   //打印true
			console.log(reg1.test("aaa"));  //打印fasle
			
			/*
			 * 创建一个正则表达式,用来检查一个字符串是否是一个合法的手机号
			 * 手机号规则:
			 * 1 3 345678899
			 * 1.以1开头
			 * 2.第二位3-9任意数字
			 * 3.第三位以后任意数字9个
			 */
			var phoneRegx = /^1[3-9][0-9]{9}/;
			console.log(phoneRegx.test("a18375320276"));   //打印false
			console.log(phoneRegx.test("18375320276"));   //打印true
		</script>
```

**正则表达式语法3**

```javascript
		<script type="text/javascript">
			/*
			 * 现在我想检查一个字符串中是否含有点"."
			 * 正则表达式中"."表示任意字符
			 * 在正则表达式中使用\作为转义字符
			 * 
			 * 注意:使用构造函数时,由于它的参数是一个字符串,而"\"是字符串中转义字符,
			 * 所以在字符串中如果要使用\则需要使用\\来代替
			 */
			//按照我们之前的正则表达式应该这么写
			var reg = /./;
			console.log(reg.test("abc.ss"));   //打印true
			console.log(reg.test("abc"))   //发现没有点也打印true,其实这个"."在正则里面有特殊含义
			
			reg = /\./;
			console.log(reg.test("abc.bcd"))  //打印true
			console.log(reg.test("abc"));   //打印false
			
			//我想检查字符串里面是否包含"\"
			reg = /\\/;
//			console.log(reg.test("ab\"));   //这样写会报错,因为你在字符串里面写"\"也表示转义,斜杠把它后面的引号给转义了,所以报错
			console.log(reg.test("abc\\")); //所以在字符里面写"\"要写两个,不然它就表示转义符号
			
			var reg1 = new RegExp("\.");
			console.log(reg1.test("aa"));   //打印true
			console.log(reg1);    //打印/./     这个正则表示匹配任意字符,所以上面打印true就不奇怪了
			
			reg1 = new RegExp("\\.");
			console.log(reg1.test("aa"));   //打印fasle
			console.log(reg1);   //打印/\./      
			
			/*
			 * \w   表示任意字母、数字、下划线   相当于=>[A-z0-9_]
			 * \W   除了字母、数字、下划线           相当于=>[^A-z0-9_]
			 * \d   表示任意数字            相当于=>[0-9]
			 * \D   表示除了数字             相当于=>[^0-9]
			 * \s   表示空格
			 * \S   表示除了空格
			 * \b   表示单词边界
			 * \B   表示除了单词边界
			 */
			reg = /\w/;
			console.log(reg.test("a"));  //打印true
			console.log(reg.test("1313"));  //打印true
			console.log(reg.test("_"));  //打印true
			console.log(reg.test("@##"));  //打印false
			
			reg = /\W/;
			console.log(reg.test("a"));  //打印false
			console.log(reg.test("@#$"));  //打印true
			
			reg = /\d/;
			console.log(reg.test("123"));  //打印true
			console.log(reg.test("abc"));  //打印false
			
			reg = /\D/;
			console.log(reg.test("123"))   //打印false
			console.log(reg.test("abc"));  //打印true;
			console.log(reg.test("@#$%"));     //打印true
			
			reg = /\s/;
			console.log(reg.test("a b"));   //打印true
			
			reg = /\S/;
			console.log(reg.test("fs#%$f"));  //打印true
			console.log(reg.test(" "));   //打印false 
			
			/*
			 * 创建一个正则表达式检查一个字符串中是否含有单词child
			 */
			//平常我们可能会这么写
			reg = /child/;
			console.log(reg.test("hello child"));   //打印true
			console.log(reg.test("hello children"));   //打印true,这样就有问题了,这个字符串中的单词是children而不是child
			
			//所以我们应该这么写
			reg = /\bchild\b/;      //这个就表示child前面是一个单词边界后面也是一个单词边界
			console.log(reg.test("hello children"));   //打印false
			console.log(reg.test("hello child ren"));   //打印true
			
			/*
			 * 去掉字符串中的空格
			 * 去掉空格就是使用""来替换空格
			 */
			var str = "     he llo   ";
			str = str.replace(/\s/g,"");        //注意这里一定加一个匹配模式g,不然它只会给你替换一个空格,而不是所有空格
			console.log(str);    //打印hello
			
			//使用正则表达式去除开头的空格
			str = "     he llo   ";
			str = str.replace(/^\s/g,"");    //就是把开头的空格替换为空串
			console.log(str);     //打印"    he llo   ",你会发现明明我已经加了g全局匹配模式,但它还是只给我去了一个空格,因为我们只去除开头的空格,在h前面所有的空格中,只有第一个空格表示开头的空格,所有就只有这个空格被去除了
			
			//如果我们想去除h前面的所有空格可以这么写
			str = "     he llo   ";
			str = str.replace(/^\s*/,"");   //表示开头0个或多个空格给替换成空串
			console.log(str);   //打印"he llo   "
			
			//去除o后面的所有空格
			str = "     he llo   ";
			str = str.replace(/\s*$/,"");    //*表示0个或多个,末尾0个或多个空格替换为空串
			console.log(str);   //打印"     he llo"
			
			//使用正则表达式去除前后空格
			str = "     he llo   ";
			str = str.replace(/^\s*|\s*$/g,"");   //两个结合起来,中间加个或者
			console.log(str);   //打印he llo
		</script>
```

#### DOM

> - DOM，全称Document Object Model 文档对象模型
> - DOM是干嘛的？说白了就是可以让我们可以通过JS随心所欲的操控网页
> - 文档
>   - 文档表示的就是整个HTML网页文档
> - 对象
>   - 对象表示将网页中的每一个部分都转换为了一个对象
> - 模型
>   - 使用模型来表示对象之间的关系，这样方便我们获取对象

##### 节点

> - 节点：Node-------构成HTML文档最基本的单元
> - 常用节点分为四类
>   - 文档节点：整个HTML文档
>   - 元素节点：HTML文档中的HTML标签
>   - 属性节点：元素的属性
>   - 文本节点：HTML标签中的文本内容

##### 节点的属性

|      | nodeName  | nodeType | nodeValue |
| :--: | :-------: | :------: | :-------: |
| 文档节点 | #document |    9     |   null    |
| 元素节点 |    标签名    |    1     |   null    |
| 属性节点 |    属性名    |    2     |    属性值    |
| 文本节点 |   #text   |    3     |   文本内容    |

`这三个属性 『nodeName』 『nodeType』『nodeValue』每个节点都有  `

##### DOM简介

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
	</head>
	<body>
		<button id="btn">我是一个按钮</button>
		<script type="text/javascript">
			/*
			 * 浏览器已经为我们提供了文档节点对象,这个对象是window对象的属性
			 * 可以在页面中直接使用,文档节点代表的是整个网页
			 */
			//获取到button对象
			var btn = document.getElementById("btn");
			console.log(btn.innerHTML);   // innerHTML表示内部的HTML代码,这句话的意思就是我打印一下btn这个对象内部的HTML代码,这个btn对象内部的HTML代码就是"我是一个按钮"
		</script>
      <!--innerHTML用于获取元素内部的HTML代码,对于自结束标签这个属性没有意义(获取不到) -->
	</body>
</html>
```

##### 事件

> - 事件，就是用户和浏览器之间的交互行为
> - JavaScript  与 HTML 之间的交互是通过事件实现的

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
	</head>
	<body>
		<button id="btn">我是一个按钮</button>
		<script type="text/javascript">
			//获取按钮对象
			var btn = document.getElementById("btn");
			
			/*
			 * 可以为按钮的对应事件绑定处理函数的形式来响应事件
			 * 这样当事件被触发时,其对应的函数将会被调用
			 */
			//绑定一个单击事件
			btn.onclick = function() {    //可以理解成btn对象的onclick属性,属性值是一个函数
				alert("点击啦");
			}
		</script>
	</body>
</html>
```

##### 文档加载

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<script type="text/javascript">
			
			/*
			 * onload事件会在整个页面加载完成之后触发
			 * 为window对象绑定一个onload事件
			 * 		该事件对应的响应函数会在整个页面加载完成之后执行,这样可以确保我们的代码执行时所有的DOM对象已经加载完毕了
			 */
			window.onload = function() {
				var btn = document.getElementById("btn");
				btn.onclick = function() {
					alert("点击啦");
				};
			};
		</script>
	</head>
	<body>
		<button id="btn">我是按钮</button>
	</body>
</html>
```

##### 获取元素节点

> - 通过 document 对象调用
>   - getElementById()   通过id属性获取一个元素节点对象
>   - getElementsByTagName()    通过标签名获取一组元素节点对象
>   - getElementsByName()         通过name 属性获取一组元素节点的对象

##### 获取元素节点的子节点

> - 通过具体的元素节点调用
>   - getElementsByTagName()    方法，返回当前节点的指定标签名后代节点
>   - childNodes    属性，表示当前节点的所有子节点。这个属性会获取包括文本节点在内的所有节点，根据DOM标准，标签间空白也会当成文本节点。注意，在IE8及以下浏览器中，不会将空白文本当成子节点。
>   - firstChild      属性，表示当前节点的第一个子节点
>   - lastChild        属性，表示当前节点的最后一个子节点

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<script type="text/javascript">
			window.onload = function() {
				var city = document.getElementById("city");
				alert(city.childNodes.length);    //获取包括文本节点在内的所有子节点
				
				/*
				 * children属性可以获取当前元素的所有子元素 
				 */
				var eles = city.children;
				alert(eles.length);   //打印4
				
				var text = city.firstChild;
				alert(text);    //拿到的是一个空白文本节点,因为ul标签和li标签之间有空格
				
				/*
				 * firstElementChild 不支持IE8及以下浏览器
				 */
				var elementNode = city.firstElementChild;
				alert(elementNode.innerHTML);   //获取到的是<li id="bj">北京</li>这个节点,打印"北京"
			}
		</script>
	</head>

	<body>
		<div class="inner">
			<p>
				你喜欢哪个城市?
			</p>

			<ul id="city">
				<li id="bj">北京</li>
				<li>上海</li>
				<li>东京</li>
				<li>首尔</li>
			</ul>
		</div>
	</body>
</html>
```

##### 获取父节点和兄弟节点

> - 通过具体的节点调用
>   - parentNode       属性，表示当前节点的父节点
>   - previousSibling           属性，表示当前节点的前一个兄弟节点
>   - nextSibling       属性，表示当前节点的后一个兄弟节点 

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<script type="text/javascript">
			window.onload = function() {
				//定义一个函数,专门用来为指定元素绑定单击响应函数
				/*
				 * 参数:
				 * 		idStr 要绑定单击响应函数的对象的id属性值
				 * 		fun  事件的回调函数,当单击该元素时,该函数将会被触发
				 */
				function myclick(idStr,fun) {
					var btn = document.getElementById(idStr);
					btn.onclick = fun;
				}
				var bj = document.getElementById("bj");
				alert(bj.parentNode.innerHTML);  //获取"北京"节点的父节点,它的innerHTML内容就是ul里面的几个li节点
				/*
				 * innerText
				 * 		该属性可以获取到元素内部的文本内容
				 * 		它和innerHTML类似,不同的是它会自动将html代码去除
				 */
//				alert(bj.parentNode.innerText);   //打印"city"节点里面的文本内容,"北京 上海 东京 首尔"
				
				//获取id为dj节点的前一个兄弟节点
				var dj = document.getElementById("dj");
				alert(dj.previousSibling); //打印[object Text],"dj"节点前一个节点是一个空白文本节点
				alert(dj.previousElementSibling.innerHTML);  //获取"dj"节点前一个元素兄弟节点,(previousElementSibling这个属性IE8及以下不支持)
				alert(dj.nextSibling); //获取下一个兄弟节点,可以是文本节点
				
				//读取id为username元素的value属性值
				var username = document.getElementById("username");
				alert(username.value);   //打印Jimmy
				//为id为username元素设置value属性值
				username.value="hello";
				alert(username.value);
				
				//返回#bj的文本值
				/*
				 * 第一种方法,获取到#bj这个节点,然后直接innerHTML,因为它里面只有"北京"这个两个字
				 */
				var bj = document.getElementById("bj");
				alert(bj.innerHTML);  //打印"北京"
				/*
				 * 第二种方法
				 * "北京"是一个文本节点,而且是#bj这个元素节点的子节点,而且是第一个子节点
				 * 所以我可以先获取到这个文本节点,然后再获取这个文本节点的文本内容
				 */
				var textNode = bj.firstChild;    //获取到这个文本节点
				alert(textNode.nodeValue);    //获取这个文本节点的文本内容
			}
		</script>
	</head>

	<body>
		<div class="inner">
			<p>
				你喜欢哪个城市?
			</p>

			<ul id="city">
				<li id="bj">北京</li>
				<li>上海</li>
				<li id="dj">东京</li>
				<li>首尔</li>
			</ul>
			<input type="text" id="username" value="Jimmy" />
		</div>
	</body>
</html>
```
#### 全选练习

```html
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>全选练习</title>
<script type="text/javascript">

	window.onload = function(){
		//获取全选按钮对象
		var checkedAllBtn = document.getElementById("checkedAllBtn");
		checkedAllBtn.onclick = function() {
			//获取四个多选框的items
			var items = document.getElementsByName("items");
			//遍历items
			for (var i = 0; i < items.length; i++) {
				//通过多选框的checked属性可以来获取或设置多选框的选中状态
				items[i].checked = true;     //设置四个多选框变成选中状态
			}
		}
		//获取全不选按钮对象
		var checkedNoBtn = document.getElementById("checkedNoBtn");
		checkedNoBtn.onclick = function() {
			//获取四个多选框的items
			var items = document.getElementsByName("items");
			//遍历items
			for (var i = 0; i < items.length; i++) {
				//通过多选框的checked属性可以来获取或设置多选框的选中状态
				items[i].checked = false;     //设置四个多选框变成全不选状态
			}
		}
		//获取反选按钮对象
		var checkedRevBtn = document.getElementById("checkedRevBtn");
		checkedRevBtn.onclick = function() {
			//获取四个多选框的items
			var items = document.getElementsByName("items");
			for (var i = 0; i < items.length; i++) {
				/*//判断多选框状态
				if (items[i].checked) {
					//证明多选框已选中,则设置为没选中
					items[i].checked = false;
				} else {
					//证明多选框没选中,则设置为选中状态
					items[i].checked = true;
				}*/
				//上面代码的意思就是,如果是true,我就给它设置成false。如果是false,我就给它设置成true
				//所以可以直接这么写
				items[i].checked = !items[i].checked;   //一行代码搞定
			}
		}
		//获取提交按钮对象,将所有选中的多选框的value属性值弹出
		var sendBtn = document.getElementById("sendBtn");
		sendBtn.onclick = function() {
			var items = document.getElementsByName("items");
			for (var i = 0; i < items.length; i++) {
				if (items[i].checked) {
					alert(items[i].value);
				}
			}
		}
		
		//获取   全选/全不选 按钮对象
		var checkedAllBox = document.getElementById("checkedAllBox");
		checkedAllBox.onclick = function() {
			var items = document.getElementsByName("items");
			for (var i = 0; i < items.length; i++) {
				//全选/全不选 这个多选框,当它选中时,其余也选中,当它取消时,其余的也取消
				items[i].checked = this.checked;
			}
		}
		
		/*
		 * 如果四个多选框全都选中,则checkedAllBox也应该选中
		 * 如果四个多选框没都选中,则checkedAllBox也不应该选中
		 */
		//为四个多选框分别绑定响应函数
		var itemsInput = document.getElementsByName("items");
		for (var i = 0; i < itemsInput.length; i++) {
			itemsInput[i].onclick = function() {   //只要我一点击四个多选框中的一个我就把checkedAllBox设置为选中状态
				//将checkedAllBox设置为选中状态
				checkedAllBox.checked = true;
				for (var j = 0; j < itemsInput.length; j++) {   //再遍历四个多选框
					if (!itemsInput[j].checked) {   //如果有一个没选中,我就让checkedAllBox设置为没选中状态
						checkedAllBox.checked = false;
						//一旦进入判断,就得出结果证明不是全选状态,就不用再继续循环下去了,所以为了提升效率,加个break
						break;
					}
				}
			}
		}
		
	}
</script>
</head>
<body>

	<form method="post" action="">
		你爱好的运动是？<input type="checkbox" id="checkedAllBox" />全选/全不选 
		
		<br />
		<input type="checkbox" name="items" value="足球" />足球
		<input type="checkbox" name="items" value="篮球" />篮球
		<input type="checkbox" name="items" value="羽毛球" />羽毛球
		<input type="checkbox" name="items" value="乒乓球" />乒乓球
		<br />
		<input type="button" id="checkedAllBtn" value="全　选" />
		<input type="button" id="checkedNoBtn" value="全不选" />
		<input type="button" id="checkedRevBtn" value="反　选" />
		<input type="button" id="sendBtn" value="提　交" />
	</form>
</body>
</html>
```
##### DOM查询剩余方法

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<script type="text/javascript">
			window.onload = function() {
				//获取body标签
//				var body = document.getElementsByTagName("body")[0];
				console.log(body);     //打印[object HTMLBodyElement]
				
				//上面这种获取body标签比较麻烦
				/*
				 * 在document中有一个属性body,它保存的是body的引用
				 */
				var body = document.body;
				console.log(body);    //打印[object HTMLBodyElement]
				
				/*
				 * document.documentElement 保存的是html根标签
				 */
				var html = document.documentElement;
				console.log(html);    //打印[object HTMLHtmlElement]
				
				/*
				 * document.all 代表页面中所有的元素
				 */
				var all = document.all;
				console.log(all);   //为什么打印undefind？可能是个bug
				console.log(all.length);  //打印6,这里我可以获取到它的长度,代表页面中有6个元素
				for (var i = 0; i < all.length; i++) {
					console.log(all[i]);  //打印[object HTMLHtmlElement] [object HTMLHeadElement] [object HTMLMetaElement] [object HTMLTitleElement] [object HTMLScriptElement] [object HTMLBodyElement]  
				}
				
				/*
				 * 获取页面中所有的元素,也可以用另一种方法,跟 document.all 效果一样
				 */
				all = document.getElementsByTagName("*");
				console.log(all.length);  //打印6
				
				/*
				 * 根据元素的 class 属性值查询一组元素节点对象
				 * document.getElementsByClassName()可以根据class属性值获取一组元素节点对象
				 * 但是该方法不支持IE8及以下的浏览器
				 */
				var box1 = document.getElementsByClassName("box1")
				console.log(box1.length)  //打印2
				
				//获取页面中所有的div
				var divs = document.getElementsByTagName("div");
				console.log(divs.length);  //打印3
				
				//获取 class 为box1中的所有的 div
				/*
				 * document.querySelector()
				 * 		需要一个选择器的字符串作为参数,可以根据CSS选择器来查询一个元素节点对象
				 * 		使用该方法总会返回唯一的一个元素,如果满足条件的元素有多个,那么它只会返回第一个元素
				 */
				var divChild = document.querySelector(".box1 div");
				console.log(divChild.innerHTML);   //打印"我是box1中的div"
				
				/*
				 * 虽然 IE8 中没有getElementsByClassName()但是可以使用querySelector()代替
				 */
				var box1div = document.querySelector(".box1");
				//这里我虽然查询 class 属性值为box1的,但是这个方法只会给我返回第一个元素
				console.log(box1div.innerHTML);   //打印 我是第一个div中的元素 <div>我是box1中的div</div>
				
				/*
				 * document.querySelectorAll()
				 * 		该方法和querySelector()用法类似,不同的是它会将符合条件的元素封装到一个数组里面返回
				 * 		即使符合条件的元素只有一个,它也会返回数组
				 */
				box1 = document.querySelectorAll(".box1");
				console.log(box1.length);  //打印2,有两个class属性值为box1的元素
				var box3 = document.querySelectorAll("#box3");
				console.log(box3);   //打印[object NodeList]
			}
		</script>
	</head>
	<body>
		<div id="box3"></div>
		<div class="box1">
			我是第一个div中的元素
			<div>我是box1中的div</div>
		</div>
		<div class="box1"></div>
	</body>
</html>
```

##### DOM 增删改

```html
<!DOCTYPE html>
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>Untitled Document</title>
		<link rel="stylesheet" type="text/css" href="style/css.css" />
		<script type="text/javascript">
			window.onload = function() {
				//创建一个"广州"节点,添加到#city下
				myclick("btn01",function() {
					//创建 "广州"节点 <li>广州</li>
					//先创建li元素节点
					/*
					 * document.createElement()
					 * 可以用于创建一个元素节点对象,它需要一个标签名作为参数,将会根据标签名创建元素节点对象
					 * 并将创建好的对象作为返回值返回
					 */
					var li = document.createElement("li");
					//然后创建广州文本节点
					/*
					 * document.createTextNode()
					 * 可以用来创建一个文本节点对象
					 * 需要一个文本内容作为参数,将会根据该内容创建文本节点,并将新的节点返回
					 */
					var gzText = document.createTextNode("广州");
					
					//将gzText节点设置为li的子节点
					/*
					 * appendChild()
					 * 		向一个父节点中添加一个新的子节点
					 * 用法:
					 * 		父节点.appendChild(子节点);
					 */
					li.appendChild(gzText);
					
					//将li节点添加到city下
					var city = document.getElementById("city");
					city.appendChild(gzText);
				});
				
				//将 "广州" 节点插入到#bj节点的前面
				myclick("btn02",function() {
					//创建一个广州节点
					var li = document.createElement("li");
					var gzText = document.createTextNode("广州");
					li.appendChild(gzText);
					var bj = document.getElementById("bj");
					var city = document.getElementById("city");
					/*
					 * insertBefore()
					 * 		可以在指定的子节点前插入新的子节点
					 * 语法:
					 * 		父节点.insertBefore(新节点,旧节点)
					 */
					city.insertBefore(li,bj);
				});
				
				//使用"广州"节点替换#bj节点
				myclick("btn03",function() {
					//创建一个广州节点
					var li = document.createElement("li");
					var gzText = document.createTextNode("广州");
					li.appendChild(gzText);
					//获取bj节点
					var bj = document.getElementById("bj");
					//获取city节点
					var city = document.getElementById("city");
					/*
					 * replaceChild()
					 * 		可以使用指定的子节点替换已有的子节点
					 * 语法:
					 * 		父节点.replaceChild(新节点,旧节点)
					 */
					city.replaceChild(li,bj);
				});
				//删除#bj节点
				myclick("btn04",function() {
					var bj = document.getElementById("bj");
					var city = document.getElementById("city");
					/*
					 * removeChild()
					 * 		可以删除一个子节点
					 * 语法:
					 * 		父节点.removeChild(子节点 )
					 */
					city.removeChild(bj);
					//但是我们更常用这种方法
					bj.parentNode.removeChild(bj);   //获取到bj节点的父节点,然后通过父节点来删
				});
				//读取#city内的HTML代码
				myclick("btn05",function() {
					var city = document.getElementById("city");
					var htmls = city.innerHTML;
					alert(htmls);
				});
				//设置#bj内的HTML代码
				myclick("btn06",function() {
					var bj = document.getElementById("bj");
					bj.innerHTML = "bj";
				});
				//通过innerHTML属性,创建一个"广州"节点,添加到#city下
				myclick("btn07",function() {
					var city = document.getElementById("city");
					/*
					 * 使用innerHTML也可以完成DOM的增删改的相关操作
					 * 但是使用这种方式操作动静可能会有点大,相当于把city下面的节点先删了,然后再把新节点给它设置进去
					 * 要是你原来city下面的节点绑定了事件,使用这种方式新增节点,绑定的事件可能就没了
					 * 所以一般我们会和上面新增节点的方式,两种方式结合使用
					 */
//					city.innerHTML += "<li>广州</li>";
					
					//两种方式结合使用
					var li = document.createElement("li");   //创建li节点
					li.innerHTML = "广州";
					city.appendChild(li);   //将li节点加到city节点后面
				});
			}
			function myclick(idStr, fun) {
				var btn = document.getElementById(idStr);
				btn.onclick = fun;
			}
		</script>
	</head>
	<body>
		<div id="total">
			<div class="inner">
				<p>
					你喜欢哪个城市?
				</p>

				<ul id="city">
					<li id="bj">北京</li>
					<li>上海</li>
					<li>东京</li>
					<li>首尔</li>
				</ul>

			</div>
		</div>
		<div id="btnList">
			<div><button id="btn01">创建一个"广州"节点,添加到#city下</button></div>
			<div><button id="btn02">将"广州"节点插入到#bj前面</button></div>
			<div><button id="btn03">使用"广州"节点替换#bj节点</button></div>
			<div><button id="btn04">删除#bj节点</button></div>
			<div><button id="btn05">读取#city内的HTML代码</button></div>
			<div><button id="btn06">设置#bj内的HTML代码</button></div>
			<div><button id="btn07">创建一个"广州"节点,添加到#city下</button></div>
		</div>
	</body>
</html>
```

##### DOM删除节点练习

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<script type="text/javascript">
			window.onload = function() {
				var aAll = document.getElementsByTagName("a");
				//为每个超链接都绑定一个单击响应函数
				for (var i = 0; i < aAll.length; i++) {
					aAll[i].onclick = function() {
						/**
						 * 点击超链接以后,超链接会跳转页面,这个是超链接的默认行为
						 * 但是此时我们不希望出现默认行为,可以通过在响应函数的最后 return false来取消默认行为
						 */
						//获取当前tr
						var tr = this.parentNode.parentNode;
						//删除之前弹出一个提示框
						/*
						 * confirm()用于弹出一个带有确认和取消按钮的提示框
						 * 需要一个字符串作为参数,该字符串将会作为提示文字显示出来
						 */
						var flag = confirm("是否确认删除？");
						if (flag) {
							//删除tr
							tr.parentNode.removeChild(tr);
						}
						return false;
					}
				}
			}
		</script>
	</head>

	<body>
		<table id="employeeTable">
			<tr>
				<th>Name</th>
				<th>Email</th>
				<th>Salary</th>
				<th>&nbsp;</th>
			</tr>
			<tr>
				<td>Tom</td>
				<td>tom@tom.com</td>
				<td>5000</td>
				<td>
					<a href="javascript:;">Delete</a>
				</td>
			</tr>
			<tr>
				<td>Jerry</td>
				<td>jerry@sohu.com</td>
				<td>8000</td>
				<td>
					<a href="deleteEmp?id=002">Delete</a>
				</td>
			</tr>
			<tr>
				<td>Bob</td>
				<td>bob@tom.com</td>
				<td>10000</td>
				<td>
					<a href="deleteEmp?id=003">Delete</a>
				</td>
			</tr>
		</table>
	</body>
</html>
```

##### a的索引问题

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<script type="text/javascript">
			window.onload = function() {
				var aAll = document.getElementsByTagName("a");
				//为每个超链接都绑定一个单击响应函数
				for (var i = 0; i < aAll.length; i++) {
					alert("for循环开始执行" + i);      //会弹出0 1 2   然后for循环执行完毕
					/*
					 * for循环会在页面加载完成后立即执行
					 * 而响应函数会在超链接被点击时才执行
					 * 当响应函数执行时,for循环早已执行完毕
					 */
					aAll[i].onclick = function() {   
						alert("响应函数开始执行" + i);    //这里会永远弹出3, for循环执行完成之后,i=3,这里只有当点击的时候才会执行,而当你点击的时候for循环已经执行完了
					}
				}
			}
		</script>
	</head>
	<body>
		<a href="javascript:;">第一个超链接</a>
		<a href="javascript:;">第二个超链接</a>
		<a href="javascript:;">第三个超链接</a>
	</body>
</html>
```

##### DOM操作内联样式

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>DOM操作CSS</title>
		<style type="text/css">
			#box1{
				width: 100px;
				height: 100px;
				background-color: red;
			}
		</style>
		<script type="text/javascript">
			window.onload = function() {
				//获取box1对象
				var box1 = document.getElementById("box1");
				//为按钮绑定单击响应函数
				var btn1 = document.getElementById("btn1");
				btn1.onclick = function() {
					//修改box1的宽度
					/*
					 * 通过 JS 修改元素的样式
					 * 语法: 元素.style.样式名 = 样式值
					 * 
					 * 注意：
					 * 如果 CSS 的样式名中含有 "-",这种名称在 JS 中是不合法的,比如 background-color
					 * 所以需要将这种样式名修改为驼峰命名法,去掉"-",然后将"-"后的字母大写
					 * 像上面background-color 应该改成 backgroundColor
					 * 
					 * 我们通过style属性设置的样式都是内联样式
					 * 而内联样式有较高的优先级,所以通过 JS 修改的样式往往会立即显示
					 * 但是如果在样式中写了!important,则此时样式会有最高的优先级,即使通过 JS 也不能覆盖该样式,此时将会
					 * 导致 JS 修改样式失效
					 */
					box1.style.width="200px";
					box1.style.height="200px";
//					box1.style.background-color="yello";     这种写法会报错
					box1.style.backgroundColor="yellow";
				}
				
				//点击按钮2以后,读取元素的样式
				var btn2 = document.getElementById("btn2");
				btn2.onclick = function() {
					//读取box1样式
					/*
					 * 语法:元素.style.样式名
					 * 
					 * 注意:
					 * 通过 style 属性设置和读取的都是内联样式
					 * 无法读取样式表中的样式
					 */
					alert(box1.style.width);  //发现这个弹出来一个空,因为这里的样式是样式表中的样式不是内联样式
				}
			}
		</script>
	</head>
	<body>
		<button id="btn1">点我</button>
		<button id="btn2">点我</button>
		<div id="box1"></div>
	</body>
</html>
```

##### 获取元素的样式

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<style type="text/css">
			#box1{
				width: 100px;
				height: 100px;
				background-color: red;
			}
		</style>
		<script type="text/javascript">
			window.onload = function() {
				//点击按钮后读取box1的样式
				var box1 = document.getElementById("box1");
				var btn1 = document.getElementById("btn1");
				btn1.onclick = function() {
					/*
					 * 获取元素当前显示的样式
					 * 语法: 元素.currentStyle.样式名
					 * 它可以用来读取当前元素正在显示的样式(并不是一定读的就是样式表的样式)
					 * 如果当前元素没有设置该样式,则获取它的默认值
					 * currentStyle 只有IE浏览器支持,其他浏览器都不支持
					 */
//					alert(box1.currentStyle.width);        //在chrome或者火狐里面没反应,因为这个属性只支持IE
//					alert(box1.currentStyle.backgroundColor);
					
					/*
					 * 在其他浏览器中可以使用getComputedStyle()这个方法来获取元素当前的样式
					 * 这个方法是 window 的方法,可以直接使用
					 * 这个方法需要两个参数
					 * 		第一个参数:要获取样式的元素
					 * 		第二个参数:可以传递一个伪元素,但是我们一般都传null
					 * 该方法会返回一个对象,对象中封装了当前元素对应的样式
					 * 可以通过 "对象.样式名"来读取样式
					 * 如果获取的样式没有设置,则会获取到真实的值,而不是默认值
					 * 
					 * 但是该方法不支持 IE8 及以下浏览器
					 */
					var obj = getComputedStyle(box1,null);    //这个对象中封装了元素对应的样式
					alert(obj.width);     //假如我div没有设置width,currentStyle会获取样式的默认值,宽度的默认值是auto,而使用该方法会获取当前窗口真实值,当前窗口多大,值就为多少,而不是auto
				}
                
                
                
                
 /* ---------------------------------------解决兼容性问题 ------------------------------------*/                      
				
				/*
				 * 定义一个函数,用来获取指定元素的当前样式
				 * 参数:
				 * 		obj  要获取样式的元素
				 * 		name  要获取的样式名
				 */
				function getStyle(obj, name) {
					
					if (window.getComputedStyle) {   //这里你如果直接写getComputedStyle,在IE8里面它会直接报错,就不走下面的else里面的内容。因为你在这里直接写getComputedStyle,它就是一个变量,这个变量它会先在函数作用域里面找,找不到会去全局作用域里面找,全局作用域要是也找不到就报错。但是如果你写window.getComputedStyle这就表示getComputedStyle是window对象的一个属性,这个属性值要是没有,会报一个undefined而不是报错
						//正常浏览器的方式(具有getComputedStyle()方法)
						return getComputedStyle(obj,null)[name];
					} else {
						//IE8的方式(没有getComputedStyle()方法)
						return obj.currentStyle[name];
					}
				}
			}
		</script>
	</head>
	<body>
		<button id="btn1">点我一下</button>
		<div id="box1"></div>
	</body>
</html>
```

##### 其它样式相关的属性

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>其他样式相关的属性</title>
		<style type="text/css">
			#box1{
				width: 100px;
				height: 100px;
				background-color: red;
				padding: 10px;
				border: 10px yellow solid;
			}
			#box2{
				padding: 20px;
				background-color: greenyellow;
			}
			#box4{
				width: 200px;
				height: 300px;
				background-color: #bfa;
				overflow: auto;
			}
			#box5{
				width: 150px;
				height: 500px;
				background-color: yellow;
			}
		</style>
		<script type="text/javascript">
			window.onload = function() {
				var box1 = document.getElementById("box1");
				var btn = document.getElementById("btn");
				var box4 = document.getElementById("box4");
				btn.onclick = function() {
					/*
					 * clientWidth
					 * clientHeight
					 * 这两个属性可以获取元素的可见宽度和高度
					 * 这些属性都是不带px的,返回的都是一个数字,可以直接进行计算
					 * 会获取元素宽度和高度,包括内容区和内边距
					 * 这些属性都是只读的,不可修改
					 */
					alert(box1.clientHeight);  //弹出120,高是100px上下各10px的内边距，并不包括边框长度
//					box1.clientHeight = 300;   //你会发现不起作用,因为这个属性它不仅仅代表一个值,它包括内容区和内边距,你要是修改值它肯定不知道要改哪个值
					
					/*
					 * offsetHeight
					 * offsetWidth
					 * 获取元素的整个宽度和高度,包括内容区、内边距和边框
					 */
					alert(box1.offsetWidth);  //弹出140,包括宽100px,左右内边距各10px,左右边框各10px
					
					/*
					 * offsetParent
					 * 可以用来获取当前元素的定位父元素
					 * 会获取到离当期元素最近的开启了定位的祖先元素,如果所有的祖先元素都没有开启定位,则返回body
					 */
					var op = box1.offsetParent;
					alert(op.id);   //弹出box2
					
					/*
					 * offsetLeft  当前元素相对于其定位元素的水平偏移量
					 * offsetTop   当前元素相对于其定位元素的垂直偏移量
					 */
					alert(box1.offsetLeft);  //弹出20,box2开启了定位,box1相对于box2的水平偏移量
					
					/*
					 * scrollHeight
					 * scrollWidth
					 * 可以获取元素整个滚动区域的高度和宽度
					 */
					alert(box4.scrollHeight);   //弹出500,box5的高度溢出的父元素,我给box4设置了overflow:auto,它会出现滚动条,这个属性就是获取元素整个滚动区域的高度
					
					/*
					 * scrollLeft  可以获取水平滚动条滚动的距离
					 * scrollTop   可以获取垂直滚动条滚动的距离
					 */
					alert(box4.scrollTop);    //现在我滚动条在最上面,弹出的就是0.我要是把滚动条往下拉,这个值也会增大
					
					/*
					 * 当满足scrollHeight - scrollTop == clientHeight 说明垂直滚动条滚动到底了
					 * 当满足scrollWidth - scrollLeft == clientWidth  说明水平滚动条滚动到底了
					 * 这里可以应用于注册的时候的那些细则,只有我读完这些细则,才让你点击"我已阅读内容"的那个单选框
					 * 那怎么判断它有没有读完？就是当它把滚动条拖到底的时候我就认为你读完了这些细则
					 */
					alert(box4.clientHeight);  //打印300
					alert(box4.scrollHeight - box4.scrollTop)    //当我把垂直滚动条拖到最底面的时候,这里弹出300
				}
			}
		</script>
	</head>
	<body>
		<button id="btn">按钮</button>
		<br />
		<div id="box4">
			<div id="box5"></div>
		</div>
		<div id="box2" style="position: relative;">
			<div id="box1"></div>
		</div>
	</body>
</html>
```

##### 事件对象

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<style type="text/css">
			#areaDiv {
				border: 1px solid black;
				width: 300px;
				height: 50px;
				margin-bottom: 10px;
			}
			
			#showMsg {
				border: 1px solid black;
				width: 300px;
				height: 20px;
			}
		</style>
		<script type="text/javascript">
			window.onload = function() {
				/*
				 * 当鼠标在areaDiv中移动时,在showMsg中来显示鼠标的坐标
				 */
				//获取两个div
				var areaDiv = document.getElementById("areaDiv");
				var showMsg = document.getElementById("showMsg");
				
				/*
				 * onmousemove 该事件将会在鼠标在元素中移动时被触发
				 * 
				 * 事件对象
				 * 		当事件的响应函数被触发时(事件的响应函数都是由浏览器去调用的),浏览器每次都会将一个事件对象作为实参传递进响应函数
				 * 		那我想用这个实参怎么办呢？我可以在函括号里面定义一个形参
				 * 		在事件对象中封装了当前事件相关的一切信息,比如:鼠标的坐标,键盘哪个按键被按下,鼠标滚轮滚动的方向
				 */
				areaDiv.onmousemove = function(event) {  //假设这里我没有定义形参,浏览器也会传一个事件对象,不是说你定义形参了浏览器就给你传递事件对象,你要是没有定义形参浏览器就不给你传递事件对象,不是这样的！
					/*
					 * 在IE8中,响应函数被触发时,浏览器不会传递事件对象,
					 * 在IE8及以下浏览器中,是将事件对象作为window对象的属性保存的
					 */
					if (!event) {    //解决兼容性问题
						event = window.event;
					}
					//或者更简单的写法:
					event = event || window.event;   //我们说或者是找true,如果传了event对象,就直接返回第一个后面就不看了,如果要是没传event,那event就是undefined转换成布尔就是fasle,那么直接返回第二个
					
					/*
					 * clientX 可以获取鼠标指针的水平坐标
					 * clientY 可以获取鼠标指针的垂直坐标
					 */
					var x = event.clientX;      //IE8中应该这么写:var x = window.event.clientX
					var y = event.clientY;		//IE8中应该这么写:var y = window.event.clientY
					showMsg.innerHTML = "x="+ x +"y=" + y;
				}
			}
		</script>
	</head>

	<body>
		<div id="areaDiv"></div>
		<div id="showMsg"></div>
	</body>
</html>
```

##### div随鼠标移动小练习

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<style type="text/css">
			#box1{
				width: 100px;
				height: 100px;
				background-color: red;
			}
		</style>
		<script type="text/javascript">
			window.onload = function() {
				/*
				 * 使div可以跟随鼠标移动
				 */
//				var box1 = document.getElementById("box1");
				document.onmousemove = function(event) {   //把这个onmousemove事件加给doucument
					
					//解决兼容性问题
					event = event || window.event;
					
					/*
					 * 获取滚动条滚动的距离
					 * chrome认为浏览器的滚动条是body的,可以通过body.scrollTop来获取
					 * 火狐等浏览器认为浏览器的滚动条是html的
					 */
//					var st = document.body.scrollTop;    //chrome的方式
//					var st = document.documentElement.scrollTop;    //火狐或者IE的方式
					var st = document.body.scrollTop || document.documentElement.scrollTop; //可以合起来这么写,如果前面可以获取到不是undefined转换成布尔直接就是true,后面就不看可,如果前面是false直接返回后面的
					var sl = document.body.scrollLeft || document.documentElement.scrollLeft;
					
					//获取到鼠标坐标
					/*
					 * clientX和clientY
					 * 用于获取鼠标在当前的可见窗口的坐标
					 * div的偏移量,是相对于整个页面的
					 * 
					 * pageX和pageY可以获取鼠标相对于当前页面的坐标
					 * 但是这两个属性在IE8中不支持,所以如果需要兼容IE8,则不要使用
					 */
					var left = event.clientX;   //这里我们要做的功能是div跟随鼠标移动,当页面出现滚动条时,如果你用clientX或clientY来获取鼠标坐标,你就会发现会出问题,因为这两个属性是获取当前可见窗口坐标,而div是相对于整个页面所以鼠标和div之间就会有段距离
					var top = event.clientY;    //改成event.pageY就不会有这个问题了,都是相对于当前页面获取坐标
					box1.style.position="relative";
					box1.style.left = left + sl + "px";
					box1.style.top = top + st + "px"; 
				}
			};
		</script>
	</head>
	<body>
		<div id="box1"></div>
	</body>
</html>
```

##### 事件的冒泡

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<style type="text/css">
			#box1{
				width: 200px;
				height: 200px;
				background-color: yellowgreen;
			}
			#s1{
				background-color: yellow;
			}
		</style>
		<script type="text/javascript">
			window.onload = function() {
				
				/*
				 * 事件冒泡(Bubble)
				 * 		1.所谓的事件冒泡指的就是事件的向上传导,当后代元素上的事件被触发时,其祖先元素的相同事件也会被触发
				 * 		2.在开发中大部分情况冒泡都是有用的,如果不希望发生事件冒泡可以通过事件对象来取消冒泡
				 */
				var s1 = document.getElementById("s1");
				s1.onclick = function(event) {    //为span绑定单击响应函数
					event = event || window.event;
					alert("我是span单击响应函数");
					//取消冒泡
					//可以将事件对象的cancelBubble设置为true,即可取消冒泡
					event.cancelBubble = true;   //现在所有浏览器都支持cancelBubble这个属性了,没有兼容性问题
				}
				var box1 = document.getElementById("box1");
				box1.onclick = function() {     //为box1绑定单击响应函数
					alert("我是div单击响应函数");
				}
				var body = document.body;
				body.onclick = function() {     //为body绑定单击响应函数
					alert("我是body的单击响应函数");
				}
			}
		</script>
	</head>
	<body>
		<div id="box1">
			我是box1
			<span id="s1">我是span</span>
		</div>
	</body>
</html>
```

##### 事件的委派

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<script type="text/javascript">
			window.onload = function() {
				//点击按钮以后添加超链接
				var button = document.getElementById("btn01");
				var u1 = document.getElementById("u1");
				button.onclick = function() {
					//创建一个li
					var li = document.createElement("li");
					li.innerHTML = "<a href='javascript:;' class='link'>新超链接</a>";
					u1.appendChild(li);
				}
				
				/*
				 * 为每一个超链接都绑定一个单击响应函数
				 * 这里我们为每一个超链接都绑定了一个单击响应函数,这种操作比较麻烦
				 * 而且这些操作只能为已有的超链接设置事件,而新添加的超链接必须重新绑定(因为我页面刚加载的时候,你还没新建超链接所以肯定没绑上)
				 */
				/*var allA = document.getElementsByTagName("a");
				for (var i = 0; i < allA.length; i++) {
					allA[i].onclick = function() {     //这种方式不推荐使用,一方面麻烦,一方面性能也不好,假设我有1000个超链接那这里就要绑定1000次
						alert("aa");
					}
				}*/
				/*
				 * 我们希望,只绑定一次事件,即可应用到多个事件上,即使元素是后添加的
				 * 我们可以尝试将其绑定给元素的共同的祖先元素
				 * 
				 * 事件的委派:
				 * 		1.指将事件统一绑定给元素的共同的祖先元素,这样当后代元素上的事件被触发时,会一直冒泡到祖先元素,从而通过祖先元素的响应函数来处理事件
				 * 		2.事件委派是利用了冒泡,通过委派可以减少事件绑定的次数,提高程序的性能
				 */
				u1.onclick = function(event) {   //但是这样会有一个问题,因为我事件是绑定给ul的,所以在ul的范围内,我点击都会触发这个事件
					event = event || window.event;
					
					/*
					 * target
					 * 		event中的target表示的是触发事件的对象
					 */
//					alert(event.target);//(比如说这个onclick事件是由li触发的那么这个target就是li,要是这个onclick的事件是由a触发的,那么这个target就是a)
					//如果触发事件的对象是我们期望的元素,则执行否则不执行
					if (event.target.className == 'link') {
						alert("点击啦");
					}
				}
			}
		</script>
	</head>
	<body>
		<button id="btn01">添加超链接</button>
		<ul id="u1">
			<li><a href="javascript:;" class="link">超链接一</a></li>
			<li><a href="javascript:;" class="link">超链接二</a></li>
			<li><a href="javascript:;" class="link">超链接三</a></li>
		</ul>
	</body>
</html>
```

##### 事件的绑定

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>事件的绑定</title>
		<script type="text/javascript">
			window.onload = function() {
				/*var obj = {"dd":function(){
					alert("aa");
				}};
				alert("***" + obj.dd);*/      //判断obj这个对象里面有没有dd这个方法,这里会弹出dd的属性值
				var btn01 = document.getElementById("btn01");
				
				/*
				 * 使用 对象.事件=函数 的形式绑定响应函数,它只能同时为一个元素的一个事件绑定一个响应函数,
				 * 不能绑定多个,如果绑定了多个,则后边的会覆盖前边的
				 */
				//为btn01绑定第一个响应函数
 				/*btn01.onclick = function() {
					alert("1");
				};
				//为btn01绑定第二响应函数
				btn01.onclick = function() {     //再一次绑定就会把前面那个给覆盖掉
					alert("2");
				};*/
				
				/*
				 * addEventListener()
				 * 		--通过这个方法也可以为元素绑定响应函数
				 * 		--参数:
				 * 			1.事件的字符串,不要on
				 * 			2.回调函数,当事件触发时该函数会被调用
				 * 			3.是否在捕获阶段触发事件,需要一个布尔值,一般都传false
				 * 使用addEventListener()可以同时为一个元素的相同事件同时绑定多个响应函数,
				 * 这样当事件被触发时,响应函数将会按照函数的绑定顺序执行
				 * 这个方法不支持IE8及以下的浏览器
				 */
				/*btn01.addEventListener("click",function(){
					alert("1");
					alert(this);     //弹出[object HTMLButtonElement]
				},false);
				btn01.addEventListener("click",function(){   //会按照顺序弹出1,2
					alert("2");
				},false);*/
				
				/*
				 * attachEvent()
				 * 	--在IE8中可以使用attachEvent()来绑定事件
				 * 	--参数:
				 * 		1.事件的字符串,要on
				 * 		2.回调函数
				 * 	--这个方法也可以同时为一个事件绑定多个处理函数,不同的是它是后绑定先执行,执行顺序和addEventListener()相反
				 */
				btn01.attachEvent("onclick",function() {
					alert(this);    //弹出[object window]
					alert("1");
				});
				btn01.attachEvent("onclick",function() {
					alert("2");
				});
				btn01.attachEvent("onclick",function() {    //会弹出3,2,1
					alert("3");
				});
				
				//****************定义一个函数,用来为指定元素绑定响应函数**************************
				/*
				 * addEventListener() 中的this  指的是绑定事件的对象
				 * attachEvent() 中的this    是window对象
				 * 需要统一两个方法this
				 */
				/*
				 * 参数:
				 * 	obj 要绑定事件的对象
				 * 	eventStr 事件的字符串(要绑定哪个事件)
				 * 	callback 回调函数
				 */
				function bind(obj, eventStr, callback) {
					//判断obj这个对象里面是否有addEventListener()这个方法
					if (obj.addEventListener) {
						//大部分浏览器兼容的方式
						obj.addEventListener(eventStr,callback,false);
					} else {
						/*
						 * 解决一下this不统一问题,在attachEvent()中 this 是表示window对象
						 * callback.call(obj)   这个call()方法可以修改函数this,之前有说过 
						 */
						//IE8及以下浏览器
//						obj.attachEvent("on"+eventStr,callback);   //现在这里callback这个回调函数是由浏览器调用的,现在我不想让浏览器调用,想把调用回调函数的权利给拿回来
						//怎么拿回来呢？
						obj.attachEvent("on"+eventStr,function() {  //这里我不传callback了,传一个匿名函数,到时候浏览器调用就调用我这个匿名函数
							callback.call(obj);     //我在匿名函数里面再调用callback
							//上面那个callback是浏览器调用的,这里的这个callback就变成我们自己调用的了,浏览器调用的是
							//匿名函数,我们自己调用,想怎么调用就怎么调用,所以就可以使用call()方法修改函数的this
						});
					}
				}
			};
		</script>
	</head>
	<body>
		<button id="btn01">我是按钮</button>
	</body>
</html>
```
##### 事件的传播

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<style type="text/css">
			#box1{
				width: 300px;
				height: 300px;
				background-color: yellowgreen;
			}
			#box2{
				width: 200px;
				height: 200px;
				background-color: yellow;
			}
			#box3{
				width: 150px;
				height: 150px;
				background-color: skyblue;
			}
		</style>
		<script type="text/javascript">
			window.onload = function() {
				var box1 = document.getElementById("box1");
				var box2 = document.getElementById("box2");
				var box3 = document.getElementById("box3");
				//为三个div分别绑定单击响应函数
				bind(box1,"click",function() {    //点击box1会先弹出box1再弹出box2再弹出box3
					alert("box1");
				});
				bind(box2,"click",function() {
					alert("box2");
				});
				bind(box3,"click",function() {
					alert("box3");
				});
			};
			/*
			 * 事件的传播
			 * 	-关于事件的传播网景公司和微软公司有这不同的理解
			 * 	-微软公司认为事件应该是由内向外传播,也就是当事件触发时,应该先触发当前元素上的事件,然后再向当前元素的祖先元素上传播,
			 * 	 也就是说事件应该在冒泡阶段执行
			 * 	-网景公司认为事件应该是由外向内传播的,也就是当事件触发时,应该先触发当前元素的最外层的祖先元素的事件,然后再向内传播给后代元素
			 * 	-W3C综合了两个公司的方案,将事件传播分成了三个阶段
			 * 		1.捕获阶段
			 * 			在捕获阶段时从最外层的祖先元素,向目标元素进行事件的捕获,但是默认此时不会触发事件
			 * 		2.目标阶段
			 * 			事件捕获到目标元素,捕获结束开始在目标元素上触发事件
			 * 		3.冒泡阶段
			 * 			事件从目标元素向他的祖先元素传递,依次触发祖先元素上的事件
			 * 	-如果希望在捕获阶段就触发事件,可以将addEventListener()的第三个参数设置为true.一般情况下我们不会希望在捕获阶段触发事件,所以这个参数一般都是false
			 * 	-对于IE8及以下浏览器来说,是没有捕获阶段的,只有冒泡阶段
			 */
			function bind(obj, eventStr, callback) {
						if (obj.addEventListener) {
							obj.addEventListener(eventStr,callback,false);
						} else {
							obj.attachEvent("on"+eventStr,function() {
								callback.call(obj);
						});
					}
				};
		</script>
	</head>
	<body>
		<div id="box1">
			<div id="box2">
				<div id="box3"></div>
			</div>
		</div>
	</body>
</html>
```
##### 鼠标滚动事件

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>鼠标滚轮事件</title>
		<style type="text/css">
			#box1{
				width: 100px;
				height: 100px;
				background-color: red;
			}
		</style>
		<script type="text/javascript">
			window.onload = function() {
				/*
				 * 当鼠标滚轮向下滚动时,box1变长
				 * 当鼠标滚轮向上滚动时,box1变短
				 */
				var box1 = document.getElementById("box1");
				/*
				 * onmousewheel 鼠标滚轮滚动事件,会在滚轮滚动时触发,但是火狐不支持该属性
				 * 在火狐中需要使用DOMMouseScroll 来绑定滚动事件
				 */
				box1.onmousewheel = function(event) {
					event = event || window.event;
					//鼠标滚轮向上滚wheelDelta值为120,向下滚wheelDelta值为-120
					//wheelDelta这个值我们不看大小,只看正负
//					alert(event.wheelDelta);
					//wheelDelta这个属性火狐中不支持,在火狐中使用event.detail来获取滚动的方向
					//滚轮向上滚值为-3,滚轮向下滚值为3
//					alert(event.detail);
					
					//判断鼠标滚轮滚动的方向
					if (event.wheelDelta > 0 || event.detail < 0) {
						box1.style.height = box1.clientHeight + 10 + "px";
					} else {
						box1.style.height = box1.clientHeight -10 + "px";
					}
					
					/*
					 * 使用addEventListener()方法绑定响应函数,取消默认行为时不能使用 return false(不起作用),
					 * 需要使用event.preventDefault()来取消默认行为
					 * 但是IE8不支持event.preventDefault(),如果你直接调用的话会报错
					 */
//					event.preventDefault();  所以可以这么写
					event.preventDefault && event.preventDefault();  //如果event有preventDefault这个属性,那么我就直接用"&"后面的,没有就不用
					/*
					 * 说明:
					 * 当滚轮滚动时,如果浏览器有滚动条,滚动条会随之滚动,这是浏览器的默认行为,
					 * 如果不希望发生,则可以取消默认行为
					 */
					return false;
				};
				//为火狐绑定鼠标滚轮事件
				bind(box1,"DOMMouseScroll",box1.onmousewheel);
				
				function bind(obj, eventStr, callback) {
						if (obj.addEventListener) {
							obj.addEventListener(eventStr,callback,false);
						} else {
							obj.attachEvent("on"+eventStr,function() {
								callback.call(obj);
						});
					}
				};
			}
		</script>
	</head>
	<body>
		<div id="box1"></div>
	</body>
</html>

```

##### 键盘事件

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>键盘事件</title>
		<script type="text/javascript">
			window.onload = function() {
				/*
				 * 键盘事件
				 * 		onkeydown
				 * 			1.按键被按下事件
				 * 			2.对于onkeydown来说如果一直按着某个键不松手,则事件会一直触发
				 * 			3.当onkeydown连续触发时,第一次和第二次之间会间隔稍微长一点,其它的会非常快,这种设计是为了防止误操作的发生
				 * 		onkeyup
				 * 			1.按键被松开事件
				 * 键盘事件一般都会绑定给一些可以获取到焦点的对象或者是document
				 */
				document.onkeydown = function(event) {
					event = event || window.event;
					/*
					 * 可以通过keyCode来获取按键的编码
					 * 通过它可以判断哪个按键被按下
					 * 除了keyCode,事件对象中还提供了几个属性
					 * 	altKey
					 * 	ctrlKey
					 * 	shiftKey
					 * 这三个用来判断 alt ctrl 和 shift 是否被按下,如果按下则返回 true,否则返回 false
					 */
					//比如我现在想知道ctrl的keyCode值是多少,那我就可以这样:
//					console.log(event.keyCode);    //然后你按一下ctrl就会出来它的编码
					if (event.keyCode === 89) {   //y的unicode编码是89(其实就是unicode码)
						console.log("y键被按下了");
					}
				}
				var input = document.getElementsByTagName("input")[0];
				input.onkeydown = function(event) {
					event = event || window.event;
					/*
					 * 在文本框中输入内容,属于onkeydown的默认行为
					 * 如果在onkeydown中取消了默认行为,则输入的内容,不会出现在文本框中
					 */
					//利用这个特性我们可以做一个小功能,比如我们让文本框中不能输入数字
					//数字的keyCode是 48-57    (0-9)
					if (event.keyCode >= 48 && event.keyCode <= 57) {
						return false;    //这样你会发现在文本框中输入数字是没有反应的
					}
				}
			}
		</script>
	</head>
	<body>
		<input type="text" />
	</body>
</html>
```
##### 小练习，移动键盘方向键实现DIV随光标移动

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<style type="text/css">
			#box1{
				width: 100px;
				height: 100px;
				background-color: red;
				position: absolute;
			}
		</style>
		<script type="text/javascript">
			window.onload = function() {
				var box1 = document.getElementById("box1");
				var speed = 10;
				document.onkeydown = function(event) {
					switch (event.keyCode){
						case 37:
							box1.style.left = box1.offsetLeft - speed + "px";
							break;
						case 39:
							box1.style.left = box1.offsetLeft + speed + "px";
						case 38:
							box1.style.top = box1.offsetTop - speed + "px";
						case 40:
							box1.style.top = box1.offsetTop + speed + "px";
					}
				}
			}
		</script>
	</head>
	<body>
		<div id="box1"></div>
	</body>
</html>

```

##### BOM

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<script type="text/javascript">
			/*
			 * BOM
			 * 		1.浏览器对象模型
			 * 		2.BOM可以使我们通过JS来操作浏览器
			 * 		3.在BOM中为我们提供了一组对象,用来完成对浏览器的操作
			 * BOM对象
			 * 		1.Window
			 * 			代表的是整个浏览器窗口,同时window也是网页中的全局对象
			 * 		2.Navigator
			 * 			代表的是当前浏览器的信息,通过该对象可以来识别不同的浏览器
			 * 		3.Location
			 * 			代表的是当前浏览器的地址栏信息,通过Location可以获取地址栏信息,或者操作浏览器跳转页面
			 * 		4.History
			 * 			代表浏览器的历史记录,可以通过该对象来操作浏览器的历史记录。由于隐式原因,该对象不能获取到具体的历史记录,
			 * 			只能操作浏览器向前或向后翻页。而且该操作只在当次访问时有效(也就是说你浏览器关了,再次打开,就无效了)
			 * 
			 * 这些BOM对象在浏览器中都是作为window对象的属性保存的,可以通过window对象来使用,也可以直接使用
			 */
			console.log(window.navigator);   //或者直接写console.log(navigator);
			console.log(location);
		</script>
	</head>
	<body>
	</body>
</html>
```

##### Navigator

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<script type="text/javascript">
			
			/*
			 * Navigator
			 * 	- 代表的当前浏览器的信息，通过该对象可以来识别不同的浏览器
			 * 	- 由于历史原因，Navigator对象中的大部分属性都已经不能帮助我们识别浏览器了
			 * 	- 一般我们只会使用userAgent来判断浏览器的信息，
			 * 		userAgent是一个字符串，这个字符串中包含有用来描述浏览器信息的内容，
			 * 		不同的浏览器会有不同的userAgent
			 * 
			 * 火狐的userAgent
			 * 	Mozilla/5.0 (Windows NT 6.1; WOW64; rv:50.0) Gecko/20100101 Firefox/50.0
			 * 
			 * Chrome的userAgent
			 *  Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.82 Safari/537.36
			 * 
			 * IE8
			 * 	Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/7.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)
			 * 
			 * IE9
			 * 	Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/7.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)
			 * 
			 * IE10
			 * 	Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.1; WOW64; Trident/7.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)
			 * 
			 * IE11
			 * 	Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E; rv:11.0) like Gecko
			 * 	- 在IE11中已经将微软和IE相关的标识都已经去除了，所以我们基本已经不能通过UserAgent来识别一个浏览器是否是IE了
			 */
			
			//alert(navigator.appName);
			
			var ua = navigator.userAgent;
			
			console.log(ua);
			
			if(/firefox/i.test(ua)){
				alert("你是火狐！！！");
			}else if(/chrome/i.test(ua)){
				alert("你是Chrome");
			}else if(/msie/i.test(ua)){
				alert("你是IE浏览器~~~");
			}else if("ActiveXObject" in window){
				alert("你是IE11，枪毙了你~~~");
			}
			
			/*
			 * 如果通过UserAgent不能判断，还可以通过一些浏览器中特有的对象，来判断浏览器的信息
			 * 比如：ActiveXObject
			 */
			/*if("ActiveXObject" in window){
				alert("你是IE，我已经抓住你了~~~");
			}else{
				alert("你不是IE~~~");
			}*/
			
			/*alert("ActiveXObject" in window);*/
		</script>
	</head>
	<body>
		
	</body>
</html>
```
##### History

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>history</title>
		<script type="text/javascript">
			/*
			 * History
			 * 		对象可以用来操作浏览器向前或向后翻页
			 */
			
			/*
			 * length
			 * 		属性,可以获取到当次访问的链接数量(你页面一关它就又重新计数了)
			 */
			alert(history.length);  
			window.onload = function() {
				var btn = document.getElementById("btn");
				btn.onclick = function() {
					/**
					 * back()
					 * 		可以用来回退到上一个页面,作用和浏览器的回退按钮一样
					 */
					history.back();    //现在只要我一点击按钮,页面就会回退到上一个页面
					
					/**
					 * forward()
					 * 		可以跳转到下一个页面,作用和浏览器的前进按钮一样
					 */
					history.forward();
					
					/**
					 * go()
					 * 		可以用来跳转到指定页面
					 * 		它需要一个整数作为参数
					 * 			1:表示向前跳转一个页面,相当于forward()
					 * 	`		2:表示向前跳转两个页面
					 * 			-1:表示向后跳转一个页面
					 * 			-2:表示向后跳转两个页面
					 */
					history.go(2);
				};
			};
		</script>
	</head>
	<body>	
		<button id="btn">我是按钮</button>
	</body>
</html>
```

##### Location

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<script type="text/javascript">
			/*
			 * Location
			 * 		该对象封装了浏览器地址栏信息
			 */
			window.onload = function() {
				var btn = document.getElementById("btn");
				btn.onclick = function() {
					//如果直接打印location,则可以获取到地址栏的信息(当前页面的完整路径)
					alert(location);   //弹出http://127.0.0.1:8020/jsfour/location.html
					//如果直接将location属性修改为一个完整的路径或相对路径,则页面会自动跳转到该路径
					location = "http://www.baidu.com";
					
					/**
					 * assign()
					 * 		用来跳转到其他页面,作用和直接修改location一样
					 *      会生成历史记录,你可以点击回退按钮回退到上一个连接的页面
					 */
					location.assign("http://www.baidu.com");
					
					/**
					 * reload()
					 * 		用于重新加载当前页面,作用和刷新按钮一样
					 * 		如果在方法中传递一个true作为参数,则会强制清空缓存刷新
					 */
					location.reload(true);  //强制情况缓存刷新
					
					/*
					 * replace()
					 * 		可以使用一个新的页面替换当前页面,调用完毕也会跳转页面
					 * 		但是它不会生成历史记录(即不能使用回退按钮进行回退)
					 */
					location.replace("http://www.baidu.com");
				}
			};
		</script>
	</head>
	<body>
		<button id="btn">我是按钮</button>
	</body>
</html>
```
##### 定时器

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<script type="text/javascript">
			window.onload = function() {
				var count = document.getElementById("count");
				for (var i = 0; i < 10; i ++) {
					count.innerHTML = i; 
				}
				
				/**
				 * 定时调用,可以将一个函数,每隔一段时间执行一次
				 * 参数:
				 * 		第一个参数:回调函数,该函数会每隔一段时间被调用一次
				 * 		第二个参数:每次调用间隔的时间,单位是毫秒
				 * 返回值:
				 * 		返回一个Number类型的数据,这个数字用来作为定时器的唯一标识
				 */
				var num = 1;
				var timer = setInterval(function(){
					count.innerHTML = num++;
					if (num == 11) {
                      
                      /*
                      * clearInterval()可以接收任何类型的参数,如果参数是一个有效的定时器标识,则停止对应的定时器;如果参数是一个无效的标识,则什么也不做
                      */
                      
						clearInterval(timer); //用来关闭一个定时器,需要一个定时器标识作为参数,这样将3关闭标识对应定时器
					}
				},1000);    //让这个函数每隔1秒执行一次
			};
		</script>
	</head>
	<body>
		<h1 id="count"></h1>
	</body>
</html>
```

##### 定时器小练习

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<script type="text/javascript">
			window.onload = function() {
				var img1 = document.getElementById("img1");
				var imgArray = ["img/1.jpg","img/2.jpg","img/3.jpg","img/4.jpg"];
				var index = 0;
				var btn01 = document.getElementById("btn01");
				var btn02 = document.getElementById("btn02");
				/**
				 * 目前我每点一次按钮,就会开启一个定时器,点击多次就会开启多个定时器,就会导致图片切换速度过快
				 */
				
				var timer;
				
				//在开启定时器之前需要将当前元素上的其它定时器关闭（如果不关的话我点很多次，就会开启很多个定时器）
				clearInterval(timer);
				btn01.onclick = function() {
					timer = setInterval(function(){      //因为这里返回的都是同一个timer,所以如果我点击很多次,这个time的值就是最后一次点击的值,导致你关闭定时器的时候也只能关最后一个定时器
					index++;
					if (index == imgArray.length) {
						index = 0;
					}
					img1.src = imgArray[index];
				},1000);
				};
				btn02.onclick = function() {
					clearInterval(timer);
				}
				
			}
		</script>
	</head>
	<body>
		<img id="img1" src="img/1.jpg"/>
		<br />
		<button id="btn01">开始</button>
		<button id="btn02">关闭</button>
	</body>
</html>
```

##### 延时调用

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>延时调用</title>
		<script type="text/javascript">
			window.onload = function() {
				var num = 1;
				//开启一个定时器
				/*setInterval(function(){
					console.log(num++);
				},3000);*/
				
				/*
				 * 延时调用
				 * 		延时调用一个函数不马上执行,而是隔一段时间以后再执行,而且只会执行一次。
				 * 延时调用和定时调用的区别就是,定时调用会执行多次,而定时调用只会执行一次
				 * 
				 * 延时调用和定时调用实际上可以互相代替的(意思就是我一个定时调用,我只让它调用一次然后就给它关了,它不就变成延时调用了嘛;一个延时调用我让它调用多次,它不就变成定时调用了嘛)
				 * 在开发中可以根据自己的需要去选择
				 */
				var timer = setTimeout(function(){
					console.log(num++);
				},3000);
				
				//使用 clearTimeout() 来关闭一个延时调用
				clearTimeout(timer);
			}
		</script>
	</head>
	<body>
	</body>
</html>

```



##### 类的操作

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>类的操作</title>
		<style type="text/css">
			.b1{
				width: 100px;
				height: 100px;
				background-color: red;
			}
			.b2{
				width: 200px;
				height: 200px;
				background-color: yellow;
			}
		</style>
		<script type="text/javascript">
			window.onload = function() {
				var box = document.getElementById("box");
				var btn01 = document.getElementById("btn01");
				btn01.onclick = function() {
					//修改box的样式
					/*
					 * 通过 style 属性来修改元素的样式,每修改一个样式,浏览器就需要重新渲染一次页面
					 * 这样执行的性能是比较差的,而且这种形式修改多个样式时,也是不方便的(比如我有30个样式要修改,你就要修改30行)
					 */
					/*box.style.width="200px";
					box.style.height="200px";
					box.style.backgroundColor="yellow";*/
					
					//现在我希望一行代码,可以同时修改多个样式
					//采取方法:修改box的class属性(我把要修改的样式写在一个class名为b2的css样式表中)
					/*
					 * 我们可以通过修改元素的class属性来间接修改样式,这样的话我们只需要修改一次,就可以同时修改多个样式
					 * 此时浏览器也只需渲染一次页面,性能比较高
					 * 并且这种方式,可以使js和css进一步分离(原来我是直接通过js去改具体的css样式,现在js只去改class属性名)
					 */
					box.className="b2";
					//如果想在原来class基础上添加,可以这么写
					box.className += " b2";   //注意这里要有个空格,不然到时候你两个class会连在一起
				};
			};
			//定义一个函数,用来向一个元素中添加指定的 class 属性值
			/*
			 * 参数:
			 * 		obj:要添加class 属性的元素
			 * 		cn: 要添加的class值
			 */
			function addClass(obj,cn){
				//检查obj中是否含有cn
				if (!hasClass(obj,cn)) {
					obj.className += " " + cn;   //加个空格是因为防止你新添加的class值和之前的class值连在一起
				}
			};
			
			/*
			 * 判断一个元素中是否含有指定的 class 属性值
			 * 如果有该 class 则返回true,没有返回 false
			 */
			function hasClass(obj,cn) {
				var reg = new RegExp("\\b" + cn + "\\b");
				return reg.test(obj.className);
			};
			
			/*
			 * 移除class属性值 
			 */
			function removeClass(obj,cn) {
				//创建一个正则表达式
				var reg = new RegExp("\\b" + cn + "\\b");  //   \b表示单词边界,加这个就是防止我想匹配b2的时候把b2016给匹配到了
				//删除class
				obj.className = obj.className.replace(reg,"");   //用空串去替换正则表达式匹配到的内容
			};
			
			/*
			 * 可以用来切换一个类
			 * 如果元素中有该类,则删除
			 * 如果元素中没有该类,则添加
			 */
			function toggleClass(obj,cn) {
				if (hasClass(obj,cn)) {
					removeClass(obj,cn);
				} else {
					addClass(obj,cn);
				}
			}
		</script>
	</head>
	<body>
		<button id="btn01">点击修改box的样式</button>
		<br /><br />
		<div id="box" class="b1">
			
		</div>
	</body>
</html>

```












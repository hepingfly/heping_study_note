# GO语言笔记

### Golang 开山篇

#### 一、Golang 简介

#####  1、Golang 是什么意思

Go 语言，也就是 Golanguage，但是这个单词太长了，所以我们一般简写成 Golang。

##### 2、Golang 核心编程将来会用在哪些方面

> - 区块链研发工程师
>
> 如果你想从事区块链相关工作，那么你就要学一下 Golang ，因为区块链主流的开发语言就是 Golang。简单说一下区块链，说白了区块链就是一种分布式账本技术，它是一种互联网技术，核心思想就是去中心化，让人人都可以参与数据库的记录。
>
> - Go 服务器端/游戏软件工程师
>
> 目前服务器端开发主要用的是 C 或者 C++ 偏多一点，这里说一下，只要是 C 或者 C++ 能做的事情，Go 都可以做，而且可以做的更好。因为 Go 语言的设计者，以前设计过 C 语言，而 Go 语言是后设计的，所以在设计 Go 的时候，它会在某些方面把 C 语言的设计理念给优化一下。
>
> - Golang 分布式/云计算

------

### Golang 概述

#### 一、程序的基本概念

程序：就是完成某个功能的指令的集合。

#### 二、Go语言发展简史

##### 1、Go语言的核心开发团队

① Ken Thompson（肯-汤普森），这个人是 Unix 的作者之一，而且也是 C 语言的发明人

②Rob Pike（罗布-派克），它也参与过 Unix 的设计，同时这个人还是个运动健将，拿过 1980 年奥运会的射箭银牌，而且他还是一个天文学家

③Robert Griesemer，这个人曾协助制作 Java 的 HotSpot 编译器，和 Chrome 浏览器的 JavaScript 引擎

##### 2、Google 为什么要创造 Go 语言

①计算机**硬件技术**更新频繁，性能提高很快，目前主流的编程语言发展明显落后于硬件，不能合理利用**多核多 CPU** 的优势提升软件系统性能。

②软件系统复杂度越来越高，维护程度也越来越高，目前缺乏一个足够简洁高效的编程语言

③企业运行维护很多 c/c++ 的项目，c/c++ 程序运行速度虽然很快，但是编译速度却很慢，同时还存在内存泄露等一系列的困扰需要解决。

#### 三、Go 语言的特性

##### 1、Go 语言的特点

Go 语言可以既能达到静态编译语言的安全和性能，又能达到动态语言开发和维护的高效率，使用一个表达式来形容 Go 语言， Go = C + Python ，说明 Go 语言既有 C 静态语言程序的运行速度，又能达到 Python 动态语言的快速开发。

Go 语言除了以上描述的，还具有如下特点：

> 1. 从 C 语言中继承了很多理念，包括表达式语法，控制结构，基础数据类型，调用参数传值，**指针**等，也保留了和 C 语言一样的编译执行方式及弱化的指针
>
> 2. 引入包的概念，Go 语言的每一个文件都要归属于一个包，而不能单独存在。（就和 Java 中每一个文件上面都会有一个 package 一样）
>
> 3. 垃圾回收机制，内存自动回收，不需要开发人员管理
>
> 4. 天然并发（Go 语言的一个很重要特点）
>
>    因为我们说 Go 语言在刚开始设计的时候，它本身就是冲着能够有效利用多核多 CPU 这个特点，这个特点就是为了处理高并发。
>
>    - 所以说 Go 语言在语言层面就支持并发，而且实现简单
>    - goroutine，轻量级线程，可实现高并发处理，高效利用多核
>    - 基于 CPS 并发模型（Communicating Sequential Processes）实现
>
> 5. 吸收了管道通信机制，形成了 Go 语言特有的管道 channel，通过管道 channel ，可以实现不同的 goroute 之间的相互通信。
>
> 6. 函数可以返回多个值
>
> 7. 新的创新：比如：切片 slice 、延时执行 defer 等
>
> 所以记住一句话，Go 语言对处理高并发非常到位

##### 2、小知识点

> 如果你想要远程连接 mac 系统，你需要通过 ssh 服务进行连接。
>
> mac 本身安装了 ssh 服务，默认情况下不会开机自启
>
> 1、启动 sshd 服务
>
> `sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist`
>
> 2、停止 sshd 服务
>
> `sudo launchctl unload -w /System/Library/LaunchDaemons/ssh.plist`
>
> 3、查看是否启动
>
> `sudo launchctl list | grep ssh`

#### 四、Go 语言开发环境的搭建

##### 1、Windows 下 Go 环境的搭建

搭建 Go 环境其实就是安装和配置 SDK。

①SDK 基本介绍：

1、SDK 全称（Software Development Kit）软件开发工具包

2、SDK 是提供给开发人员使用的，其中包含了对应开发语言的工具包

②SDK 下载

1、Go 语言官网下载，golang.org 需要翻墙不然无法访问

2、Golang 中国下载，下载地址：https://www.golangtc.com/download

![](http://pd59gqjb6.bkt.clouddn.com/go%E8%AF%AD%E8%A8%80sdk%E8%AF%B4%E6%98%8E.png)

`注：windows 下建议下载 zip 格式，直接解压就可以使用`

③配置 SDK 环境变量

配置方式和配置 Java_HOME 类似，配置完后打开 cmd 窗口输入 「go verison」 验证一下是否出现  go 的版本信息

##### 2、Linux 下 Go 环境搭建

主要就是把 SDK 解压缩，然后配置环境变量。

Linux 下配置 Go 环境变量

> 在 /etc/profile 文件下添加下面三条语句
>
> `export GO_HOME=/opt/go`
>
> `export PATH=$GO_HOME/bin:$PATH` 
>
> `export GOPATH=$HOME/goproject`

##### 3、MAC 下 Go 环境搭建

主要也是先把 SDK 解压缩，然后配置环境变量

① SDK 解压缩很简单，直接把上传好的 tar 包，使用 tar -xzvf 命令解压即可。

②配置环境变量

mac 下配置环境变量同样是在 /etc/profile 文件，但是要注意的是，修改这个文件需要使用 root 用户，所以如果你使用的是 mac 操作系统，需要「启用 root 用户」

在 /etc/profile 文件下添加下面三条语句

> `export GO_HOME=$HOME/go_dev/go`
>
> `export PATH=$GO_HOME/bin:$PATH` 
>
> `export GOPATH=$HOME/goproject`

#### 五、Go 语言快速开发和目录结构说明

##### 1、我们在开发项目时，Go 的目录结构处理

![o开发目录结](http://pd59gqjb6.bkt.clouddn.com/go%E5%BC%80%E5%8F%91%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84.png)

目录结构大致为：

一开始在最外层建一个总的目录，准备用来放 go 所有相关的文件，然后在里面再建一个 「src」目录，这个目录里面放源码相关的，「src」里面再建一个 「go_code」文件夹，用来放 go 相关代码。「go_code」文件夹下面就可以来建一个个你的项目的文件夹了，这些文件加都用来存放你的一个个项目，如：project01、project02

所以一般一个稍微标准的目录结构就是：「E:\workspace-goproject\src\go_code\project01」

##### 2、Go 开发 hello world 程序

```go
//要求开发一个 hello.go 程序,可以输出 'hello world'
package main
import "fmt"

func main() {
	fmt.Println("hello world")
}
```

**说明：**

①上面的程序写好了之后，生成的是一个 hello.go 文件，我们可以通过 「go build 」命令对该 go 文件进行「编译」，生成 exe 文件（hello.exe 文件）然后我们只需要运行 hello.exe 文件即可。

②除了上面的方法，我们还可以通过 「go run」命令直接运行 hello.go 文件（相当于编译的过程在底层给你做了）

#### 六、Golang 执行流程分析

##### 1、对源码编译后再执行，Go 的执行流程

![golang执行流程](http://pd59gqjb6.bkt.clouddn.com/golang%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B.png)

##### 2、如果是对源码直接执行 go run 指令，Go 的执行流程

![golang执行流程2](http://pd59gqjb6.bkt.clouddn.com/golang%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B2.png)

##### 3、上面两种执行流程方式区别

> 1、如果我们先编译成了可执行文件，那么我们可以将该可执行文件拷贝到没有 go 开发环境的机器上，仍然可以运行。
>
> 2、如果我们是直接 go run 源代码，那么如果要在另外一台机器上运行，也需要 go 开发环境，否则无法执行。
>
> 3、在编译时，编译器会将程序运行依赖的库文件包含在可执行文件中，所以，可执行文件变大了很多。

注：

`可以使用 go build -o myhello.exe hello.go 来指定生成的可执行文件名`

#### 七、Go 语法要求和注意事项

> 1、Go 源文件以 "go" 为扩展名
>
> 2、Go 应用程序的执行入口是 main() 函数
>
> 3、Go 语言严格区分大小写
>
> 4、Go 方法由一条条语句构成，每个语句后不需要分号（Go 语言会在每行后自动加分号），这也体现 Golang 的简洁性
>
> 5、Go 编译器是一行行进行编译的，因此我们一行就写一条语句，不能把多条语句写在同一行，否则报错
>
> 6、Go 语言==定义的变量==或者 ==import 的包==如果没有用到，代码不能编译通过
>
> 7、大括号都是成对出现的，缺一不可

八、Go 语言转义字符

> - 「\t」：制表符
> - 「\n」：换行符
> - 「\\」：一个 "\"
> - 「\"」：一个 "
> - 「\r」：一个回车

```go
package main

import "fmt"

func main() {
	//   \r表示回车,从当前行的最前面开始输出,覆盖掉以前的内容
	fmt.Println("天龙八部\r张飞")   //打印结果是 张飞八部
}
```

#### 八、Go 语言中的注释

Go 支持 C 语言风格的 /* */块注释，也支持 C++ 风格的行注释。行注释更通用，块注释主要用于针对 包的详细说明或者屏蔽大块的代码

#### 九、Go 语言代码风格和规范

1、Go 官方推荐使用行注释来注释整个方法和语句（这点从 Go 源码中也可以看到，用的都是行注释）

2、代码中要有缩进和空白（可以在命令行使用 gofmt -w main.go 对一个 go 文件进行格式化）

3、go 语言中大括号的写法

```go
package main
import "fmt"
//这种写法是正确的
func main() {
	fmt.Println("天龙八部\r张飞")
}
//---------------------------------------------------------------
package main
import "fmt"
//这种写法是错误的,main后面的大括号不能换行写
func main() 
{
	fmt.Println("天龙八部\r张飞")
}

//在别的语言中,关于大括号的写法,上面两种方式都是可以的,但是在 go 中不行
//go 语言设计者的设计思想就是,一个问题尽量只有一种解决方法,所以规定的大括号只有一种写法,避免了风格不统一
```

4、go 代码中一行最长不超过 80 个字符，超过的请使用换行展示，尽量保持格式优雅

#### 十、Go 官方编程指南

1、Go 语言官网为 https://golang.org/

2、https://golang.org/pkg/    可以查看 Golang 所有包下的函数和使用

#### 十一、Golang 标准库 API 文档

1、Go 语言提供了大量的标准库，因此 google 公司也为这些标准库提供了相应的 API 文档，用于告诉开发者如何使用这些标准库，以及标准库包含的方法。

2、Golang 中文网 在线标准库文档 ：   https://studygolang.com/pkgdoc

3、在 Go 语言中你引入的包的源代码都是可以找到的，在你安装的 sdk 的 src 目录下



------

### DOS 基本介绍

#### 一、 DOS 常用命令介绍

##### 1、说一下 Dos 操作的基本原理

DOS 的全称：Disk Operating System 磁盘操作系统

我们使用 win + R 输入 cmd 命令，会打开一个黑窗口，那个黑窗口是一个终端，并不是 DOS 系统，可以说是 DOS 终端，用来操作 DOS 的。我们在终端写一个操作指定，这个操作指定会发送到 DOS 系统，然后 DOS 系统解析这个指令，做出相应的操作。

##### 2、 Dos 一些基本命令

dos 创建目录命令： md aa bb     这样就会创建 aa 和 bb 两个文件夹

dos 删除目录命令：rd /q/s 目录名     这样就会把一个目录给删除掉

dos 新建或追加内容到文件中： echo hello > hello.txt      这样就会新建一个 hello.txt 然后把 hello 追加到这个文件中

dos 拷贝文件命令： copy  hello.txt  bb\hello1.txt        这样就把 hello.txt 文件拷贝到 bb 文件夹下并且重新命名叫 hello1

dos 移动文件命令：move hello.txt bb           这样就把 hello.txt 这个文件移动到了 bb 文件夹下

dos 删除文件命令 ：   del hello.txt     这样就会把 hello.txt 这个文件给删掉

dos 清屏指定：   cls     这样就会清屏

dos 退出指令：    exit    退出 dos 窗口，或者你也可以直接点击窗口上的叉

------

### GO 核心编程

#### 一、变量的介绍和使用

##### 1、定义变量和使用变量

```go
package main

import "fmt"

func main() {
	//定义变量
	var i int
	//给 i 赋值
	i = 10
	//使用变量
	fmt.Println("i=",i)
}
```

##### 2、Golang 变量使用的三种方式

①指定变量类型，声明后不赋值，使用默认值

②根据自行判定变量类型(类型推导)

③省略 var ,注意 := 左侧的变量不应该是已经声明过的,否则会导致编译错误

```go
package main
import "fmt"

func main() {
	//第一种：指定变量类型,声明后若不赋值,使用默认值
	var i int
	fmt.Println("i=",i)   //打印 i=0

	//第二种：根据自行判定变量类型(类型推导)
	var num = 10.11
	fmt.Println("num=",num)

	//第三种：省略 var ,注意 := 左侧的变量不应该是已经声明过的,否则会导致编译错误
	name := "tom"   //就等价于 var name string = "tom"
	fmt.Println("name=",name)
}
```

##### 3、Golang 一次性定义多个变量

```go
package main
import "fmt"

//定义全局变量
var c1 = 100
var c2 = 200
var name2 = "lucy"

var (
	c3 = 300
	c4 = 400
	name3 = "kimi"
)//这里就是同时声明三个变量,和上面一个一个声明是等价的

func main() {
	//golang 一次性声明多个变量

	//方式一
	var n1,n2,n3 int
	fmt.Println("n1=", n1, "n2=", n2, "n3=", n3) //打印 n1= 0 n2= 0 n3= 0

	//方式二
	var a1, name, a3 = 10, "tom",21.11
	fmt.Println("a1=", a1, "name=", name, "a3=", a3) //打印a1= 10 name= tom a3= 21.11

	//方式三
	b1, name1, b3 := 66, "kate", 88
	fmt.Println("b1=", b1, "name1=", name1, "b3=", b3) //b1= 66 name1= kate b3= 88
}
```

##### 4、变量使用注意事项

> 1、该区域的数据值可以在同一类型范围内不断变化。
>
> 意思就是说，变量可以重复赋值，但是变量类型不能改
>
> 2、变量在同一个作用域内不能重名
>
> 可以理解为在一个函数或者代码块中变量名不能重复
>
> 3、变量=变量名 + 值 + 数据类型
>
> 4、Golang 的变量如果没有赋初值，编译器就会使用默认值

##### 5、变量的声明、初始化和赋值

> - 声明变量
>
> 基本语法： var 变量名 数据类型        如：var age int  其中数据类型可以省略，直接  var age
>
> - 初始化变量
>
> var age int = 12     或者省略数据类型     var age = 12
>
> - 给变量赋值
>
> 比如你先声明了变量    var num int     然后再给变量赋值    num = 80
>
> 

##### 6、Golang 中的 “+” 号

1、当左右两边都是数值型时，则做加法运算

2、当左右两边都是字符串，则做字符串拼接

```go
package main
import "fmt"

//变量使用的注意事项
func main() {
	var i = 10
	var j = 20
	fmt.Println("i + j = ",i + j)   //打印 i + j =  30

	var str1 = "hello"
	var str2 = "world"
	fmt.Println("str1 + str2 = ", str1 + str2)   //打印 str1 + str2 =  helloworld
	//还有一点要注意的就是,你在 go 中定义的变量如果没有被使用,是编译不过去的
}
```

#### 二、变量的数据类型

每一种数据都定义了明确的数据类型，在内存中分配了不同大小的内存空间

![Go数据类型](http://pd59gqjb6.bkt.clouddn.com/Go%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B.png)

##### 1、整数类型的基本使用

整型的类型

| 类型   | 有无符号 | 占用存储空间                                 | 取值范围                                 |
| ------ | -------- | -------------------------------------------- | ---------------------------------------- |
| int8   | 有       | 1字节                                        | -128~127                                 |
| int16  | 有       | 2字节                                        | -2^15  ~   2^15 - 1                      |
| int32  | 有       | 4字节                                        | -2^31  ~  2^31 -1                        |
| int64  | 有       | 8字节                                        | -2^63   ~   2^63 -1                      |
| uint8  | 无       | 1字节                                        | 0  ~ 255                                 |
| uint16 | 无       | 2字节                                        | 0  ~ 2^16 -1                             |
| uint32 | 无       | 4字节                                        | 0  ~ 2^32 -1                             |
| uint64 | 无       | 8字节                                        | 0  ~ 2^64 -1                             |
| int    | 有====   | ==32位操作系统4个字节<br />64位系统8个字节== | -2^31  ~  2^31 -1<br />-2^63  ~  2^63 -1 |
| uint   | 无       | 32位操作系统4个字节<br />64位操作系统8个字节 | 0  ~ 2^32 -1<br />0  ~ 2^64 - 1          |
| rune   | 有       | 与  int32  一样，等价于 int32                | -2^31  ~  2^31 -1                        |
| byte   | 无       | 与 uint8 等价（当要存储字符时选用 byte）     | 0  ~  255                                |

##### 2、整型的使用细节

①Golang 整型分为 ：有符号和无符号，int   uint 的大小和系统有关

②Golang 的整型默认声明为 int 型

```go
var num = 100    //这个num 默认就是 int 类型,而不是int32、int64这些类型的
fmt.Printf("num的类型是 %T",num)  //这个可以查看某个变量的数据类型
```

③如何在程序中查看某个变量的占用字节大小和数据类型

```go
package main
import (
	"fmt"
	"unsafe"
) 

func main() {
	var num int64 = 100
    //如何在程序中查看某个变量的占用字节大小和数据类型,unsafe.Sizeof()可以返回变量占用的字节数
	fmt.Printf("num 的数据类型是 %T,占用的字节大小是 %d",num,unsafe.Sizeof(num))
}
```

④Golang 程序中关于整型的使用,在保证程序正确运行下,尽量使用占用空间较小的数据类型

```go
func main() {
	//Golang 程序中关于整型的使用,在保证程序正确运行下,尽量使用占用空间较小的数据类型
	var age byte = 20  //例如年龄,我们使用 byte 类型其实就够了,byte 的取值范围是 0~255
}
```

⑤ bit：计算机中最小的存储单位。byte：计算机中基本存储单元

##### 3、浮点类型

浮点类型的分类

| 类型           | 占用的存储空间 |
| -------------- | -------------- |
| 单精度 float32 | 4字节          |
| 双精度 float64 | 8字节          |

①关于浮点数在机器中存放的形式是：浮点数 = 符号位 + 指数位 + 尾数位

也就是说浮点数都是有符号位的

②尾数部分可能丢失，造成精度损失，例如：-123.0000901

```go
package main
import "fmt"

func main() {
	//golang 中小数类型的使用
	var num1 float32 = -0.00089
	var num2 float64 = -787766.09
	fmt.Println("num1=",num1,"num2=",num2)  //打印 num1= -0.00089 num2= -787766.09

	var num3 float32 = -123.0000901
	var num4 float64 = -123.0000091
	//精度损失
	fmt.Println("num3=",num3,"num4=",num4) //打印 num3= -123.00009 num4= -123.0000091
}
```

**说明：**

1）、 float64 的精度比 float32 要准确

2）、如果我们要保存一个精度高的数，则应该选用 float64

##### 4、浮点型使用细节

① Golang 浮点类型有固定的范围和字段长度，不受具体的 OS  影响

② Golang 的浮点类型默认声明为 float64 类型

```go
	//Golang 的浮点型默认声明为 float64 类型
	var num5 = 1.01
	fmt.Printf("num5的类型是 %T",num5)  // 打印num5的类型是 float64
```

**注：**

`var num6 = .123  //这个就等价于 var num6=0.123 在 Golang 中这个0可以省略`

③ 科学计数法

```go
	num7 := 5.1234e2 // 这个就等价于 5.1234 * 10的2次方
	num8 := 5.1234E2 //这个和上面的一样,5.1234 * 10的2次方, e 可以大写也可以小写
	num9 := 5.1234E-2  //这个就相当于 5.1234 / 10的2次方
```

④通常情况下，应该使用 float64，因为它比 float32 更精确（开发中推荐使用 float64）

##### 5、字符类型

==Golang 中没有专门的字符类型==，如果要存储单个字符（如：字母），一般使用 byte 来存。

字符串就是一串固定长度的字符连接起来的字符序列。Go 的字符串是由单个字节连接起来的。也就是说对于传统的字符串是由字符组成的，而 ==Go 的字符串==不同，它是由==字节==组成的。

```go
package main
import "fmt"

//golang 中字符类型的使用
func main() {
	//在 golang 中没有专门的字符类型,如果要存储单个字符,一般使用 byte 来存
	var c1 byte = 'a'  //byte 的取值范围是 0 ~ 255   a~z的 ASCII 是在 0 ~ 255 之间的所以可以用 byte 来存
	var c2 byte = '0'   //同上面
	fmt.Println("c1=",c1)   //输出c1= 97    这里其实就是输出 c1、c2 的 ASCII 码值
	fmt.Println("c2=",c2)   //输出c2= 48
	//如果我们希望输出对应的字符,需要使用格式化输出
	fmt.Printf("c1=%c,c2=%c",c1,c2) //c1=%c 其实就是告诉编译器,c1按照字符的形式输出

	// var c3 byte = '中'    如果你这样定义的话,编译会报错,overflows byte,因为 '中' 的ASCII值 已经超过了255
	var c3 int = '中'  //如果你我们要保存的字符对应 ASCII 码值大于 255,这时候我们可以考虑使用 int 类型保存,因为 int 的取值范围比较大
	fmt.Println("c3的 ASCII值为：",c3)   //打印c3的 ASCII值为： 20013
	fmt.Printf("c3=%c",c3)  //打印 c3=中,这个就是输出 c3 这个字符
}
```

**说明：**

> 1、如果我们保存的字符的 ASCII 值在 byte 的取值范围内（0 ~ 255），字符类型可以直接使用 byte 。如：字符 a ~ z
>
> 2、如果我们保存的字符对应的 ASCII 码值大于 255，这时候我们可以考虑使用 int 类型保存，因为 int 的取值范围比较大

##### 6、字符类型使用细节

①字符常量使用单引号括起来的单个字符。如：var c1 byte = 'a'

② Go 中允许使用转义字符来将其后的字符转变为特殊字符。如：\n 表示换行

③ Go 语言的字符使用 UTF-8 编码。其中==英文字母占 1 个字节，汉字占 3 个字节==

④在 Go 中，字符的本质是一个整数，直接输出时，是该字符对应的 unicode 值

```go
var c1 byte = 'a'
fmt.Println("c1=",c1)   //输出c1= 97     这里直接输出的是 c1 的 unicode 值
```

⑤可以直接给某个变量赋一个数字，然后按照格式化输出 %c，会输出该数字对应的 unicode 字符

```go
var c4 int = 20013
fmt.Printf("c4=%c",c4)  //打印c4=中
```

⑥字符类型是可以进行运算的，相当于一个整数，因为它都有对应的 unicode 值

```go
var c5 = 10 + 'a'  //相当于 10 + 97
fmt.Println("值为：",c5)   //打印 值为： 107
```

##### 7、布尔类型

①布尔类型也叫 bool 类型，bool 类型数据只允许取值 true 和 false

②bool 类型占 1 个字节

```go
package main
import (
	"fmt"
	"unsafe"
)
//演示 bool 类型的使用
func main() {
	var b = false
	fmt.Println("b=",b)  //输出b= false
	//注意事项
	//1.bool 类型占用存储空间是 1 个字节
	fmt.Println("b 的字节大小是",unsafe.Sizeof(b))   //打印b 的字节大小是 1
	//2.bool 类型只能取 true 或者 false
}
```

##### 8、字符串类型

字符串就是一串固定长度的字符连接起来的字符序列。Go 的字符串是由单个字节连接起来的。Go 语言的字符串的字节使用 UTF-8 标识 Unicode 文本。

```go
package main
import (
	"fmt"
)
//演示 golang 中 string 类型使用
func main() {
	var address string = "这里是安徽合肥"
	fmt.Println(address)   //打印这里是安徽合肥
}
```

##### 9、字符串使用细节

①Go 语言的字符串使用 UTF-8 编码标识 Unicode 文本，这样 Golang 统一使用 UTF-8 编码，就不会出现中文乱码问题。

②字符串一旦赋值了，字符串就不能修改了，在 ==Go 中字符串是不可变的==

```go
func main() {
	var str = "hello"
	str[0] = 'a'  //我想把字符串的第一个字符修改一下,是不允许的
}
```

③字符串的两种表示形式

1）、双引号，会识别转义字符

2）、反引号，以字符串的原生形式输出，包括换行和特殊字符，可以实现防止攻击、输出源代码等效果

```go
func main() {
	var str2 = `
		var str = 'aa'
	`
	fmt.Println(str2)  //会输出   var str = 'aa'
}
```

④字符串拼接方式

字符串拼接换行时，"+" 号必须写在==当前行的末尾==不能写在下一行的开始

```go
package main
import (
	"fmt"
)
func main() {
	//字符串拼接方式
	var str = "hello" + "world"
	str += "haha"
	fmt.Println(str)  //打印 helloworldhaha
	//当一个字符串拼接操作很长时,可以分行写
	var str2 = "hello" + "world" +
				"hello" + "world" + 
				"hello" + "world"
	//注意：这样写是错的
	var str3 = "hello" + "world"
				+ "hello" + "world"
				+ "hello" + "world"
	//"+"号必须写在需要换行的那一行的末尾,不能写在下一行的开头,因为在 Golang 中是不用写分号的
	//编译器会默认帮你在每一行的最后加一个分号,你把"+"号写在末尾,编译器看末尾有加号就不会给你
	//加分号了,要是你把加号写在开头,那么编译器会在每一行结尾给你加一个分号,这样字符串拼接
	//语法就错了
}
```

##### 10、基本数据类型默认值

| 数据类型 | 默认值 |
| -------- | ------ |
| 整型     | 0      |
| 浮点型   | 0      |
| 字符串   | ""     |
| 布尔类型 | false  |

#### 三、数据类型转换

##### 1、基本数据类型的转换

**介绍：**

Golang 和 java/c 不同，Go 在不同类型的变量之间赋值时需要==显示转换==，也就是说 Golang 中数据类型==不能自动转换==

**基本语法：**

表达式T(v)将值 v 转换为类型 T

T：就是数据类型，比如 int32，int64，float32等

v：就是需要转换的变量

```go
package main
import (
	"fmt"
)
//演示 golang 中基本数据类型的转换
func main() {
	var i int32 = 100
	var n1 float32 = float32(i)   //这里就把 int32 类型转成 float32 类型
	var n2 int64 = int64(n1)     //
	fmt.Printf("i = %v, n1 = %v",i,n1)   //i = 100, n1 = 100
}
```

##### 2、基本数据类型转换细节

①Go 中，数据类型的转换可以是从表示范围小的转化为表示范围大的，也可以从表示范围大的转化为表示范围小的

例如：我可以把 int64 类型转换成 int32类型，也可以把 int32 类型转成 int64 类型

②被转换的是变量存储的数据（即值），变量本身的数据类型并没有发生变化

```go
func main() {
	var i int32 = 100    //这里我定义了一个变量 i 是 int32 类型的
	var n2 int64 = int64 (i)   //然后我把 i 强转成 int64 类型,其实是把 i 的值转成 int64 类型,但是 i 这个变量还是 int32 类型
	fmt.Printf("i 的 类型是 T%",i)   //输出 i 的类型是 int32   
}
```

③在转换中，比如将 int64 转成 int8 ，编译时不会报错，只是转换的结果是按溢出处理，和我们希望的结果不一样

```go
func main() {
	var num1 int64 = 99999
	var num2 int8 = int8(num1)
	//int8 存不了这么大的数,这里按溢出处理
	fmt.Println("num2=", num2)  //打印 num2= -97
}
```

**小练习**

```go
package main
import (
	"fmt"
)
func main() {
	var n1 int32 = 12
	var n2 int64
	var n3 int8

	//这里编译的时候会报错,因为 n1 是 int32 类型的, 所以 n1 + 30 的结果也是 int32 类型的
	//然后你把 int32 类型的赋值给 int64 类型的肯定会报错
	n2 = n1 + 30
	n3 = n1 + 20   //同上面
}
```

##### 3、基本数据类型转 string

方式一：

fmt.Sprintf("%参数",表达式)

①参数需要和表达式的数据类型相匹配

②fmt.Sprintf()会返回转换后的字符串

方式二：

使用 strconv 包的函数

```java
package main
import (
	"fmt"
	"strconv"
)
//golang 中基本数据类型转成 string
func main() {

	var num1 int64 = 90
	var num2 float64 = 22.3
	var b bool = true
	var mychar byte = 'a'
	var str string

	//使用第一种方式来转换 fmt.Sprintf方法
	str = fmt.Sprintf("%d",num1)
	fmt.Printf("str type %T str=%v",str,str)  //打印 str type string str=90

	str = fmt.Sprintf("%f",num2)
	fmt.Printf("str type %T str=%v",str,str)  //打印 str type string str=22.300000

	str = fmt.Sprintf("%t",b)
	fmt.Printf("str type %T str=%v",str,str)  //打印 str type string str=true

	str = fmt.Sprintf("%c",mychar)
	fmt.Printf("str type %T str=%v",str,str)  //打印 str type string str=a

	//使用第二种方式 strconv 函数
	str = strconv.FormatInt(num1,10)
	fmt.Printf("str type %T str=%q",str,str)  //打印 str type string str="90"

	//f表示格式,10表示小数位保留10位,64表示这个小数是 float64
	str = strconv.FormatFloat(num2,'f',10,64)
	fmt.Printf("str type %T str=%q",str,str)  //str type string str="22.3000000000"

	str = strconv.FormatBool(b)
	fmt.Printf("str type %T str=%q",str,str)  //打印str type string str="true"
        
     //strconv 包中有一个函数叫 Itoa   这个函数可以直接把 int 转成 string
	var num5 int = 234
	str = strconv.Itoa(num5)   //里面的参数只能放 int 要是不是int 需要转成int 放里面
	fmt.Printf("str type %T str=%q\n",str,str)
}
```

##### 4、string 转基本数据类型

**使用 strconv 包函数**

```go
package main
import (
	"fmt"
	"strconv"
)
//演示 golang 中 string 转基本数据类型
func main() {
	var str string = "true"
	var b bool
	//说明：
	//1.strconv.ParseBool(str)函数会返回两个值 (value bool, err error)
	//2.因为我只想获取到 value bool, 不想获取 err 所以我用 _ 忽略
	b , _ = strconv.ParseBool(str)
	fmt.Printf("b type %T b = %v", b,b)  //打印 b type bool b = true


	str = "12340"
	var n int64
	//func ParseInt(s string, base int, bitSize int) (i int64, err error)
	//第一个参数就是需要转成 int 类型的字符串,第二个参数是进制,要转成几进制的数,第三个参数是要转成的整数类型,想转 int64 这里就写64
	n , _ = strconv.ParseInt(str,10,64)  //这里就是我想把 str 这个字符串,转成 10 进制,int64 类型
	fmt.Printf("n type %T, n = %v",n,n)  //打印 n type int64, n = 12340

	str = "21.24"
	var f float64
	//ParseFloat(s string, bitSize int)
	//这个函数第一个参数是要转成 float 类型的字符串,第二个参数是要转成的 float 类型,要转 float32 这里就写32
	f , _ = strconv.ParseFloat(str,64)
	fmt.Printf("f type %T, f = %v",f,f)  //打印 f type float64, f = 21.24
}
```

> `func ParseInt(s string, base int, bitSize int) (i int64, err error)`
>
> 上面这个函数的返回值是 int64 ，所以你要拿 int64 来接收，如果你想要得到 int32 的值，强转一个即可
>
> `func ParseFloat(s string, bitSize int) (f float64, err error)`
>
> 上面这个函数的返回值是 float64 ，所以你要拿 float64 来接收，如果你想要得到 float32 的值，强转一个即可

##### 5、string 转基本数据类型细节

在将 String 类型转成基本数据类型时，要**确保 String 类型能够转成有效的数据**，比如，我们可以把 "123" 转成一个整数，但是不能把 "hello" 转成一个整数。如果你直接转的话，Golang 不会报错，会直接将其转成 0 

```go
func main() {
	var str string = "hello"
	var i int64
	i , _ = strconv.ParseInt(str,10,64)
	fmt.Printf("i = %v",i)  //打印 i= 0,所以说如果 string 转基本数据类型没有转成功,就会给你置成默认值
}
```

#### 四、指针

##### 1、指针基本介绍

1）、基本数据类型，变量存的就是值，也叫值类型

2）、获取变量的地址，用 & ，比如 var num int    获取 num 的地址：&num

3）、指针类型，变量存的是一个地址，这个地址指向的空间才是值。比如： var ptr *int = &num

4）、获取指针类型所指向的值，使用：  *    比如：  var ptr *int   使用 *ptr 获取 ptr 指向的值

```go
package main

import (
	"fmt"
)
func main() {
	var i int = 10
	//想要获取 i 这个变量的地址,可以使用 &i 来获取
	fmt.Println("i 的地址为：", &i)  //打印 i 的地址为： 0x1f2200a0

	//下面的 var ptr *int = &i
	//1. ptr 是一个指针变量
	//2. ptr 的类型 *int
	//3. ptr 本身的值是 &i
	var ptr *int = &i
	fmt.Printf("ptr 的值是：%v\n",ptr )  //打印 ptr 的值是：0x1f25e080

	//说一下 var ptr *int = &i 在内存中的情况：
	//首先在内存中会有一个指针变量叫 ptr,这个变量指向一块空间(这块空间其实也有地址, ptr 就是通过这个地址找打这块空间),这个空间里面存的是地址,
	//这个地址值就是 &i,然后通过这个地址值去找变量值

	fmt.Printf("ptr 指向的值为：%v",*ptr)   //打印 ptr 指向的值为：10
}
```

##### 2、指针的细节说明

1）、值类型，都有对应的指针类型，形式为 「*数据类型」，比如 int 对应的指针就是 *int    float32对应的指针类型就是 *float，依次类推

2）、值类型包括：基本数据类型 int 系列，float 系列，bool , string ，数组和结构体

**小案例：**

```go
package main
import (
	"fmt"
)

func main() {
	var num int = 10
	//输出 num 的地址
	fmt.Printf("num 的地址为：%v", &num)  //打印 num 的地址为：0x1f21e0a0

	var ptr *int = &num  //这里需要注意的是, ptr 是一个指针变量,你只能把一个地址值赋给指针变量
	*ptr = 20
	fmt.Printf("num 的值为： %d",num)   //打印 num 的值为： 20
}
```

#### 五、值类型和引用类型

> - 值类型：基本数据类型 int 系列，float 系列，bool ，string ，数组和结构体
> - 引用类型：指针，slice 切片，map，管道 chan ，interface等都是引用类型

值类型和引用类型使用特点：

1、值类型：变量直接存储值，内存通常在栈中分配

2、引用类型，变量存储的是一个地址，这个地址对应的空间才真正存储数据（值），内存通常在 堆 上分配，当没有任何变量引用这个地址时，该地址对应的数据空间就成为一个垃圾，由 GC 来回收

#### 六、标识符的基本使用

##### 1、标识符的概念

① Golang 中对各种变量、方法、函数等命名时使用的字符序列称为标识符

②凡是自己可以起名字的地方都叫标识符

##### 2、标识符的命令规则

> ①标识符的命名只能有字母、数字、下划线组成的
>
> ②不能以数字开头
>
> ③Golang 中严格区分大小写
>
> ④标识符中不能包含空格
>
> ⑤下划线 "_" 本身在 Go 中是一个特殊的标识符，称为==空标识符==。可以代表任何其他标识符，但是它对应的值会被忽略（比如：忽略某个返回值）所以仅能作为占位符使用，不能作为标识符使用
>
> ⑥不能以系统保留关键字（共 25 个）作为标识符，比如 break   if   等等

**3、标识符命名注意事项**

> - 包名：保持 package 的名字和目录保持一致，尽量采取有意义的包名，简短，有意义，但是不要和标准库的包名冲突
> - 变量名、函数名、常量名采用驼峰法
> - 如果变量名、函数名、常量名==首字母大写==，则可以被其他包访问；如果首字母小写，则只能在本包中使用（注：可以简单理解成，首字母大写是公有的，首字母小写是私有的）

```go
package main
import (
	"fmt"
	//为了使用 model 包下 utils.go 文件的变量或者函数,我们需要先引入该 model 包
	//其实真正的绝对路径应该是 E:\workspace-all\workspace-goproject\src\go_code\chapter03\demo13\model
	//但是由于我们之前在环境变量中配置过 「GOPATH」为 E:\workspace-all\workspace-goproject 然后它会默认去找 src 下面的文件,所以 src 可以省略
	"go_code/chapter03/demo13/model"
)
func main() {
	//我们使用 「包名.标识符」  来引用变量
	fmt.Println("英雄的名字是：",model.HeroName)
}
```

### 运算符

#### 一、算术运算符

##### 1、介绍

算术运算符是对数值类型的变量进行运算的，比如：加减乘除。

```go
package main
import (
	"fmt"
)

func main() {
	//如果运算的都是整数,那么除后,去掉小数部分,保留整数部分
	fmt.Println(10 / 4)   //打印 2

	var n1 float32 = 10 / 4
	fmt.Println(n1)   //打印 2

	//如果我们希望保留小数部分,则需要有浮点数参与运算
	var n2 float32 = 10.0 / 4   
	fmt.Println(n2)   //打印2.5

	//演示 % 的使用
	//取模的结果符合公式 a % b = a - a / b * b
	fmt.Println(10 % 3)   // 1
	fmt.Println(-10 % 3)  // -1
	fmt.Println(10 % -3)  // 1
	fmt.Println(-10 % -3)  // -1
}
```

##### 2、算术运算符细节

> 1、对于除号 “/” ，它的整数除和小数除是有区别的：整数之间做除法时，只保留整数部分而舍弃小数部分。例如： 19 / 5 结果是3
>
> 2、当对一个数取模时，可以等价 a % b = a -a / b * b，这样我们可以看到一个取模的本质运算
>
> 3、Golang 的自增自减只能当做一个独立语言使用
>
> 4、Golang 的 ++ 和 -- 只能写在变量的后面，不能写在变量的前面。即：只有 a++ a-- 没有 ++a --a
>
> 5、Golang 的设计者去掉 c/java 中的自增自减容易混淆的写法，让 Golang 更加简洁，统一。（强制性）

```go
package main
import (
	"fmt"
)
func main() {
	//在golang 中, ++ 和 -- 只能独立使用
	var i int = 8
	var a int
	i++
	a = i   //这里只能 先 i++ 过后,然后把 i 赋值给 a  不能用 a = i++
	//另： Golang 中只允许 i++ 不允许 ++i
}
```

#### 二、关系运算符

##### 1、介绍

①关系运算符的结果都是 bool 型，也就是要么是 true 要么是 false

②关系运算符经常用在 if 结构的条件中或循环结构的条件中

③关系运算符包括 == 、！=、>、< 、>=、 <= 

```go
package main
import "fmt"
func main() {
	var n1 int = 10
	var n2 int = 20
	fmt.Println(n1 == n2)  //false
	fmt.Println(n1 != n2)  //true
	fmt.Println(n1 > n2)  //false
	fmt.Println(n1 < n2)   //true
	fmt.Println(n1 >= n2)  //false
	fmt.Println(n1 <= n2)  //true
	flag := n1 < n2
	fmt.Println(flag)  //true
}
```

##### 2、关系运算符细节

> 1、关系运算符的结果都是 bool 型，要么为 true 要么为 false
>
> 2、关系运算符组成的表达式，我们称为 关系表达式。如： a > b
>
> 3、比较运算符 "==" 不能误写成 "="

#### 三、逻辑运算符

##### 1、介绍

用于连接多个条件，最终结果也是一个 bool 值

```go
package main
import (
	"fmt"
)
func main() {
	//演示逻辑运算符的使用
	var age int = 50
	if age > 40 && age < 60 {
		fmt.Println("ok1")
	}
	//逻辑或使用
	if age > 40 || age < 45 {
		fmt.Println("ok2")
	}
	//逻辑非使用
	if !(age > 60) {
		fmt.Println("ok3")
	}
}
```

##### 2、逻辑运算符细节

> 1、&& 也叫短路与，如果第一个条件为 false ，则第二个条件不会判断，最终结果为 false
>
> 2、|| 也叫短路或，如果第一个条件为 true，则第二个条件不会判断，最终结果为 true

#### 四、赋值运算符

##### 1、介绍

赋值运算符就是将某个运算后的值，赋给指定的变量

**赋值运算符分类**

| 运算符 |             描述             |          实例           |
| :----: | :--------------------------: | :---------------------: |
|   =    | 将一个表达式的值赋给一个左值 | C=A+B将 A+B的结果赋给 C |
|   +=   |         相加后再赋值         |  C += A 等于 C = C + A  |
|   -=   |         相减后再赋值         |  C -= A 等于 C = C - A  |
|   *=   |         相乘后再赋值         |  C *= A 等于 C = C * A  |
|   /=   |         相除后再赋值         |  C /= A 等于 C = C / A  |
|   %=   |         取模后再赋值         |  C %= A 等于 C = C % A  |

##### 2、关于赋值运算符的一个面试题

有两个变量，a 和 b ，要求将其进行交换，但是不允许使用中间变量，最终打印结果

```go
package main
import (
	"fmt"
)
func main() {
	//有两个变量，a 和 b ，要求将其进行交换，但是不允许使用中间变量，最终打印结果
	var a int = 10
	var b int = 8
	a = a + b
	b = a - b   // 相当于 b = a + b - b  这样就把 a 赋值给 b 了
	a = a - b   // 相当于 a = a + b - a  这样就相当于把 b 赋值给 a 了
	fmt.Printf("a 的值是：%d, b 的值是：%d",a,b)   //打印 a 的值是：8, b 的值是：10
}
```

#### 五、其它运算符

| 运算符 | 描述             | 实例                         |
| ------ | ---------------- | ---------------------------- |
| &      | 返回变量存储地址 | &a  将给出变量的实际地址     |
| *      | 指针变量         | *a    是一个指针变量特别**** |

**特别说明：**

`Go 语言明确不支持三元运算符`

```go
package main
import (
	"fmt"
)
func main() {
	//演示 & 和 * 的使用
	a := 10
	fmt.Println("a 的地址为：", &a)   //打印 a 的地址为： 0x1f25e080

	var ptr *int = &a
	fmt.Println("ptr 指向的值为：",*ptr)  // 打印ptr 指向的值为： 10

	//Golang 中不支持三元运算符,需要使用 if else 替代
	var n int
	var i int = 10
	var j int = 20
	if i > j {
		n = i
	} else {
		n = j
	}
	fmt.Println("n 的值为：",n)   //打印 n 的值为： 20
}
```

#### 六、键盘输入语句

##### 1、介绍

在编程中，需要接收用户输入的语句，就可以使用键盘输入语句来获取

##### 2、步骤

①导入 fmt 包

②调用 fmt 包的 『**fmt.Scanln()**』 或者 『**fmt.Scanf()**』

```go
package main
import (
	"fmt"
)
func main() {
	//从控制台接收用户信息,包括：姓名、年龄、薪水、是否通过考试
	//方式一：fmt.Scanln
	var name string
	var age byte
	var salary float32
	var isPass bool
	fmt.Println("请输入姓名：")
	fmt.Scanln(&name)  //这里传一个地址
	fmt.Println("请输入年龄：")
	fmt.Scanln(&age)
	fmt.Println("请输入薪水：")
	fmt.Scanln(&salary)
	fmt.Println("请输入是否通过考试：")
	fmt.Scanln(&isPass)
	fmt.Printf("姓名：%s,年龄：%d,薪水：%f,是否通过考试：%v",name, age, salary, isPass)
	//打印 姓名：tome,年龄：23,薪水：2000.000000,是否通过考试：true

	//方式二：fmt.Scanf  可以按指定的格式输入
	fmt.Println("请输入你的姓名,年龄,薪水,是否通过考试,使用空格隔开")
	fmt.Scanf("%s %d %f %t",&name,&age,&salary,&isPass)
	fmt.Printf("姓名：%s,年龄：%d,薪水：%f,是否通过考试：%v",name, age, salary, isPass)
	//打印 姓名：tome,年龄：23,薪水：3400.000000,是否通过考试：true
}
```

### 计算机进制

#### 一、进制

##### 1、进制介绍

> 对于整数，有四种表示方式：
>
> - 二进制：0， 1   满 2 进 1
>
> `在 golang 中，不能直接使用二进制来表示一个整数，它沿用了 c 的特点`
>
> - 十进制：0-9    满 10 进 1
> - 八进制：0-7   满 8 进 1，以数字 0 开头
> - 十六进制：0-9 及 A-F ，满 16 进 1，以 0x 或者 0X 开头，此处的 A-F 不区分大小写

```go
package main
import (
	"fmt"
)
func main() {
	var i int = 5
	//我想要按照 二进制格式输出 i 的值
	fmt.Printf("按照二进制输出：%b",i)   //打印 按照二进制输出：101

	//八进制：0-7   满 8 进 1，以数字 0 开头
	var j int = 011     //Golang 中允许使用八进制来表示一个整数
	fmt.Println("j = ",j)   //打印 j = 9

	//十六进制：0-9 及 A-F ，满 16 进 1，以 0x 或者 0X 开头
	var k int = 0x11
	fmt.Println("k=",k)   //打印 k= 17
}
```

#### 二、其他进制转十进制

##### 1、二进制转十进制

规则：从最低位开始（右边的），将每个位上的数提取出来，乘以 2 的（位数-1）次方，然后求和

> 案例：
>
> 将二进制 1011 转成十进制数
>
> 1011 = 1 * 2的 0 次方 + 1 * 2 的 1 次方 + 0 * 2 的 2 次方 + 1 * 2 的 3 次方 = 9

##### 2、八进制转成十进制

规则：从最低位开始（右边的），将每个位上的数提取出来，乘以 8 的（位数-1）次方，然后求和

> 案例：
>
> 将八进制 0123 转成十进制数
>
> 0123 = 3 * 8的 0 次方 + 2 * 8 的 1 次方 + 1 * 8 的 2 次方 + 0 * 8 的 3 次方 = 83

##### 3、十六进制转成十进制

规则：从最低位开始（右边的），将每个位上的数提取出来，乘以 16 的（位数-1）次方，然后求和

> 案例：
>
> 将八进制 0X34A 转成十进制数
>
> 0X34A = 10 * 16的 0 次方 + 4 * 16 的 1 次方 + 3 * 16 的 2 次方  = 842

#### 三、十进制转成其它进制

##### 1、十进制转成二进制

规则：将该数不断除以 2 ，直到商为 0 为止，然后将每步得到的余数倒过来，就是对应的二进制。

##### 2、十进制转成八进制

规则：将该数不断除以 8 ，直到商为 0 为止，然后将每步得到的余数倒过来，就是对应的八进制。

##### 3、十进制转成十六进制

规则：将该数不断除以 16 ，直到商为 0 为止，然后将每步得到的余数倒过来，就是对应的十六进制。

#### 四、二进制转其它进制

##### 1、二进制转八进制

规则：将二进制数==每三位==一组（从低位开始组合），转成对应的八进制即可。

> 案例：
>
> 将二进制：11010101 转成八进制
>
> 11010101  = 0325

##### 2、二进制转十六进制

规则：将二进制数==每四位==一组（从低位开始组合），转成对应的十六进制即可。

> 案例：
>
> 将二进制：11010101 转成十六进制
>
> 11010101  = 0xD5

#### 五、其它进制转成二进制

##### 1、八进制转二进制

规则：将八进制数每 1 位，转成对应的一个 3 位的二进制数即可。

> 案例：将 0237 转成二进制
>
> 0237 = 10011111

##### 2、十六进制转二进制

规则：将十六进制数每 1 位，转成对应的一个 4 位的二进制数即可。

> 案例：将 0x237 转成二进制
>
> 0x237 = 1000110111

### 位运算

#### 一、原码、反码、补码

对于有符号的而言：

> - 二进制的最高位是符号位： 0 表示正数   1 表示负数
>
>
> - 正数的原码，反码，补码都一样
> - 负数的反码等于它的原码符号位不变，其他位取反（就是把 0 变成 1,1 变成 0）
> - 负数的补码等于它的反码加 1
> - 0 的反码、补码都是 0
> - 计算机在运算的时候，都是以补码的方式来运算的

#### 二、位运算符和移位运算符

##### 1、Golang 中的 3 个位运算：

分别是，『按位与 &』、『按位或 |』、『按位异或 ^』，他们的运算规则是：

> 按位与 &  ：两位全为 1，结果为 1，否则为 0
>
> 按位或 |   ：两位有一个为 1，结果为 1，否则为 0
>
> 位异或 ^   ： 两位一个为 0 ，一个为 1，结果为 1（两个不一样，结果才是 1），否则为 0

##### 2、Golang 中的 2 个移位运算符

分别是， 『>> 右移』、『<< 左移』，运算规则：

> 右移运算符  >> ：低位溢出，符号位不变，并用符号位补溢出的高位
>
> 左移运算符  << ：符号位不变，低位补 0

### 程序流程控制

#### 一、流程控制介绍

主要有三大流程控制语句：

> 1、顺序控制
>
> 2、分支控制
>
> 3、循环控制
>

#### 二、顺序控制

##### 1、顺序控制介绍

程序从上到下逐行地执行，中间没有任何判断和跳转

#### 三、分支控制

##### 1、单分支

> **基本语法：**
>
> if 条件表达式 {
>
> ​	执行代码块
>
> }

**注意：**

==大括号是必须写的不能省略，就算你执行代码块只有一行代码==

```go
package main
import (
	"fmt"
)
func main() {
	//编写一个程序,可以输入人的年龄,如果该同志的年龄大于 18 岁,则输出你的年龄大于 18岁
	fmt.Println("请输入人的年龄：")
	var age byte
	fmt.Scanln(&age)
	if age > 18 {
		fmt.Println("此人年龄大于 18 岁！")
	}
}
```

**单分支的一个细节**

> Go 的 if 语句还有一个强大的地方就是条件判断语句里面允许声明一个变量，这个变量的作用域只能在该条件逻辑块内，其他地方就不起作用了。
>

```go
//golang 支持在 if 中,直接定义一个变量
if age := 20; age > 8 {
    fmt.Println("此人年龄大于 18 岁！")    //定义变量的作用域只在逻辑块内有效
}
```

##### 2、双分支

> **基本语法：**
>
> if 条件表达式 {
>
> ​	执行代码块1
>
> } else {
>
> ​	执行代码块2
>
> }

**注意：**

==else 必须紧跟在大括号后面，不能换行输出，否则会编译错误==

```go
package main
import (
	"fmt"
)
func main() {
	//输入人的年龄,如果该同志年龄大于 18 岁,输出“年龄大于18岁”,否则输出“年龄小于18岁”
	var age byte
	fmt.Println("请输入人的年龄")
	fmt.Scanln(&age)
	if age > 18 {
		fmt.Println("此人年龄大于 18 岁")
	} else {
		fmt.Println("此人年龄小于 18 岁")
	}
}
```

##### 3、多分支

> **基本语法：**
>
> if 条件表达式1 {
>
> ​	执行代码块1
>
> } else if 条件表达式2 {
>
> ​	执行代码块2
>
> } else {
>
> ​	执行代码块 n
>
> }

```go
package main
import (
	"fmt"
)
func main() {
	//小明参加考试,如果成绩为 100 分,奖励一辆 BMW
	//成绩为 (80,99] 时,奖励一台 iphone7
	//成绩为 [60, 80]时,奖励一台 ipad
	//其他时,什么也没有。要求从键盘输入小明成绩并加以判断
	fmt.Println("请输入小明的考试成绩")
	var score int
	fmt.Scanln(&score)
	if score == 100 {
		fmt.Println("恭喜你，得到了一辆 BMW")
	} else if score > 80 && score <= 99 {
		fmt.Println("恭喜你，得到一台 iphone7")
	} else if score >= 60 && score <= 80 {
		fmt.Println("恭喜你，得到一台 ipad")
	} else {
		fmt.Println("什么都没有")
	}
}
```

**说明：**

```go
	var b bool = true
	if b = false {     //这种写法是错误的,if 后的条件表达式不能是赋值语句,应该是 b == false
		fmt.Println("a")
	} else if b {
		fmt.Println("b")
	}
```

##### 4、嵌套分支

基本介绍：

在一个分支结构中又完整的嵌套了另一个完整的分支结构，里面的分支结构称为内层分支外面的分支结构称为外层分支。

基本语法：

> if 条件表达式 {
>
> ​	if 条件表达式 {
>
> ​	} else {
>
> ​	}
>
> } 

```go
package main
import (
	"fmt"
)
func main() {
	//参加百米运动会,如果用时 8 秒以内进入决赛,否则提示淘汰。并且根据性别提示进入男子组或女子组
	var second float64
	var gender string 
	fmt.Println("请输入你的跑步成绩：")
	fmt.Scanln(&second)
	if second <= 8.0 {
		fmt.Println("请输入性别：")
		fmt.Scanln(&gender)
		if gender == "man" {
			fmt.Println("恭喜你，进入男子组决赛")
		} else {
			fmt.Println("恭喜你，进入女子组决赛")
		}
	} else {
		fmt.Println("你被 out 了")
	}
}
```

##### 5、switch 分支结构

基本介绍：

1、switch 语句用于基于不同条件执行不同动作，每一个 case 分支都是唯一的，从上到下逐一测试，知道匹配为止。

2、匹配项后面也==不需要再加 break==

基本语法：

> switch 表达式 {
>
> ​	case 表达式1，表达式2，表达式n:
>
> ​		语句块1
>
> ​	case 表达式3，表达式4:
>
> ​		语句块2
>
> ​	default:
>
> ​		语句块
>
> }

注：

==1、Golang 中 switch 语句是没有 break 的，只要匹配了一个 case 语句，执行完后就会跳出整个 switch==

==2、Golang 中 case 后的表达式可以有多个，使用逗号分隔。匹配上任意一个表达式都会进入 case 后的语句块==

```go
package main
import (
	"fmt"
)
func main() {
	//字符一般定义成 byte
	var key byte
	fmt.Println("请输入一个字母")
	fmt.Scanf("%c",&key)
	switch key {
		case 'a':
			fmt.Println("星期一")
		case 'b':
			fmt.Println("星期二")
		case 'c':
			fmt.Println("星期三")
		default:
			fmt.Println("输入有误")
	}	
}
```

##### 6、switch 分支结构细节讨论

> 1、case 或 switch 后是一个表达式（这个表达式可以是：常量值、变量、一个有返回值的函数）
>
> 2、case 后的各个表达式的值的数据类型，必须和 switch 的表达式数据类型一致
>
> ```go
> 	var n1 int32 = 20
> 	var n2 int64 = 20   //这里如果n2 和 n1 类型不匹配,在下面 case 语句会编译不通过
> 	switch n1 {
> 		case n2:
> 			fmt.Println("ok")
> 		default:
> 			fmt.Println("error")
> 	}
> ```
>
> 3、case 后面可以带多个表达式，使用逗号间隔。
>
> ```go
> 	var n1 int32 = 10
> 	var n2 int32 = 20  
> 	switch n1 {
> 		case n2,5,10:       //case 后面可以跟多个表达式
> 			fmt.Println("ok")
> 		default:
> 			fmt.Println("error")
> 	}
> ```
>
> 4、case 后面的表达式如果是常量值（字面量），则要求不能重复 
>
> ```go
> 	var n1 int32 = 10
> 	var n2 int32 = 20  
> 	switch n1 {
> 		case n2,5,10:       
> 			fmt.Println("ok")
> 		case 5:       //前面 case 语句已经声明过 5 了,这里就不能重复声明了
> 			fmt.Println("ok")
> 		default:
> 			fmt.Println("error")
> 	}
> ```
>
> 5、case 后面不需要带 break ，程序匹配到一个 case 后就会执行对应的代码块，然后退出 switch ，如果一个都匹配不到，则执行 default
>
> 6、default 语句不是必须的
>
> 7、switch 后也可以不带表达式，类似 if --else 分支来使用
>
> ```go
> 	var age int = 20
> 	switch {         //switch 后也可以不带表达式,类似 if--else 来使用
> 		case age == 10:
> 			fmt.Println("age == 10")
> 		case age == 20:
> 			fmt.Println("age == 20")
> 		default:
> 			fmt.Println("没有匹配到")
> 	}
>
> 	//case 后也可以对范围进行判断
> 	var score int = 80
> 	switch {
> 		case score > 90:
> 			fmt.Println("成绩优良")
> 		case score > 60:
> 			fmt.Println("成绩合格")
> 		default:
> 			fmt.Println("不及格")
> 	}
> ```
>
> 8、switch 后也可以直接声明定义一个变量，分号结束，**不推荐**
>
> ```go
> switch score := 95; {
> 		case score > 90:
> 			fmt.Println("成绩优良")
> 		case score > 60:
> 			fmt.Println("成绩合格")
> 		default:
> 			fmt.Println("不及格")
> 	}
> ```
>
> 9、switch 穿透（fallthrough），如果在 case 语句块后增加 fallthrough，则会继续执行下一个 case ，也叫 switch 穿透
>
> ```go
> 	//switch 的穿透 fallthrough
> 	var num int = 10
> 	switch num {
> 		case 10:
> 			fmt.Println("ok1")
> 			fallthrough   //默认只能穿透一层
> 		case 20:
> 			fmt.Println("ok2")  // 这样 ok1 和 ok2 都会输出
> 		case 30:
> 			fmt.Println("ok3")
> 		default:
> 			fmt.Println("没有匹配到")
> 	}
> ```
>
> 

##### 7、switch 练习

```go
package main
import (
	"fmt"
)
func main() {
	//1.使用 switch 把小写类型的 char 型转为大写(键盘输入)
	只转换 a, b, c, d 其他的输出 other
	fmt.Println("请输入一个小写字母")
	var c byte
	fmt.Scanf("%c", &c)
	switch c {
		case 'a':
			fmt.Println("A")
		case 'b':
			fmt.Println("B")
		case 'c':
			fmt.Println("C")
		case 'd':
			fmt.Println("D")
		default:
			fmt.Println("other")
	}

	//2、对学生成绩大于 60 分的,输出合格。低于 60 分的输出不合格
	要求输入成绩不能大于 100
	var score int
	fmt.Println("请输入学生的成绩：")
	fmt.Scanln(&score)
	switch {
		case score > 100:
			fmt.Println("输入成绩有误！")
		case score > 60:
			fmt.Println("成绩合格")
		case score < 60:
			fmt.Println("成绩不合格")
	}

	//3、根据用户指定月份，打印该月份所属季节。3,4,5 春季 6,7,8 夏季 9,10,11 秋季 12,1,2 冬季
	fmt.Println("请输入月份：")
	var month int 
	fmt.Scanln(&month)
	switch month {
		case 3,4,5:
			fmt.Println("春季")
		case 6,7,8:
			fmt.Println("夏季")
		case 9,10,11:
			fmt.Println("秋季")
		case 12,1,2:
			fmt.Println("冬季")
	}
}
```

##### 8、switch 和 if 的比较

> 其实在 Golang 中 switch 和 if 的区别已经不大了
>
> 1、如果判断的具体数值不多，而且符合整数、浮点数、字符、字符串这几种类型。建议使用 switch 语句，简洁高效。
>
> 2、其他情况，对于区间判断和结果为 bool 类型的判断，使用 if ，if 的使用范围更广

#### 四、循环控制

##### 1、基本语法

> for 循环变量初始化;循环条件;循环变量迭代 {
>
> ​	循环语句
>
> }

##### 2、注意事项和细节说明

1）、循环条件是返回一个布尔值的表达式

2）、for 循环的第二种使用方式：

```go
for 循环判断条件 {
    //循环执行语句
}
//将变量初始化和变量迭代写到其他位置
```

3）、for 循环的第三种使用方式：

```go
for {
    //循环执行语句
}
//上面的写法等价 for ;;{}，通常需要配合 break 语句使用
```

4）、Golang 提供 for-range 的方式，可以方便的遍历字符串和数组

```go
package main
import (
	"fmt"
)
func main() {
	//for 循环的第二种写法
	j := 1   //循环变量初始化
	for j < 5 {   //循环条件
		fmt.Println("hello")
		j++   //循环变量迭代
	}

	//for 循环的第三种写法
	k := 1
	for {
		if k <= 5 {
			fmt.Println("hi")
		} else {
			break
		}
		k++
	}

	
	//字符串遍历方式1-传统方式
	//这种方式是按照字节来遍历的,是一个字节一个字节的取
	//golang 中是按照 UTF-8 编码,一个汉字三个字节,所以存在汉字时遍历就会乱码
	var str string = "hello,world"
	for i := 0; i < len(str); i++ {
		fmt.Printf("%c \n",str[i])
	}

	//字符串遍历方式2-  for-range 
	//这种方式是按照字符来遍历的,所以即使字符串中有中文也不会出现乱码问题
	str = "hi,Jimmy"
	for index, val := range str {
		fmt.Printf("index=%d,val=%c \n", index, val)
	}
}
```

**小练习**

```go
package main
import (
	"fmt"
)
func main() {
	//打印 1-100 之间所有是 9 的倍数的整数的个数及总和
	var maxnum int64 = 100
	var count int64 = 0
	var sum int64 = 0
	var i int64 = 1
	for ; i < maxnum; i++ {
		if i % 9 == 0 {
			count++
			sum += i
		}
	}
	fmt.Printf("count=%d,sum=%d",count,sum)  //打印 count=11,sum=594
}
```

##### 3、while 和 do...while 的实现

==Go 语言中没有 while 和 do...while 语法==，但是我们可以通过 for 循环来实现它的效果。

```go
package main
import (
	"fmt"
)
func main() {
	//使用 while 方式输出 10 句 "hello,world"
	var i int = 1
	for {
		if i > 10 {
			break
		}
		fmt.Println("hello,world", i)
		i++
	}

	//使用 do...while 实现完成输出 10 句 "hello"
	var j int = 1
	for {
		fmt.Println("hello")
		j++
		if j > 10 {
			break
		}
	}
}
```

##### 4、break 语句

**生成随机数**

```go
package main
import (
	"fmt"
	"math/rand"
	"time"
)
func main() {
	//随机的生成 1-100 之间的整数
	//为了生成一个随机数,还需要为 rand 设置一个种子
	rand.Seed(time.Now().Unix())  //time.Now().Unix() 返回一个 1970 年到现在的一个秒数,这个秒数会一直变,所以得到的数是随机的
	n := rand.Intn(100) + 1  //生成 [0,100)之间的整数,这个随机数是个伪随机数,需要配合 rand.Seed() 来生成随机数
	fmt.Println(n)
}
```

**基本介绍：**

break 语句用于终止某个语句块的执行，用于中断当前 for 循环或跳出 switch 语句

##### 5、break 注意事项和细节说明

1、break 语句出现在多层嵌套的语句块中时，可以通过标签指明要终止的是哪一层语句块

```go
package main
import (
	"fmt"
)
func main() {
	//演示一下指定标签的形式来使用 break
	label:   //设置一个标签,名字可以随便起
	for i := 0; i < 5; i++ {
		for j := 0; j < 4; j++ {
			if j == 2 {
				break label    //跳出指定标签的 for 循环
			}
			fmt.Println(j)
		}
	}
}
```

##### 6、continue 语句

**基本介绍：**

1、continue 语句用于结束本次循环，继续执行下一次循环

2、continue 语句出现在多层嵌套的循环语句中时，可以通过标签指明要跳过的是哪一层循环

##### 7、跳转控制语句-goto

**介绍：**

> 1、Go 语言的 goto 语句可以无条件地转移到程序中指定的行
>
> 2、goto 语句通常与条件语句配合使用。可用来实现条件转移，跳出循环体等功能。
>
> 3、在 Go 程序设计中一般不主张使用 goto 语句，以免造成程序流程混乱，使理解和调试程序都产生困难

**基本语法：**

> goto label
>
> .......
>
> label:statement

```go
package main
import (
	"fmt"
)
func main() {
	fmt.Println("ok1")
	goto label            //执行到这里的时候就会直接跳过下面的语句，直接执行 label 标记的语句
	fmt.Println("ok2")
	fmt.Println("ok3")
	fmt.Println("ok4")
	label:
	fmt.Println("ok5")
}
```

------

### 函数

#### 一、函数的介绍

##### 1、什么是函数

为完成某一功能的程序指令（语句）的集合，称为函数。在 Go 中，函数分为：自定义函数、系统函数

##### 2、函数的基本语法

> func 函数名 （形参列表）（返回值列表）{
>
> ​	执行语句。。。
>
> ​	return 返回值列表
>
> }

```go
package main
import (
	"fmt"
)

func cal(n1 float64, n2 float64, operator byte) (float64) {
	var result float64
	switch operator {
		case '+':
			result = n1 + n2
		case '-':
			result = n1 - n2
		case '*':
			result = n1 * n2
		case '/':
			result = n1 / n2
		default:
			fmt.Println("操作符错误")
	}
	return result
}

func main() {
	result := cal(2.3,3.6,'+')
	fmt.Println(result)
}
```

##### 3、包的快速入门

包的基本概念：

go 的每一个文件都是属于一个包的，也就是说 go 是以包的形式来管理文件和项目目录结构的

##### 4、包注意事项

①在给一个文件打包时，该包会对应一个文件夹。文件的包名通常和文件所在的文件夹名一致，一般为小写字母。但是并没有强制包名一定需要和文件夹的名字一样，Go 语言中没有做强制规定。

②引包的方式：

> 方式1：
>
> import "包名"
>
> 方式2：
>
> import (
>
> ​    "包名"
>
> ​     “包名”
>
> )

③ package 指令在文件第一行，然后是 import 指令

④为了让其它包的文件可以访问到本包的函数，则该函数名的首字母需要大写

⑤在访问其它包函数时，其语法是 ==包名.函数名==

⑥如果包名较长，Go 支持给包取别名。但是注意：取别名后原来的包名就不能使用了

⑦==在同一包下，不能有相同的函数名，否则报重复定义。==因为在 Golang 中，函数调用时通过 包名.函数名来调用的，你一个包下有两个相同的函数名，肯定不知道调用哪个。

⑧如果你要编译成一个可执行文件，就需要将这个包声明为 main，即 package main这就是一个语法规范。如果你写的是一个库，包名可以自定义。

#### 二、函数的调用机制

##### 1、函数调用示例

```go
package main
import (
	"fmt"
)

//函数 test
func test(n1 int) {
	n1 += 1
	fmt.Println("n1=",n1)
}
//函数 getSum
func getSum(n1 int, n2 int) int {
	sum := n1 + n2
	return sum
}

func main() {
	n1 := 2
	test(n1)    //打印n1=3
	sum := getSum(3,5)
	fmt.Println(sum)    // 打印 8
}
```

##### 2、return 语句

**基本语法：**

Go 函数支持返回多个值，这一点是其他编程语言都没有的

> func 函数名(形参列表)(返回值类型) {
>
> ​      语句。。。
>
> ​     return  返回值列表
>
> }

注：

1、如果返回多个值，在接收时，希望忽略某个返回值，则使用 `_`表示占位忽略

2、如果返回值只有一个，（返回值类型列表）可以不写小括号

```go
package main

import (
	"fmt"
)

func getSumAndSub(n1 int, n2 int) (int,int) {
	sum := n1 + n2
	sub := n1 - n2
	return sum,sub
}

func main() {
	//调用函数
	result1,result2 := getSumAndSub(5,2)
	fmt.Printf("result1 = %v,result2 = %v",result1,result2)   //打印result1 = 7,result2 = 3

	//如果你希望忽略某个返回值, 可以使用 "_" 来进行占位忽略
	result3,_ := getSumAndSub(6,3)
	fmt.Println("result3 = ",result3)   //打印 result3 = 9
}
```

#### 三、函数递归调用

##### 1、基本介绍

一个函数在函数体内又调用了本身，我们称为递归调用。

```go
package main
import (
	"fmt"
)

func test(n int) {
	if n > 2 {
		n--
		test(n)
	} else {
		fmt.Println("n=",n)
	}
}
func main() {
	test(4)     //最后执行结果是：输出一个 n = 2
}
```

##### 2、函数递归需要遵守的重要原则：

> 1、执行一个函数时，就创建一个新的受保护的独立空间（新函数栈）
>
> 2、函数的局部变量是独立的，不会相互影响
>
> 3、递归必须向退出递归的条件逼近，否则就是无限递归了
>
> 4、当一个函数执行完毕，或者遇到 return ，就会返回，遵守谁调用，就将结果返回给谁。同时当函数执行完毕或者返回时，该函数本身也会被系统销毁。

```go
package main
import (
	"fmt"
)
func fbn(n int) int {
	if n == 1 || n == 2 {
		return 1
	} else {
		return fbn(n - 1) + fbn(n -2)
	}
}

func main() {
	//给定一个整数 n,求出它的斐波那契数（第一个数和第二个数固定为 1, 从第三个数开始,后面的数为前面两个数的和）
	//例：1,1,2,3,5,8,13
	fmt.Println("result = ",fbn(3))    //第 3 个斐波那契数是 2
	fmt.Println("result = ",fbn(4))    //第 4 个斐波那契数是 3
}
```

#### 四、函数注意事项

> 1、函数的形参列表可以是多个，返回值列表也可以是多个。
>
> 2、形参列表和返回值列表的数据类型可以是值类型和引用类型
>
> 3、函数的命名遵循标识符命名规范，首字母不能是数字，==首字母大写该函数可以被本包文件和其他包文件使用，类似 public ，首字母小写，只能被本包文件使用，其他包文件不能使用，类似 private==
>
> 4、函数中的变量是局部的，函数外不生效
>
> 5、基本数据类型和数组默认都是值传递的，即进行值拷贝，在函数内修改，不会影响到原来的值。
>
> ```go
> package main
> import (
> 	"fmt"
> )
>
> func test(n1 int) {
> 	n1 = n1 + 10
> }
>
> func main() {
> 	n1 := 20
> 	test(n1)
> 	fmt.Println("main 中 n1 的值为：", n1)  //打印 main 中 n1 的值为： 20
> }
> ```
>
> 6、如果希望函数内的变量能修改函数外的变量，可以传入变量的地址 &，函数内以指针的方式操作变量。从效果上看类似引用。
>
> ```go
> package main
> import (
> 	"fmt"
> )
> //这里的 n1 是 *int 类型
> func test2(n1 *int) {
> 	*n1 = *n1 + 10
> }
>
> func main() {
> 	n1 := 20
> 	test2(&n1)
> 	fmt.Println("main 中 n1 的值为：", n1)  //打印 main 中 n1 的值为： 30
> }
> ```
>
> 7、Go 函数不支持重载
>
> 8、在 Go 中，函数也是一种数据类型，可以赋值给一个变量，则该变量就是一个函数类型的变量了。通过该变量可以对函数进行调用
>
> ```go
> package main
> import (
> 	"fmt"
> )
>
> func getSum(n1 int, n2 int) int {
> 	return n1 + n2
> }
> func main() {
> 	a := getSum
> 	fmt.Printf("a 的数据类型是%T,getSum 的数据类型是%T",a,getSum)
> 	//打印 a 的数据类型是func(int, int) int,getSum 的数据类型是func(int, int) int
> 	result := a(2,3)    //等价于 res:=getSum(2,3)
> 	fmt.Println(result)   //结果是 5
> }
> ```
>
> 9、函数既然是一种数据类型，因此在 Go 中，函数可以作为形参并且调用
>
> ```go
> package main
> import (
> 	"fmt"
> )
> func getSum(n1 int, n2 int) int {
> 	return n1 + n2
> }
> //函数既然是一种数据类型,因此在 Go 中,函数可以作为形参,并且调用
> func myFun(funvar func(int,int) int,num1 int, num2 int) int {
> 	return funvar(num1, num2)
> }
> func main() {
> 	result := myFun(getSum,1,2)
> 	fmt.Println("结果是：", result)  //打印结果是 3
> }
> ```
>
> 10、为了简化数据类型定义，Go 支持自定义数据类型
>
> 基本语法：type 自定义数据类型名 数据类型           //理解：相当于起了一个别名
>
> 案例：type myint int     //这时 myint 就等价于 int 来使用了
>
> 案例：type mySum func(int,int) int             //这时  mySum 就等价一个函数类型  func(int,int) int
>
> ```go
> package main
> import (
> 	"fmt"
> )
> func getSum(n1 int, n2 int) int {
> 	return n1 + n2
> }
> // 给函数类型定义一个别名
> type myFunType func(int,int) int
> func myFun(funvar myFunType,num1 int, num2 int) int {
> 	return funvar(num1, num2)
> }
> func main() {
>
> 	//给 int 取别名, 在 go 中 myint 和 int 虽然都是 int 类型,但是 go 认为 myint 和 int 是两个类型
> 	type myint int
>
> 	var num1 myint
> 	var num2 int
> 	num1 = 10
> 	num2 = int(num1)   //这里依然要强转,虽然都是 int 类型,但是 go 就认为你 num1 是 myint 类型
> 	fmt.Printf("num1=%d,num2=d%",num1,num2)
> }
> ```
>
> 11、支持对函数返回值命名
>
> ```go
> //支持对函数返回值进行命名
> func getSumAndSub(n1 int, n2 int) (sum int,sub int) {
> 	sum = n1 + n2
> 	sub = n1 - n2
> 	//这里就不用写 return sum,sub 了,直接写 return 就可以了
> 	return      //这里直接 return 就可以了,它会把 sum 和 sub 返回出去,因为你代码块中的 sum 和 sub 与 返回值参数对应起来了,所以直接写个 return 就行了
> }
> ```
>
> 12、使用 _ 标识符，忽略返回值
>
> 13、Go 支持可变参数
>
> ```go
> // 编写一个函数，可以求出 1 到多个 int 的和
> func sum(n1 int, args... int) int {
> 	sum := n1
> 	//遍历 args
> 	for i := 0; i < len(args); i++ {
> 		sum += args[i]
> 	}
> 	return sum
> }
> func main() {
> 	result := sum(1,2,3)
> 	fmt.Println("结果是",result)
> }
> ```
>
> 

#### 五、init 函数

##### 1、基本介绍

每一个源文件都可以包含一个 init 函数，该函数会在 main 函数执行之前，被 Go 运行框架调用，也就是说 init 函数会在 main 函数之前被调用

```go
package main
import (
	"fmt"
)
func init() {
	fmt.Println("我是 init 函数...")
}

func main() {
	fmt.Println("我是 main 函数...")
}
//最后控制台打印的结果是：  我是 init 函数...    我是 main 函数...
```

##### 2、init 相关细节

① 如果一个文件同时包含全局变量定义、init 函数、main 函数，则执行的流程是：变量定义 -----> init 函数  -----> main 函数

```go
package main
import (
	"fmt"
)
//这里我把 test 函数的执行结果赋值给 age,如果 age 先执行的话,那么就会打印 test 函数里面的内容
var age = test()

func test() int {
	fmt.Println("全局变量先执行...")
	return 100
}
func init() {
	fmt.Println("我是 init 函数...")
}

func main() {
	fmt.Println("我是 main 函数...,age = ",age)
}
//最后控制台打印的结果是： 
//全局变量先执行...      我是 init 函数...    我是 main 函数...,age =  100
```

② init 函数最主要的作用，就是完成一些初始化的工作

③ 假如 main.go 文件引入了一个别的文件，那么执行流程是怎么样的？

> 假如在 main.go 文件中，我引用了一个 util.go 文件，那么执行流程是这样的：
>
> 1、先执行 util.go 中的全局变量定义
>
> 2、再执行 util.go 中的 init 函数
>
> 3、在执行 main.go 中的全局变量定义
>
> 4、在执行 main.go 中的 init 函数
>
> 5、在执行 main.go 中的 main 函数

![不同文件之间函数执行顺序](C:\Users\Administrator\Desktop\笔记\不同文件之间函数执行顺序.png)

#### 六、匿名函数

##### 1、匿名函数介绍

Go 支持匿名函数，如果我们某个函数只是希望使用一次，可以考虑使用匿名函数，匿名函数也可以实现多次调用。

##### 2、匿名函数使用方式一

在定义匿名函数时就直接调用，这种方式匿名函数只能调用一次

```go
package main
import (
	"fmt"
)
func main() {
	//在定义匿名函数时就直接调用，这种方式匿名函数只能调用一次
	result := func (n1 int, n2 int) int {
		return n1 + n2
	}(10,20)     //在这里就直接调用匿名函数
	fmt.Println("结果是：",result)   //结果是： 30
}
```

##### 3、匿名函数使用方式二

将匿名函数赋给一个变量（函数变量），再通过该变量来调用匿名函数

```go
package main
import (
	"fmt"
)
func main() {
	//将匿名函数赋值给变量 a
	//这时候 a 的数据类型就是函数类型,我们可以通过 a 来对函数进行调用
	a := func (n1 int, n2 int) int {
		return n1 + n2
	}
	result := a(11,22)
	fmt.Println("结果是：",result)    //结果是： 33
}
```

##### 4、全局匿名函数

如果将匿名函数赋给一个全局变量，那么这个匿名函数，就成了一个全局匿名函数。

```go
package main
import (
	"fmt"
)
var (
	//Fun1 是一个全局匿名函数
	Fun1 = func(n1 int, n2 int) int {  //把一个匿名函数赋给一个全局变量,这个匿名函数就变成了全局匿名函数
		return n1 * n2
	}
)

func main() {
	result := Fun1(2,6)
	fmt.Println("结果是：",result)    //结果是： 12
}
```

#### 七、闭包

##### 1、基本介绍

闭包就是一个*函数*和与其相关的引用环境组合的一个整体。也就是说闭包它其实返回的是一个函数，但是这个函数它会用到函数外的一些变量，然后他们共同组成一个整体，这个整体我们把它叫做闭包。

```go
package main
import (
	"fmt"
)

//累加器
func AddUpper() func (int) int {  //这是一个函数，返回的数据类型是 func(int) int
	var n int = 10
	return func (x int) int {
		n = n + x
		return n
	}
}
func main() {
	f := AddUpper()
	fmt.Println(f(1))   //打印 11
	fmt.Println(f(2))   //打印 13，因为闭包的原因
	fmt.Println(f(3))   //打印 16
}
```

##### 2、闭包说明

![闭包](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-go/%E9%97%AD%E5%8C%85.png)

①从上面的截图我们可以看到，返回的是一个匿名函数，但是这个匿名函数引用到了函数外的变量，因此这个匿名函数和函数外的变量形成一个整体，构成闭包。

②我们搞清楚闭包的关键，就是要分析出返回的函数它使用到哪些变量，因为函数和它引用到的变量共同构成了闭包。

#### 八、defer 的基本使用

##### 1、为什么需要 defer

在函数中，程序员经常需要创建资源（比如：数据库连接，文件句柄，锁等），为了在函数执行完毕后，及时的释放资源，Go 的设计者提供 defer （延时机制）

##### 2、defer 使用

```go
package main
import (
	"fmt"
)
func sum(n1 int, n2 int) int {
	//当代码执行到 defer 的时候，会暂时不执行这条语句，系统会将 defer 后面的语句压入到一个栈中
	//当函数执行完毕后，再从 defer 栈，按照先进后出的方式出栈，然后执行
	defer fmt.Println("ok1 n1 =", n1)  //执行到这的时候，这行代码被压入到栈中
	defer fmt.Println("ok2 n2 =", n2)  //执行到这的时候，这行代码被压入到栈中
	res := n1 + n2
	fmt.Println("ok3 res = ",res)
	return res
}

func main() {
	res := sum(1,2)
	fmt.Println("res = ",res)
} 

//执行后的结果：
ok3 res =  3
ok2 n2 = 2
ok1 n1 = 1
res =  3
```

##### 3、defer 细节及注意事项

> 1、当 go 执行到一个 defer 时，不会立即执行 defer 后的语句，而是将 defer 后的语句压入到一个栈中，然后继续执行函数下一个语句。
>
> 2、当函数执行完毕后，再从 defer 栈中，依次取出语句执行（遵循栈先进后出的机制）
>
> 3、将 defer 语句放入到栈时，也会将相关的值拷贝同时入栈

```go
package main
import (
	"fmt"
)
func sum(n1 int, n2 int) int {
	defer fmt.Println("ok1 n1 =", n1)  //执行到这的时候，这行代码被压入到栈中，n1 的值也会被压入到栈中
	defer fmt.Println("ok2 n2 =", n2)  //执行到这的时候，这行代码被压入到栈中，n2 的值也会被压入栈中
	n1++   //即使在这里 n1 n2 的值改变了，但是等 defer 语句执行的时候，n1 n2 的值还是未改变之前的值
	n2++
	res := n1 + n2
	fmt.Println("ok3 res = ",res)
	return res
}

func main() {
	res := sum(1,2)
	fmt.Println("res = ",res)
}

//执行后的结果：
ok3 res =  5
ok2 n2 = 2
ok1 n1 = 1
res =  5
```

**注：**

① defer 最主要的价值在于，当函数执行完毕后，可以及时的释放函数创建的资源。

② 在 golang 编程中的通常做法是，在创建资源后，比如打开文件、获取数据库资源，可以立即执行 例如：defer file.close 代码，然后你在 defer 后面，还可以继续使用创建的资源，等到函数执行完毕后，系统会从 defer 栈中按照先进后出的顺序，取出语句，关闭资源。

#### 九、函数参数的传递方式

前面我们已经说过值类型和引用类型，值类型参数默认就是值传递，而引用类型参数默认就是引用传递。

> - 值类型：基本数据类型 int 系列、float 系列、bool、string、数组和结构体 struct
> - 引用类型：指针、slice 切片、map、管道chan、interface等都是引用类型

##### 1、两种传递方式

> - 值传递
>
>
> - 引用传递

其实，不管是值传递还是引用传递，传递给函数的都是变量的副本，不同的是，值传递的是值的拷贝，引用传递的是地址的拷贝，一般来说，地址拷贝效率高，因为数据量小，而值拷贝决定于拷贝数据的大小，数据越大效率越低。

##### 2、值传递和引用传递使用特点

>- 值类型默认是值传递
>
>变量直接存储值，内存通常在栈中分配
>
>- 引用类型默认是引用传递
>
>变量存储的是一个地址，这个地址对应的空间才真正存储数据（值），内存通常在堆上分配，当没有任何变量引用这个地址时，该地址对应的数据空间就成为一个垃圾，由 GC 来回收。
>
>- 如果希望函数内的变量能修改函数外的变量，可以传入变量的地址 &，函数内以指针的方式操作变量

#### 十、变量作用域

**1、说明**

> - 函数内部声明/定义的变量叫局部变量，作用域仅限于函数内部
> - 函数外部声明/定义的变量叫全局变量，作用域在整个包都有效，如果其首字母大写，则作用域在整个程序有效
> - 如果变量是在一个代码块中，比如：for/if 中，那么这个变量的作用域就在该代码块

```go
package main
import (
	"fmt"
)

//函数外部定义的变量就是全局变量，如果变量首字母小写，则在整个包内有效，如果变量首字母大写，则在整个程序内有效
var age int = 50
var Name string = "Jimmy"

func test() {
	// 这两个变量的作用域都仅限于这个函数内，如果出了这个函数这两个变量就用不了了
	age := 10
	Name := "tom"
	fmt.Println("age = %d,Name=%s",age,Name)
}

func main() {
	fmt.Println("age = ",age)  // age = 50
	fmt.Println("Name =",Name) // Name = Jimmy

	//如果变量是在一个代码块中，比如 for/if 中，那么这个变量的作用域就在该代码块
	//就像下面 for 循环中的 i ，作用域仅限于 for 循环
	for i := 0; i < 10; i++ {
		fmt.Println("i =",i)
	}
	//这里的 i 就是局部变量
	var i int
	for i = 0; i < 10; i++ {
		fmt.Println("i = ",i)
	}
}
```

**小说明：**

```go
package main
import (
	"fmt"
)
// var age int = 5  这种写法是正确的,定义变量的时候就给它赋值
var age int
age = 5   // 这种写法是错误的，赋值语句不能写在函数体外
// age := 5  类似这种写法也就是错误的（和上面那种错误情况是等价的）
func main() {
	fmt.Println("年龄是：",age)
}
```

#### 十一、常用字符串函数

1、统计字符串的长度，按字节返回 `len(str)`

```go
func main() {
	//统计字符串的长度，按字节返回 len(str)
	// golang 的编码统一为 utf-8（ascii 的字符（字母和数字）占一个字节，汉字占用 3 个字节）
	str := "hello沈"
	fmt.Println(len(str))
}
```

2、字符串遍历，同时处理有中文问题 `r:=[]rune(str)`

```go
func main() {
	str2 := "hello和平"
	for i := 0; i < len(str2);i++ {
		//结果打印 helloåå¹³
		fmt.Printf("%c",str2[i])  // 这样直接打印会乱码，因为它是按照字节去遍历的,一个汉字占三个字节，所以遍历的时候肯定会被拆开，所以就乱码了
	}

	str3 := "hello和平"
	// 字符串遍历，同时处理有中文问题 r:=[]rune(str)
	s := []rune(str3)
	for i := 0; i < len(s); i++ {
		//结果打印 hello和平
		fmt.Printf("%c",s[i])  //这个时候它是按照字符来遍历的，所以以后你如果想要去遍历一个含有中文的字符串可以先把它转成一个含有 rune 的切片
	}
}
```

3、字符串转整数  `n,err := strconv.Atoi("123")`

```go
package main
import (
	"fmt"
	"strconv"
)
func main() {
	// 字符串转整数：n,err := strconv.Atoi("123")
	n,err := strconv.Atoi("123")
	if err != nil {
		fmt.Println("转换出错",err)
	} else {
		fmt.Println("转换成功",n)
	}
}
```

4、整数转字符串  `str = strconv.Itoa(123)`

 ```go
func main() {
	// 整数转字符串 
	str = strconv.Itoa(123)
	fmt.Printf("str=%s,类型是：%T",str,str)  // 打印 str=123,类型是：string
}
 ```

5、字符串转 `[]byte`

```go
func main() {
	//字符串转 []byte   
	var bytes = []byte("hello")
	fmt.Printf("bytes=%v",bytes)  // 打印 bytes=[104 101 108 108 111]
}
```

6、`[]byte` 转字符串

```go
func main() {
	//[]byte 转字符串
	str = string([]byte{97,98,99})
	fmt.Printf("str=%v",str)  //打印 str=abc
}
```

7、10进制转 2,8,16 进制 `strconv.FormatInt(123,2)`

```go
func main() {
	//10进制转 2,8,16 进制 strconv.FormatInt(123,2)
	str = strconv.FormatInt(123,2)
	fmt.Printf("123对应的二进制是：%v",str)  //打印 123对应的二进制是：1111011
	str = strconv.FormatInt(123,16)
	fmt.Printf("123对应的十六进制是：%v",str) // 打印123对应的十六进制是：7b
}
```

8、查找子串是否在指定的字符串中：`strings.Contains("seafood","foo")`

```go
func main() {
	//查找子串是否在指定的字符串中：strings.Contains("seafood","foo")
	b := strings.Contains("seafood","foo")
	fmt.Printf("b=%v",b)   // 打印 b=true
}
```

9、统计一个字符串有几个指定的子串 `strings.Count("cehhh","h")`

```go
func main() {
	//统计一个字符串有几个指定的子串 strings.Count("cehhh","h")
	var i = strings.Count("cehhh","h")
	fmt.Println(i)   //打印 3
}
```

10、不区分大小写的字符串比较（== 是区分字母大小写的）：`strings.EqualFold("abc","Abc")`

```go
func main() {
	// 不区分大小写的字符串比较（== 是区分字母大小写的）
	b = strings.EqualFold("abc","Abc")
	fmt.Printf("%v",b)  // 打印 true
	fmt.Println("abc" == "Abc")// 打印 false,区分字母大小写
}
```

11、返回子串在字符串第一次出现的 index 值，如果没有则返回 -1 ： `strings.Index("NLT_abc","a")`

```go
func main() {
	//返回子串在字符串第一次出现的 index 值，如果没有则返回 -1
	index := strings.Index("NLT_abc","a")
	fmt.Println(index)  // 打印 4
}
```

12、返回子串在字符串中最后一次出现的 index,如果没有返回 -1 ：`strings.LastIndex("go golang","go")`

```go
func main() {
	//返回子串在字符串中最后一次出现的 index,如果没有返回 -1 
	index = strings.LastIndex("go golang","go")
	fmt.Println(index)  // 打印 3
}
```

13、将指定的子串替换成另外一个子串：`strings.Replace("go go golang","go","shp",n)`，其中参数 n 可以指定你希望替换几个，如果 n = -1 表示全部替换

```go
func main() {
	//将指定的子串替换成另外一个子串：strings.Replace("go go golang","go","shp",n)
	//其中参数 n 可以指定你希望替换几个，如果 n = -1 表示全部替换
	str = strings.Replace("go go golang","go","shp",-1)
	fmt.Println(str)   // 打印 shp shp shplang
}
```

14、按照指定的某个字符为分隔标识，将一个字符串拆分成字符串数组：`strings.Split("hello,world",",")`

```go
func main() {
	//将指定的子串替换成另外一个子串：strings.Replace("go go golang","go","shp",n)
	//其中参数 n 可以指定你希望替换几个，如果 n = -1 表示全部替换
	str = strings.Replace("go go golang","go","shp",-1)
	fmt.Println(str)   // 打印 shp shp shplang
}
```

15、将字符串的字母进行大小写的转换：`strings.ToLower("Go")  strings.ToUpper("go")`

```go
func main() {
	//将字符串的字母进行大小写的转换：strings.ToLower("Go")  strings.ToUpper("go")
	str = strings.ToLower("Go")
	str2 = strings.ToUpper("hello world")
	fmt.Printf("str=%v,str2=%v",str,str2)  // 打印 str=go,str2=HELLO WORLD
}
```

16、将字符串左右两边的空格去掉：`strings.TrimSpace(" hello world ")`

```go
func main() {
	//将字符串左右两边的空格去掉：strings.TrimSpace(" hello world ")
	str = strings.TrimSpace(" hello world ")
	fmt.Println(str)  // 打印 hello world
}
```

17、将字符串左右两边指定的字符去掉：`strings.Trim("p hello p","p")`

```go
func main() {
	// 将字符串左右两边指定的字符去掉：strings.Trim("p hello p","p")
	str = strings.Trim("p hello p","p")
	fmt.Println(str)  // 打印 hello
}
```

18、将字符串左边指定的字符去掉：`strings.TrimLeft("p hello p","p")`

将字符串右边指定的字符去掉：`strings.TrimRight("p hello p","p")`

```go
func main() {
	//将字符串左边指定的字符去掉：strings.TrimLeft("p hello p","p")
	//将字符串右边指定的字符去掉：strings.TrimRight("p hello p","p")
	strLeft := strings.TrimLeft("p hello p","p")
	strRight := strings.TrimLeft("p hello p","p")
	fmt.Printf("strLeft=%v",strLeft)  // 打印 strLeft= hello p
	fmt.Printf("strRight=%v",strRight)  // 打印 strRight= hello p
}
```

19、判断字符串是否以指定的字符串开头：`strings.HasPrefix("www.baidu.com","www")`

```go
func main() {
	//判断字符串是否以指定的字符串开头：strings.HasPrefix("www.baidu.com","www")
	b = strings.HasPrefix("www.baidu.com","www")
	fmt.Println(b)   // 打印 true
}
```

20、判断字符串是否以指定的字符串结尾：`strings.HasSuffix("www.baidu.com","com")`

```go
func main() {
	//判断字符串是否以指定的字符串结尾：strings.HasSuffix("www.baidu.com","com")
	b = strings.HasSuffix("www.baidu.com","com")
	fmt.Println(b)   // 打印 true
}
```

#### 十二、时间和日期相关的函数

##### 1、时间和日期相关函数，需要导入 time 包

##### 2、获取当前时间  ：   `time.Now()`

```go
package main
import (
	"fmt"
	"time"
)
func main() {
	// 1.获取当前时间
	now := time.Now()
	fmt.Printf("now=%v,type=%T",now,now)
	// 打印 now=2018-12-04 23:14:33.8390633 +0800 CST m=+0.013000701,type=time.Time
}
```

##### 3、格式化时间

```go
package main
import (
	"fmt"
	"time"
)
func main() {
    // 1.获取当前时间
	now := time.Now()
	// 3.格式化时间
	fmt.Printf("当前年月日 %d-%d-%d %d:%d:%d",now.Year(),now.Month(),
	now.Day(),now.Hour(),now.Minute(),now.Second())  // 打印 当前年月日 2018-12-4 23:47:30

	// SPrintf 会将打印的内容返回给一个字符串
	dateStr := fmt.Sprintf("当前年月日 %d-%d-%d %d:%d:%d",now.Year(),now.Month(),
	now.Day(),now.Hour(),now.Minute(),now.Second())
	fmt.Printf("dateStr=%v",dateStr)  // 打印 dateStr=当前年月日 2018-12-4 23:57:1

	fmt.Println()
	//4.格式化时间的第二种方式：
	// "2006/01/02 15:04:05" 这个字符串的各个数字是固定的，必须这样写,否则你就拿不到对应的时间
	// "2006/01/02 15:04:05" 这个字符串各个数字可以自由的组合
	// 至于为什么是这个时间，官方文档并没有说明，姑且你可以把这个时间认为是 go 语言设计者在这个时间突然有一个想法去设计出 go 这门语言,所以采用了这个时间
	fmt.Printf(now.Format("2006/01/02 15:04:05"))  // 2018/12/05 00:06:03
	fmt.Println()
	fmt.Printf(now.Format("2006/01/02")) // 2018/12/05
	fmt.Println()
	fmt.Printf(now.Format("15:04:05"))  // 00:06:03
}
```

##### 4、时间常量

- ```go
  const (
      Nanosecond  Duration = 1
      Microsecond          = 1000 * Nanosecond
      Millisecond          = 1000 * Microsecond
      Second               = 1000 * Millisecond
      Minute               = 60 * Second
      Hour                 = 60 * Minute
  )
  ```

- 常量的作用：在程序中可用于获取指定时间单位的时间，比如想得到 100 毫秒，`100 * time.Millisecond`

```go
package main
import (
	"fmt"
	"time"
)
func main() {
    // 1.获取当前时间
	now := time.Now()
	// 需求：每隔一秒打印一个数字，到 10 的时候退出
	j := 0
	for {
		j ++
		fmt.Println(j)
		// 休眠 1 秒
		time.Sleep(time.Second)
		// 休眠 0.1 秒
		time.Sleep(time.Millisecond * 100)  // 你这里不能写 time.Second * 0.1,写 0.1 编译器通不过
		if j == 10 {
			break
		}
	}
}
```

##### 5、获取当前 unix 时间戳和 unixnano 时间戳

```go
package main
import (
	"fmt"
	"time"
)
func main() {
	// 1.获取当前时间
	now := time.Now()
	fmt.Printf("now=%v,type=%T",now,now)
	// 获取 unix 时间戳和 unixnano 时间戳
	fmt.Printf("unix 时间戳=%v,unixnano 时间戳=%v",now.Unix(),now.UnixNano())
	// 打印 unix 时间戳=1544020617,unixnano 时间戳=1544020617006029400
}
```

**关于时间的小练习：**

```go
package main
import (
	"fmt"
	"time"
	"strconv"
)
func test() {
	str := ""
	for i := 0; i < 100; i++ {
		str += "hello" + strconv.Itoa(i)
	}
}
func main() {
	startTime := time.Now().UnixNano()
	fmt.Println("开始执行时间:",startTime)  // 开始执行时间: 1544022424218396000
	test()
	endTime := time.Now().UnixNano()
	fmt.Println("结束执行时间:",endTime)   // 结束执行时间: 1544022424219396100
	fmt.Println("花费时间：",endTime - startTime)  // 花费时间： 1000100
}
```

#### 十三、内置函数

Golang 设计者为了编程方便，提供了一些函数，这些函数可以直接使用，我们称为 Go 的内置函数

1、len：用来求长度，比如：string、array、slice、map、channel

2、new：用来分配内存，主要用来分配值类型，比如 int、float32 返回的是指针

```go
package main
import (
	"fmt"
)
func main() {
	num1 := 100
	// 打印 num1 的类型：int,num1 的值：100,num1 的地址：0x1f2200a0
	fmt.Printf("num1 的类型：%T,num1 的值：%v,num1 的地址：%v\n",num1,num1,&num1)

	num2 := new(int)  // 这个 num2 就是一个指针类型的变量 *int
	//num2的类型：*int,num2的值：0x1f2200ac, num2的地址：0x1f214108,num2 这个指针指向的值：0x1f2200ac
	fmt.Printf("num2的类型：%T,num2的值：%v, num2的地址：%v,num2 这个指针指向的值：%v",num2,num2,&num2,num2) 
}
```

3、make：用来分配内存，主要用来分配引用类型，比如 channel、map、slice

#### 十四、Go 错误处理机制

##### 1、错误处理引言

1）、默认情况下，当程序发生错误后（panic），程序就会退出

2）、如果我们希望，当发生错误后，可以捕获到错误，并进行处理，保证程序可以继续执行，还可以在捕获到错误后，给管理员一个提示（邮件、短信）

```go
package main
import (
	"fmt"
)
func test() {
	num1 := 10
	num2 := 0
	res := num1 / num2   //程序执行到这行的时候，直接报错退出
	fmt.Println("res = ",res)
}

func main() {
	test()
	fmt.Println("main 下面的代码")
}
```

##### 2、错误处理基本说明

1）、Go 语言追求简洁优雅，所以，Go 语言不支持传统的 try..catch..finally 这种处理

2）、Go 中引入的处理方式为：defer，panic，recover

3）、这几个异常使用场景可以这么简单描述：Go 中可以抛出一个 panic 异常，然后在 defer 中通过 recover 捕获这个异常，然后正常处理

```go
package main
import (
	"fmt"
)
func test() {
	// 使用 defer + recover 来捕获和处理异常
	defer func() {
		err := recover()   // recover() 为内置函数，可以捕获异常
		if err != nil {  // 说明捕获到异常
			fmt.Println("err=",err)   // err= runtime error: integer divide by zero
			//这里你还可以把错误信息发送给管理员
			fmt.Println("发送邮件给 XXX 管理员")
		}
	}()
	num1 := 10
	num2 := 0
	res := num1 / num2  
	fmt.Println("res = ",res)
}

func main() {
	test()
	fmt.Println("main 下面的代码")  //这里的代码也会执行，打印 main 下面的代码
}
```

##### 3、自定义错误

Go 程序中，也支持自定义错误，使用 errors.New 和 panic 内置函数

> - `errors.New("错误说明")`，会返回一个 error 类型的值，表示一个错误
> - panic 内置函数，接收一个 interface{} 类型的值（也就是任何值了）作为参数，可以接收 error 类型的变量，输出错误信息，并退出程序

```go
package main
import (
	"fmt"
	"errors"
)
// 函数去读取配置文件 init.conf 的信息，如果文件名传入不正确，我们就返回一个自定义错误
func readConf(name string) (err error) {
	if name == "init.conf" {
		// 去读取配置文件...
		return nil
	} else {
		// 返回一个自定义错误
		return errors.New("读取文件时发生错误")
	}
}

func test2() {
	err := readConf("init2.conf")
	if err != nil {
		//说明读取文件发送错误，那么我输出这个错误，并终止程序
		panic(err)   // panic 内置函数，可以接收 error 类型的变量，输出错误信息，并退出程序
	}
	fmt.Println("test2...")
}
func main() {
	test2()
}
// 分析一下上面函数的执行过程
// 首先我写了一个 readConf 函数，这个函数传入一个 string ，返回一个 error 类型的变量，返回我去判断传入的参数如果是 init.conf，我就返回 nil，否则我就返回一个自定义错误，这个自定义错误是通过 「errors.New("错误说明")」这个函数来实现的，这个函数会返回一个 error 类型的值，所以我在 test2 中调用时，如果发现结果不为 nil ，那么肯定就发生我们自定义的异常了，那么我就用  panic 内置函数，接收 error 类型的变量，输出错误信息，并终止程序
```

------

### 数组和切片

#### 一、数组快速入门

##### 1、数组介绍

数组可以存放多个同一类型数据，数组也是一种数据类型，在 Go 中，数组是值类型。

```go
package main
import (
	"fmt"
)
func main() {
	// 定义一个数组
	var hens [4]float64
	// 给数组的每个元素赋值
	hens[0] = 2.0
	hens[1] = 3.0
	hens[2] = 4.0
	hens[3] = 5.0
	// 遍历数组元素求出元素总和
	total := 0.0
	for i := 0; i < len(hens); i++ {
		total += hens[i]
	}
	fmt.Printf("total = %v",total)
}
```

#### 二、数组的使用

##### 1、数组的定义和内存情况

**数组定义：**

> `var 数组名 [数组大小]数据类型`

```go
package main
import (
	"fmt"
)
func main() {
	var intarry [3]int
	// 当我们定义完数组后，其实数组的各个元素就已经有默认值了，就是 0
	fmt.Println(intarry)   // [0 0 0]
	// intarray 的地址是：0x1f25e080,intarry[0] 的地址是：0x1f25e080, intarry[1]的地址是：0x1f25e084
	fmt.Printf("intarray 的地址是：%p,intarry[0] 的地址是：%p, intarry[1]的地址是：%p",
		&intarry,&intarry[0],&intarry[1])

	// 上面我们定义了一个数组，数组名叫 intarry,这个 intarry 里面存的其实就是
	//数组在内存空间中的首地址值，通过上面的打印我们也可以发现，我们打印 &intarry 的
	//地址，其实是和 &intarry[0] 地址是一样的

	//2.数组中第二个元素的地址就是第一个元素的地址加上这个数组类型占用的字节数
	//比如说，我这里定义的是 int 类型的数组，占用 8 个字节，那么我数组第二个元素的地址就是第一个元素地址加 8
	// 通过上面打印的地址信息，我们也可以验证这个结论是正确的
}
```

**小总结：**

> 1、你定义一个数组，数组名中存的其实就是这个数组在内存空间中的首地址值。
>
> 2、数组中第二个元素的地址其实就是第一个元素的地址加上这个数组类型所占用的字节数。
>

##### 2、数组常规使用

1）、访问数组元素

`数组名[下标]` 比如：你想使用 a 数组的第三个元素，a[2]

```go
package main
import (
	"fmt"
)
func main() {
	// 从终端循环输入 5 个成绩，保存到 float64 数组, 并输出
	var score [5]float64
	for i := 0; i < len(score); i++ {
		fmt.Printf("请输入第%d个人的成绩：", i + 1)
		fmt.Scanln(&score[i])
	}
	fmt.Println(score)   // [2 3 4 5 6]
}
```

2）、五种初始化数组的方式

> - `var numarr01 [3]int = [3]int{1,2,3}`
> - `var numarr02 = [3]int{4,5,6}` 
> - `var numarr03 = [...]int{7,8,9}` 
> - `var numarr04 = [...]int{1:20,2:8,0:5}`
> - `numarr05 := [3]int{2,2,3}`

```go
package main
import (
	"fmt"
)
func main() {
	// 四种初始化数组的方式
	var numarr01 [3]int = [3]int{1,2,3}
	fmt.Println("numarr01=",numarr01)   // numarr01= [1 2 3]
	var numarr02 = [3]int{4,5,6}  // 使用类型推导，你把右边的赋值给左边，编译器就会自动推导出你左边定义的数组有多大，是什么类型的
	fmt.Println("numarr02=",numarr02)   // numarr02= [4 5 6]
	// 这里的 [...] 是固定写法
	var numarr03 = [...]int{7,8,9}  
	fmt.Println("numarr03=",numarr03)   // numarr03= [7 8 9]
	// 下标为 0 的值为 5，下标为 1 的值为 20 ，下标为 2 的值为 8
	var numarr04 = [...]int{1:20,2:8,0:5}
	fmt.Println("numarr04=",numarr04)   // numarr04= [5 20 8]

	numarr05 := [3]int{2,2,3}
	fmt.Println("numarr05=",numarr05)   // numarr05= [2 2 3]
}
```

##### 3、数组的遍历

方式一：常规遍历

就是利用  for 循环进行遍历，前面已经演示过，这里不再赘述。

方式二： for -- range 结构遍历

**基本语法：**

> for index,value := range array01 {
>
> }
>
> 1、第一个返回值 index 是数组的下标
>
> 2、第二个 value 是在该下标位置的值
>
> 3、他们都是仅在 for 循环内部可见的局部变量
>
> 4、遍历数组元素的时候，如果不想使用下标 index ，可以直接把下标 Index 设置为下划线
>
> 5、index  和  value 的名称不是固定的，我们可以自己指定，一般命名为 index 和 value

```go
package main
import (
	"fmt"
)
func main() {
	// for-range 遍历数组
	var heroes [3]string = [3]string{"宋江","林冲","吴用"}
	for index,value := range heroes {
		fmt.Printf("数组下标是：%d,数组的值是：%v\n",index,value)
	}
	// 如果你只关心 value 值，不关心下标
	for _,value := range heroes {
		fmt.Printf("数组的值是：%v\n",value)
	}
}
```

##### 4、数组注意事项和细节

1）、数组是多个==相同类型==数据的组合，一个数组一旦声明了/定义了，其==长度是固定的，不能动态变化==

2）、`var arr []int` ，中括号中如果什么都不写，这时 arr 就是一个 slice 切片

3）、数组中的元素可以是任何数据类型，包括值类型和引用类型，但是不能混用

4）、数组创建后，如果没赋值，有默认值

> 数值类型数组，默认值为 0
>
> 字符串数组，默认值为 ""
>
> bool 数组，默认值为 false

5）、使用数组的步骤：

> 1、声明数组并开辟空间
>
> 2、给数组各个元素赋值
>
> 3、使用数组

6）、数组的下标是从 0 开始的

7）、==Go 的数组属于值类型==，在默认情况下是值传递，因此会进行值拷贝。数组间不会相互影响。

```go
package main
import (
	"fmt"
)
// 在 go 中，数组的长度，是数组类型的一部分
func test01 (arr [3]int) {  // 在 go 中会认为 [3]int 和 [4]int 不是同一种数据类型，如果这你写 []int 那么它就不是一个数组，而是一个切片
	arr[0] = 10
}

func main() {
	var arr [3]int = [3]int{2,3,4}
	test01(arr)  // 我在函数中把数组中的一个元素改了，但是发现原数组并没有发生变化，就是因为传递的时候进行了值拷贝，拷贝了一份新的数组传递到函数里面
	fmt.Println("arr[0]=",arr[0])  // arr[0]= 2
}
```

8）、如果想在其他函数中，去修改原来的数组，可以使用 引用传递（指针方式）

```go
package main
import (
	"fmt"
)
func test02(arr *[3]int) {
    // 这个 arr 变量存的是下面定义的数组的地址，然后我通过地址找到这个数组，然后修改里面的值，原数组自然就改变了
	(*arr)[0] = 10
}

func main() {
	var arr [3]int = [3]int{2,3,4}
	test02(&arr)  // 使用引用传递的方式，发现原数组的值已经被改了
	fmt.Println("arr[0]=",arr[0])  // arr[0]= 10
}
```

9）、在 Go 中，长度是数组类型的一部分，在传递函数参数时，需要考虑数组的长度

```go
package main
import (
	"fmt"
)
func test03(arr [4]int) {
	fmt.Println("aa")
}

func main() {
	var arr [3]int = [3]int{2,3,4}
	test03(arr)   // 这里会编译错误，因为不能把 [3]int 传递给 [4]int
}
```

**小案例：**

golang 随机生成一个数组：

```go
package main
import (
	"fmt"
	"math/rand"
	"time"
)
func main() {
	// 随机生成 5 个数，并将其反转打印
	var arr [5]int 
	// 为了每次生成的随机数不一样，我们需要给一个 seed 值
	rand.Seed(time.Now().UnixNano())
	for i:=0; i < len(arr); i++ {
		// 这里有个问题，就是你如果不指定 seed,不管随机多少次生成的数组都是 [81 87 47 59 81]
		arr[i] = rand.Intn(100)
	}
	fmt.Println(arr)
}
```

#### 三、切片

##### 1、基本介绍

> - 切片的英文是 slice
> - 切片是数组的一个引用，因此切片是引用类型，在进行传递时，遵守引用传递的机制
> - 切片的使用和数组类似，遍历切片、访问切片的元素和求切片长度数组都一样
> - ==切片的长度是可以变化的，因此切片是一个可以动态变化的数组==
>
> 

**切片定义的基本语法：**

> `var 切片名 []类型`

```go
package main
import (
	"fmt"
)
func main() {
	//演示切片的基本使用
	var intArr [4]int = [...]int{2,3,4,5}
	slice := intArr[0:2]
	//1. slice 就是切片名
	//2. intArr[0:2] 表示 slice 引用到 intArr 这个数组
	//3.引用 intArr 数组的起始下标为 0，结束下标为 2（但是不包括 2）
	fmt.Println(slice)    // [2 3]
}
```

##### 2、切片使用三种方式

**方式一：**

定义一个切片，然后让切片去引用一个已经创建好的数组。

```go
package main
import (
	"fmt"
)
func main() {
	//演示切片的基本使用
	var intArr [4]int = [...]int{2,3,4,5}
	slice := intArr[1:3]
	fmt.Println(slice)    // [3 4]
}
```

![切片内存结构图](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-go/%E5%88%87%E7%89%87%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%E5%9B%BE.png?q-sign-algorithm=sha1&q-ak=AKIDOLaAhqPnxkl5cBCNeqrDhGgbYmGm5kz7&q-sign-time=1547386702;1547388502&q-key-time=1547386702;1547388502&q-header-list=&q-url-param-list=&q-signature=7fd7ea6085d23647eb970aa77e76fecbd785330a&x-cos-security-token=aeebe9d2e801c4495967bb8a9bb48c2794cd17b210001)



**方式二：**

通过 `make` 来创建切片

**基本语法：**

> `var 切片名 []type = make([]type,len,[cap])`
>
> type 就是切片的数据类型，len 就是切片的大小，cap 就是切片的容量，该参数可选，==如果你指定了 cap ,则要求 cap >= len==

```go
package main
import (
	"fmt"
)
func main() {
	// 切片的使用 make 方法
	var slice []float64 = make([]float64,5,10)
	fmt.Println(slice)  // [0 0 0 0 0]
	slice[2] = 10
	slice[3] = 20
	fmt.Println(slice)   // [0 0 10 20 0]
	fmt.Println("slice 的 size 是:",len(slice))   // slice 的 size 是: 5
	fmt.Println("slice 的 cap 是：",cap(slice))  // slice 的 cap 是： 10
}
```



![make 方式切片内存结构图](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-go/mak%E6%96%B9%E5%BC%8F%E5%88%87%E7%89%87%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%E5%9B%BE.png?q-sign-algorithm=sha1&q-ak=AKIDkWMMtk3j5CQMBW934JZyeiMVdJ21Qvfr&q-sign-time=1547387171;1547388971&q-key-time=1547387171;1547388971&q-header-list=&q-url-param-list=&q-signature=217d5e1562020357889ccb0c2d4d9b9bdabfd72d&x-cos-security-token=64fb64a22bbd27a2d26a51e5bb026184cec1207610001)

这种方式和上面最主要的区别就是下面的数组，只能通过 slice 来访问了，上面的那种方式，数组中的元素即能通过 slice 去访问，也能通过你定义的数组去访问。所以我们通过 make 去创建切片的方式，数组中的元素相当于对外部不可见。

**小总结：**

> - 通过 make 方式创建切片可以指定切片的大小和容量
> - 如果没有个切片的各个元素赋值，那么就会使用默认值（int、float 系列默认值为 0，string 默认值为空串，bool 默认值为 false）
> - 通过 make 方式创建的切片对应的数组是由 make 底层维护，对外不可见，即只能通过 slice 去访问各个元素

**方式三：**

定义一个切片，直接就指定具体的数组，使用原理类似 make 的方式 

```go
package main
import (
	"fmt"
)
func main() {
	// 定义一个切片，直接就指定具体的数组，使用原理类型 make 的方式
	var strSlice []string = []string{"kimi","kelly","Jimmy"}
	fmt.Println(strSlice)   // [kimi kelly Jimmy]
	fmt.Println("strSlice 的 size 是:",len(strSlice))   // strSlice 的 size 是: 3
	fmt.Println("strSlice 的 cap 是：",cap(strSlice))  // strSlice 的 cap 是： 3
}
```

**再次总结一下方式一和方式二的区别：**

> 方式一时直接引用数组，这个数组是事先存在的，程序员是可见的。
>
> 方式二是通过 make 方式来创建切片的，make 也会创建一个数组，是由切片在底层进行维护，程序员是看不见的。该数组只能通过切片访问。

##### 3、切片的遍历

```go
package main
import (
	"fmt"
)
func main() {
	// 使用常规 for 循环遍历切片
	var arr [5]int = [...]int{10,20,30,40,50}
	slice := arr[1:4]
	for i := 0; i < len(slice); i++ {
		fmt.Printf("slice[%v] = %v\n",i,slice[i])
	}

	// 使用 for--range 方式遍历切片
	for i,v := range slice {
		fmt.Printf("i=%v,v=%v",i,v)
	}
}
```

##### 4、切片注意事项和细节说明

1）、切片初始化时，`var slice = arr[startIndex:endIndex]`(使用引用数组的方式创建切片)，这个切片截取的是前包括后不包括

2）、切片初始化时，仍然不能越界。范围在[0-len(arr)] 之间，但是可以动态增长

> - `var slice = arr[0:end]`   意思就是从第 0 给位置开始截取到指定下标。可以简写成：   `var slice = arr[:end]`
> - `var slice = arr[start:len(arr)]`   意思是从指定下标一直截取到最后。可以简写成： `var slice = arr[start:]`
> - `var slice = arr[0:len(arr)]`     意思是从开始位置一直截取到最后位置。 可以简写成：`var slice = arr[:]`

3）、cap 是一个内置函数，用于统计切片的容量，即最大可以存放多少个元素

4）、切片定义完后，还不能使用，因为本身是一个空的，需要让其引用到一个数组，或者 make 一个空间供切片来使用

5）、切片可以继续切片

6）、用 append 内置函数，可以对切片进行动态追加

```go
package main
import (
	"fmt"
)
func main() {
	// 用 append 内置函数，可以对切片进行动态追加
	var slice3 []int = []int{10,20,30}
	slice3 = append(slice3,40,50)
	fmt.Println(slice3)   // [10 20 30 40 50]

	slice4 := slice3[0:3]
	// 通过 append 将切片 slice4 追加给 slice3
	slice3 = append(slice3,slice4...)   // 第二个参数必须是切片,并且后面要带三个点
	fmt.Println(slice3)   // [10 20 30 40 50 10 20 30]
}
```

![append底层原理](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-go/append%E5%BA%95%E5%B1%82%E5%8E%9F%E7%90%86.png?q-sign-algorithm=sha1&q-ak=AKIDCOSy5GJXVwxXF79ijPAipQ33fXt7MjIU&q-sign-time=1547563802;1547565602&q-key-time=1547563802;1547565602&q-header-list=&q-url-param-list=&q-signature=3bd09358e0d68d77a7c6b29771700136c3d0405a&x-cos-security-token=87cdceaccc6be849cfc1470d81a29ede1d720ab610001)

简单解释一下上面这个图，说一下 append 的底层原理：

> 首先左边第一行代码，你声明了一个切片，会在内存中创建一个数组，然后切片指向这个数组，这时候第二行代码，你要进行 append 操作，它会在内存中新创建一个数组，数组的长度可以根据你原来 slice3 的长度，加上你要追加的 40 50 来确定，数组创建完之后里面的元素默认都是 0 ，然后它会把 slice3 中的数据拷贝一份到新的数组中（因为不可能就在原来 slice3 的基础上进行扩容，数组的长度一旦确定就不可能修改了，所以肯定是新创建一个数组），这时候数组中就会有原来 10 20 30 三个数据，然后在把你追加的元素放到新的数组中，这样就完成了。然后你这个数组是把它继续赋值给 slice3，所以原来  slice3 的引用会改变，指向这个新的数组，原来的旧的数组因为没有切片指向它，会变成垃圾被回收。

**总结：**

> - 切片 append 操作的本质就是对数组扩容
> - go 底层会创建一个新的数组 newArr
> - 将 slice 原来包含的元素拷贝到新的数组 newArr
> - slice 重新引用到 newArr

7）、切片的拷贝操作（切片使用 copy 内置函数完成拷贝）

```go
package main
import (
	"fmt"
)
func main() {
	// 切片的拷贝操作，使用 copy 内置函数完成拷贝
	var slice5 []int = []int{1,2,3,4}
	var slice6 = make([]int,7)
	copy(slice6,slice5)
	fmt.Println(slice6)   // [1 2 3 4 0 0 0]
}
```

**说明：**

1、copy(param1,param2)  参数的数据类型都必须要是切片

2、上面的 slice5 和 slice6 数据空间是相互独立的，互不影响（也就是说我修改 slice5 中的元素，是不会影响 slice6 中的元素的）

**关于切片拷贝的一个小点：**

```go
package main
import (
	"fmt"
)
func main() {
	// 切片的拷贝操作，使用 copy 内置函数完成拷贝
	var slice5 []int = []int{1,2,3,4}
	var slice6 = make([]int,1)
	copy(slice6,slice5)   // slice6 的长度比 slice5 的长度小，执行切片拷贝时不会报错
	fmt.Println(slice6)   // [1]
}
```

8）、切片是引用类型，所以在传递时，遵守引用传递机制

```go
package main
import (
	"fmt"
)
func main() {
	var slice []int
	var arr [4]int = [...]int {1,2,3,4}
	slice = arr[:]
	var slice2 = slice
	slice2[0] = 10
	fmt.Println("slice2",slice2)   // slice2 [10 2 3 4]
	fmt.Println("slice",slice)    // slice [10 2 3 4]
	fmt.Println("arr",arr)       // arr [10 2 3 4]
}
```

```go
package main
import (
	"fmt"
)

func test (slice []int) {   // 这里传递给形参的实际上是一个地址值
	slice[0] = 20
}
func main() {
	var slice []int = []int {1,2,3}
	fmt.Println(slice)  // [1 2 3]
	test(slice)
	fmt.Println(slice)   // [20 2 3]
}
```

##### 5、string 和 slice

1）、string 底层是一个 byte 数组，因此 string 也可以进行切片处理

```go
package main
import (
	"fmt"
)
func main() {
	//string 底层是一个 byte 数组，因此 string 也可以进行切片处理
	str := "i love Jimmy"
	slice := str[7:]
	fmt.Println(slice)   // Jimmy
}
```

2）、string 和切片在内存中的结构图

![string 和 slice 内存结构图](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-go/string%E5%92%8Cslice%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%E5%9B%BE.png?q-sign-algorithm=sha1&q-ak=AKIDPev1jUWun6FpgARtbmAE2Omp2P9xDwFP&q-sign-time=1550412417;1550414217&q-key-time=1550412417;1550414217&q-header-list=&q-url-param-list=&q-signature=786a0abb6f75abcf6941ab12eda0468e2241f201&x-cos-security-token=32d02c2546903552e781c777994c8194194f342310001)

3）、string 是不可变的，也就说不能通过 str[0]='z' 方式修改字符串

```go
package main
import (
	"fmt"
)
func main() {
	// string 是不可变的，也就说不能通过 str[0]='z' 方式修改字符串
	str[0] = 'z'  // 编译不通过
}
```

4）、如果需要修改字符串，可以先将 string  --> []byte 或者 []rune  --> 修改  --> 重写转成 string

```go
package main
import (
	"fmt"
)
func main() {
	//string 底层是一个 byte 数组，因此 string 也可以进行切片处理
	str := "i love Jimmy"
	// 如果需要修改字符串，可以先将 string  --> []byte 或者 []rune  --> 修改  --> 重写转成 string
	// 把 str 修改为 "u love Jimmy"
	arr1 := []byte(str)
	arr1[0] = 'u'
	str = string(arr1)
	fmt.Println(str)   // u love Jimmy
}
```

上面还有个细节问题：

```go
package main
import (
	"fmt"
)
func main() {
	//string 底层是一个 byte 数组，因此 string 也可以进行切片处理
	str := "i love Jimmy"
	// 细节，我们转成 []byte 后，可以处理英文和数字，但是不能处理中文
	// 原因是 []byte 按照字节来处理，而一个汉字，是 3 个字节，因此就会出现乱码
	// 解决方法是将 string 转成 []rune 即可，因为 []rune 是按字符处理，兼容汉字
	arr1 := []rune(str)
	arr1[0] = '发'
	str = string(arr1)
	fmt.Println(str)   // 发 love Jimmy
}
```

##### 6、切片练习

```go
package main
import (
	"fmt"
)
func fbn(n int) ([]uint64) {
	slice := make([]uint64,n)
	slice[0] = 1
	slice[1] = 1
	for i := 2; i < n; i++ {
		slice[i] = slice[i - 1] + slice[i - 2]
	}
	return slice;
}
func main() {

	/*
		练习：
			1.可以接收一个 n int
			2.能够将斐波那契的数列放到切片中
			3.斐波那契数列形式：
			arr[0]=1;arr[1]=1;arr[2]=2;arr[3]=3;arr[4]=5
	*/
	slice := fbn(4)
	fmt.Println(slice)
}
```

#### 四、排序和查找

##### 1、排序基本介绍

排序是将一组数据，按照指定的顺序进行排列的过程。

**排序的分类：**

> 1）、内部排序
>
> 指将需要处理的所有数据都加载内部存储器中进行排序。包括：交换式排序法、选择式排序法和插入式排序法
>
> 2）、外部排序
>
> 数据了过大，无法全部加载到内存中，需要借助外部存储进行排序。包括：合并排序法和直接合并排序法

**交换式排序法：**

> 交换式排序属于内部排序法，是运用数据值比较后，按照判断规则对数据位置进行交换，以达到排序的目的。
>
> 交换式排序分为两种：
>
> - 冒泡排序（Bubble sort）
> - 快速排序（Quick sort）

冒泡排序：

```
arr=[24,69,80,57,13]  让前面的数和后面的数进行比较，如果前面的数大，则交换
第一轮排序：
第 1 次比较：[24,69,80,57,13] 
第 2 次比较：[24,69,80,57,13] 
第 3 次比较：[24,69,57,80,13] 
第 4 次比较：[24,69,57,13,80] 

第二轮排序：
第 1 次比较：[24,69,57,13,80] 
第 2 次比较：[24,57,69,13,80] 
第 3 次比较：[24,57,13,69,80] 

第三轮排序：
第 1 次比较：[24,57,13,69,80] 
第 2 次比较：[24,13,57,69,80] 

第四轮排序：
第 1 次比较：[13,24,57,69,80]
```

**冒泡排序的规则：**

> 1）、一共会经过  n -1 轮比较，每一轮将会确定一个数的位置
>
> 2）、每一轮的比较次数在逐渐减少
>
> 3）、当发现前面一个数比后面一个数大的时候就进行了交换

**冒泡排序代码：**

```go
package main
import (
	"fmt"	
)

func bubbleSort(arr *[5]int) {
	fmt.Println("排序前：",*arr)   // 排序前： [24 69 80 57 13]
	// 临时变量
	temp := 0
	//外层循环控制第几轮排序
	for i := 0; i < len(*arr) - 1; i ++ {
		//内层循环控制比较多少次
		for j := 0; j < len(*arr) - 1 - i; j++ {
			if (*arr)[j] > (*arr)[j + 1] {
				temp = (*arr)[j + 1]
				(*arr)[j + 1] = (*arr)[j]
				(*arr)[j] = temp
			} 
		}
	}
	fmt.Println("排序后：",*arr)    // 排序后： [13 24 57 69 80]
}
func main() {
	// 定义一个数组
	arr := [5]int{24,69,80,57,13}
	bubbleSort(&arr)
}
```

##### 2、顺序查找

```go
package main
import (
	"fmt"
)
func main() {
	// 有一个数组：Jimmy kelly kimi jyson
	// 从键盘中任意输入一个名称，判断数组中是否包含此名词
	names := [4]string{"Jimmy","kelly","kimi","jyson"}
	var starName = ""
	fmt.Println("请输入一个名字：")
	fmt.Scanln(&starName)
	for i := 0; i < len(names); i++ {
		if starName == names[i] {
			fmt.Printf("找到 %v,下标是 %v",starName,i)
			break
		} else if i == (len(names) -1) {
			fmt.Println("没有找到",starName)
		}
	}
	// 将要查找的内容与数组中的内容逐一比较
}
```

##### 3、二分查找

```go
package main
import (
	"fmt"
)
// 二分查找函数
func binaryFind(arr *[6]int,leftIndex int, rightIndex int,value int) {
	if leftIndex > rightIndex {
		fmt.Println("找不到")
		return
	}
	// 找到中间元素的下标
	middleIndex := (leftIndex + rightIndex) / 2
	if value < (*arr)[middleIndex] {
		// 说明我们要查找的数，应该在 leftIndex 和 middleIndex - 1 之间
		binaryFind(arr,leftIndex,middleIndex - 1, value)
	} else if value > (*arr)[middleIndex] {
		// 说明我们要查找的数，应该在 middleIndex + 1 和 rightIndex 之间
		binaryFind(arr,middleIndex + 1, rightIndex, value)
	} else {
		fmt.Println("找到了，数组下标为：",middleIndex)
	}
}

func main() {
	var arr = [6]int{1,8,10,45,67,100}
	binaryFind(&arr,0,5,1)
}
```

#### 五、二维数组

##### 1、二维数组的介绍和入门

**使用方式：**

先声明再赋值

语法：

> `var 数组名 [大小][大小]类型`

```go
package main
import (
	"fmt"
)
func main() {

	// 二维数组演示案例
	/*
		0 0 0 0 0 0
		0 0 1 0 0 0
		0 2 0 3 0 0
		0 0 0 0 0 0
	*/
	// 定义一个数组
	var arr [4][6]int
	// 赋初值
	arr[1][2] = 1
	arr[2][1] = 2
	arr[2][3] = 3
	//遍历二维数组
	for i := 0; i < 4; i++ {
		for j := 0; j < 6; j++ {
			fmt.Print(arr[i][j]," ")
		}
		fmt.Println("")
	}
}
```

##### 2、二维数组的使用和内存布局

1）、内存布局

```go
package main
import (
	"fmt"
)
func main() {
	var array [2][3]int
	array[1][1] = 10
	fmt.Printf("二维数组arr[0]的地址是：%p\n",&arr[0])  // 0x1f276000
	fmt.Printf("二维数组arr[1]的地址是：%p\n",&arr[1])  // 0x1f276018
	fmt.Printf("二维数组arr[0][0]地址是：%p",&arr[0][0]) // 0x1f276000
	fmt.Printf("二维数组arr[1][0]地址是：%p",&arr[1][0])  // 0x1f276018
}
```

![二维数组内存布局](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-go/%E4%BA%8C%E7%BB%B4%E6%95%B0%E7%BB%84%E5%86%85%E5%AD%98%E5%B8%83%E5%B1%80.png?q-sign-algorithm=sha1&q-ak=AKID5nPxXz8Yi430qRfacv05vH875PDEYJHM&q-sign-time=1551365277;1551367077&q-key-time=1551365277;1551367077&q-header-list=&q-url-param-list=&q-signature=4d86549c8c2ab51b3ae383a512645aedf9bb271e&x-cos-security-token=df4fbada4780d68ea0f3c235778a06d3c466aa8310001)

解释下上面这张图：

二维数组 array 有两个元素，每个元素又是一个数组，第一个元素里面存着它所指向的数组的首地址值。第二个元素同理。

2）、二维数组使用

> 直接初始化：
>
> `var 数组名 [大小][大小]类型 = [大小][大小]类型{{值1,值2},{值1,值2}}`

二维数组的另外几种写法：

> - `var 数组名 [大小][大小]类型 = [大小][大小]类型{{值1,值2},{值1,值2}}`
> - `var 数组名 [大小][大小]类型 = [...][大小]类型{{值1,值2},{值1,值2}}`
> - `var 数组名 = [大小][大小]类型{{值1,值2},{值1,值2}}`
> - `var 数组名 = [...][大小]类型{{值1,值2},{值1,值2}}`

##### 3、二维数组的遍历

```go
package main
import (
	"fmt"
)
func main() {
	// 二维数组的遍历
	var array [2][3]int  = [2][3]int {{1,2,3},{4,5}}
	// for 循环遍历
	for i := 0; i < len(array); i++ {
		for j := 0; j < len(array[i]); j++ {
			fmt.Printf("%v\t",array[i][j])
		}
		fmt.Println()
	}

	// for-range 遍历
	for _,v := range array {
		for _,v2 := range v {
			fmt.Printf("%v\t",v2)
		}
		fmt.Println()
	}
}
```

##### 4、二维数组的应用

```go
package main
import (
	"fmt"
)
func main() {
	/* 
		定义二维数组，用于保存三个班，每个班无名同学成绩，
		并求出每个班级平均分，以及所有班级平均分
	*/
	// 定义一个二维数组
	var scores [3][5]float64
	// 循环输入成绩
	for i := 0; i < len(scores); i++ {
		for j := 0; j < len(scores[i]); j++ {
			fmt.Printf("请输入第%d班第%d个学生的成绩\n",i + 1, j + 1)
			fmt.Scanln(&scores[i][j])
		}
	}
	// 遍历二维数组，计算平均分
	totalSum := 0.0
	for i,v := range scores {
		sum := 0.0
		for _,v2 := range v {
			sum += v2
		}
		fmt.Printf("第 %d 个班的总分为：%v,平均分为：%v",i + 1,sum,sum/float64(len(v)))
		totalSum += sum
	}
	fmt.Printf("所有班的平均分为：%v",totalSum/15.0)
}

// 输出结果
第 1 个班的总分为：5,平均分为：1第 2 个班的总分为：10,平均分为：2第 3 个班的总分为：15,平均分为：3所有班的平均分为：2
```

### Map

#### 一、Map 的介绍

##### 1、map 的介绍和声明

**1）、map 介绍：**

map 是 key-value 数据结构，又称为字段或者关联数组。类似其他编程语言的集合，在编程中经常用到

**2）、map 的声明：**

基本语法：

> `var 变量名 map[keytype]valuetype`
>
> - 其中这里的 map 是一个关键字，必须要这么写
> - 说一下 key 可以是什么类型
>   - golang 中的 map ，key 可以是很多种类型，比如：bool、int、string、指针、channel，还可以是前面几种类型所对应的接口，结构体和数组，==但是通常为 int 和 string==
>   - 注意 slice 、map 还有 function不能用来做 key，因为这几个没法用 == 来判断
> - 说一下 value 可以是什么类型
>   - value 的类型和 key 基本一样，通常为数字（整数、浮点数）、string、map、struct

map 声明举例：

> - var a map[string]string
> - var a map[string]int
> - var a map[int]string
> - var a map[string]map[string]string

**注意：**

声明是不会分配内存的，初始化需要 make，分配内存后才能赋值和使用

```go
package main
import (
	"fmt"
)
func main() {
	// map 的声明
	var a map[string]string 
	// 在使用 map 前，需要先 make， make 的作用就是给 map 分配数据空间
	a = make(map[string]string,10)  // 第一个参数放 map 的数据类型,第二个参数指明能放多个个键值对
	a["k1"] = "Jimmy"
	a["k2"] = "kimi"
	fmt.Println(a)    // map[k1:Jimmy k2:kimi]
}
```

说明：

> - map 在使用前一定要 make
> - map 的 key 是不能重复的，如果重复了，则按照最后的 key-value 为准
> - map 的 value 可以重复
> - map 的 key-value 是无序的
>
> make 的用法：
>
> ```
> func make(Type, size IntegerType) Type
> 内建函数make分配并初始化一个类型为切片、映射、或通道的对象。其第一个实参为类型，而非值。make的返回类型与其参数相同，而非指向它的指针
>
> 切片：size指定了其长度。该切片的容量等于其长度。切片支持第二个整数实参可用来指定不同的容量；
>      它必须不小于其长度，因此 make([]int, 0, 10) 会分配一个长度为0，容量为10的切片。
> 映射：初始分配的创建取决于size，但产生的映射长度为0。size可以省略，这种情况下就会分配一个
>      小的起始大小。
> 通道：通道的缓存根据指定的缓存容量初始化。若 size为零或被省略，该信道即为无缓存的。
> ```
>
> 

##### 2、map 的三种用法

**方式一：**

```go
package main
import (
	"fmt"
)
func main() {
	// 第一种使用方式
	var a map[string]string
	a = make(map[string]string,10)  // 使用 map 前对 map 先 make
	a["h1"] = "swk"
	a["h2"] = "zbj"
	fmt.Println(a)    // map[h2:zbj h1:swk]
}
```

**方式二：**

```go
package main
import (
	"fmt"
)
func main() {
	// 第二种使用方式
	cities := make(map[string]string)
	cities["n1"] = "北京"
	cities["n2"] = "上海"
	fmt.Println(cities)   // map[n1:北京 n2:上海]
}
```

**方式三：**

```go
package main
import (
	"fmt"
)
func main() {
	// 第三种方式
	heros := map[string]string{
		"hero1":"孙悟空",
		"hero2":"猪八戒",                          // 注意，这里的 "," 是需要加的
	}
	fmt.Println(heros)
}
```

**使用案例：**

```go
package main
import (
	"fmt"
)
func main() {
	// 存放两个学生信息，每个学生有 name 和 sex 信息
	studentMap := make(map[string]map[string]string)
	studentMap["stu01"] = make(map[string]string,10)
	studentMap["stu01"]["name"] = "Jimmy"
	studentMap["stu01"]["sex"] = "man"

	studentMap["stu02"] = make(map[string]string,10)
	studentMap["stu02"]["name"] = "kelly"
	studentMap["stu02"]["sex"] = "woman"
	fmt.Println(studentMap)   // map[stu01:map[sex:man name:Jimmy] stu02:map[sex:woman name:kelly]]
}
```

##### 3、map 增删改查操作

**1）、map 添加和修改**

```go
package main
import (
	"fmt"
)
func main() {
	cities := make(map[string]string)
	// map 中如果不存在这个 key 就是添加，如果存在这个 key 就是修改
	cities["bj"] = "北京"
	fmt.Println(cities)  // map[bj:北京]
	cities["bj"] = "大北京"    // map 中存在了 key 就是修改
	fmt.Println(cities)  // map[bj:大北京]
}
```

**2）、map 删除**

> `delete(map,"key")`      delete 是一个内置函数，如果 key 存在，就删除该 key-value，如果 key 不存在，不操作，但是也不会报错

```go
package main
import (
	"fmt"
)
func main() {
	cities := make(map[string]string)
	cities["bj"] = "北京"
	cities["sh"] = "上海"
	fmt.Println(cities)  // map[bj:北京 sh:上海]
	// 删除 map 中的元素
	delete(cities,"bj")
	fmt.Println(cities)  // map[sh:上海]
	// 当 delete 指定的 key 不存在时，删除不会操作也不会报错
	delete(cities,"6666")     // 发现他也没有报错
	fmt.Println(cities)  // map[sh:上海]
}
```

**说明：**

> 1、如果我们要删除 map 的所有 key，没有一个专门的方法一次删除，可以遍历 key ，逐个删除。
>
> 2、使用 map = make(map[string]string) ，重新 make 一次，让原来的成为垃圾，被 gc 回收

**3）、map 查找**

```go
package main
import (
	"fmt"
)
func main() {
	cities := make(map[string]string)
	cities["bj"] = "北京"
	cities["sh"] = "上海"
	fmt.Println(cities)  // map[bj:北京 sh:上海]
	val,flag := cities["sh"]    // map 中存在 key，flag 就为 true，不存在 flag 为 false, val 为 key 对应的值
	if flag {
		fmt.Println("map 中存在这个 key,对应的 value值为：",val)
	} else {
		// 没有对应的 key 的话，val 为空
		fmt.Println("map 中没有这个 key)
	}
}
```

##### 4、map 遍历

**使用 for-range 对 map 进行遍历**

```go
package main
import (
	"fmt"
)
func main() {
	// 使用 for-range 遍历 map
	cities := make(map[string]string)
	cities["bj"] = "北京"
	cities["ah"] = "安徽"
	for k,v := range cities {
		fmt.Println(k,v)
	}
	fmt.Println(len(cities))
	fmt.Println("-----------------")
	stuMap := make(map[string]map[string]string)
	stuMap["stu01"] = make(map[string]string)
	stuMap["stu01"]["name"] = "Jimmy"
	stuMap["stu01"]["age"] = "44"
	stuMap["stu01"]["sex"] = "man"
	stuMap["stu02"] = make(map[string]string)
	stuMap["stu02"]["name"] = "kelly"
	stuMap["stu02"]["age"] = "34"
	stuMap["stu02"]["sex"] = "woman"
	fmt.Println(len(stuMap["stu01"]))

	for _,v := range stuMap {
		for k2,v2 := range v {
			fmt.Println(k2,v2)
		}
		fmt.Println()
	}
}
```

获取 map 的长度：

使用 `len` 函数

```go
package main
import (
	"fmt"
)
func main() {
	cities := make(map[string]string)
	cities["bj"] = "北京"
	cities["ah"] = "安徽"
	fmt.Println(len(cities))      // 打印 2
	fmt.Println("-----------------")
	stuMap := make(map[string]map[string]string)
	stuMap["stu01"] = make(map[string]string)
	stuMap["stu01"]["name"] = "Jimmy"
	stuMap["stu01"]["age"] = "44"
	stuMap["stu01"]["sex"] = "man"
    fmt.Println(len(stuMap["stu01"]))          // 打印 3
}
```

##### 5、map 切片

1）、基本介绍

切片的数据类型如果是 map，则我们称为 slice of map。使用 map 切片，map 的个数就可以动态的变化了。

```go
package main
import (
	"fmt"
)
func main() {
	// 演示 map 切片的使用
	/* 
		使用一个 map 来记录 monster 的信息 name 和 age 
		也就是说一个 monster 对应一个 map, 并且 monster 的个数可以动态的增加
	*/
	// 声明一个切片
	var monster []map[string]string
	monster = make([]map[string]string,2)  // size 暂时写为 2
	if monster[0] == nil {
		monster[0] = make(map[string]string)
		monster[0]["name"] = "孙悟空"
		monster[0]["age"] = "20"
	}
	fmt.Println(monster)    // [map[name:孙悟空 age:20] map[]]
}

```

**利用切片动态增加 map 个数**

```go
package main
import (
	"fmt"
)
func main() {
	// 演示 map 切片的使用
	/* 
		使用一个 map 来记录 monster 的信息 name 和 age 
		也就是说一个 monster 对应一个 map, 并且 monster 的个数可以动态的增加
	*/
	// 声明一个切片
	var monster []map[string]string
	monster = make([]map[string]string,2)  // size 暂时写为 2
	if monster[0] == nil {
		monster[0] = make(map[string]string)
		monster[0]["name"] = "孙悟空"
		monster[0]["age"] = "20"
	}
	fmt.Println(monster)    // [map[name:孙悟空 age:20] map[]]

	// 这里我们使用切片的 append 函数，可以动态的增加 map 中元素
	// 先定义一个 monster
	newMonster := map[string]string {
		"name":"猪八戒",
		"age":"30",
	}
	monster = append(monster,newMonster)
	fmt.Println(monster)   // [map[name:孙悟空 age:20] map[] map[age:30 name:猪八戒]]

}
```

##### 6、map 排序

> 1、golang 中没有一个专门的方法针对 map 的 key 进行排序
>
> 2、golang 中的 map 默认是无序的，也不是按照添加的顺序存放的，你每次遍历得到的输出结果可能都不一样。
>
> 3、golang 中的 map 排序，是先将 key 进行排序，然后根据 key 值遍历输出即可

map 排序思路：

> 如果按照 map 的 key 进行排序输出
>
>  1、先将 map 的 key 放入切片中
>
> 2、对切片排序
>
>  3、遍历切片，按照 key 来输出 map 中的值

```go
package main
import (
	"fmt"
	"sort"
)
func main() {
	// map 排序
	map1 := make(map[int]int)
	map1[3] = 10
	map1[2] = 7
	map1[6] = 10
	map1[7] = 11
	fmt.Println(map1)   // map[2:7 6:10 7:11 3:10] 发现这个 map 并没有顺序
	// 如果按照 map 的 key 进行排序输出
	// 1.先将 map 的 key 放入切片中
	// 2.对切片排序
	// 3.遍历切片，按照 key 来输出 map 中的值
	var keys []int   // 定义一个切片
	for k,_ := range map1 {
		keys = append(keys,k)
	}
	fmt.Println(keys)   // [7 3 2 6]
	// 排序
	sort.Ints(keys)
	fmt.Println(keys)   // [2 3 6 7]
	// 遍历切片，按照 key 输出 map 值
	for _,v := range keys {
		fmt.Printf("map 的key 为：%v,value 值为：%v\n",v,map1[v])
	}
}
```

##### 7、map  使用细节

> - map 是引用类型，遵守引用类型传递机制，在一个函数接收 map ，修改后会直接修改原 map
> - map 的容量达到后，再往 map 中增加元素，map 会自动扩容，并不会发生 panic，也就是说 map 能动态的增长键值对
> - map 的 value 也经常使用 struct 类型，更适合管理复杂的数据（比之前我们把 value 用 map 做更好）

```go
package main
import (
	"fmt"
)
func modify(map1 map[int]int) {
	map1[5] = 100
}
// 定义一个结构体
type Stu struct {
	Name string
	Age int
	Address string
}

func main() {
	// map 是引用类型，遵守引用类型传递机制，在一个函数接收 map ，修改后会直接修改原 map
	map1 := make(map[int]int)
	map1[0] = 2
	map1[5] = 3
	fmt.Println(map1)   // map[5:3 0:2]
	modify(map1)
	fmt.Println(map1)   // map[0:2 5:100]
	// map 的 value 在实际开发的时候，更多用的是结构体
	students := make(map[string]Stu,10)   // key 是 string 类型，value 是一个结构体
	// 创建 2 个学生
	stu1 := Stu{"Jimmy",44,"北京"}
	stu2 := Stu{"kelly",34,"北京"}
	students["stu01"] = stu1
	students["stu02"] = stu2
	fmt.Println(students)   // map[stu01:{Jimmy 44 北京} stu02:{kelly 34 北京}]
	// 遍历学生信息
	for i,v := range students {
		fmt.Printf("学生的编号是：%v\n",i)
		fmt.Printf("学生的姓名是：%v\n",v.Name)
		fmt.Printf("学生的年龄是：%v\n",v.Age)
		fmt.Printf("学生的地址是：%v\n\n",v.Address)
	}
}
```

##### 8、map 的应用案例

```go
package main
import (
	"fmt"
)
/* 
	1、使用 map[string]map[string]string
	2、key: 表示用户名，唯一不可重复
	3、如果某个用户名存在，就将其密码修改为 "8888"，如果不存在就增加这个用户信息
	4、编写一个函数 modifyUser(users map[string]map[string]string, name string)
*/
func modifyUser(users map[string]map[string]string, name string) {
	// 判断 users 中是否有 map
	if users[name] != nil {
		users[name]["password"] = "8888"
		} else {
			users[name] = make(map[string]string,10)
			users[name]["password"] = "8888"
			users[name]["nickname"] = "hello"
	}
}
func main() {
	users := make(map[string]map[string]string)
	users["Jimmy"] = make(map[string]string)
	users["Jimmy"]["nickname"] = "hi"
	users["Jimmy"]["password"] = "1234"
	modifyUser(users,"Jimmy")
	fmt.Println(users)
}
```

### 面向对象编程

#### 一、面向对象及结构体

##### 1、Go 面向对象说明

> - Golang 也支持面向对象编程（OOP），但是和传统的面向对象编程有区别，并不是纯粹的面向对象语言，所以我们说 ==Golang 支持面向对象编程特性==是比较准确的
> - Golang 没有类(class)，Go 语言的结构体(struct)和其他编程语言的类(class)有同等的地位，你可以理解为 Golang 是基于 struct 来实现 OOP 特性的
> - Golang 面向对象编程非常简洁，去掉了传统 OOP 语言的继承、方法重载、构造函数等
> - Golang 仍然有面向对象编程的继承，封装和多态的特性，只是实现的方式和其他 OOP 语言不一样，比如：继承，Golang 没有 extends 关键字，继承是通过匿名字段来实现
> - Golang 面向对象（OOP）很优雅，通过接口（interface）关联，耦合性低。也就是说在 Golang 中面向接口编程是非常重要的特性

##### 2、面向对象快速入门

> 1、将一类事物的特性提取出来，形成一个新的数据类型，就是结构体
>
> 2、通过这个结构体，我们可以创建多个变量（实例/对象）

```go
package main
import (
	"fmt"
)
// 定义一个 Cat 结构体，将 Cat 的各个属性，放入到结构体中进行管理
type Cat struct {
	Name string    // 大写，表示这个变量是全局的
	Age int
	Color string
}
func main() {
	var cat1 Cat
	cat1.Name = "小白"
	cat1.Age = 5
	cat1.Color = "白色"
	fmt.Println(cat1)    // {小白 5 白色}
}
```

##### 3、结构体的内存布局

![结构体变量在内存中的布局](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-go/%E7%BB%93%E6%9E%84%E4%BD%93%E5%8F%98%E9%87%8F%E5%9C%A8%E5%86%85%E5%AD%98%E4%B8%AD%E7%9A%84%E5%B8%83%E5%B1%80.png?q-sign-algorithm=sha1&q-ak=AKIDAA3Xl6UII7KRlaF5MOGvXizomMyZtUmQ&q-sign-time=1553430166;1553431966&q-key-time=1553430166;1553431966&q-header-list=&q-url-param-list=&q-signature=711368edc9570bfa3c9661a399d4f0730e025629&x-cos-security-token=074f1655a9c19459ca8bc182bde179325a09f20b10001)

> 你声明了一个结构体变量，它就会在内存中给你开辟一块空间，这块空间里面包括了你结构体中所有的字段，这些字段最开始用的都是默认值，等你向字段里面设置值的时候，才会把对应的值放到对应的字段中去。
>
> 另外，结构体变量直接指向这块空间，说明==结构体是值类型==。

##### 4、结构体的声明和使用陷阱

**声明结构体：**

> `type 结构体名称 struct {`
>
> ​	`field1 类型`
>
> ​	`field2 类型`
>
> `}`

**举例：**

```go
type Student struct {       // Student 大写表示这个结构体在其他包可以被使用
    Name string            // 字段名大写表示可以在其他包使用
    Age int
}
```

**结构体的字段/属性：**

> - 从概念或叫法上看，结构体字段，结构体属性表达的都是同一个意思，不同的叫法而已。
> - 字段是结构体的一个组成部分，一般是基本数据类型，数组，也可以是引用类型

**结构体的字段/属性细节：**

> 1、结构体字段声明的语法同变量，   `字段名  字段类型`
>
> 2、字段的类型可以为：基本数据类型、数组、引用类型
>
> 3、在创建一个结构体变量后，如果没有给字段赋值，都对应一个零值（默认值）
>
> 4、不同结构体变量的字段是独立的，互不影响，一个结构体变量字段的更改，不影响另外一个。结构体是值类型。

针对第三点：

```go
package main
import (
	"fmt"
)

// 如果结构体是字段类型是：指针，slice ，map 它的零值都是 nil ，即还没有分配空间
// 如果需要使用这样的字段，需要先 make ，才能使用

type Person struct {
	Name string
	Age int
	Scores [5]float64
	ptr *int;  // 指针
	slice []int // 切片
	map1 map[string]string  // map
}
func main() {
	// 定义一个结构体变量
	var p1 Person
	fmt.Println(p1.ptr == nil)   // true
	fmt.Println(p1.slice == nil) // true
	fmt.Println(p1.map1 == nil)  // true

	// 使用 slice 之前一定要 make
	p1.slice = make([]int,5)
	p1.slice[0] = 10
	fmt.Println(p1.slice)    // [10 0 0 0 0]

	// 使用 map 前一定要先 make
	p1.map1 = make(map[string]string)
	p1.map1["name"] = "Jimmy"
	fmt.Println(p1.map1)   // map[name:Jimmy]
}
```

针对第四点：

```go
package main
import (
	"fmt"
)
type Monster struct {
	Name string
	Age int
}
func main() {
	var m1 Monster
	m1.Name = "孙悟空"
	m1.Age = 12
	m2 := m1     // 把 m1 赋值给 m2 其实是一个值拷贝
	m2.Name = "猪八戒"   // 所以修改 m2 并不会影响 m1
	fmt.Println(m1)    // {孙悟空 12}
	fmt.Println(m2)     // {猪八戒 12}
}
```

结构体值拷贝内存结构图说明：

![结构体值拷贝](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-go/%E7%BB%93%E6%9E%84%E4%BD%93%E5%80%BC%E6%8B%B7%E8%B4%9D.png?q-sign-algorithm=sha1&q-ak=AKIDNXIXyjJaPlk8ZH4ptS47maCysxbxx1rM&q-sign-time=1553523877;1553525677&q-key-time=1553523877;1553525677&q-header-list=&q-url-param-list=&q-signature=6388d22b0933c0f79cc49e424568245f81370991&x-cos-security-token=f44f2083daa35e68d31a54eef38f8372298614d210001)

##### 5、创建结构体实例的四种方式

**方式一：**

var person Person

**方式二：**

`var person Person = Person{}`

```go
package main
import (
	"fmt"
)
type Person struct {
	Name string
	Age int
}
func main() {
	// 方式二：
	person2 := Person{}
	person2.Name = "Jimmy"
	person2.Age = 10
	fmt.Println(person2)    // {Jimmy 10}
}
```

**方式三：**

`var person *Person = new(Person)`

```go
package main
import (
	"fmt"
)
type Person struct {
	Name string
	Age int
}
func main() {
	// 方式三：
	var person3 *Person = new(Person)
	// 因为 person3 是一个指针，因此标准的给字段赋值方式为下面这样：
	(*person3).Name = "kimi"   // 这是标准写法
	(*person3).Age = 12
	// 像上面的代码完全可以这么写： person2.Name = "kimi"
	// 也就是说  (*person3).Name = "kimi"  和  person2.Name = "kimi" 这两种写法是等价的
	// 原因： go 的设计者为了程序员使用方便，底层会对 person2.Name = "kimi" 进行处理，会给 person3 加上取值运算  (*person3).Name = "kimi"
	fmt.Println(*person3)
}
```

**方式四：**

`var person *Person = &Person{}`

 ```go
package main
import (
	"fmt"
)
type Person struct {
	Name string
	Age int
}
func main() {
	// 方式四：
	// var person4 *Person = &Person{"tom",12}   直接这样初始化也可以
	var person4 *Person = &Person{}
	// 因为 person4 是一个指针，因此标准的访问字段的方法 (*person4).Name = "Jimmy"
	// go 的设计者为了程序员使用方便，也可以 person4.Name = "Jimmy" 原因和之前说过的一样
	//(*person4).Name = "kelly"   标准写法
	person4.Name = "kelly"   // go 底层会帮我们进行处理成 (*person4).Name = "kelly"
	(*person4).Age = 13
	fmt.Println(*person4)   // {kelly 13}
}
 ```

**注：**

> 1、方式三和方式四返回的都是结构体指针
>
> 2、结构体指针访问字段的标准方式应该是：(*结构体指针).字段名   
>
> 3、但是 go 做了一个简化，也支持    结构体指针.字段名     ，这样更加符合程序员的使用习惯。

##### 6、结构体内存分配机制

```go
package main
import (
	"fmt"
)
type Person struct {
	Name string
	Age int
}
func main() {
	var p1 Person
	p1.Age = 10
	p1.Name = "Jimmy"
	var p2 *Person = &p1
	fmt.Println((*p2).Age)   // 10
	fmt.Println(p2.Name)   // Jimmy
	p2.Name = "kimi"
	fmt.Printf("p2.Name=%v,p1.Name=%v",p2.Name,p1.Name)  // p2.Name=kimi,p1.Name=kimi
	fmt.Printf("p2.Name=%v,p1.Name=%v",(*p2).Name,p1.Name)  // p2.Name=kimi,p1.Name=kimi
}
```

![结构体内存图](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-go/%E7%BB%93%E6%9E%84%E4%BD%93%E5%86%85%E5%AD%98%E5%9B%BE.png?q-sign-algorithm=sha1&q-ak=AKIDcoFDG6Jvl2Eec3h51JZdCbRShlQrVsjg&q-sign-time=1554652039;1554653839&q-key-time=1554652039;1554653839&q-header-list=&q-url-param-list=&q-signature=9a150c9dbde51305f16cd1d1442b301e747ab156&x-cos-security-token=2e916cd6c77ad0f03b7521860e4d138ba8ffa9fa10001)

##### 7、结构体的使用细节

1）、结构体的所有字段在内存中是连续的

```go
package main
import (
	"fmt"
)
type Point struct {
	x int
	y int
}
type Rect struct {
	leftUp Point
	rightDown Point
}
type Rect2 struct {
	leftUp *Point
	rightDown *Point
}
func main() {
	// r1 中有 4 个 int,在内存中是连续分布的
	r1 := Rect{Point{1,2},Point{3,4}}
	// 0x1f2200a0 0x1f2200a4 0x1f2200a8 0x1f2200ac
	// 发现地址是连续的，int 32 为操作系统占 4 个字节，64 位操作系统占 8 个字节
	fmt.Println(&r1.leftUp.x,&r1.leftUp.y,&r1.rightDown.x,&r1.rightDown.y)

	// r2 有两个 *Point 类型，这两个 *Point 类型的本身地址也是连续的，他们指向的地址不一定是连续的
	r2 := Rect2{&Point{10,20},&Point{30,40}}
	// 0x1f214108 0x1f21410c
	fmt.Println(&r2.leftUp,&r2.rightDown)
	fmt.Println(r2.leftUp,r2.rightDown)  // 指针中存的地址不一定是连续的
}
```

2）、结构体是用户单独定义的类型，和其它类型进行转换时需要有完全相同的字段（名称、个数、类型） 

```go
package main
import (
	"fmt"
)
type A struct {
	Num int
}

type B struct {
	Num int
}
func main() {
	var a A
	var b B
	b = B(a)   // 可以转换，但是有要求，就是结构体的字段要完全一样,包括名称、个数和类型
	fmt.Println(b)  // {0}
}
```

3）、结构体进行 type 重新定义（相当于取别名），Golang 认为是新的数据类型，但是相互间可以强转

```go
package main
import (
	"fmt"
)
type Student struct {
	Name string
	Age int
}
type Stu Student

func main() {
	var stu1 Student
	var stu2 Stu
	stu2 = Stu(stu1)  // 可以进行强转
}
```

4）、struct 的每个字段上，可以写一个  tag ，该 tag 可以通过反射的机制获取，常见的使用场景就是序列化和反序列化。

```go
package main
import (
	"fmt";
	"encoding/json"
)

type Monster struct {
	Name string `json:"name"`   //用反引号包起来,前面 json: 固定，后面写小写的字段名
	Age int `json:"age"`
	Skill string `json:"skill"`
}

func main() {
	// 创建一个 Monster 变量
	monster := Monster{"孙悟空",20,"金箍棒"}
	// 将 monster 变量序列化为 json 格式字符串
	jsonstr,err := json.Marshal(monster)
	if (err != nil) {
		fmt.Println("序列化错误")
	}
	fmt.Println("jsonstr",string(jsonstr))   // jsonstr {"Name":"孙悟空","Age":20,"Skill":"金箍棒"}
	// 你会发现序列化后的结果, Name Age Skill 这些字段都是大写，假如我不想大写怎么办？
	// 一种方法就是把结构体中定义的字段名改成小写，但是改成小写会有一个问题，就是别的包访问不到这个字段了
	// 假如你把结构体中定义的字段名改成小写，你会发现序列化后的结构是这个： jsonstr {}
	// 原因是我们序列化需要调用 encoding/json 这个包，和我们自己包不在同一个包中，你结构体中字段名小写，自然访问不到你这个字段名，所以序列化为空

	// 第二种方法就是加 tag ，具体方式见结构体中修改
}
```

#### 二、方法的介绍和使用

##### 1、方法介绍和使用

基本介绍：

> 在某些情况下，我们需要声明（定义）方法。比如 Person 结构体，除了有一些字段外，Person 结构体还可以有一些行为。这时候就要用方法才能完成。
>
> **Golang 中的方法是作用在指定的数据类型上的**（即：和指定的数据类型绑定），因此自定义类型，都可以有方法，而不仅仅是 struct

方法的声明：

> type A struct {
>
> ​	Num int
>
> }
>
> func (a A) test() {
>
> ​	fmt.Println(a.Num)
>
> }

```go
package main
import (
	"fmt"
)
type Person struct {
	Name string
}
// 给 Person 类型绑定一个方法
func (p Person) test() {
    // 假如我在这里把 p.name 的值改了
    //p.Name = "kimi"
	fmt.Println("test",p.Name)
}

func main() {
	var p Person
	p.Name = "Jimmy"
	// 这里 test 方法只能通过 Person 类型的变量调用
	p.test()  // 调用方法   打印 test Jimmy
    
    //假如上面方法中把 p.Name 的值改了，那么我调用 p.test() 结果就应该是 kimi
    // 但是如果我在 main 方法中打印 p.Name 结果还是 Jimmy,因为方法中的 p 相当于是 main ff 方法中声明的 Person 的一个副本，你可以理解为值拷贝
}
```

对上面的代码总结一下：

> - test 方法和 Person 类型绑定
> - test 方法只能通过 Person 类型的变量来调用，而不能直接调用，也不能使用其他类型的变量来调用
> - func (p Person) test() {}  这里的 p 表示，哪个 Person 变量调用，这个 p 就是它的副本，这点和函数传参类型
> - func (p Person) test() {}  这个 p 的名称是随意指定的不是固定的

##### 2、方法快速入门

① 案例1：

```go
package main
import (
	"fmt"
)
type Person struct {
	Name string
}
// 给 Person 结构体添加 speak 方法，输出 xxx 是一个好人 
func (p Person) speak() {     //(p Person)  表示给 Person 结构体绑定方法
	fmt.Println(p.Name,"是一个好人")
}
func main() {
	var p Person
	p.Name = "Jimmy"
	p.speak()    // Jimmy 是一个好人
}
```

② 案例 2：

```go
package main
import (
	"fmt"
)
type Person struct {
	Name string
}
// 给 Person 结构体添加 calc 方法，可以计算 1+..+..+100 的结果
func (p Person) calc() {
	// 说明方法体内和函数一样，可以进行各种运算
	result := 0
	for i := 0; i <= 100; i++ {
		result += i
	}
	fmt.Println("结果为：",result)
}

func main() {
	var p Person
	p.Name = "Jimmy"
	p.calc()    // 结果为： 5050
}
```

③ 案例3：

```go
package main
import (
	"fmt"
)
type Person struct {
	Name string
}
// 给 Person 结构体添加 calc2 方法，该方法可以接收一个参数 n，计算 1+..+..+n 的结果
func (p Person) calc2(n int) {     // 这里可以传入参数
	result := 0
	for i := 0; i <= n; i++ {
		result += i
	}
	fmt.Println("结果为：",result)
}
func main() {
	var p Person
	p.Name = "Jimmy"
	p.calc2(10)   // 结果为： 55
}
```

④ 案例 4：

```go
package main
import (
	"fmt"
)
type Person struct {
	Name string
}
// 给 Person 结构体添加 getSum 方法，可以计算两个数的和，并返回结果
func (p Person) getSum(a int, b int) int {    // 方法可以有返回值
	return a + b
}
func main() {
	var p Person
	p.Name = "Jimmy"
	result := p.getSum(2,3)
	fmt.Println(result)   // 5
}
```

##### 3、方法的调用和传参机制

方法的调用和传参机制和函数基本一样，不一样的地方是方法调用时，会将调用方法的变量，当做实参也传递给方法（如果变量是值类型，则进行值拷贝，如果变量是引用类型，则进行地址拷贝）

```java
package main
import (
	"fmt"
)
// 1.声明一个结构体 Circle ，字段为 radius
// 2.声明一个方法 area 和 Circle 绑定，可以返回面积

type Circle struct {
	radius float64
}

func (c Circle) area() float64 {
	return 3.14 * c.radius * c.radius
}

func main() {
	// 创建 Circle 结构体变量
	var c Circle
	c.radius = 2.0
	result := c.area()    // 这里要注意一下，当我执行到这里去调用方法的时候，会把 c 这个结构体变量做一个值拷贝，传递给方法，方法里面操作的 c 是 main 方法里面结构体变量的一个值拷贝
	fmt.Println(result)   // 12.56
}
```

##### 4、方法使用剖析

**方法的声明（定义）：**

> `func (recevier type) methodName (参数列表) (返回值列表) {`
>
> ​	`方法体`
>
> ​	`return 返回值`
>
> `}`

> - 参数列表：方法的入参
> - recevier type ：表示这个方法和 type 这个类型进行绑定，或者说该方法作用于 type 类型，type 可以是结构体，也可以是其它的自定义类型
> - 返回值列表：表示返回的值，可以是多个。
> - return 语句不是必须的，方法不一定必须要有返回值

方法的注意事项：

> - 结构体类型是值类型，在方法调用中，遵循值类型的传递机制，是值拷贝传递方式
> - 如果希望在方法中，修改结构体变量的值，可以通过结构体指针的方式来处理
> - Golang 中的方法作用在指定的数据类型上（即：和指定的数据类型绑定），因此自定义类型，都可以有方法，而不仅仅是 struct ，比如 int   float32 等都可以有方法
> - 方法的访问控制范围的规则，和函数一样。方法名首字母小写，只能在本包中访问，方法首字母大写，可以在本包和其他包访问
> - 如果一个变量实现了 String() 这个方法，那么 fmt.Println 默认会调用这个变量的 String() 进行输出 

```go
package main
import (
	"fmt"
)
type Circle struct {
	radius float64
}

func (c Circle) area() float64 {
	c.radius = 1.0
	return 3.14 * c.radius * c.radius
}

func (c *Circle) area2() float64 {    // 这里传一个指针
	(*c).radius = 3.0
	// 因为 c 是一个指针，因此一个标准的访问其字段方式如下
	return 3.14 * (*c).radius * (*c).radius
}
func main() {
	// 值拷贝方式
	var c Circle
	c.radius = 2.0
	result := c.area()
	fmt.Println("值拷贝：",c.radius)  // 值拷贝： 2
	fmt.Println(result)   // 3.14

	// 地址传递方式
	var c2 Circle
	c2.radius = 2.0
	result2 := (&c2).area2()
	// 编译器底层做了优化，(&c2).area2() 等价于 c2.area2()，如果你是一个指针类型，编译器会自动给你加上 &
	fmt.Println("地址拷贝：",c2.radius)  // 地址拷贝： 3
	fmt.Println(result2)  // 28.259999999999998
}
```

```go
package main
import (
	"fmt"
)
// golang 中的方法作用在指定的数据类型上，即和指定的数据类型绑定，因此自定义类型都可以有方法, 比如 int、float32 都可以有方法
type integer int 
func (i integer) test() {
	fmt.Println("i=",i)
}
// 编写一个方法，可以改变 i 的值
func (i *integer) change() {
	*i = *i + 1
}

type Student struct {
	Name string
	Age int
}
// 你使用 fmt 打印这个结构体信息的时候，就会自动调用这个方法
func (stu *Student) String() string {
	str := fmt.Sprintf("Name=[%v],Age=[%d]",stu.Name,stu.Age)
	return str
}
func main() {
	var i integer = 10;
	i.test()   // i= 10
	i.change()
	fmt.Println(i)   // 11

	// 定义一个 Student 变量
	var str Student = Student{Name :"Jimmy",Age :44}
	// 如果你实现了 *Student 类型的 String 方法，就会自动调用，假如你没实现这里打印的就是 str 的地址
	fmt.Println(&str)   // Name=[Jimmy],Age=[44]  假如你这里不写 & 直接写 str ，那么会按照默认方式给你输出
}
```

##### 5、练习

```go
package main
import (
	"fmt"
)
type MethodUtils struct {

}
/*
	编写结构体 MethodUtils 编写一个方法，方法不需要参数，在方法中打印一个 10 * 8 的矩形，在　main 方法中调用该方法
*/
func (method MethodUtils) printJuXing() {
	for j := 0; j < 8; j++ {
		for i := 0; i < 10; i++ {
			fmt.Printf("*")
		}
		fmt.Println()
	}
}

// 编写一个方法，提供 m 和 n 两个参数，方法中打印一个 m * n 的矩形
func (method MethodUtils) printJuXing2(m int, n int) {
	for j := 0; j < n; j++ {
		for i := 0; i < m; i++ {
			fmt.Printf("*")
		}
		fmt.Println()
	}
}

// 编写一个方法算矩形的面积（可以接收长 len,宽 width）将其作为方法返回值，在 main 方法中调用该方法
func (method MethodUtils) printJuXing3(m int, n int) int {
	return m * n
}
func main() {
	var method MethodUtils
	method.printJuXing()

	method.printJuXing2(2,3)

	result := method.printJuXing3(2,4)
	fmt.Println(result)   // 打印 8
}
```

```go
package main
import (
	"fmt"
)
type MethodUtils struct {

}

// 编写一个方法判断一个数是奇数还是偶数
func (method *MethodUtils) judgeNum(num int) {
	if num % 2 == 0 {
		fmt.Println("是偶数")
	} else {
		fmt.Println("是奇数")
	}
}
/*
 根据行、列、字符 打印对应的行数和列数的字符
 比如： 行3 列2 字符 * 则打印相应的效果
*/

func (method *MethodUtils) printSign(line int, col int ,sign string) {
	for i := 0; i < line; i++ {
		for j := 0; j < col; j++ {
			fmt.Printf(sign)
		}
		fmt.Println()
	}
}

/*
定义一个计算机结构体（Calculator），实现加减乘除四个功能
实现1：用 4 个方法完成
实现2：用 1 个方法完成
*/

type Calculator struct {

}

func (calc *Calculator) calcNum(num1 float64, num2 float64, sign string) float64 {
	result := 0.0
	if sign == "+" {
		result = num1 + num2
	} else if sign == "-" {
		result = num1 - num2
	} else if sign == "*" {
		result = num1 * num2
	} else if sign == "/" {
		result = num1 / num2
	} else {
		fmt.Println("运算符输入有误")
	}
	return result
}

func main() {
	var method MethodUtils
	method.judgeNum(7)
	method.printSign(5,3,"#")

	var calc Calculator
	result := calc.calcNum(10.0,20.0,"-")
	fmt.Println(result)
}
```

##### 6、方法和函数的区别

> - 调用方式不同
>   - 函数调用方式：      函数名（实参列表）
>   - 方法调用方式：      变量.方法名（实参列表）
> - 对于普通函数，接收者为值类型时，不能将指针类型的数据直接传递，反之亦然。
> - 对于方法，接收者为值类型时，可以直接使用指针类型的变量调用方法，反过来同样也可以。

```go
package main
import (
	"fmt"
)

type Person struct {
	Name string
}
// 对于普通函数，接收者为值类型时，不能将指针类型的数据直接传递，反之亦然
func test01(p Person) {
	fmt.Println(p.Name)
}
// 对于方法，接收者为值类型时，可以直接使用指针类型的变量调用方法，反过来同样也可以
func (p Person) test02() {
	fmt.Println(p.Name)
}
func (p *Person) test03() {
	fmt.Println(p.Name)
}
func main() {
	p := Person{"Jimmy"}
	test01(p)
	// 我可以这么去调用方法
	p.test02()
	// 也可以这么去调用方法
	(&p).test02()  // 你这样调用编译器底层会帮你优化  // 因为方法里面是 Person 类型变量，所以两种调用方法的方式都是值拷贝

	// 上面这种调用方式和下面那种调用方式都可以
	p.test03()  // 就算你这么调用，其实编译器底层会帮你优化
	(&p).test03() // 因为方法里面是 *Person 指针类型变量，所以两种调用方法的方式都是地址拷贝
}
```

总结：

>  不管调用形式如何，真正决定是值拷贝还是地址拷贝，是看这个方法和哪个类型绑定
>
> 如果是和值类型绑定，如：(p Person) 是值拷贝，如果是和指针类型绑定，如：(p *Person) 则是地址拷贝

##### 7、面向对象编程实例

步骤：

> 1、声明结构体，确定结构体名
>
> 2、编写结构体字段
>
> 3、编写结构体方法

```go
package main
import (
	"fmt"
)
/*
编写一个 Student 结构体，包含 name gender age id score 字段
结构体中声明一个 say 方法，返回 string 类型，方法返回信息中包含所有字段的值
在 main 方法中，创建 Student 结构体实例，并访问 say 方法，并将调用结果打印输出
*/
type Student struct {
	Name string
	Gender string
	Age int
	Id int
	Score float64
}
func (stu *Student) say() string {
	stuInfo := fmt.Sprintf("Name=%v,Gender=%v,Age=%d,Id=%d,Score=%f",
		stu.Name,stu.Gender,stu.Age,stu.Id,stu.Score)
	return stuInfo
}

type Visitor struct {
	Name string
	Age int
}
func (visit *Visitor) ticket() {
	if visit.Age > 18 {
		fmt.Printf("游客的名字是：%v,年龄是：%d,收费 20 元",visit.Name,visit.Age)
	} else {
		fmt.Printf("游客的名字是：%v,年龄是：%d,免费",visit.Name,visit.Age)
	}
}
func main() {
	var stu Student = Student{"Jimmy","man",44,10,99.5}
	result := (&stu).say()
	fmt.Println(result)  // Name=Jimmy,Gender=man,Age=44,Id=10,Score=99.500000
	
	var visit Visitor
	for {
		fmt.Println("请输入你的名字：")
		fmt.Scanln(&visit.Name)
		if visit.Name == "n" {
			fmt.Println("程序结束")
			break
		}
		fmt.Println("请输入你的年龄：")
		fmt.Scanln(&visit.Age)
		visit.ticket()
	}
}
```

##### 8、创建 struct 实例指定字段值

Golang 在创建结构体实例的时候，可以直接指定字段的值。

**方式一：**

```go
var stu1 Student = Student{"tom",12} //   这就要求按照结构体字段的顺序来赋值
stu2 := Student{"tom",12}    // 这种和上面一样，只不过使用了类型推导

var stu3 Student = Student{Name:"tom",Age:12}    //这种可以不用按照结构体字段的顺序    
stu4 := Student{Name:"tom",Age:12}    // 这种和上面一样，使用了类型推导
```

**方式二：**

```go
var stu5 *Student = &Student{"durant",23}   // stu5 是一个指针类型的变量，创建实例的时候记得写 & 符号
stu6 := &Student{"James",30}

var stu7 = &Student{Name :"curry",Age : 26}   //这种可以不用按照结构体字段的顺序  
stu8 := &Student{Name :"weishao",Age : 26}
```

案例：

```go
package main
import (
	"fmt"
)
type Student struct {
	Name string
	Age int
}
func main() {
	// 创建结构体变量时，就直接指定字段的值
	var stu1 Student = Student{"Jimmy",45}  // 也可以这么写 var stu1 = Student{"Jimmy",45}
	stu2 := Student{"kelly",34}
	fmt.Println(stu1,stu2)  // {Jimmy 45} {kelly 34}

	// 这种方式不用考虑结构体字段顺序的问题
	var stu3 = Student{Name:"kimi",Age:12} // 也可以这么写 var stu3 Student = Student{Name:"kimi",Age:12}
	stu4 := Student{Age :12,Name:"shp"}
	fmt.Println(stu3,stu4)  // {kimi 12} {shp 12}

	// 方式2，返回结构体的指针类型
	var stu5 *Student = &Student{"durant",23}  // 这个 stu5 它是一个指针类型的结构体变量, 也可以这么写 var stu5 = &Student{"durant",23}
	stu6 := &Student{"James",30}
	fmt.Println(stu5,stu6)  // &{durant 23} &{James 30}

	// 这种方法不会依赖结构体字段的顺序
	var stu7 = &Student{Name :"curry",Age : 26}   // 这里的 stu7 是指针类型的变量
	stu8 := &Student{Name :"weishao",Age : 26}
	fmt.Println(stu7,stu8)  // &{curry 26} &{weishao 26}
	fmt.Println(*stu7,*stu8)  // {curry 26} {weishao 26}
}
```

##### 9、工厂模式

Golang 的结构体没有构造函数，通常可以使用工厂模式来解决这个问题。

**先说下为什么要使用工厂模式：**

```go
packge model
type Student struct {
    Name string
}
```

上面声明的结构体 Student 的首字母是大写的，如果我们想在其他包创建 Student 的实例，引入 model 包后，就可以直接创建 Student 的实例，但是如果结构体 student 首字母是小写的，这样在别的包就创建不了了，这个问题可以通过工厂模式来解决。

student.go ：

```go
package model
// 定义一个结构体
type Student struct {
	Name string
	Score float64
}
```

main.go ：

```go
package main
import (
	"fmt"
	"../model"
)
func main() {
	// 创建结构体 Student 的实例
	var stu = model.Student{Name:"Jimmy",Score:12.5}  // 因为我 model 包中 Student 是大写，所以这里使用没有问题
	fmt.Println(stu)   // {Jimmy 12.5}
}
```

上面中结构体 Student 首字母是大写，所以在别的包中使用没有问题。现在使用工厂模式解决结构体 student 首字母小写问题：

student.go ：

```go
package model
// 定义一个结构体
type student struct {        // 结构体名称小写
	Name string
	score float64
}
// 因为 student 结构体首字母是小写，因此只能在本包中使用
// 我们通过工厂模式来解决

func NewStudent(nameArg string, scoreArg float64) *student {   // 对外提供一个方法，在方法中创建结构体实例
	return &student{Name : nameArg,score:scoreArg}
}

// 如果 score 字段首字母小写，那么在其他包中就不能直接访问，我们可以提供一个方法
func (stu *student) GetScore() float64 {
	return stu.score
}
```

main.go ：

```go
package main
import (
	"fmt"
	"../model"
)
func main() {
	// 创建结构体 Student 的实例
	stu := model.NewStudent("kimi",11)  // 这里返回的是一个指针类型的结构体变量
	fmt.Println(*stu)   // {kimi 11}
	fmt.Println(stu.Name)  // kimi  前面我们说过了标准写法应该是 (*stu).Name 但是你 stu.Name 也可以编译器底层会自动给你加上 *
    score := stu.GetScore()
	fmt.Println(score)  // 11
}
```

#### 三、封装

##### 1、封装介绍

**基本介绍：**

Golang 仍然有面向对象编程的继承，封装和多态的特性，只是实现的方式和其他 OOP 的语言不一样。

**封装介绍：**

封装就是把抽象出的字段和对字段的操作封装在一起，数据被保护在内部，程序的其它包只有通过被授权的方法，才能对字段进行操作。

封装的好处：

> 1、隐藏实现细节
>
> 2、可以对数据进行验证，保证安全合理

如何体现封装：

> 1、对结构体中的属性进行封装
>
> 2、通过方法实现封装

**封装实现的步骤：**

> 1、将结构体、字段的首字母小写（这样其他包就不能使用了）
>
> 2、给结构体所在的包提供一个工厂模式的函数，首字母大写。类似一个构造函数。
>
> 3、提供一个首字母大写的 Set 方法，用于对属性判断并赋值
>
> 4、提供一个首字母大写的 Get 方法，用户获取属性的值

注：

在 Golang 开发中并没有特别强调封装，Golang 本身是对面向对象的特性做了简化的。

##### 2、封装快速入门

person.go ：

```go
package model
import (
	"fmt"
)
type person struct {
	Name string
	age int   // 其他包不能访问
	salary float64
}

// 写一个工厂模式的函数，相当于构造函数
func NewPerson(name string) *person {
	return &person{Name:name}
}
// 为了访问 age 和 salary 我们写 get 和 set 方法
func (person *person) GetAge() int {
	return person.age
}

func (person *person) SetAge(age int) {
	if age > 0 && age < 150 {
		person.age = age
	} else {
		fmt.Println("年龄不符合条件")
	}
}
func (person *person) GetSalary() float64 {
	return person.salary
}

func (person *person) SetSalary(salary float64) {
	person.salary = salary
}
```

main.go ：

```go
package main
import (
	"fmt"
	"../model"
)
func main() {
	person := model.NewPerson("shp")
	person.SetAge(12)
	person.SetSalary(1000.0)
	fmt.Println(*person)   // {shp 12 1000}
}
```

##### 3、封装练习

account.go ：

```go
package model
import (
	"fmt"
)
// 定义一个结构体 account
type account struct {
	accountNum string
	password string
	balance float64
}
// 工厂模式的函数，构造函数
func NewAccount(accountNum string, password string, balance float64) *account {
	if len(accountNum) < 6 || len(accountNum) > 10 {
		fmt.Println("账号格式不正确")
		return nil
	} 
	if len(password) < 6 {
		fmt.Println("密码长度小于 6")
		return nil
	} 
	if balance < 20 {
		fmt.Println("余额不满足条件")
		return nil
	} 
	return &account{accountNum:accountNum,password:password,balance:balance}
}
func (acc *account) GetAccountNum() string {
	return acc.accountNum
}
func (acc *account) SetAccountNum(accountNum string) {
	acc.accountNum = accountNum
}
func (acc *account) GetPassword() string {
	return acc.password
}
func (acc *account) SetPassword(password string) {
	acc.password = password
}
func (acc *account) GetBalance() float64 {
	return acc.balance
}
func (acc *account) SetBalance(balance float64) {
	acc.balance = balance
}
```

main.go ：

```go
package main
import (
	"fmt"
	"../model"
)
func main() {
	account := model.NewAccount("123333","123456",1000.0)
	if account != nil {
		fmt.Println(*account)   // {123333 123456 1000}
	} else {
		fmt.Println("创建失败")
	}
}
```

#### 四、继承

##### 1、继承基本语法

当多个结构体存在相同的属性和方法时，可以从这些结构体中抽象出结构体，在该结构体中定义这些相同的属性和方法。其他结构体不需要重新定义这些属性和方法，只需要嵌套一个匿名结构体即可。

也就是说，在 Golang 中，如果一个 struct 嵌套了另一个匿名结构体，那么这个结构体可以直接访问匿名结构体的字段和方法，从而实现了继承性。

**基本语法：**

> type Goods struct {
>
> ​	Name string
>
> ​	Price int
>
> }
>
> type Book struct {
>
> ​	Goods         这里就是引入的嵌套匿名结构体
>
> ​	bookeName string
>
> }

##### 2、继承快速入门案例

```go
package main
import (
	"fmt"
)
type Student struct {
	Name string
	Age int
	Score int
}

// 将 Pupil 和 Graduate 共有的方法绑定到 *Student
func (stu *Student) ShowInfo() {
	fmt.Println("学生名=%v，年龄=%v，成绩=%v",stu.Name,stu.Age,stu.Score)
}

func (stu *Student) SetScore(score int) {
	stu.Score = score
}

// 小学生
type Pupil struct {
	Student    // 嵌入 Student 匿名结构体
}

// Pupil 特有的方法
func (pupil *Pupil) testing() {
	fmt.Println("小学生特有的方法")
}

// 大学生
type Graduate struct {
	Student    // 嵌入 Student 匿名结构体
}
// Graduate 特有的方法
func (graduate *Graduate) testing() {
	fmt.Println("大学生特有的方法")
}

func main() {
	// 当我们对结构体嵌入了匿名结构体,使用方法会发生变化
	pupil := &Pupil{}
	pupil.Student.Name = "Jimmy"
	pupil.Student.Age = 20
	pupil.testing()
	pupil.Student.SetScore(95)  // 结构体的名称 --> 找到它的匿名结构体 --> 匿名结构体里面的方法
	fmt.Println(*pupil)

}
```

继承给编程带来的便利：

> - 代码的复用性提高了
> - 代码的扩展性和维护性提高了

##### 3、继承深入讨论

**1）、结构体可以使用嵌套匿名结构体的所有字段和方法。即：首字母大写或者小写的字段、方法都可以使用。**

```go
package main
import (
	"fmt"
)
type A struct {
	Name string
	age int
}
func (a *A) SayOk() {
	fmt.Println("A Say ok", a.Name)
}
func (a *A) hello() {
	fmt.Println("A hello", a.Name)
}
type B struct {
	A
}

func main() {
	b := B{}
	b.A.Name = "Jimmy"
	b.A.age = 12
	b.A.SayOk()    // A Say ok Jimmy
	b.A.hello()    // A hello Jimmy
}
```

**2）、匿名结构体字段访问可以简化**

```go
func main() {
	b := B{}
	b.A.Name = "Jimmy"
	b.A.age = 12
	b.A.SayOk()    // A Say ok Jimmy
	b.A.hello()    // A hello Jimmy

	// 上面的写法等价于这种写法
	b.Name = "Jimmy"
	b.age = 12
	b.SayOk() 
	b.hello()
	// B 结构体体中没有如上的字段或方法，但是 B 结构体中嵌入了 A 结构体，A 中有，因此编译器在编译的时候，会先在 B 中找，B 中没有再去 B 中嵌入的结构体中找
}
```

**3）、当结构体和匿名结构体有相同的字段或者方法时，编译器采用*就近访问原则*访问，如果希望访问匿名结构体的字段和方法，可以通过匿名结构体名来区分。**

```go
package main
import (
	"fmt"
)
type A struct {
	Name string
	age int
}
func (a *A) SayOk() {
	fmt.Println("A Say ok", a.Name)
}
func (a *A) hello() {
	fmt.Println("A hello", a.Name)
}
type B struct {
	A
	Name string
}
func (b *B) SayOk() {
	fmt.Println("B Say ok", b.Name)
}

func main() {
	b := B{}
	b.Name = "kimi"   // 因为 B 结构体中有 Name 属性，所以直接给 B 结构体中 Name 属性赋值
	b.A.Name = "shp"  // 这样就是指定给 A 结构体的 Name 赋值
	b.SayOk()  // B 结构体有绑定的 SayOk() 方法，因此调用的是 B 结构体的 SayOk() 方法
	b.hello()  // B 结构体中没有 hello() 方法，因此回去 B 中嵌入的结构体中找 hello() 方法，找到调用 
}
```

**4）、结构体嵌入两个或多个匿名结构体，如两个匿名结构体有相同的字段和方法（同时结构体本身没有同名的字段和方法），在访问时，就必须明确指定匿名结构体名字，否则编译报错**

```go
package main
import (
	"fmt"
)
type A struct {
	Name string
	age int
}
type B struct {
	Name string
	Score float64
}
type C struct {
	A
	B
}
func main() {
	var c C
	//c.Name = "Jimmy"  这种写法是错误的,这种写法意思就是一开始,我在 C 结构体中找 Name 属性,发现没有,然后看 C 中有没有嵌入结构体,在嵌入结构体中找,发现嵌入的两个结构体中都有 Name 属性,就不知道找哪个了,编译报错
	c.A.Name = "Jimmy"  // A 和 B 中都有 Name 属性,这里需要明确指定调用哪个结构体中的属性
}
```

**5）、如果一个 struct 嵌套了一个有名结构体，这种模式就是*组合*，如果是组合关系，那么在访问组合的结构体字段或方法时，就必须带上结构体的名字。**

```go
package main
import (
	"fmt"
)
type A struct {
	Name string
	age int
}
type D struct {
	a A   // 嵌套有名结构体
}
func main() {
	var d D
	d.a.Name = "kimi"  // 有名结构体必须要通过结构体的名字来调用
}
```

**6）、嵌套匿名结构体后，也可以在创建结构体变量时，直接指定各个匿名结构体字段的值**

```go
package main
import (
	"fmt"
)
type Goods struct {
	Name string
	price float64
}

type Brand struct {
	Name string
	Address string
}

type TV struct {
	Goods
	Brand
}
type TV2 struct {
	*Goods    // 也可以把匿名结构体的指针嵌套在结构体中
	*Brand
}


func main() {
	tv := TV{Goods{"手机",4000.0},Brand{"iphone","美国"}}   // 创建结构体的时候直接指定匿名结构体字段的值
	tv2 := TV{
		Goods{
			Name:"手机",
			price:3000,
		},
		Brand{
			Name:"华为",
			Address:"中国",
		},
	}
	fmt.Println(tv)  // {{手机 4000} {iphone 美国}}
	fmt.Println(tv2)  // {{手机 3000} {华为 中国}}

	tv3 := TV2{ &Goods{"手机",4000.0}, &Brand{"iphone","美国"}}
	fmt.Println((*tv3.Goods).Name)   // 手机
}
```

##### 4、多重继承

> 如果一个 struct 嵌套了多个匿名结构体，那么该结构体可以直接访问嵌套的匿名结构体的字段和方法，从而实现了多重继承。
>
> 如果嵌入的多个匿名结构体有相同的字段和方法名（比如，A 结构体的字段 B 中也有），这时候在访问的时候就需要指定结构体的名字来指定访问哪个结构体中的字段。
>
> 为了保证代码的简洁性，建议尽量不要使用多重继承

#### 五、接口

##### 1、接口入门

```go
package main
import (
	"fmt"
)
// 声明一个接口
type Usb interface {
	// 声明了两个没有实现的方法
	Start()
	Stop()
}
type Phone struct {

}
// 让 Phone 实现 usb 接口的方法
func (p Phone) Start() {
	fmt.Println("手机开始工作")
}
func (p Phone) Stop() {
	fmt.Println("手机停止工作")
}
type Caramer struct {

}

// 让 Caramer 实现 usb 接口的方法
func (c Caramer) Start() {
	fmt.Println("相机开始工作")
}
func (c Caramer) Stop() {
	fmt.Println("相机停止工作")
}

// 计算机
type Computer struct {

}

// 编写一个 Working 方法，接收一个 Usb 接口类型的变量
// 只要实现了 Usb 接口（所谓实现 Usb 接口，就是指实现了 Usb 接口声明的所有方法）
func (c Computer) Working(usb Usb) {
	// 通过 usb 接口变量来调用 Start 和 Stop 方法
	usb.Start()
	usb.Stop()
}

func main() {
	// 创建结构体变量
	computer := Computer{}
	phone := Phone{}
	caramer := Caramer{}
	computer.Working(phone)
	computer.Working(caramer)
}
```

##### 2、接口特点和语法说明

**基本介绍：**

> interface 类型可以定义一组方法，但是这些方法不需要实现。并且 *interface 不能包含任何变量* 。某个自定义类型要使用的时候，在根据具体情况去实现接口中的方法。

**基本语法：**

> type 接口名 interface {
>
> ​	method1(参数列表) 返回值列表
>
> ​	method2(参数列表) 返回值列表
>
> }

说明：

> 1、接口里面的所有方法都没有方法体，即接口的方法都是没有实现的方法。接口体现了程序设计的多态和高内聚低耦合的思想。
>
> 2、Golang 中的接口，不需要显示的实现。只要一个变量，含有接口类型中的所有方法，那么这个变量就实现了这个接口。因此 Golang 中没有 implement 这样的关键字




















































































































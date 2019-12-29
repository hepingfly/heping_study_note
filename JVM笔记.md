# JVM 笔记

> - Class Loader 类加载器
>   - 负责加载 class 文件，class 文件在文件开头有特定的文件标识，并且 classloader 只负责 class 文件的加载，至于它是否可以运行，则由 Execution Engine 决定。
> - Native Interface（Java 中会有 native 关键字标识的方法）
>   - 本地接口的作用是融合不同的编程语言为 Java 所用，它的初衷是融合 c/c++ 程序，Java 诞生的时候，是 c/c++ 横行的时候，要想立足，必须可以调用 c/c++ 程序，于是就在内存中专门开辟了一块区域处理标记为 native 的代码，它的具体做法是 Native Method Stack 中登记 native 方法，在 Execution Engine 执行时加载 native libraies 。
>   - 目前该方法的使用越来越少了，除非是与硬件有关的应用，比如通过 Java 程序驱动打印机，或者 Java 系统管理生产设备，在企业级应用中已经比较少见。因为现在异构领域的通信很发达，比如可以使用 socket 通信也可以使用 webservice 等等。
> - Method Area 方法区
>   - 方法区是被所有线程共享，所有字段和方法字节码，以及一些特殊方法如构造函数，接口代码也在此定义。简单说，所有定义的方法的信息都保存在该区域，此区属于共享区间
>   - 静态变量、常量、类信息、常量池都存在方法区中
> - PC Register 程序计数器
>   - 每个线程都有一个程序计数器，就是一个指针，指向方法区中的方法字节码（下一个将要执行的指令代码），由执行引擎读取下一条指令，是一个非常小的内存空间，几乎可以忽略不计。
> - Native Method Stack 本地方法栈
>   - 它的具体做法是 Native Method Stack 中登记 native 方法，在 Execution Engine 执行时加载 native libraies 
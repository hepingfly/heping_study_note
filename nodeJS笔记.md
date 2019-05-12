#   NodeJS 笔记

### 一、NodeJs 简介

1、Node.js 是一个能够在==服务器端==运行 JavaScript 的开放源代码、跨平台的 ==JavaScript 运行环境== 

> 我们之前学的 js 都是在浏览器中运行的，而 node.js 做了一件非常厉害的事，它让 js 可以直接在服务器上运行。要知道在浏览器中运行，最大限度它只能操作浏览器（比如说开个窗口，改个页面）。而 node.js 可以在服务器上运行，那么这时候它就是直接在和操作系统进行交互。

2、Node 采用 Google 开发的 V8 引擎运行 js 代码，使用==事件驱动==、==非阻塞和====异步 IO 模型==等技术来提高性能，可优化应用程序的传输量和规模

介绍下 「瑞安·达尔（Ryan Dahl）」 这个人：

> 这个人是 node.js 之父，Ryan Dahl 并非科班出身的开发者，在 2004 年的时候他还在纽约的罗彻斯特大学数学系读博士，后来觉得读博无聊，走上了码农的道路，Ryan Dahl 经过两年的工作后，成为了高性能的 web 服务器的专家。

### 二、模块化简介

- 在 Node 中，一个 js 文件就是一个模块。
- 在 Node 中，每一个 js 文件中的 js 代码都是独立运行在一个函数中（你可以把它看成在代码的最外边有一个函数包裹着它），而不是全局作用域，所以一个模块中的变量和函数在其他模块中无法访问

```js
/*
    我们可以通过 exports 来向外部暴露变量和方法
    只需要将需要暴露给外部的变量或方法设置为 exports 的属性即可
*/
exports.x = "我是 x 属性";
exports.add = function(a,b) {
    return a + b;
};
```

```js
// 引入其他模块
/*
    在 node 中，通过 require() 函数来引入外部的模块
    require() 可以传递一个文件的路径作为参数，node 将会自动根据该路径来引入外部的模块
    这里的路径如果使用相对路径，必须以 . 或 .. 开头
    
    使用 require() 引入模块以后，该函数会返回一个对象，这个对象代表的就是引入的模块
    
    我们使用 require() 引入外部模块时，使用的就是模块标识，我们可以通过模块标识来找到指定的模块
    模块分成两大类：
    	核心模块
    		由 node 引擎提供的模块
    		核心模块的标识就是，模块的名字
    	文件模块
    		由用户自己创建的模块
    		文件模块的标识就是，文件的路径（绝对路径和相对路径都可以）
*/
var a = require("./a.js");

// 我想引入一个文件模块
var fs = require("fs")   // 这里直接写模块的名字即可
```

### 三、模块化详细介绍

```js
/*
    在 node 中有一个全局对象 global ,它的作用和网页中 window 类似
    在全局中创建的变量都会作为 global 的属性保存
    在全局中创建的函数都会作为 global 的方法保存
    
    当 node 在执行模块中的代码时，它会首先在代码的最顶部，添加如下代码：
    function (exports, require, module, __filename, __dirname) {
    在代码的最底部，添加如下代码：
    }
    
    实际上模块中的代码都是包装在一个函数中执行的，并且在函数执行时，同时传递进了 5 个实参：
    exports
        该对象用来将变量或函数暴露到外部
    require
        函数，用来引入外部的模块
    module
        module 代表的是当前模块本身，exports 就是 module 的属性，既可以使用 exports 导出，也可以使用 module.exports 导出
    __filename
        当前模块的完整路径
    __dirname
        当前模块所在文件夹的完整路径
*/
var a = 10;
console.log(global.a);   // 结果是 undefined，证明这个 a 不是全局变量
b = 20;
console.log(global.b);   // 结果是 20，证明这个 b 是全局变量
```

**说一下 exports 和 module.exports 的区别：**

helloModule.js：

```js
module.exports = {
	name:"kimi",
	age:11,
	sayHello:function() {
		console.log("我是 kimi");
	}
};

// 下面这样写是错误的
exports = {
	name:"kimi",
	age:11,
	sayHello:function() {
		console.log("我是 kimi");
	}
};
```

module.js：

```js
var hello = require("./helloModule.js");
console.log(hello.name);
console.log(hello.age);
hello.sayHello();
```

`module.exports = {}`相当于是在改 module 这个对象的 exports 属性，exports 属性的值是一个对象，而`exports={}`相当于在堆空间中新开辟一块内存，栈空间的 exports 变量指向这个新开辟的内存空间。

总结一下：

- exports 只能使用 "." 的方式来向外暴露内部变量
  - exports.xxx = xxxx
- module.exports 既可以通过 "." 的方式，也可以直接赋值
  - module.exports.xxx = xxxx
  - module.exports = {}








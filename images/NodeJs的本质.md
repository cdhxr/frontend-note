# gpt answer

Node.js 是一个 **基于 Chrome V8 引擎的 JavaScript 运行时环境**

| 浏览器中 JavaScript      | Node.js 中 JavaScript |
| -------------------- | -------------------- |
| 运行在网页中，与 HTML/CSS 互动 | 运行在计算机上，访问文件系统、网络等资源 |
| 没法访问本地文件、系统命令        | 可以读写文件、执行命令、启动服务器    |
✅ 核心特点

| 特点       | 说明                                |
| -------- | --------------------------------- |
| 异步非阻塞    | I/O 操作（如文件、网络）不会阻塞主线程，效率高         |
| 单线程事件循环  | 利用事件机制处理并发，适合高并发场景                |
| 内置模块丰富   | `fs`、`http`、`path`、`os` 等常用模块开箱即用 |
| 跨平台      | 可以在 Windows / Mac / Linux 上运行     |
| 有 npm 生态 | 拥有全球最大包管理平台 npm，几乎什么功能都能找到包实现     |


我的理解：Nodejs相当于让js成为一个像java，C，Python等，一个独立的高级程序语言，使其地位对标其他常用语言，用于计算机中的编程，不依赖于浏览器环境

# 深入理解

## JavaScript Engine V8

类似于C语言的g++，将javaScript编译为机器码的东西
现在最广泛使用的JavaScript Engine是V8

- why v8 is fast: 
	- cuz 它直接将js转换为机器码，而中间没有其他抽象层，这使得它比之前的jsEngine更快

- V8 实现了EMCAScript and WebAssembly，EMCAScript（一个语言规范，规定了js的语法，降低了不同Engine解释js的成本），是基于C++实现的Engine

- V8是无法操纵文件，网络相关的功能的，但c++是功能完备的语言，可以处理硬件，OS，网卡，请求等等，这也是NodeJS能有这些功能的原因之一

- NodeJs，include了V8，以及扩展了js功能，所以js某种意义上是进一步抽象的Cpp

扩展功能的过程（大致想法）：

- include v8
- 创建Cpp函数
- 将函数与v8绑定

V8的可嵌入Cpp的特性，使得JS易于扩展有了更大的自由性，不同的人可以创建自己的JS，有名的例子就是NodeJs，React Native等等

## NodeJs is just A Cpp application

### download NodeJS

- installer
- binary
- source code

Nodejs源码的构成
- deps依赖项，包括V8这样的模块
- src包括像Node.h，nodebinding.h这样的头文件
- lib包括了很多JavaScript代码，这也是nodejs的实现部分，只要包含了V8的Cpp项目，你也可以使用Js coding，包括像是Http，fs，os，等等模块

你可以Build source code，通过一系列命令

对于二进制文件，点开其内部，bin的内部有node，的exe文件

## libuv事件循环和事件驱动编程

nodeJs是一种系统语言，是为了创建网络应用程序的语言，换句话说，他是服务器端语言

服务器端语言，意味着他需要能做到一些JavaScript做不到的事，比如

- 要能够处理计算机进程
- 要能与数据库交互
- 要能处理文件系统
- 要有可扩展性，要能够同时处理大量用户的请求
- 要能处理网络请求

以上的很多事情都是通过libuv实现的

libuv是nodeJs除了V8一样，一项重要的dependency，他是一个C library

NodeJs是操作系统启动的进程之一
![[Pasted image 20250508201848.png]]
让我们来专注nodeJs
![[Pasted image 20250508202059.png]]

 V8和libuv是NodeJs最重要的两个库（依赖），还有一个重要的组成是，事件循环，他不是库，而是NodeJs实现的一种机制
![[Pasted image 20250508202436.png]]

```js
console.log("1")

for(let i = 0; i<1000000000;i++){}

console.log("second")
```

如上，如果程序同步的运行，则会出现，在for循环处变的无响应，等待后才能继续执行
但是，NodeJs允许程序异步的执行，由于事件循环和libuv，这是可能的


![[Pasted image 20250508214625.png]]

- 在第二行代码的阅读文件事件会被push进事件循环，然后在事件循环中继续进行，然后v8会立刻自动向后执行，无论第二行代码是否执行完
![[Pasted image 20250508215241.png]]
- 从V8到EventLoop Stack到libuv到Storage再返回所需的数据逐步到V8
- V8拿到数据，则会callback回调至事件触发处执行代码

- 特殊的代码会被当做事件进入EventLoop，定时器函数，fs操作，http网络相关的操作等等

```js
console.log("A")

setTimeout(()=>{
  console.log("B")
},0)
//这里，B依旧会最后一个出现，因为他被放入了事件循环中

console.log("C")
console.log("D")
console.log("E")
```

事件循环中的代码，不会打断同步的代码，在同步的代码执行完毕后，回调要等到主线程空闲才能运行。

`setInterval(()=>{},100);`这句代码被推到了在事件循环上，且永远不会退出

只要事件循环和主线程上有东西在运行，NodeJs就会Keep running，直到手动退出
所以构建一个服务器，只要不手动关闭，收到请求它就会做出响应

![[Pasted image 20250508221148.png]]他们属于一个线程（可以理解为一个线性的执行代码过程，一次只能执行一件事）

一个线程只能做一件事，线程被占据时，其他事件都会被阻塞，可以通过设置多个线程来修复它，不同的线程做不同的事

因为V8和EventLoop在同一个线程中，所以其中一个被阻塞，另一个也会无法运行

NodeJs还有更多的线程，在libuv中，称为线程池，默认情况下有4个
不是所有的任务都是通过线程池处理的，比如网络请求，事实上NodeJs在尽可能减少线程池的使用，当可以使用操作系统异步的完成某些事情时，不会使用线程池

比如网络请求，会从网卡到OS然后直接到libuv中（不通过线程池，直接到事件循环中），但比如处理文件是需要通过线程池的

尽量不使用线程池的想法，使NodeJs拥有了很好的可扩展性 ，在处理的信息量大时，也可以不使用大量硬件资源

永远不应该阻塞事件循环或者线程池，即不要让以下情况出现：

```js
setTimeout(()=>
  console.log("Done");
},1)；

for(leti=0;i<9000000000;i++){}
```
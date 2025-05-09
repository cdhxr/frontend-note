是一个NodeJs给我们的JavaScript对象

```js
// 引入 Node.js 的内置 'events' 模块，它提供了事件触发与监听的功能
const EventEmitter = require("events");

// 创建一个继承自 EventEmitter 的类 Emitter
class Emitter extends EventEmitter {}

// 创建 Emitter 类的实例 myE，此实例具有事件监听和触发的能力
const myE = new Emitter();

// 使用 'on' 方法为 'foo' 事件注册一个监听器，当事件被触发时执行回调函数
myE.on("foo", () => {
  console.log("Event occurred."); // 当 'foo' 事件被触发时输出这行文字
});

// 触发名为 'foo' 的事件，执行所有注册在这个事件上的回调函数
myE.emit("foo");

```

首先，这段代码与libuv与nodejs层面的事件循环，与异步等等东西都完全无关，这只是JavaScript

nodeJs是事件驱动的，这种机制不是nodeJs独有的，一个例子是操作系统

假设有一个OS，其中CPU有两个线程，现在需要处理键盘的输入输出，往往存在两种方案

- 同步方案
	- 其中一个线程需要一直读取键盘的输入
	- 另一个线程用于处理信息
	- 这意味着一个珍贵的线程将一直被一个很简单的事情占据了
- 异步方案
	- 
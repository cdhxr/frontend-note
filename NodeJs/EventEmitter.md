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

假设有一个OS，其中CPU有两个core（可以粗略的理解为线程），现在需要处理键盘的输入输出，往往存在两种方案

- 同步方案
	- 其中一个core需要一直读取键盘的输入
	- 另一个线程用于处理信息
	- 这意味着一个珍贵的线程将一直被一个很简单的事情占据了
- 异步方案
	- 不分配 一个core来一直监听键盘
	- 而当键盘中的按键被按下时，发布一个命令
	- 这种情境下，OS成为中断（interrupt）
	- 我们可以发布中断，然后CPU会对中断做出响应，这样就不会杀死一个core，只需要不断的向CPU发布中断（事件），CPU对事件响应，并异步的完成他们
	- 这就是事件驱动的机制

EventEmitter对象做的事是给我们一种更好的方式，来处理NodeJS这种异步的，事件驱动的机制

这只是一种模式（pattern）,使我们的代码更加易于阅读和管理
![[Pasted image 20250509160430.png]]
比如，这段代码做的是，当有请求进入时，会触发该回调函数

请求的过程

- Internet
- =>网卡
- =>OS( 会调查该请求，并处理请求，这种情况下，可能有个port number与Nodejs的某个进程相联系 ) 
- => 对应的NodeJs进程 
- => 此时libuv package就会使用事件循环，来执行一个内部回调函数=> 这个回调函数会调用V8中的回调函数（如图中的app.on）

app.on或者Object.on函数就是为了对事件做出反应存在的

这就 是EventEmitter的想法，以及所做的一切
![[Pasted image 20250509164920.png]]

每次调用on所做的一切，只是将一个函数对象push进Object中，仅仅如此

![[Pasted image 20250509165420.png]]

每次Emit，他会遍历Object中所有项，然后将与事件名称（foo）相关联的函数执行

这个图片可能带来误解的点在于，这个对象内其实只有两个Object对象，一个是foo，一个是Bar，两个都是数组，一个是长度为3，一个长度为一

第三个emit带了参数，但依旧会对所有foo中的函数进行调用

理解了这里发生了什么之后，就能理解EventEmitter的行为是完全同步的，它只是JavaScript，与libuv，Eventloop等等无关这句话了

关于EventEmitter的更多方法，可以看这里
https://nodejs.org/docs/latest/api/events.html#class-eventemitter

EventEmitter的实现

```js
class EventEmitter {
  listeners = {}

  addListener(eventName, fn) {
    this.listeners[eventName] = this.listeners[eventName] || [];
    this.listeners[eventName].push(fn);
    return this;
  }

  on(eventName, fn) {
    return this.addListener(eventName, fn);
  }

  once(eventName, fn) {
    this.listeners[eventName] = this.listeners[eventName] || [];
    const onceWrapper = () => {
      fn();
      this.off(eventName, onceWrapper);
    }
    this.listeners[eventName].push(onceWrapper);
    return this;
  }

  off(eventName, fn) {
    return this.removeListener(eventName, fn);
  }

  removeListener (eventName, fn) {
    let lis = this.listeners[eventName];
    if (!lis) return this;
    for(let i = lis.length; i >= 0; i--) {
      if (lis[i] === fn) {
        lis.splice(i,1);
        break;
      }
    }
    return this;
  }

  emit(eventName, ...args) {
    let fns = this.listeners[eventName];
    if (!fns) return false;
    fns.forEach((f) => {
      f(...args);
    });
    return true;
  }

  listenerCount(eventName) {
    let fns = this.listeners[eventName] || [];
    return fns.length;
  }

  rawListeners(eventName) {
    return this.listeners[eventName];
  }
}
```

这个对象将被NodeJs大量使用
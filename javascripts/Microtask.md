
简单地说，当一个 promise 准备就绪时，它的 `.then/catch/finally` 处理程序就会被放入队列中：但是它们不会立即被执行。当 JavaScript 引擎执行完当前的代码，它会从队列中获取任务并执行它。

异步任务需要适当的管理。为此，ECMA 标准规定了一个内部队列 `PromiseJobs`，通常被称为“微任务队列（microtask queue）”（V8 术语）。

微任务队列清空后，JavaScript 引擎会触发下面这事件
`window.addEventListener('unhandledrejection', event => alert(event.reason));

当 Promise 被拒绝时，如果这个拒绝没有被 **`.catch()`** 或 **`try...catch`** 捕获，JavaScript 引擎会在微任务队列执行完后，检查是否有未处理的拒绝状态的 Promise。如果有，就会触发 `unhandledrejection` 事件。

文件的本质是一串01序列，其中会有特定的序列代表文件名，文件类型，创建时间，内容等等信息

NodeJS通过调用函数，让libuv调用操作系统，来进行System Call，对硬盘中的文件进行处理，他不会直接处理文件（操作系统有open()，rename()之类的system Call）

# 三种方法

fs模块有三种API可以用于处理文件，做到几乎一样的事：分别是Promise API、Callback API 和 Synchronous API

百分之90以上的情况，应该会使用Promise API

使用 Node.js 的 `fs` 模块中三种不同方式来复制文件：Promise API、Callback API 和 Synchronous API。下面是每段代码的注释以及三者的简要区别：

---

### ✅ **Promise API（基于 async/await）**

```js
const fs = require("fs/promises"); // 引入 promises 版本的 fs 模块

(async () => {
  try {
    // 使用 await 异步等待复制完成
    await fs.copyFile("command.txt", "copied-promise.txt");
  } catch (error) {
    console.log(error); // 捕获并打印错误
  }
})();
```

**特点：**

- 基于 Promise，语法简洁，适合写结构清晰的异步代码。
- 使用 `await` 等待操作完成，异常可通过 try-catch 统一处理。

---

### ✅ **Callback API（回调方式）**

```js
const fs = require("fs"); // 引入标准 fs 模块

fs.copyFile("command.txt", "copied-callback.txt", (error) => {
  if (error) console.log(error); // 错误通过回调函数参数处理
});
```

**特点：**

- 传统写法，通过回调处理结果。
- 代码可能会变得嵌套混乱（回调地狱）。

---

### ✅ **Synchronous API（同步方式）**

```js
const fs = require("fs"); // 引入标准 fs 模块

fs.copyFileSync("command.txt", "copied-sync.txt"); // 阻塞式复制
```

**特点：**

- 阻塞式操作，代码会等待复制完成再继续执行。
- 简单直接，但不推荐在高并发或 I/O 密集场景使用。

---

### 总结对比：

|方式|是否异步|错误处理方式|优点|缺点|
|---|---|---|---|---|
|Promise API|✅ 是|try-catch|结构清晰、现代语法|需要 Node.js v10+|
|Callback API|✅ 是|回调函数内处理|广泛支持，兼容性好|可读性差，容易嵌套混乱|
|Sync API|❌ 否|try-catch（外部）|简洁明了，适合小脚本或启动代码|阻塞线程，影响性能|

基本上用的都是Promise API

# 
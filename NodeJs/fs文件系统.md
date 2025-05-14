
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

# project

## 异步迭代器

**`async function*`** 声明创建一个[绑定](https://developer.mozilla.org/zh-CN/docs/Glossary/Binding)到给定名称的新异步生成器函数。

产生一个异步可迭代对象，可以异步的迭代

```js
async function* foo() {
  yield 1;
  yield 2;
}

(async function () {
  for await (const num of foo()) {
    console.log(num);
    // Expected output: 1

    break; // Closes iterator, triggers return
  }
})();
```

遍历async iterable object ( 异步的返回数据的对象 )

## fs module
### `fs.watch(filename[, options][, listener])`

#### history

- **filename**：`<string>` | `<Buffer>` | `<URL>`  
    要监听的文件或目录的路径。
    
- **options**：`<string>` | `<Object>`  
    可选参数。如果是字符串，则表示编码方式。如果是对象，可以设置以下字段：
    
    - **persistent**：`<boolean>`  
        是否在监听文件时保持 Node.js 进程持续运行。默认值是 `true`。
    - **recursive**：`<boolean>`  
        是否递归地监听所有子目录（仅当监听的是目录时有效，并且仅在部分平台上受支持，具体见注意事项）。默认值是 `false`。
    - **encoding**：`<string>`  
        指定传给回调函数的文件名所用的字符编码。默认是 `'utf8'`。
    - **signal**：`<AbortSignal>`  
        允许通过 `AbortSignal` 来关闭返回的监听器。
    
- **listener**：`<Function>` | `undefined`  
    可选的回调函数，默认值是 `undefined`。


返回一个 `<fs.FSWatcher>` 实例，用于监听变化。
- 值类似于：`{eventType:'change',filename:'command.txt'}`
- 分别为变化的类型，和发生变化的文件

一旦指定的路径中的内容发生变化，`<fs.FSWatcher>` 实例的值就会改变

```js
const fs = require("fs/promises")
//immediately invoked function
(async =>{
  const watcher = fs.watch("./command.txt")

  for await (const event of watcher) {
    if (event.eventType === "change") {
      console.log("The file was changed")
    }
  }
})();
```


读取文件

- 先打开，再读写
- open(file descriptor), 并没有移动所有内容进入mem，只将file descriptor移动了
- file descriptor 是一个number

```js
const fs = require("fs/promises")

(async =>{
  const watcher = fs.watch("./command.txt")
  
  const commandFileHandler await fs.open("./command.txt","r")
  
  for await (const event of watcher) {
    if (event.eventType === "change") {
      console.log("The file was changed")
    }
    // get the size of our file
    const size = (await commandFileHandler.stat()).size;
    const buff = Buffer.alloc(size);

    const offset = 0;
    const length = buff.byteLength;
    const position = 0;

    const content = await commandFileHandler.read(
       buff,
       offset,
       length,
       position
    );
    
    console.log(buff.tostring ("'utf-8"));
    console.log(content);
  }
})();
```

通过stat返回的对象，得到文件大小，已创建大小合适的Buffer
指定position和offset以正确的读取文件

- **`buff`（Buffer）**
    
    - 用于接收从文件中读取的数据。
    - 是通过 `Buffer.alloc(size)` 创建的，大小为文件的总字节数。
    
- **`offset`（number）**
    
    - 表示从 `buff` 的哪个字节位置开始写入数据。这里是 `0`，即从 buffer 的起始位置开始写入。
    
- **`length`（number）**
    
    - 表示要读取多少字节的数据，这里是 `buff.byteLength`，即读取整个文件大小的数据。
    
- **`position`（number | null）**
    
    - 表示从文件的哪个字节位置开始读取数据。这里是 `0`，意味着从文件开头开始读取。
    - 如果是 `null`，则从当前文件指针位置开始读取（对于流式读取时更常见）。


NodeJS只能处理字符编码和解码，不能处理视频或者图片（除非引入了第三方包）

用事件循环来写出更好的代码，加上创建文件功能

```js
const fs = require("fs/promises");

(async () => {
  const createFile = async (path) => {
    try {
      // we want to check whether or not we already have that file
      const existingFileHandle = await fs.open(path, "r");
      existingFileHandle.close();

      // we already have that file...
      return console.log(`The file ${path} already exists.`);
    } catch (e) {
      // we don't have the file, now we should create it
      const newFileHandle = await fs.open(path, "w");
      console.log("A new file was successfully created.");
      newFileHandle.close();
    }
  };

  // commands
  const CREATE_FILE = "create a file";

  const commandFileHandler = await fs.open("./command.txt", "r");

  commandFileHandler.on("change", async () => {
    // get the size of our file
    const size = (await commandFileHandler.stat()).size;
    // allocate our buffer with the size of the file
    const buff = Buffer.alloc(size);
    // the location at which we want to start filling our buffer
    const offset = 0;
    // how many bytes we want to read
    const length = buff.byteLength;
    // the position that we want to start reading the file from
    const position = 0;

    // we always want to read the whole content (from beginning all the way to the end)
    await commandFileHandler.read(buff, offset, length, position);

    const command = buff.toString("utf-8");

    // create a file:
    // create a file <path>
    if (command.includes(CREATE_FILE)) {
      const filePath = command.substring(CREATE_FILE.length + 1);
      createFile(filePath);
    }
  });

  // watcher...
  const watcher = fs.watch("./command.txt");
  for await (const event of watcher) {
    if (event.eventType === "change") {
      commandFileHandler.emit("change");
    }
  }
})();
```

创建文件，
在 Node.js 中，**Stream（流）** 是处理 **大数据** 或 **持续数据源**（如文件、网络、用户输入等）的一种高效机制。它能边读取边处理，避免一次性加载到内存中，极大提升性能。

Stream 就是“按块读取/写入数据”的机制，用于节省内存并提升效率。

| 类型          | 说明         | 举例           |
| ----------- | ---------- | ------------ |
| `Readable`  | 可读流（读取数据）  | 文件读取、HTTP请求体 |
| `Writable`  | 可写流（写入数据）  | 文件写入、HTTP响应体 |
| `Duplex`    | 可读可写流（双工）  | TCP 套接字      |
| `Transform` | 可读可写+数据可转换 | 压缩、加密、编码流    |
|             |            |              |
数据转换的含义：![[Pasted image 20250512175925.png]]

NodeJs是单线程的，这意味着它默认情况下只能使用一个CPU，想使用多个core，则需要用到clustering 或者worker thread 模块

## 🧰 常见的 Stream 方法和事件

### 方法

- `.pipe()` 会把读流自动传入写流，常用于复制、压缩等操作。
- `.pipe(destination)`：连接到另一个流 
- `.unpipe()`：断开连接
- `.read() / .write()`：手动读写数据

### 事件

| 事件名      | 说明      |
| -------- | ------- |
| `data`   | 可读流收到数据 |
| `end`    | 可读流数据读完 |
| `error`  | 出现错误    |
| `finish` | 可写流写入完成 |

---

## 🧪 示例 1：读取文件的可读流

```js
const fs = require('fs');

const readStream = fs.createReadStream('bigfile.txt', { encoding: 'utf8' });

readStream.on('data', chunk => {
  console.log('Received chunk:', chunk);
});

readStream.on('end', () => {
  console.log('No more data.');
});
```

---

## 🧪 示例 2：写入文件的可写流

```js
const fs = require('fs');

const writeStream = fs.createWriteStream('output.txt');

writeStream.write('Hello, ');
writeStream.write('world!\n');
writeStream.end(); // 关闭写入
```

---

## 🔁 示例 3：管道（pipe）——复制文件

```js
const fs = require('fs');

fs.createReadStream('input.txt')
  .pipe(fs.createWriteStream('output.txt'));
```

> `.pipe()` 会把读流自动传入写流，常用于复制、压缩等操作。

---

# 不同类型的Stream

## Writable Stream

一个Stream对象，有内部缓冲区（默认大小16384 Byte，可以改变），有属于它的events，properties，methods

![[Pasted image 20250512181850.png]]
快速的原因：
- 没有Stream时，我们需要对底层资源进行 1million次 写操作
- 有了Stream，将数据聚合了，再进行写，减少了写操作的次数，使CPU任务更集中而不用切换

如果最后一个数据块太大，当数据大小综合超出了内部缓冲区时，多余的部分会被存在内存中，然后在缓存区被清空时，立即被推入buffer
(Buffer是内存中一个特殊的存在特定size数据的区域)

为了避免内存问题，我们应该在Buffer被清空后，再进行写数据

## readable Stream

![[Pasted image 20250512183240.png]]

- 填满后，得到一个事件，事件名是data，一填满就得到一个新的事件
- 对于一个大的数据的Write，我们应该先通过readable Stream将其分块，再放入Writable Stream进行写操作

```js
//通过read流，读数据，放入write流，写数据
//数据量大时，避免内存的占用，使用pause，resume机制优化

  streamRead.on("data", (chunk) => {
    if (!streamWrite.write(chunk)) {
      streamRead.pause();
    }
  });

  streamWrite.on("drain", () => {
    streamRead.resume();
  });
```

- 监听 "data" 事件，每当读取到一块数据（chunk）：
- 把数据写入写入流:如果写入返回 false，说明写入缓冲区满了（不能继续写），就暂停读取流以避免内存占用增加。
- drain事件被监听到时，意味着writeBuffer被清空，这时再resume Readable Stream继续读

# Drain event

当stream.write(buff)返回false时，说明需要先清空internal buffer，在其返回false时，应该立刻清空Buffer，不该继续write，否则会造成可怕的问题

```javascript
stream.on("drain", () => {
  console.log("We are now safe to write more!");
});
```

---

### 解释：

- `stream.on("drain", ...)`：  
    表示当 `stream` 的缓冲区排空时，触发 `"drain"` 事件。

- `() => { ... }` 是箭头函数，表示我们传入的回调函数。
- `console.log(...)`：在控制台输出一句话。

---

### 什么是 `"drain"` 事件？

当你使用 `stream.write(data)` 写入数据时，如果返回 `false`，表示内部缓冲区已满。你需要等 `"drain"` 事件触发后再继续写入。`"drain"` 事件触发，也意味着已满的内部缓冲区被释放

也就是说，这个事件**告诉你可以继续写数据了**。

## Duplex and Transform

他们有两个internal Buffer，相当于上面两个的组合版


# Duplex Stream 和 Transform Stream

![[Pasted image 20250514132309.png]]

Duplex仅仅是内部有两个Buffer并且彼此是不相关的

![[Pasted image 20250514132432.png]]如果数据流像是这样，其实就是Transform Stream了，虽然可以在Read前对数据进行转换，也可以不加工

# pipe

事实上就是对上述读写时，pause，resume的封装

在 Node.js 中，`pipe` 和 `pipeline` 都是用于连接多个 **流（Stream）** 的工具，它们能实现 **数据的自动传输和背压控制**，但用法、适用场景、稳定性略有不同。

---

## 📌 一、`stream.pipe()` 简介

### ✅ 基本用法：

```js
readableStream.pipe(writableStream);
```

它的作用是：

- **自动读取** `readableStream` 的数据，
    
- 并 **自动写入** `writableStream`，
    
- 同时 **自动处理背压（backpressure）**。
    

### 🌊 示例：

```js
const fs = require('fs');

const readStream = fs.createReadStream('input.txt');
const writeStream = fs.createWriteStream('output.txt');

readStream.pipe(writeStream);
```

这个代码会将 `input.txt` 的内容流式写入 `output.txt`。

### 🧠 特点：

|优点|缺点|
|---|---|
|简洁、语法短|错误处理麻烦（每个流都要单独加 `.on('error', ...)`）|
|自动处理背压|多个流组合时容易混乱|

---

## 📌 二、`stream.pipeline()` 简介（推荐）

Node.js v10.0 引入的 `stream.pipeline` 是对 `.pipe()` 的封装，**更安全、更现代**。

### ✅ 用法：

```js
const { pipeline } = require('stream');
const fs = require('fs');

pipeline(
  fs.createReadStream('input.txt'),
  fs.createWriteStream('output.txt'),
  (err) => {
    if (err) {
      console.error('Pipeline failed.', err);
    } else {
      console.log('Pipeline succeeded.');
    }
  }
);
```

### 🧠 特点：

|特性|说明|
|---|---|
|自动传播错误|所有流的错误集中在一个回调处理|
|更健壮|不会出现未捕获的异常导致程序崩溃|
|支持 async/await|可以与 `promisify` 一起使用|

```js
const { pipeline } = require('stream/promises');
await pipeline(readable, transform, writable);
```

---

## 🔍 `pipe` vs `pipeline` 对比总结

|特性|`pipe()`|`pipeline()`|
|---|---|---|
|写法|简洁|稍复杂|
|错误处理|手动处理每个流|自动统一处理|
|背压支持|支持|支持|
|推荐程度|小型、简单场景可用|🚀 **推荐用于生产环境**|

---

## ✅ 什么时候用哪个？

- 👉 **简单任务**：复制文件、解压缩等简单流操作 → 用 `pipe()`
    
- ✅ **复杂任务/生产代码**：需要多个流（如解密 → 解压 → 写入），或你关心可靠性 → 用 `pipeline()`
    

---

如果你在开发自己的命令行工具、构建流式数据处理系统，比如视频转码、日志处理、代理中转等，用 `pipeline()` 几乎总是更好。

需要例子（比如用 pipeline 压缩文件、用 transform 处理数据）我也可以马上写一个给你。
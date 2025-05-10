在 Node.js 中，`Buffer` 是一个用于处理**二进制数据流**的类，是 Node.js 的核心模块之一，特别适用于处理文件、网络协议、图像、音频等不是纯文本的数据。

### 一、为什么需要 Buffer？

JavaScript 原生是基于字符串处理数据的，不支持直接操作二进制数据。而 Node.js 主要用于服务端开发，经常需要处理如 TCP 流、文件读写、加密等，这些都需要操作**原始二进制数据**，这时就需要 `Buffer`。

---

### 二、Buffer 的特点

- 不可变的原始二进制数据片段。
- 类似数组，但底层是 `C++` 实现的，性能高。
- 存储在固定内存中，不受 V8 垃圾回收影响。

# Buffer（缓冲区）

是内存中的容器，创建Buffer需要以字节为单位分配一部分memory，

创建Buffer意味着将0全部填充，在nodeJS中Buffer中的每个元素有8bits的空间
Buffer就像是一个有很多元素组成的数组，缓冲区大小是固定的

通过Buffer可以直接控制缓冲区中的0和1，这允许了高速的数据操作

```js
const { Buffer } = require("buffer");
const buff Buffer.alloc(3);/24 bits 8 =3 bytes
buff[0]=0x48;
buff[1]=0x69;
buff[2]=0x21;

console.log(buff.tostring("utf-8"));  //解码缓冲区
//输出Hi!

//等价写法
const buff Buffer.from([0x48,0x69,0x21]);
console.log(buff.tostring("utf-8"))

//逆向的操作
const buff = Buffer.from("Hi!","utf-8");
console.log(buff);
```
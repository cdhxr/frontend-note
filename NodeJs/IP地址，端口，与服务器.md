# simple tcp server

客户端代码（client.js）：

```js
const net = require("net");

const socket = net.createConnection({ host: "127.0.0.1", port: 3099 }, () => {
    const buff = Buffer.alloc(8); // 创建长度为 8 的缓冲区
    buff[0] = 12;
    buff[1] = 34;
    socket.write(buff); // 向服务器发送数据
});

```

服务端代码（server.js）：

```js
const net = require("net");

const server = net.createServer((socket) => {
    socket.on("data", (data) => {
        console.log("接收到数据:", data); // 打印接收到的数据 Buffer
    });
});

server.listen(80, "127.0.0.1", () => {
    console.log("opened server on", server.address());
});

```


连接了相同网络的设备如何访问另一台设备的信息？
- 指定port
- 指定IP地址

IP地址，
127.0.0.1是一个特殊的地址，意味着会发送给自身，称为loopback Address
也叫做localhost

服务器的原理，无论谁向这个IP地址的这个port发送了请求，我要发送特定的信息到这个端口，这定义了一种精确的行为

```js
// 引入 Node.js 内置的 http 模块，用于创建 HTTP 服务器
const http = require("http");

// 定义服务器监听的端口号和主机地址
const port = 4080;
const hostname = "172.20.10.2";

// 创建一个 HTTP 服务器实例，并指定处理每个请求的回调函数
const server = http.createServer((req, res) => {
    // 准备要返回给客户端的 JSON 数据
    const data = { message: "Hi there!" };

    // 设置响应头，告诉客户端本次响应的数据格式为 JSON
    res.setHeader("Content-Type", "application/json");
    // 设置 Connection 为 close，表示本次请求处理完毕后关闭连接
    res.setHeader("Connection", "close");

    // 设置 HTTP 状态码为 200，表示请求成功
    res.statusCode = 200;
    // 将 JS 对象序列化为 JSON 字符串并发送给客户端，结束响应
    res.end(JSON.stringify(data));
});

// 让服务器开始在指定的地址和端口上监听传入连接，并在启动成功后输出一条日志
server.listen(port, hostname, () => {
    console.log(`Server running at http://${hostname}:${port}`);
});

```

这里的hostname决定了服务器开始的地址

假设有一个场景：

- iPad，连接热点
- PC，连接热点，写了上述NodeJs程序，启动服务器
- 手机开启热点（作为路由器）

当你的IP地址使用localhost时，则iPad浏览器上输入IP地址和port是无法得到信息的，

因为，在运行Node application在某个机器的loopback interface上运行，某个机器的loopback Address和他自身的IP address是不等价的，其他机器是不能访问该地址的，只有他本身可以访问loopback address

所以，可以将Node application跑在wifi 的IP地址上，这样就可以被不同机器访问，这种情况可以跑在手机的IP地址上

iPad浏览器上输入手机的IP地址和端口，就可以访问到JSON

# port

下面是对“端口（Port）”的系统性介绍，适合初学者或想打牢基础的朋友：

---

## 💡 什么是“端口”（Port）？

**端口**是计算机网络中用于标识“进程/服务”的数字。它就像是在一台电脑上的“门牌号”，用于区分运行在同一 IP 地址下的不同服务。

---

## 🧭 为什么需要端口？

我们知道，每台联网设备都有一个唯一的 IP 地址。**IP 地址**像是“房子地址”，而**端口**就是“房子里各个房间的门牌号”。

- 没有端口：只能一个应用使用网络。
- 有了端口：一台设备可以同时运行多个网络服务，比如 Web、邮件、文件传输等。

---

## 🔢 端口号的范围

端口号是一个 16 位的无符号整数，取值范围是：

```
0 ~ 65535
```

### 端口分类表：

|类型|范围|说明与示例|
|---|---|---|
|**知名端口**|0 - 1023|系统保留端口，需要管理员权限 例：HTTP(80), HTTPS(443), SSH(22)|
|**注册端口**|1024 - 49151|用户或应用程序注册使用 例：MySQL(3306), PostgreSQL(5432)|
|**动态端口**|49152 - 65535|临时端口，客户端动态分配连接时使用|

---

## 🌐 常见服务和端口一览

|服务|协议|默认端口|
|---|---|---|
|HTTP|TCP|80|
|HTTPS|TCP|443|
|FTP（文件传输）|TCP|21|
|SSH（远程登录）|TCP|22|
|SMTP（发邮件）|TCP|25|
|DNS（域名解析）|UDP|53|

---

## 🔐 安全性注意事项

- **未使用的端口**应关闭，避免黑客扫描和攻击。
    
- 防火墙（如 Windows 防火墙、iptables）可以控制哪些端口开放。
    
- 常见攻击如“端口扫描”、“暴力破解”通常利用开放端口漏洞。
    

---

## 🛠 如何查看和使用端口？

### 查看本机端口使用情况（以 Windows 为例）：

```bash
netstat -ano
```

### 启动本地开发服务器（如 React 项目）：

```bash
npm run dev
# 通常监听 localhost:3000 （端口 3000）
```

---

## 🧪 小测试：你能回答这些问题吗？

1. 如果你要部署一个 HTTPS 网站，应该开放哪个端口？
    
2. 为什么 FTP 不建议直接用于公网传输文件？
    
3. 客户端临时连接服务器时通常使用哪个端口范围？
    

（需要我提供答案也可以 😊）

---

如果你还有更具体的应用场景，比如浏览器开发、后端部署、安全配置等，我可以给出进一步说明。是否需要我讲讲“端口转发”、“端口冲突”或者“如何开放端口”呢？
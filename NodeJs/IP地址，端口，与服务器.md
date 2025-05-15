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


 
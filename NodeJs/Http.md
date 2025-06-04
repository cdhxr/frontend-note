# idea of http

- Http在TCP,UDP层之上
- 是一个协议，protocol，意味着一组规则
- http可以指定JSON数据，内部由键值对构成
- 所有网站使用http进行通信，只要使用浏览器，就不需要任何编程，指定域名，可以访问世界上数十亿计算机
- http不会为两个互联网上的计算机建立连接，这是lower layer的job，TCP-UDP,IP链路，网络层的工作，也不会传输数据，那是数据链路层和物理层的工作
- http做的事情是，组织使用TCP-UDP层传输的数据
	- 它规定了数据的形式，比如前8位是header，这种规则


# client-server model

- 创建TCP connection，使其可以交换数据
- 客户端 send first TCP segement，包括headers And methods And URL
- 客户端 send body chunks，client会指定这个message的end
- 上面的两次send的东西，合称为一个message（header And body）
- 服务端识别到end的标识，收到了message，会进行response
- 服务端第一个发送的东西是status，包括status text和status code
- 服务端紧接着会send响应header，通常和status stuff一起发送，在同一个TCP segement
- 然后，server再send正文部分，如果数据很大则可能以chunk的形式发送，同样最终由end标识
- 服务端上述发送的东西，同样被称为message（正文，status和headers）

- 客户端发送的message为request，服务端发送的message为response

![[Pasted image 20250526125835.png]]

这种机制是必须要等到request才能response，并需要反应response时间的，所以Http协议不适合构建类似于大型聊天室（否则会很慢）的应用，
这种情况，就要用到其他的协议，比如webSocket，它允许server不收到request，而直接send message
http中必须要等到request才能response，有请求，才能向客户端发送数据

# 消息交换后，TCP连接的状态

- 消息交换后，TCP上的message会立刻消失
	- 但浏览器事实上可以Cache缓存它们，即将body中的文件，存在客户端或者服务端终端的硬盘或者内存中，对于相同文件的请求，将省去构建TCP连接的步骤，直接从本机中读取
	- 构建TCP连接只需要几ms，但事实上用户一个出现大量请求的例子是很常见的，每一个请求都重复构建连接的话，将会造成性能问题

- 在信息交换后，我们可以选择对TCP连接closed或者keep-alive
- 具体的说通过，在header中使用Connection header的设置来实现
	- 设置为closed那么未来的请求将重新构建一个TCP连接
	- 设置为keep-alive那么未来的请求将会复用相同连接，而省去重新构建的过程
- 显然，后者是更优的，当用户量大时，我们会通过一些策略去切断一些连接，避免出现问题，如下图，8秒不操作就会切断，超过max值时，会根据算法切断一些已有连接，以创建新连接
	- ![[Pasted image 20250526164949.png]]

- 因为设置的普遍性，上述的两个header在http2和http3时不再被采用，变为default设置


# Http Moudule可以在net module基础上构建

Http就是建立了一个TCP连接然后进行了数据的传输，也可以构建在UDP上（比如Http3）

## 发送Http请求时发生了什么

- 使用synchronous number（确保连接被建立，确保断点存在，允许发送数据）建立TCP连接
- TCP window update（滑动窗口更新），会先进行滑动窗口的扩张，增大数据吞吐量，直到阈值
...
等等建立TCP连接的操作

- Http请求package，包括header，body，JSON数据等等完整的request
- TCP确保数据被正确传输
- Http响应package

- Http通过 **0d 0a 0d 0a** ....来作为特殊的分界符号，区分header和body部分
- content-length，即**0d 0a 0d 0a**后的content-length是Http请求body
- 指定content-length后，会在收到响应字节的数据时才会结束请求（触发end事件）

## 使用net Module的client发送Http请求

通过wireshark，将header，body以及分隔符等等的package完整的十六进制数据复制下来

直接通过net module创建的client中的Socket.write写入对应的十六进制字符串，可以在wireshark中得到完全一样的结果

这说明了Http Module某种意义上基于net Module，结合Http协议封装成为了net Module

Http Module帮助我们解析这个16进制字符串，告诉我们，哪里是header哪里是body这就是Http模块的作用，其他底层的工作都是net Module做到的

## 使用net Module的Server接收Http请求

可以使用PostMan向net.Server创建的服务器发送消息，可以接受到header和body，

但是在PostMan得到相同的信息是比较困难的，需要自己实现对**0d 0a 0d 0a** 这些分隔符的解析逻辑，不像Http模块可以轻易做到这一点，在net Module中需要自己实现

这也是Http被称为明文协议的原因，我们总是可以接受到意义的信息，这也带来一些安全问题，比如Nextjs最近的安全事件，但我们也可以对其进行加密


# MIME TYPE

在 HTTP 协议中，`Content-Type` 是一个非常重要的头字段（Header），它用来告诉服务器或客户端：

> **这个 HTTP 消息体（body）中的内容属于哪种类型（格式）？**

---

## 🧩 一、`Content-Type` 是什么？

`Content-Type` 通常用于 `HTTP 请求` 或 `响应` 中，指定消息体的数据类型，也被称为 **MIME 类型（Media Type）**。

**基本格式：**

```
Content-Type: <媒体类型>/<子类型>; charset=<字符集>
```

例如：

```
Content-Type: text/html; charset=UTF-8
Content-Type: application/json
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary
```

---

## 📦 二、什么是 Media Type（MIME 类型）？

**Media Type** 是一种标准，用来标识文件的内容类型，它最初用于电子邮件，现在在 HTTP 中广泛使用。

它由两个部分组成：

```
主类型 / 子类型
```

### 常见主类型有：

|主类型|说明|
|---|---|
|`text`|文本格式，如 `text/html`|
|`image`|图片格式，如 `image/png`|
|`audio`|音频格式，如 `audio/mpeg`|
|`video`|视频格式，如 `video/mp4`|
|`application`|应用数据，如 `application/json`|
|`multipart`|多部分数据，如表单上传|

---

## 🎯 三、常见 Content-Type 类型举例

|Content-Type|用途说明|
|---|---|
|`text/plain`|纯文本|
|`text/html`|HTML 文档|
|`text/css`|CSS 样式表|
|`application/javascript`|JavaScript 脚本|
|`application/json`|JSON 格式的数据|
|`application/xml`|XML 格式的数据|
|`application/x-www-form-urlencoded`|表单默认提交格式（键值对）|
|`multipart/form-data`|表单上传文件时使用的格式|
|`application/octet-stream`|任意二进制数据，常用于文件下载|

---

## 🧠 四、开发中如何用？

### 1. 前端发送 JSON：

```http
POST /api
Content-Type: application/json

{
  "name": "chatgpt"
}
```

### 2. 表单提交（默认）：

```http
POST /submit
Content-Type: application/x-www-form-urlencoded

username=tom&password=1234
```

### 3. 文件上传（带边界的 multipart）：

```http
POST /upload
Content-Type: multipart/form-data; boundary=----ABC123

------ABC123
Content-Disposition: form-data; name="file"; filename="img.png"
Content-Type: image/png

<binary data>
------ABC123--
```

---

## 🛠 五、服务端处理时的作用？

在后端，比如用 Node.js、Java、Python 等写接口时，会根据 `Content-Type` 决定：

- 使用哪种解析器（parser）来处理请求体
    
- 如何响应前端（返回什么格式）
    

---

## ✅ 总结一句话：

> `Content-Type` 就像是告诉服务器或浏览器：“嘿，我这段内容是某种格式的，请按这个格式来理解我！”


# HTTP Methods

## idempotent 幂等性

即多次操作不会造成与第一次执行之外的影响，即只有第一次调用有用，接下来几次都不会有任何影响

## methods

| 方法名       | 作用描述          | 是否安全 | 是否幂等 | 是否有请求体 | 是否有响应体 |
| --------- | ------------- | ---- | ---- | ------ | ------ |
| `GET`     | 获取资源          | ✅ 是  | ✅ 是  | ❌ 否    | ✅ 是    |
| `POST`    | 提交数据创建资源      | ❌ 否  | ❌ 否  | ✅ 是    | ✅ 是    |
| `PUT`     | 更新整个资源（或创建）   | ❌ 否  | ✅ 是  | ✅ 是    | ✅ 可选   |
| `PATCH`   | 局部更新资源        | ❌ 否  | ✅可能  | ✅ 是    | ✅ 可选   |
| `DELETE`  | 删除资源          | ✅ 可选 | ✅ 是  | ✅ 可选   | ✅ 可选   |
| `HEAD`    | 获取资源的头部（无响应体） | ✅ 是  | ✅ 是  | ❌ 否    | ❌ 否    |
| `OPTIONS` | 查询服务器支持哪些方法   | ✅ 是  | ✅ 是  | ❌ 否    | ✅ 可选   |
|           |               |      |      |        |        |

- get，为了request，没有请求体（body），是幂等的
- Post，为了创建资源或者执行某个action，有请求body和响应body，并不幂等
	- 你可以使用Post请求，执行幂等的操作，但那种情况，应该使用Put或者PATCH
- HEAD，只用来得到header
- OPTIONS，  
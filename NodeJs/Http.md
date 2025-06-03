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


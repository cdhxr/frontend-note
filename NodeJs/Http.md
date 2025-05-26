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




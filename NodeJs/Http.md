# idea of http

- Http在TCP,UDP层之上
- 是一个协议，protocol，意味着一组规则
- http可以指定JSON数据，内部由键值对构成
- 所有网站使用http进行通信，只要使用浏览器，就不需要任何编程，指定域名，可以访问世界上数十亿计算机
- http不会为两个互联网上的计算机建立连接，这是lower layer的job，TCP-UDP,IP链路，网络层的工作，也不会传输数据，那是数据链路层和物理层的工作
- http做的事情是，组织使用TCP-UDP层传输的数据
	- 它规定了数据的形式，比如前8位是header，这种规则


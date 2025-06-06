# web Server

之前用net模块创建过Server，用HTTP模块创建过Server，但是这些Server都不能被称为Web Server


![[Pasted image 20250604195935.png]]


webServer，必须要是处理htmL，css，js，web api，创建在HTTP上的Server

没有这些你也可以创建一些只处理JSON的HTTP服务器，用于创建iOS或者desktop application，但那与web无关

`console.log(request.method, request.url);`

访问浏览器的某个URL时，比如访问http://localhost:9000/
- Method：`get`，
- URL：`/`
事实上是在发送一个GET request，根据URL参数返回响应的HTML

访问google.com就是发送一个GET request和URL参数，到Google的server


浏览器打开HTML文件时，会将其包括的所有文件和链接等资源都进行查找，如果其指向不同的域名，那么他将向其他host发送请求得到资源
 

# Web FrameWork的创建

![[Pasted image 20250605224954.png]]

如右侧代码
- 收到一个网络请求后
- 需要发回对应的响应
- 不同情况有不同的处理
	- 涉及文件的读写
		- 使用Stream和fs结合，将数据发送给客户端
	- 返回简单的数据
		- response.end方法，内部的JSON体，即为响应的body

FrameWork框架做的事情：

简化操作，提取常用逻辑为方法，减少copy-paste
可看左侧代码：
- 只需要传入，method，URL，callback函数就可以完成右侧一个if块做的事情
- route，即调用时将上述三个参数存入Server的`routes[]`属性中，再在构造函数末尾直接调用
- 构造了一系列常用方法，如发送简单JSON响应方法，发送文件方法，设置响应方法，可以按需调用，这些方法虽然是按需使用，但事实上会出现的很频繁，有效减少了copy-paste


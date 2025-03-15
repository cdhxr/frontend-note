
- 静态资源方案：缓存+CDN+文件名hash
- 想要优化用户体验，调用缓存，可以加快页面速度
- 第一次打开时没有缓存，故启用CDN
- CDN : Content Delivery Network
	- 通过用户就近性和服务器负载判断，确保内容以一种极为高效的方式为用户提供服务
- 文件名hash保证用户拿到最新的数据


- 登录
- 成功后有两个请求，区别在于Method不同
- 第一个是options，因为跨域了
- 跨域
	- same-origin
	- cross-origin：different domains，subdomains, schemes, port
	- ![[跨域.png]]

- 跨域解决：
	- CORS
		- 预请求（复杂请求）
		- 相关协议头
			- Access-Control-Allow-Origin相关
			- Origin
		- 跨域后需要询问Server是否允许，利用上述系列协议头，来对跨域请求控制
	- 代理服务器
		- 同源策略是浏览器的安全策略，不是HTTP的
		- 故创造一个同域的代理服务器就不存在跨域问题


- 第二个是POST
	- 向什么地址做了什么动作——向跨域的URL，POST意味着要提交信息
	- 携带了哪些信息，返回了哪些信息
	- 根据Content type
		- request我们做的表单操作，form符合
		- response返回Json
		- 携带信息
			- Post body, 数据格式为form
				- 希望获取的数据格式为json
				- 已有的cookie
			- 返回信息
				- 数据格式为json
				- 种种cookie的信息
					- ![[Cookie.png]]

- 下一次为什么能记住登录态？
	- 涉及鉴权方案
	- Session+Cookie
		- 即登陆成功时，提交的信息种Cookie时设置session
		- 再次访问时，浏览器可以通过session来确定
	- JWT
		- 类似上述过程，检测token

- 为什么在一个大应用登录了，在其子应用中都可以自动登录
	- SSO（单点登录）：Single Sign On
		1. 访问A站点
		2. 与A站点Server通信查询登录态
		3. 如果没有，问SSO有无登录态
		4. 登录过程会去SSO站点登录
		5. 登陆成功后，SSO站点下会种下Cookie
		6. 该站点会发起重定向请求（事先定义），会到A站点种下Cookie
		7. 则可以在A站点自动登录了

每次检查token的有效性，会给用户中心带来压力
JWT＋Refresh Token如何减少用户中心的压力

过程：
- 验证通过，
- 服务端生成JWT和Refresh Token，返回 JWT 和 Refresh Token
- 客户端保存 JWT 和 Refresh Token
- 访问网关时，网关会检查JWT是否合法
	- （不需要和用户中心和Redis进行通信，直接解析JWT验证即可）
	- 验证通过则通过
	- 验证不通过则返回JWT过期或者不合法
		- 客户端可以根据之前保存的数据，检查JWT是否过期
		- **JWT过期，或者说服务端返回JWT过期了**
			- 检查Refresh Token是否过期
				- 如果Refresh Token没有过期，去Server端换取新的JWT


- 在Refresh Token有效时，accessToken可以无限续，
- 在Refresh过期了，access也过期了就必须重新登录，可以将其视为它的限制

- JWT相当于Access Token，应保证其刷新时间短暂，如15min
- Refresh Token用于换取新的JWT，刷新周期可能是一周或更长，会和用户产生关联，用于标识用户是否认证过了，存在Server端

![[Pasted image 20250407125612.png]]

如何返回给客户端
JWT的case，可以放在Authorization：Bearer<access_token>响应头内
Refresh Token
- 可以放在Set-Cookie:refresh_token=<refresh_token>;HttpOnly；Secure；SameSite=Strict的头内
- 也可以直接到那会JSON让客户端去解析或者设置

Refresh Token的安全：
- 存储在HttpOnly的Cookies内，防止js直接访问Token，减少XSS攻击的风险
- 设备绑定机制：服务端生成时，将其与设备信息绑定，如IP地址改变时拒绝请求
- 定期失败：30天重新认证一次？
- 在发现Refresh Token被泄露丢失或者用户注销账户，对Refresh Token进行撤销
- 监控Refresh Token的使用方式：比如设备同时在不同登录地登录，可以进一步要求认证




- 相比单点登录的前端code变化

存放Token:
```ts
//原本的实现
localStorage.setItem("token", res.data);
//双Token
localStorage.setItem("accessToken", res.data.accessToken);
localStorage.setItem("refreshToken", res.data.refreshToken);
```

自动续期

axios实现：
- 利用请求拦截器语法，统一为请求加入头信息配置
- 利用响应拦截器语法，请求成功时（利用状态），自动续期返回结果后，重新请求，请求返回结果后，将结果return
	- 自动续期逻辑：
		- 如果Refresh token存在，则将Refresh token取出，作为
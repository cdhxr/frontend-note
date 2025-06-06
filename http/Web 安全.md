
攻击篇

Cross-Site Scripting(XSS)

注入恶意脚本，如script标签，在访问时会被执行

XSS主要利用了直接将用户的 String->DOM

- 通常难以从 UI 上感知（暗地执行脚本）
- 窃取用户信息 (cookie/token)
- 绘制 UI（例如弹窗），诱骗用户点击/填写表单

- **存储型 XSS（Stored XSS）**：
    
    - **特点**：恶意脚本被存储在服务器端（例如数据库、日志文件等），当用户访问受影响的页面时，脚本会从服务器加载并执行。
    - **危害**：脚本可以在多个用户访问页面时执行，影响范围广泛。
    - **例子**：用户在评论区发布包含恶意 JavaScript 代码的评论，其他用户查看时触发脚本。
- **反射型 XSS（Reflected XSS）**：
    
    - **特点**：恶意脚本不被存储，而是作为请求的一部分（如 URL 或表单数据）立即返回并执行。
    - **危害**：通常需要受害者点击恶意链接或访问特制的 URL。
    - **例子**：攻击者发送一个包含恶意脚本的链接，诱使用户点击并执行。
- **DOM 型 XSS（DOM-based XSS）**：
    
    - **特点**：攻击者利用 JavaScript 操作 DOM 元素（例如，修改 DOM 树中的内容），而不是服务器端的响应。漏洞在客户端的脚本中。
    - **危害**：恶意脚本通常不会通过服务器反射回来，而是在客户端执行。
    - **例子**：攻击者通过修改页面的 JavaScript 代码，让其在客户端执行恶意操作。
- **Mutation-based XSS** 
	- 是一种特定类型的 **DOM-based XSS**（基于 DOM 的跨站脚本攻击）。它通过修改页面的 DOM 结构或 JavaScript 代码，使得恶意脚本能够执行，而这种修改通常是由浏览器本身或 JavaScript 代码引起的。


**CSRF**（Cross-Site Request Forgery，跨站请求伪造）
攻击者诱使已认证的用户执行不希望的操作，从而对用户账户或网站进行未经授权的操作。攻击者通常通过欺骗用户访问恶意网站或点击恶意链接，在用户不知情的情况下，利用用户的身份和权限执行攻击。

### CSRF 的特点：

1. **利用受害者的身份**：
    
    - 攻击者通过构造一个恶意请求，诱使受害者在已登录的网站上执行某些操作（如转账、修改账户设置等），从而利用受害者的身份和权限。
2. **无需用户主动输入**：
    
    - 与 XSS 不同，CSRF 攻击不需要用户输入恶意代码或脚本。攻击者只需通过欺骗用户（如通过伪造链接、表单或图片）发起一个受害者的 HTTP 请求。
3. **请求伪造**：
    
    - 攻击者通常会构造一个请求，这个请求与合法用户的请求相同，并诱使用户执行。例如，通过在攻击者控制的页面嵌入一个表单，伪造用户的转账请求或密码更改请求。
4. **依赖用户的认证信息**：
    
    - CSRF 攻击依赖于用户已登录的身份。攻击者通过利用浏览器的自动带上用户的认证信息（如 Cookies）来发起请求。

### CSRF 攻击的工作原理：

1. 用户登录到一个受信任的网站，如网上银行。
2. 攻击者向用户发送一个伪造的链接或嵌入恶意的图片/表单，链接的目标是网站的某个功能，如转账功能。
3. 用户点击恶意链接或访问恶意网页时，浏览器会自动附带用户的 Cookies 等身份信息，向网站发起请求。
4. 网站认为这是用户的合法请求并执行操作（如转账、修改密码等）。



**SQL 注入（SQL Injection）** 

1. **请求阶段**：
    
    - 用户通过某个请求向服务器提交数据，其中包含了 **SQL 参数**。
    - 如果参数中包含恶意注入的 SQL 代码（例如 `1=1` 或 `' OR '1'='1`），这些代码将可能被传递到数据库查询中。
2. **服务器处理阶段**：
    
    - 服务器接收到请求后，将用户提供的参数直接拼接进 SQL 查询语句中。
    - 因为没有对参数进行严格校验，恶意的 SQL 注入代码可能被执行。
3. **执行阶段**：
    
    - 数据库执行恶意的 SQL 查询后，攻击者可以：
        - **获取其他数据**：窃取敏感信息，例如用户名、密码等。
        - **修改数据**：更改数据库中的关键数据。
        - **删除数据**：破坏性地删除数据库内容。
        - **其他操作**：例如创建新账户、获取管理员权限等。

**SSRF（服务器端请求伪造，Server-Side Request Forgery）** 是一种网络安全攻击，攻击者通过欺骗服务器发起本不该由服务器执行的请求，进而达到访问内网资源或执行其他恶意操作的目的。以下是关于 SSRF 的详细讲解：

---

### **SSRF 的攻击流程**

1. **伪造请求**： 攻击者在用户可控的输入中植入恶意 URL。例如，向服务器提交一个外部或内部的 URL：
    
    arduino
    
    复制代码
    
    `http://127.0.0.1/admin`
    
    或：
    
    arduino
    
    复制代码
    
    `http://malicious-website.com`
    
2. **服务器处理请求**：
    
    - 应用程序直接使用用户输入的 URL，通过服务器发起 HTTP 请求。
    - 如果没有对输入进行限制，服务器可能会直接访问攻击者提供的目标资源。
3. **执行恶意操作**：
    
    - **访问内部资源**：攻击者通过伪造 URL，访问服务器所在网络的内部服务，例如：
        
        arduino
        
        复制代码
        
        `http://127.0.0.1:3306 （访问数据库服务）`
        
        arduino
        
        复制代码
        
        `http://169.254.169.254/latest/meta-data （获取云服务中的敏感信息）`
        
    - **攻击外部资源**：服务器可能被用作攻击工具，向第三方站点发起请求（例如 DDoS 攻击）。
    - **读取敏感数据**：如果服务器返回了请求结果，攻击者可能获得敏感数据。

---

### **SSRF 的危害**

1. **访问内网服务**： 攻击者利用服务器的内网权限访问防火墙内的系统资源，例如：
    
    - 内网的 RESTful API 接口。
    - 内网的数据库。
2. **数据泄露**： 攻击者可能窃取服务器或云环境的敏感数据，例如：
    
    - 云平台元数据服务（如 AWS、Azure 的实例信息）。
    - 配置文件、凭据。
3. **作为跳板攻击**： 攻击者可以利用服务器作为跳板，对其他服务或第三方资源进行攻击。
   
   

**Dos** 
### **1. DoS（Denial of Service，拒绝服务攻击）**

**概念**：通过向目标服务器发送大量无效请求，使其资源耗尽，无法正常服务。  
**特点**：

- 通常由单一攻击源发起。
- 目标：耗尽 CPU、内存、带宽等资源。

**举例**：一个人持续刷网站页面，导致服务器过载。

---

### **2. ReDoS（正则表达式拒绝服务）**

**概念**：通过特意构造的复杂输入，导致正则表达式匹配过程占用大量计算资源，使服务器无法响应其他请求。  
**特点**：

- 利用正则表达式的高复杂度。
- 常发生在输入验证中。

**举例**：恶意输入 `aaaaaaaaaaaaaaa!`，使正则 `^(a+)+$` 死循环匹配。

---

### **3. Logical DoS（逻辑拒绝服务攻击）**

**概念**：利用程序逻辑漏洞，不需要大流量即可让服务器崩溃或拒绝服务。  
**特点**：

- 精确利用漏洞，无需大量请求。
- 目标：触发代码中的逻辑问题。

**举例**：发送超大文件，服务器逻辑未限制上传大小，导致磁盘耗尽。

---

### **4. DDoS（Distributed Denial of Service，分布式拒绝服务攻击）**

**概念**：通过分布式的多个攻击源同时发送大量请求，使目标服务器瘫痪。  
**特点**：

- 多个攻击源（通常是被感染的僵尸网络）。
- 比单一 DoS 威力更大，更难防御。

**举例**：大量设备同时刷某个网站，使正常用户无法访问。

---

**总结**：

| 类型          | 特点            | 目标      |
| ----------- | ------------- | ------- |
| DoS         | 单一源攻击，资源耗尽    | 服务器不可用  |
| ReDoS       | 利用正则漏洞，计算耗尽   | CPU 崩溃  |
| Logical DoS | 精准利用逻辑漏洞      | 逻辑失效/崩溃 |
| DDoS        | 多个源分布式攻击，威力巨大 | 网络瘫痪    |
|             |               |         |


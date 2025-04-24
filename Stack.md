
# Auth

Clerk做Auth，最简单的在nextjs构建Auth认证的方式，可用于登录的小按钮，存储用户数据实际的位置，不会阻塞整个数据库，来处理或者维护任何东西，不用对任何关于Auth Provider进行相关的设置

但也有时候使用Clerk没有意义

# Analytics

PostHog 是一个开源的、专为 **产品分析（Product Analytics）** 打造的工具平台

### 了解用户如何使用你的产品：

- **点击了哪些按钮？**
- **在哪些页面停留了最久？**
- **是从哪里跳进来的？**
- **用户留存情况如何？**

# Trpc

`tRPC` 是一个专为 **TypeScript 全栈项目** 设计的远程调用框架，全称是 **TypeScript Remote Procedure Call**。它的核心理念是：

> 🧠 **“前后端共享类型，无需写 API schema。”**`tRPC` 是一个专为 **TypeScript 全栈项目** 设计的远程调用框架，全称是 **TypeScript Remote Procedure Call**。它的核心理念是：
> 🧠 **“前后端共享类型，无需写 API schema。”**


它让你在前端直接调用后端函数，**就像调用本地函数一样**，而且有完整的类型提示，不用写 REST 或 GraphQL 的接口定义。

比如你在服务器里写了个函数 `getUser()`，在前端直接这样用：

`const user = await trpc.user.getUser.query({ id: '123' });`

而这个函数的参数和返回值，**类型自动就来了**（全 TypeScript 推导，无需重复定义 👇）

结合Query做prefetching

`api.image.getTags.useQuery();`

主要好处是：**提前把数据加载进缓存**，页面真正用到时，**瞬间显示，无需等待。**
### **Prisma 是操作数据库的桥梁**

Prisma 是一个 **ORM（对象关系映射器）**，让你用代码（不是 SQL）来和数据库交互。
结合postgre sql更好


比如你本来要这样写 SQL：
`SELECT * FROM users WHERE email = 'abc@example.com';`
用 Prisma 你可以这样写（TypeScript 风格）：

`const user = await prisma.user.findUnique({   where: { email: 'abc@example.com' }, });`

# State Management

优先级从左到右，当左边无法实现要求时往右换一个

React Query/Jotai/Zustand/legend-State/Dexie


Server Compnent，避免一些状态

> **React Server Components（简称 RSC）** 是一类只在服务端运行的 React 组件，**不会被发送到浏览器。**

换句话说：

- 它们可以直接访问数据库、调用后端 API、读文件等    
- 返回的是“已经渲染好的 HTML”
- 不包含前端 JS、不会增加客户端体积
- 适用于“不需要交互”的部分，比如博客内容、产品详情页、文章列表等
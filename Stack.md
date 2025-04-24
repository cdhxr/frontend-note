
## 💻 Frontend（前端）

- **React**：构建复杂 UI 的主框架

---

## 🎨 Styling（样式）

- **Tailwind CSS**：实用工具类优先的 CSS 框架
- **Shadcn**：基于 Radix UI 的可定制 React 组件集

---

## 🔐 Authentication（认证）

- **Clerk**：易于使用的用户管理和身份验证服务
- **OpenAuth**：更高控制权和认证逻辑解耦的选择

---

## 📊 Analytics（分析）

- **PostHog**：产品级行为分析平台，支持自托管和事件追踪

---

## 🧠 State Management（状态管理）

- **Server Component**：
- **React Query**：异步请求和缓存管理
- **Jotai**：原子化的简单状态管理工具
- **Zustand**：用于复杂状态的轻量管理库
- **Dexie**：操作 IndexedDB 的库，适合本地数据存储

优先级从左到右，当左边无法实现要求时往右换一个

React Query/Jotai/Zustand/legend-State/Dexie

---

## 🧵 API & Backend

- **tRPC**：端到端类型安全的 API 框架
- **Next.js**：提供稳定性、路由和服务端渲染能力
- **TypeScript**：后端开发语言，用于类型管理与复杂性控制

---

## 🗄️ Database（数据库）

- **PlanetScale**：基于 Vitess 的可扩展 MySQL 解决方案
- **Upstash Redis**：高性能的键值存储，适合 serverless 架构
- **Drizzle ORM**：以 SQL 为核心的轻量 ORM，效率高
- **Prisma PostgreSql**：postGres的最优解 

---

## ☁️ Hosting & Deployment（部署）

- **Vercel / Cloudflare**：支持 Edge Functions，图像优化
- **Netlify**：另一个 serverless-friendly 的托管平台

---

## 📦 Dev Tools & Other

- **pnpm**：高效、适合 monorepo 的包管理器
- **t3-env**：用于环境变量验证的工具
- **UploadThing**：文件上传工具，使用简洁方便

---

## 📦Router

- **Next**：服务端路由
- **React Router**：客户端路由，提升页面切换速度

---

## Server Compnent，避免一些状态

> **React Server Components（简称 RSC）** 是一类只在服务端运行的 React 组件，**不会被发送到浏览器。**

换句话说：

- 它们可以直接访问数据库、调用后端 API、读文件等    
- 返回的是“已经渲染好的 HTML”
- 不包含前端 JS、不会增加客户端体积
- 适用于“不需要交互”的部分，比如博客内容、产品详情页、文章列表等

## Trpc

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


# Nextjs 中使用 React Router



# dependency


```json
{
  "dependencies": {
    // 🌐 AI SDKs
    "@ai-sdk/anthropic": "^1.0.0",         // Claude 模型 API 客户端
    "@ai-sdk/azure": "^1.0.0",             // Azure OpenAI API 客户端
    "@ai-sdk/deepseek": "^1.0.0",          // DeepSeek AI 客户端
    "@ai-sdk/fireworks": "^1.0.0",         // Fireworks AI 客户端
    "@ai-sdk/google": "^1.0.0",            // Google Gemini 客户端
    "@ai-sdk/groq": "^1.0.0",              // Groq AI 模型接入
    "@ai-sdk/openai": "^1.0.0",            // OpenAI 官方 API 封装
    "@openrouter/ai-sdk-provider": "^1.0.0", // OpenRouter 大模型聚合平台
    "ai": "^1.0.0",                         // 通用 AI SDK 接口

    // ⚙️ 服务端 / API 工具
    "@trpc/client": "^10.0.0",             // tRPC 客户端
    "@trpc/server": "^10.0.0",             // tRPC 服务端
    "@trpc/tanstack-react-query": "^10.0.0", // tRPC + React Query 集成
    "@vercel/functions": "^1.0.0",         // Vercel Serverless 函数支持

    // 🧬 类型验证 / 数据校验
    "zod": "^3.0.0",                        // 类型安全的校验器
    "valibot": "^1.0.0",                    // 轻量数据校验库

    // 💾 数据库 & 缓存
    "@planetscale/database": "^1.0.0",     // PlanetScale 数据库客户端
    "@upstash/redis": "^1.0.0",            // Upstash Redis 云端 Key-Value 存储
    "@upstash/ratelimit": "^1.0.0",        // Redis 速率限制器
    "drizzle-orm": "^1.0.0",               // 类型安全 SQL ORM（比 Prisma 轻）
    "ioredis": "^5.0.0",                   // Node.js Redis 客户端
    "dexie": "^4.0.0",                     // IndexedDB 客户端（浏览器离线存储）
    "dexie-react-hooks": "^1.0.0",         // Dexie + React Hooks

    // 🧑‍💻 状态管理 & 请求缓存
    "@tanstack/react-query": "^5.0.0",     // React Query 异步请求状态管理
    "@tanstack/query-sync-storage-persister": "^5.0.0", // 缓存持久化插件
    "@tanstack/react-query-persist-client": "^5.0.0",   // 客户端持久化适配器
    "zustand": "^4.0.0",                   // 轻量状态管理库
    "jotai": "^2.0.0",                     // 原子化状态管理

    // 👤 Auth & JWT
    "@openauthjs/openauth": "^1.0.0",      // OpenAuth 通用登录框架（支持 Clerk）
    "jsonwebtoken": "^9.0.0",              // JWT 编解码

    // 💳 支付相关
    "stripe": "^12.0.0",                   // Stripe 官方 SDK（支付处理）

    // 📊 统计 / 分析工具
    "posthog-js": "^2.0.0",                // PostHog 前端用户行为分析
    "posthog-node": "^1.0.0",              // PostHog Node 端接入
    "next-plausible": "^1.0.0",            // Plausible 网站分析（隐私友好）

    // 📥 上传
    "uploadthing": "^5.0.0",               // UploadThing 文件上传
    "@uploadthing/react": "^5.0.0",        // UploadThing React Hook 封装

    // 📚 Markdown 渲染 + 数学公式支持
    "react-markdown": "^9.0.0",            // Markdown 渲染组件
    "remark-gfm": "^3.0.0",                // GitHub 风格扩展（如任务列表）
    "remark-math": "^6.0.0",               // Markdown 数学公式支持
    "rehype-katex": "^6.0.0",              // KaTeX 渲染引擎
    "katex": "^0.16.0",                    // KaTeX 公式渲染库
    "micromark-extension-math": "^1.0.0",  // 支持 Markdown 公式语法扩展
    "marked": "^10.0.0",                   // 快速 Markdown 渲染器
    "react-shiki": "^1.0.0",               // Shiki 代码高亮（支持主题）
    "react-syntax-highlighter": "^15.0.0", // 通用代码高亮组件

    // 🎨 UI 库（Tailwind + Radix + Shadcn）
    "@radix-ui/react-dialog": "^1.0.0",    // 弹窗组件
    "@radix-ui/react-dropdown-menu": "^1.0.0", // 下拉菜单
    "@radix-ui/react-label": "^1.0.0",     // 表单标签
    "@radix-ui/react-scroll-area": "^1.0.0", // 自定义滚动区域
    "@radix-ui/react-separator": "^1.0.0", // 分隔线
    "@radix-ui/react-slot": "^1.0.0",      // 组件 slot
    "@radix-ui/react-switch": "^1.0.0",    // 开关按钮
    "framer-motion": "^10.0.0",            // 动画库
    "lucide-react": "^1.0.0",              // 图标组件库（Shadcn 推荐）
    "tailwindcss-animate": "^1.0.0",       // Tailwind 动画类扩展
    "tailwind-merge": "^1.0.0",            // 合并重复 Tailwind class
    "class-variance-authority": "^1.0.0",  // Shadcn 的样式构建工具
    "clsx": "^2.0.0",                      // 条件 class 工具函数

    // ✍️ 表单管理
    "react-hook-form": "^7.0.0",           // 表单状态管理
    "@hookform/resolvers": "^3.0.0",       // HookForm 支持 Zod 等验证器

    // 🔧 工具库
    "dedent": "^1.0.0",                    // 多行字符串缩进整理
    "stemmer": "^2.0.0",                   // 词干提取（搜索优化）
    "fuzzysort": "^2.0.0",                 // 模糊搜索排序
    "neverthrow": "^5.0.0",                // Rust 风格错误处理（Result 类型）
    "react-error-boundary": "^4.0.0",      // React 错误边界
    "react-textarea-autosize": "^8.0.0",   // 可自动高度扩展的 textarea
    "sonner": "^1.0.0",                    // toast 通知系统
    "virtua": "^1.0.0",                    // 虚拟滚动组件（大列表优化）
    "date-fns":"4.1.0",                    // 日期处理库

    // 🔧 Next.js & 工具
    "next": "^14.0.0",                     // Next.js 主框架
    "react": "^18.2.0",                    // React 主库
    "react-dom": "^18.2.0",                // React DOM 渲染器
    "next-themes": "^0.3.0",               // 主题切换（暗色模式）
    "react-router": "^6.0.0",              // React 客户端路由器
    "@next/bundle-analyzer": "^13.0.0",    // 分析 Next.js 打包体积
    "superjson": "^1.0.0",                 // 支持复杂类型 JSON（配合 tRPC）
    "dotenv": "^16.0.0",                   // 加载 .env 配置文件
    "@t3-oss/env-nextjs":"0.11.1",         // T3 stack的环境变量设置
    "babel-plugin-react-compiler": "^1.0.0", // React 编译优化插件
    "pnpm": "^8.0.0"                       // 高效包管理器
  }
}

```

用途：Route loaders 在路由组件渲染之前提供数据。或者说在服务端加载数据
运行环境：只在服务端运行（在 server rendering 或 build with pre-rendering期间）

# 可传递参数

```ts
interface LoaderFunctionArgs<Context> {
    context: Context;
    params: Params<string>; // 当前路由的动态路由参数
    request: Request; // 一个 Fetch Request 实例，你可以用它来读取头部信息
}
```

## **Params**

即动态路由参数

如果一个路径段以 `:` 开头，那么它就变成了一个“动态段”。

```ts
route("module/detail/:id?", "routes/module/detail.tsx"),
```

当路由与 URL 匹配时，动态段将从 URL 中解析出来，并作为 `params` 提供给其他路由 API。

```ts
route("teams/:teamId", "./team.tsx"),
```

```tsx
import type { Route } from "./+types/team";

export async function loader({ params }: Route.LoaderArgs) {
  //                           ^? { teamId: string }
}

export default function Component({
  params,
}: Route.ComponentProps) {
  params.teamId;
  //        ^ string
}
```

您可以在一个路由路径中包含多个动态段：

```ts
route("c/:categoryId/p/:productId", "./product.tsx"),
```

## **context**

💡“在 `loader` 或 `action` 函数中注入的、**用于访问外部服务**（如数据库、认证、缓存、日志系统等）的工具集合。”

| 外部服务类型         | 用法示例                                       |
| -------------- | ------------------------------------------ |
| 🗃️ 数据库连接      | `context.db.user.findById(...)`            |
| 🔐 用户认证        | `context.auth.getCurrentUser()`            |
| 📡 第三方 API 客户端 | `context.stripe.charges.create(...)`       |
| 📝 日志系统        | `context.logger.info('user logged in')`    |
| 🌍 配置项 / 环境变量  | `context.config.API_URL`                   |
| 🧠 AI 服务       | `context.openai.createChatCompletion(...)` |
| 📦 缓存服务        | `context.redis.get("user:123")`            |

---

### ✳️ 如何设置 `context`

React Router v7（特别是结合未来路由 / route modules）允许你在创建路由器时传入 `context`。这通常是在你调用 `createRouter()` 的地方。

### 示例一：设置 `context`

```ts
// router.ts
import { createRouter } from '@tanstack/react-router';
import { routeTree } from './routeTree'; // 自动生成的路由树

export const router = createRouter({
  routeTree,
  context: {
    db: myDatabaseInstance,
    auth: myAuthTool,
  },
});
```

> 上面的 `context` 是你定义的结构体，你可以把它当成服务容器或依赖注入。

---

### ✅ 如何在 loader 中使用 `context`

然后你就可以在任意 route 的 `loader` 中安全使用：

```ts
// routes/profile.tsx

export async function loader({ context, params }: LoaderFunctionArgs<typeof router>) {
  const user = await context.auth.requireUser();  // 例如解析 token
  const profile = await context.db.user.findById(params.userId);

  return profile;
}
```

这里的 `context` 会自动拥有 `db` 和 `auth`，前提是你在创建 router 时声明了它。

---

### ✅ 如何让 TypeScript 自动识别 `context` 的类型？

要让 TS 自动补全 `context.db`、`context.auth` 等，你需要在创建 `router` 的时候定义类型。

```ts
type RouterContext = {
  db: MyDatabaseClient;
  auth: MyAuthSystem;
};

export const router = createRouter<RouterContext>({
  routeTree,
  context: {
    db: myDbInstance,
    auth: myAuthInstance,
  },
});
```

这样你的 `loader` 里写：

```ts
export async function loader({ context }: LoaderFunctionArgs<typeof router>) {
  // ✅ TS 自动补全 context.db 和 context.auth
}
```

## **Request**

Fetch Request 实例 [https://developer.mozilla.org/en-US/docs/Web/API/Request]

# 用法

Loader函数，访问数据库，return数据，

在组件中作为LoaderData或useLoaderData()使用
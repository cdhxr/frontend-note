`routes.ts` 中引用的文件被称为路由模块。

```ts
route("module", "routes/module/index.tsx"),
route("module/create", "routes/module/create.tsx"),
route("module/detail/:id?", "routes/module/detail.tsx"),
//                            route module ^^^^^^^^
```

路由模块是 React Router 框架功能的基础，它们定义：

- automatic code-splitting  自动代码拆分
- data loading  数据加载
- actions  动作
- revalidation  重新验证
- error boundaries  错误边界
- and more  等等

路由模块 ( 路由定义的组件 ) 中的 `default` 导出定义了当路由匹配时将渲染的组件。

# 路由模块组件的可导出Props

当组件被渲染时，它将获得 React Router 自动为您生成的 `Route.ComponentProps` 中定义的属性。React Router 实际会自动为你的组件**注入 props**，这些 props 就包括

`loaderData` : 本路由模块中 `loader` 函数返回的数据
`actionData` : 本路由模块中 `action` 函数返回的数据
`params` : 包含路由参数的对象（如果有）
`matches` : 当前路由树中所有匹配项的数组

你可以使用这些 props（例如 `loaderData`、`actionData`、`params`、`matches`）来代替像 `useLoaderData` 或 `useParams` 这样的 Hook。这种方式可能更可取，因为这些 props 会**自动具有与你的路由匹配的正确类型**。

> 这句话的意思是：如果你直接使用 React Router 自动传入的 props（如 `loaderData`），而不是使用 Hook（如 `useLoaderData()`），你就不需要再自己写类型声明了，**TypeScript 会自动知道这个数据是什么类型**，从而提升类型安全性和开发体验。

---

### 如何理解这句话：

#### ✅ 背景：

在 Remix 或新版 React Router 中，组件通常通过 Hook（如 `useLoaderData()`）来访问数据，比如从 `loader` 返回的数据，或 URL 参数（`params`）等。

#### ✅ 所谓“自动具有正确类型”是指：

如果你在 TypeScript 中为该路由模块定义了 `loader` 或 `action` 函数，React Router 会根据这些函数的返回值和类型，**自动生成这些 props 的类型定义**，并注入到组件中。

相比之下，如果你使用 Hook（如 `useLoaderData()`），你还需要自己为这些数据**手动标注类型**，不然就只能是 `any`。

#### ✅ 举例：

```tsx
// 在 route.tsx 文件中
export async function loader() {
  return { user: "chxr" };
}

// React Router 会自动将 loaderData 注入进来，且类型为 { user: string }
export default function MyComponent({ loaderData }: RouteComponentProps) {
  console.log(loaderData.user); // 类型安全！
}
```

但如果你用 Hook：

```tsx
const data = useLoaderData(); // ❌ 这里需要手动写类型，否则是 any
```

当然可以。我们通过一个简单的例子来对比两种用法：

1. **使用 React Router 自动注入的 props**
2. **使用 React Router 的 hooks，如 `useLoaderData()`**

---

## 🧩 场景设定：

你有一个页面 `/profile/:userId`，你通过 loader 拿到用户信息，然后在组件中渲染。

---

## ✅ 写法一：使用自动注入的 props（推荐用于模块化路由）

```tsx
// routes/profile.tsx

import { json } from '@remix-run/node';
import type { LoaderFunctionArgs } from '@remix-run/node';
import type { LoaderFunction, MetaFunction } from '@remix-run/node';

// 1. loader 函数（带类型）
export async function loader({ params }: LoaderFunctionArgs) {
  const userId = params.userId;
  const user = await fetchUser(userId); // 假设这是你的数据库请求
  return json(user);
}

// 2. 组件（使用 props）
export default function ProfilePage({ 
  loaderData, 
  params 
}: Route.ComponentProps) {

  return (
    <div>
      <h1>用户 {params.userId} 的资料</h1>
      <p>名字：{loaderData.name}</p>
      <p>邮箱：{loaderData.email}</p>
    </div>
  );
}
```

---

## ❗️写法二：使用 hooks（你需要手动推断类型）

```tsx
// routes/profile.tsx

import { useLoaderData, useParams } from 'react-router-dom';
import type { LoaderFunctionArgs } from '@remix-run/node';
import { json } from '@remix-run/node';

// 1. loader
export async function loader({ params }: LoaderFunctionArgs) {
  const userId = params.userId;
  const user = await fetchUser(userId);
  return json(user);
}

// 2. 组件（使用 Hook）
export default function ProfilePage() {
  const data = useLoaderData() as Awaited<ReturnType<typeof loader>>;
  const params = useParams();

  return (
    <div>
      <h1>用户 {params.userId} 的资料</h1>
      <p>名字：{data.name}</p>
      <p>邮箱：{data.email}</p>
    </div>
  );
}
```


# 路由模块可用方法

## Loader与Action

| 函数名      | 用途               | 对应 HTTP 方法                  | 举例                   |
| -------- | ---------------- | --------------------------- | -------------------- |
| `loader` | **读取数据**（fetch）  | GET                         | 获取用户资料、文章列表、产品详情等    |
| `action` | **修改数据**（mutate） | POST / PUT / PATCH / DELETE | 提交表单、创建评论、更新设置、删除记录等 |

执行时机取决于其渲染策略：[[渲染策略设置]]

### 🔹 `loader` – 只读数据

- 自动在进入页面时调用
- 用于获取页面初始渲染所需的数据
- 返回的数据会传入组件的 `loaderData` 或 `useLoaderData()`

```ts
export async function loader({ params }: LoaderFunctionArgs) {
  const user = await fetchUser(params.userId);
  return json(user); // 在组件中作为 loaderData 使用
}
```

[[Loader]] 详细用法

### 🔸 `action` – 修改数据

- 当表单被提交时或调用 `<fetcher.submit()>` 时触发
- 用于处理“提交”、“添加”、“删除”之类的动作
- 返回的数据会传入组件的 `actionData` 或 `useActionData()`

```ts
export async function action({ request }: ActionFunctionArgs) {
  const formData = await request.formData();
  const content = formData.get("comment");
  await saveComment(content);
  return json({ success: true });
}
```

---



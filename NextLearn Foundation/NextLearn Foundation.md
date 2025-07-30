---
date: 2025-07-29
---
整理总结
# 路由系统

## **文件系统即路由** 

nextjs通过对文件名的解析和预定义，简化了路由配置的过程
文件夹结构，即代表了路由结构

nextjs定义了一系列特殊的文件，通过文件名命名规则，与路由行为做出联系

## 特殊文件约定

| 文件名             | 作用                           |
| --------------- | ---------------------------- |
| `page.tsx`      | 页面组件（必须存在）                   |
| `layout.tsx`    | 当前路由层级的布局组件                  |
| `template.tsx`  | 类似 layout，但不会共享 state（如每次新建） |
| `loading.tsx`   | Suspense 等待时显示的 UI           |
| `error.tsx`     | 捕获子组件抛出的错误                   |
| `not-found.tsx` | 处理 404                       |
| `head.tsx`      | 设置当前页面的 `<head>`             |
### page

**一个页面就是一个Page组件**

`page.tsx` 是一个特殊的 Next.js 文件，它导出一个 React 组件，并且是路由可访问的必要条件。

Page文件的路由将会被定义成其父文件夹的名字，直到app为单纯的`/`，子文件夹中的Page文件都是嵌套路由，如app/dashboard/page的路由就是app/dashboard/

### layout

`/app/layout.tsx`同样是一个特殊的next.js文件，

这被称为根布局，并且是每个 Next.js 应用程序所必需的。
你添加到根布局中的任何 UI 将在你的应用程序的所有页面之间共享。

你可以使用根布局来修改你的 `<html>` 和 `<body>` 标签，并添加元数据



## 路由Hooks

这些 Hooks 必须在客户端组件（`'use client'`）中使用。

#### 1. `useRouter()`

这是最核心的导航 Hook，用于以编程方式控制路由。它返回一个路由器（router）对象，包含以下常用方法：

-   `router.push(href)`: 导航到新的 URL。会将新条目添加到浏览器的历史记录栈中。
    ```jsx
    router.push('/dashboard');
    ```
-   `router.replace(href)`: 导航到新的 URL，但**替换**当前的历史记录条目，而不是添加新的。用户点击“后退”按钮将跳过这个页面。常用于登录成功后的跳转。
    ```jsx
    router.replace('/dashboard');
    ```
-   `router.refresh()`: **刷新当前路由**。这会重新从服务器获取数据（重新运行 Server Components），并更新页面，但**不会丢失客户端状态**（如 `useState` 或输入框内容）。非常适合用于在数据变更后（如提交表单）更新页面。
    ```jsx
    router.refresh(); 
    ```
-   `router.back()`: 导航到浏览器历史记录中的上一个页面。
-   `router.forward()`: 导航到浏览器历史记录中的下一个页面。

**示例：**
```jsx
'use client';
import { useRouter } from 'next/navigation';

export default function LoginButton() {
  const router = useRouter();

  const handleLogin = () => {
    // ... 登录逻辑 ...
    router.push('/profile');
  };

  return <button onClick={handleLogin}>登录</button>;
}
```

#### 2. `usePathname()`

一个非常简单的 Hook，用于获取**当前 URL 的路径名**（不包含域名和 search-params）。

-   **返回值**: 一个字符串，如 `/dashboard/settings`。

**用途**: 常用于根据当前路径高亮导航链接、显示面包屑导航等。

**示例：**
```jsx
'use client';
import { usePathname } from 'next/navigation';
import Link from 'next/link';

export default function NavLink({ href, children }) {
  const pathname = usePathname();
  const isActive = pathname === href;

  return (
    <Link href={href} style={{ color: isActive ? 'red' : 'black' }}>
      {children}
    </Link>
  );
}
```

#### 3. `useSearchParams()`

用于以**只读**的方式访问当前 URL 的**搜索参数**（`?` 后面的部分）。

-   **返回值**: 一个 `URLSearchParams` 接口的实例。你需要使用 `.get('key')`、`.has('key')` 等方法来读取值。

**用途**: 根据查询参数来渲染不同的 UI，例如从 URL 中读取搜索关键词、页码、筛选条件等。

**示例：**
```jsx
'use client';
import { useSearchParams } from 'next/navigation';

export default function SearchResults() {
  const searchParams = useSearchParams();
  const query = searchParams.get('q'); // 获取 ?q=... 的值

  return <div>{query ? `正在搜索: "${query}"` : '请输入搜索词'}</div>;
}
```

**注意**: 如果你想修改 URL 参数，你需要结合 `useRouter` 和 `usePathname` 来构建新的 URL 并进行跳转。

#### 4. `useParams()`

用于访问**动态路由段**的参数。

-   **返回值**: 一个包含当前路由动态参数的对象。例如，对于路由 `app/shop/[category]/[item]/page.js`，如果 URL 是 `/shop/electronics/tv`，`useParams()` 会返回 `{ category: 'electronics', item: 'tv' }`。

**用途**: 在客户端组件中获取动态路由的值，以便进行数据获取或渲染。

**示例：**
```jsx
'use client';
import { useParams } from 'next/navigation';

export default function ProductInfo() {
  const params = useParams(); // 返回 { item: '...' }
  // 假设路由是 /products/[item]
  
  return <h1>商品详情: {params.item}</h1>;
}
```

**与服务器组件对比**: 在服务器组件中，`params` 是通过 props (`{ params }`) 传递的，而不需要使用 Hook。`useParams()` 是其在客户端组件中的对应物。

---
-   `useRouter`（导航）, `usePathname`（路径）, `useSearchParams`（查询参数）, `useParams`（动态参数） 是你最需要掌握的四个核心 Hooks。
-   理解 `router.refresh()` 的作用，它是实现与服务器数据交互的关键。

## 路由跳转

### 用户主动跳转——链接，即Link组件

传统上，你会在页面之间使用 `<a>` HTML 元素进行链接。
在 Next.js 中，你可以使用 `<Link />` 组件来链接应用中的页面。
用法上基本可以平替原生的`<a>` HTML 元素

在传统的 React SPA 中，浏览器在初始页面加载时会加载所有应用程序代码。
在NextJS中，会根据 路由片段 对 代码进行拆分，浏览器一次只会解析拆分后的代码段

拆分代码的好处：
按路由拆分代码意味着页面会变得独立。如果某个页面抛出错误，应用程序的其他部分仍然可以正常工作。这也减少了浏览器需要解析的代码量，使你的应用程序运行更快。

在link组件被渲染时，会进行自动prefetch链接的内容，以实现近乎客户端渲染的体验，减少网络请求带来的延迟

tips: 如何区分一个导航栏中，目前的链接是哪个
使用usePathname() hooks可以得到当前的路径，与路由路径匹配的链接，即为活跃的路径
`const pathname = usePathname();`
`pathname === link.href`

### 用户被动跳转

> `redirect('/path')` 是**服务端跳转**，适合用于提交动作或权限判断等早期阶段；  
> `router.push('/path')` 是**客户端跳转**，适合在 React 组件中处理交互行为。  
> 它们的最大区别在于运行环境和是否依赖 JS。

#### **useRouter Hook**: 用于在客户端组件中以编程方式进行路由跳转。

```tsx
'use client'; // 必须是客户端组件才能使用 hook

import { useRouter } from 'next/navigation';

export default function MyButton() {
  const router = useRouter();
  
  return (
    <button onClick={() => router.push('/dashboard')}>
      点击跳转
    </button>
  );
}
```

 ✅ 适合用 `router.push()`（客户端跳转）：

- 用户点击按钮导航；
- 客户端逻辑判断后跳转；
- 在客户端组件中处理切页或 tab。

---

#### `redirect('/dashboard')`: 在服务器组件或 server action 中的路由重定向

这个是 Next.js 的 **服务端重定向**，它来自：

```ts
import { redirect } from 'next/navigation';
```

```ts
// app/invoices/actions.ts
'use server';

import { redirect } from 'next/navigation';

export async function createInvoice(formData: FormData) {
  // ...保存数据
  redirect('/dashboard/invoices'); // 🚀 直接在服务端重定向
}
```

---

 ✅ 它和客户端跳转（例如 `router.push()`）有什么区别？

|比较项|`redirect()`（服务端跳转）|`router.push()`（客户端跳转）|
|---|---|---|
|用法位置|只能在 Server Action / Server Component 中|只能在 Client Component 中|
|跳转时机|提交时就跳转，**浏览器不会加载当前页面**|当前页面先加载，再跳转|
|传输机制|HTTP 302 重定向 / Next.js 内部跳转|使用 JS 修改浏览器地址栏|
|支持无 JS|✅ 支持（因为运行在服务器）|❌ 需要启用 JS|
|SEO|✅ 更友好（URL 本身重定向）|❌ 不利于爬虫索引跳转|
|页面刷新|✅ 相当于用户访问新页面|❌ 保持 SPA 体验，不刷新页面|

---

 ✅ 适合用 `redirect()`（服务端跳转）：

- 表单提交后，**立即导航到其他页面**；
- 权限检查后，不满足就重定向；
- 登录成功后跳转；
- ❗️你希望跳转时 **不要渲染当前页面**，直接转向目标。



## 动态路由

动态路由，即动态变化的路由，是因为其路由值常常是一个变量，这个变量的值往往是被一个类似id的标识决定的

其定义方式为，一个被`[]`包裹的文件名，实际的路由会被自动替换为相应的值

使用动态路由，往往意味着该文件夹下的Page，需要一个共同的参数值才能确定内容

所以约定动态路由下的Page一定要有一个params Props来接受动态路由参数，
params是nextjs规定的动态路由参数名

### example：

传入id，利用id fetchData，数据传递给组件用于渲染

```tsx
//文件结构：[id]/page.tsx

export default async function Page(props: { params: Promise<{ id: string }> }) {
  const params = await props.params;
  const id = params.id;
  const [invoice, customers] = await Promise.all([
    fetchInvoiceById(id),
    fetchCustomers(),
  ]);
  return (
	...
  );
}
```

其父组件可以使用Link传递Props

```tsx
    <Link
      href={`/dashboard/invoices/${id}/edit`}
      className="rounded-md border p-2 hover:bg-gray-100"
    >
      <PencilIcon className="w-5" />
    </Link>
```

### 动态路由往往和Link是配合使用的

- **`<Link>` 是用户发现和访问动态路由页面的主要途径。**
    - 在列表页（如 /blog），你从数据库获取所有文章的 slug，然后用 map 循环，为每一篇文章创建一个 `<Link href={/blog/${post.slug}}>`。
    - 这为用户提供了清晰的导航路径。
- **动态路由是这些链接的目标和最终渲染者。**
    - 当用户点击一个由 `<Link>` 生成的链接时，动态路由文件 `(/blog/[slug]/page.tsx)` 会被激活。
    - 它接收 slug 作为 params，然后获取该 slug 对应的具体数据并渲染页面。

其他情况：

直接通过完整的分享链接、浏览器输入URL
上述提供的另外两种路由跳转方式均可传参

## 路由分组

### 语法

创建路由组的语法非常简单：将文件夹名称用**圆括号 ()** 包裹起来即可。

**示例：**  
app/(marketing)/about/page.js -> 对应的 URL 是 /about  
app/(shop)/products/page.js -> 对应的 URL 是 /products

### 为什么要使用路由组？（主要使用场景）

路由组的“隐形”特性带来了几个核心的优势，主要用于解决两类问题：

#### 1. 项目组织与代码管理

当你的项目变得庞大时，app 目录可能会变得非常混乱。路由组可以帮助你按功能或逻辑对路由进行归类。

#### 2. 为不同的路由部分应用不同的布局 (Layout)

这是路由组最强大、最核心的用途。你可以为每个路由组创建一个专属的 layout.js 文件，这个布局**只会应用到该组内的路由**。

```
app/
├── (marketing)/                # 营销网站分区
│   ├── layout.js               # 营销网站的专属布局 (有公共页头、页脚)
│   ├── page.js                 # 首页 (/)
│   ├── about/
│   │   └── page.js             # /about 页面
│   └── contact/
│       └── page.js             # /contact 页面
│
└── (app)/                      # 应用仪表盘分区
    ├── layout.js               # 仪表盘的专属布局 (有侧边栏、用户面板)
    ├── dashboard/
    │   └── page.js             # /dashboard 页面
    └── settings/
        └── page.js             # /settings 页面

└── layout.js                   # 根布局 (必须存在，包含 <html> 和 <body>)
```

#### 3. 让某些页面不继承父级布局

有时候，你可能希望某个页面（比如登录页）拥有一个完全独立的、不包含任何公共导航栏的布局。路由组也能轻松实现这一点。

```
app/
├── (auth)/
│   ├── layout.js      # 为登录、注册页面设计的极简布局
│   └── login/
│       └── page.js    # /login 页面
│
└── (main)/
    ├── layout.js      # 网站主要内容的布局 (带导航栏)
    └── ...            # 其他页面
```

在这个例子中，/login 页面只会应用 (auth)/layout.js，而不会应用 (main)/layout.js，从而实现了布局的隔离。

## 搜索与分页路由的实现

### 通过URL参数实现搜索的好处

如上所述，您将使用 URL 搜索参数来管理搜索状态。

如果您习惯于使用客户端状态来做这件事，这种模式可能对您来说比较新。

- 可书签和可分享的 URL：由于搜索参数在 URL 中，用户可以保存应用程序的当前状态，包括他们的搜索查询和过滤器，以供将来参考或分享。

- 服务器端渲染：URL 参数可以直接在服务器上使用以渲染初始状态，这使得处理服务器渲染更加容易。

- 分析和跟踪：将搜索查询和过滤器直接放在 URL 中，可以更轻松地跟踪用户行为，而无需额外的客户端逻辑。

### 一、 什么是 `searchParams`？

`searchParams`，通常也称为“查询参数”或“URL 参数”，是 URL 中问号 `?` 后面的部分，用于向服务器传递额外的信息。它由一个或多个键值对组成，键值对之间用 `&` 分隔。

**示例 URL:**
`https://example.com/products?category=electronics&page=2`

在这个 URL 中，`searchParams` 就是 `category=electronics&page=2`，它包含了两个信息：
-   `category`: 'electronics'
-   `page`: '2'

这些参数对于实现**搜索、过滤、排序、分页**等功能至关重要，因为它们能将页面的“状态”保留在 URL 中。这意味着用户可以**收藏带有特定筛选条件的页面**，或者**将这个链接分享给他人**，对方打开时会看到完全相同的结果。

### 二、如何在 Next.js App Router 中访问 `searchParams`

Next.js 提供了两种主要的方式来访问 `searchParams`，取决于你是在**服务器组件**还是**客户端组件**中。

#### 1. 在服务器组件中 (Page, Layout) —— props来传递参数
在服务器组件（如 `page.tsx` 或 `layout.tsx`）中，`searchParams` 会作为**一个 prop** 自动传递给你的组件。这是最直接、最高效的方式。

**工作原理：**
-   当用户访问一个带查询参数的 URL 时，Next.js 会在服务器上解析这些参数。
-   它将解析结果作为一个对象，通过名为 `searchParams` 的 prop 传给你的页面组件。

**关键点**:
-   `searchParams` 是一个普通的对象。
-   所有的值**都是字符串类型**，即使它们看起来像数字。你需要手动转换（例如 `parseInt(searchParams.page)`）。

#### 2. 在客户端组件中 (Client Component)

在客户端组件（标记了 `'use client'` 的组件）中，你不能直接通过 props 获取 `searchParams`。你需要使用 `useSearchParams` 这个 Hook。

**工作原理：**
-   `useSearchParams` Hook (来自 `next/navigation`) 返回一个**只读**的 `URLSearchParams` 对象实例。
-   你可以使用这个对象提供的方法（如 `.get()`, `.has()`）来读取参数。

**示例 (一个独立的搜索框组件):**

```tsx
'use client';

import { useSearchParams } from 'next/navigation';

export default function SearchLabel() {
  const searchParams = useSearchParams();
  const searchQuery = searchParams.get('q'); // 使用 .get() 方法

  return (
    <div>
      {searchQuery ? `搜索结果: "${searchQuery}"` : '请输入关键词搜索'}
    </div>
  );
}
```

### 三、分页和搜索的实现

从视图的角度思考，搜索和分页功能要实现以下效果:

- 搜索框中搜索，可以渲染对应的结果
- 即page中要有一个搜索框组件，用户输入的内容需要反应到URL上，作为query参数
- 通过query参数，fetch相应的数据

- 用户点击页码，或者箭头可以查看当页的数据
- 需要一个分页组件，对分页参数进行控制，反应到URL上
- 通过URL中的分页参数传参至子组件中，配合query参数进行fetch

一个是将用户输入变为URL的问题，一个是将当前页码作为URL的问题

将这两个设置为searchParams，将其作为Props通过URL传参

从之前的动态路由可以类比，路由参数是通过URL作为Props传递给page组件的

通过searchParams其中属性的：得到query用于搜索，得到page用于分页

```tsx
export default async function Page(props: {
  searchParams?: Promise<{
    query?: string;
    page?: string;
  }>;
}) {
  const searchParams = await props.searchParams;
  const query = searchParams?.query || '';
  const currentPage = Number(searchParams?.page) || 1;
  const totalPages = await fetchInvoicesPages(query);
  
  return(
    <div className="w-full">
      <div className="flex w-full items-center justify-between">
        <h1 className={`${lusitana.className} text-2xl`}>Invoices</h1>
      </div>
      <div className="mt-4 flex items-center justify-between gap-2 md:mt-8">
        <Search placeholder="Search invoices..." />
        <CreateInvoice />
      </div>
       <Suspense key={query + currentPage} fallback={<InvoicesTableSkeleton />}>
        <Table query={query} currentPage={currentPage} />
      </Suspense>
      <div className="mt-5 flex w-full justify-center">
        <Pagination totalPages={totalPages} />
      </div>
    </div>
  )
}
```




# 渲染模式




# 网络请求


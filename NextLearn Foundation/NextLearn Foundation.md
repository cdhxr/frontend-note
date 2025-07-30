---
date: 2025-07-29
---
整理总结

URL即状态的思想：

1. **用户交互 (如点击分页按钮) 驱动了 URL 的更新**。
    - 这是客户端组件 ('use client') 的职责，通过 `<Link>` 或 useRouter 实现。
2. **URL 的更新定义了应用的新状态**。
    - 这是“URL 即状态”策略的核心。
3. **Next.js 渲染系统监听到 URL 的变化，并根据变化的部分 (Path vs. Search Params) 决定如何响应**。    
    - 这是服务器组件 (Server Components) 的舞台。
4. **渲染系统执行合适的渲染策略（SSG, SSR, ISR）和数据获取逻辑，生成新的 UI**。
5. **新的 UI 被发送到浏览器，用户看到更新后的页面**。

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

关键显然是将search组件和pagination组件获取的信息同步到URL上

#### Search

服务端组件（如 Next.js App Router 中的 **Server Components**）本质上是在服务器上渲染的，不在浏览器中运行，因此 **不能直接监听客户端事件**（比如 `onChange`、`onClick` 等 DOM 事件）

所以要获取用户的操作信息，必须使用客户端组件

```tsx
'use client';
```

客户端组件无法通过Props获取参数，而是通过解析URL获取参数

```tsx
  const searchParams = useSearchParams(); //拿到路由中的搜索参数字符串
  const pathname = usePathname();
  const { replace } = useRouter();
```

防抖钩子，降低请求频率，优化性能
handleSearch函数，绑定为输入框change的触发的事件函数，
目的是在输入查询信息改变时对URL做出同步

将拿到的字符串解析为URLSearchParams对象实例，用set，delete的方法，对其进行修改
再使用replace对URL进行同步修改

```tsx
  const handleSearch = useDebouncedCallback((term) => {
    const params = new URLSearchParams(searchParams);
    params.set('page', '1');
    if (term) {
      params.set('query', term);
    } else {
      params.delete('query');
    }
    replace(`${pathname}?${params.toString()}`);
  }, 300);
```

在input中加上defaultValue，非受react控制的默认值，从URL中获取、
目的是在URL改变时在输入框做同步

```tsx
      <input
        className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
        placeholder={placeholder}
        onChange={(e) => {
          handleSearch(e.target.value);
        }}
        defaultValue={searchParams.get('query')?.toString()} // 保持 URL 和输入同步,使用defaultvalue，意味着这是非受react状态控制的
      />
```

这样就完成了，从用户输入到URL更新，然后在page组件会被重新解析传入table组件，用于fetch新数据，渲染新内容的搜索实现
#### pagination

同样，要处理用户的在浏览器DOM节点的交互，所以要用client组件
由于需要通过URL获得分页page参数,，所以需要使用Hooks解析URL，存入对象中
通过对象操作，修改路由参数，再对URL进行更新同步

不同的是，这里不采用replace进行URL的更新，而是使用Link组件，向其传递新的URL

|            |                             |                         |
| ---------- | --------------------------- | ----------------------- |
| 特性         | 搜索 (replace)                | 分页 (<Link> / push)      |
| **用户意图**   | 修改/提炼当前视图                   | 导航到下一个/上一个离散的视图         |
| **历史记录**   | **不应**创建新的历史条目              | **应该**创建新的历史条目          |
| **“后退”行为** | 跳过中间的输入步骤，直接返回搜索前的页面        | 按步骤返回上一页                |
| **实现方式**   | 编程式导航 useRouter().replace() | 声明式导航 <Link href="..."> |

**一个简单的判断法则：**
- 问自己：“用户的这个操作，是否值得在浏览器历史中留下一个‘足迹’？”
    - **是** -> 使用 <Link> (或 router.push())
    - **否** -> 使用 router.replace()

区别之一是，是否留下历史记录
区别之二是，一个是点击跳转的交互，一个是在输入信息后自动更新的交互，前者显然可以通过更新跳转的链接来实现，后者希望再用户输入后自动更新，没有用户点击的过程，是不合适的

具体实现：

```tsx
export default function Pagination({ totalPages }: { totalPages: number }) {
  const pathname = usePathname();
  const searchParams = useSearchParams();
  const currentPage = Number(searchParams.get('page')) || 1;
  const allPages = generatePagination(currentPage, totalPages);

  // 将页码转换为URL的函数
  const createPageURL = (pageNumber: number | string) => {
    const params = new URLSearchParams(searchParams);
    params.set('page', pageNumber.toString());
    return `${pathname}?${params.toString()}`;
  };
  return (
    <>
        ...
        <PaginationArrow
          direction="left"
          href={createPageURL(currentPage - 1)}
          isDisabled={currentPage <= 1}
        />
        ...
              <PaginationNumber
                key={`${page}-${index}`}
                href={createPageURL(page)}
                page={page}
                position={position}
                isActive={currentPage === page}
              />		
		...
        <PaginationArrow
          direction="right"
          href={createPageURL(currentPage + 1)}
          isDisabled={currentPage >= totalPages}
        />
		...     
    </>
  )
// ...
return
    <Link className={className} href={href}>
      {icon}
    </Link>
```

URL更新后，page组件接收到的参数更新，
currentPage会更新，fetch的数据也会更新

# 渲染模式

## RSC

在nextjs中，每个组件默认是服务端组件

服务端组件的特点：

- **安全**：由于服务器组件在服务器上运行，你可以直接查询数据库，无需额外的 API 层。这让你免于编写和维护额外的代码。
- **性能**： 服务器组件在服务器上运行，因此你可以将昂贵的请求数据和逻辑保留在服务器上，仅将结果发送到客户端。
- **原生适配性**：服务器组件支持 JavaScript Promise，为像数据获取这样的异步任务提供了原生解决方案。你可以使用 `async/await` 语法，而无需 `useEffect` 、 `useState` 或其他数据获取库。
- **限制**：不能使用Hooks以及和客户端DOM相关的事件监听函数


这个默认行为是理解 Next.js 现代渲染系统的基石。基于此，衍生出了以下几种主要的渲染策略：

## 静态渲染和动态渲染（SSR）

|类型|说明|
|---|---|
|**静态渲染**|页面内容在构建（build）时就生成好了，所有用户看到的内容一致，访问速度快。|
|**动态渲染**|每次访问时服务器动态生成内容，适合内容随用户/时间变化的场景。|

动态渲染强调这个渲染动作是在“用户访问网页的那一刻”发生的，而不是提前生成好存放着。

- 访问博客文章（内容不会变）：适合静态渲染。
- 访问用户的个人信息页（内容因人而异）：适合动态渲染。

使用动态渲染，你的应用程序的速度取决于最慢的数据获取。

动态渲染的场景：

- 使用 cookies() 或 headers() 函数。
- 使用 searchParams prop。
- 使用 fetch 时设置了 cache: 'no-store' 或 revalidate: 0。
- 在页面文件中明确声明 export const dynamic = 'force-dynamic'。

| 场景             | 说明                   | 推荐方式                          |
| -------------- | -------------------- | ----------------------------- |
| 🔐 需要鉴权的页面     | 用户登录状态不同，显示内容不同      | 动态渲染或中间件处理                    |
| 🧑‍🤝‍🧑 个性化内容 | 根据用户 ID / Token 获取数据 | `dynamic = 'force-dynamic'`   |
| 📅 实时数据页面      | 股票、天气、排行榜等随时变化       | fetch + `cache: 'no-store'`   |
| 🧾 分页、搜索结果页    | URL 参数变化 + 数据实时变化    | 使用 `searchParams` + 动态渲染      |
| 🌍 地理或设备定向     | 根据 IP、语言、设备类型返回不同内容  | `headers()` + `force-dynamic` |
SSR:
- **本质**: 生成要在浏览器中显示的**初始文档结构**。它决定了用户第一次看到页面时的内容。

如果你的**唯一**需求是“在页面加载后，保持数据与后端的实时同步”，那么你确实**不需要**为此使用 Next.js 的动态渲染 (SSR)。纯粹的客户端数据获取库（如 React Query, SWR, or even a simple useEffect with fetch）就能完美胜任。

你需要 SSR 是为了解决**页面初始加载 (Initial Load)** 时的问题，而不是为了后续的数据同步。

## Streaming

流式传输的思想，将数据分块依次传输，可以在next中体现为组件级别的分块和页面级别的分块

与Loading的想法也有共同之处

**Suspense 流式传输 (Streaming) 本质上是动态渲染 (SSR) 的一种高级、优化的形式。**
### React Suspense

React 的 `Suspense` 允许你**为异步加载的组件或数据添加“加载中”的 fallback UI**。

```tsx
import { Suspense } from 'react'

<Suspense fallback={<div>加载中...</div>}>
  <MyAsyncComponent />
</Suspense>
```

被`Suspense` 包裹的部分，即便数据没加载好，也会先显示fallback替代，不会因为个别慢请求阻碍整个页面的渲染

### **页面级别的Streaming**

`loading.tsx` 是一个基于 React Suspense 构建的 Next.js 特殊文件。它允许你在页面内容加载时显示备用 UI 作为替代。

`loading.tsx` 和 `page.tsx` 是**具有特殊意义的保留文件名（convention-based components）**，框架会根据它们的命名来自动处理对应逻辑。

 📂 示例目录结构和加载顺序

```
/app
  /dashboard
    loading.tsx     ← 加载中状态
    layout.tsx      ← 包裹 dashboard 下所有页面
    page.tsx        ← 实际页面内容
```

 加载顺序：

1. 用户访问 `/dashboard`
2. Next.js 发现 `layout.tsx` + `page.tsx` 是 **异步加载的**
3. 就立即渲染 `loading.tsx`
4. 一旦真实内容加载完，`loading.tsx` 会被卸载


### **组件级别的streaming**

同理要使用Suspense来包裹组件

```jsx
<Suspense fallback={<RevenueChartSkeleton />}> 
	<RevenueChart /> 
</Suspense>
```

在调用子组件这一层，不应该发生阻塞，所以要将Data fetching移至<RevenueChart />内部，而不是在这个主组件fetch然后进行参数传递

#### **希望同时渲染多个组件**

如果几个组件一个一个的出现可能会在视觉上出现奇怪的效果

我们希望部分组件同时渲染

则可以将几个组件包装至一个组件中，一次性显示组件内部包装所有组件的骨架屏，然后并发的加载数据

```jsx
//可以将多个Card组件包装进一个Cardwrapper组件中

<Suspense fallback={<CardsSkeleton />}>
  <CardWrapper />
</Suspense>
```
#### **suspense边界的抉择**

🎯 **如何选择放置 Suspense 边界（Suspense Boundaries）的位置，需要根据页面加载体验、数据优先级和数据依赖来权衡，没有绝对的正确答案。**

- **放置位置取决于：**
    
    - 想让用户如何体验页面加载（流式体验顺序）；
    - 哪些内容更重要，需要优先加载；
    - 哪些组件依赖数据获取。
        
- **不同策略的优缺点：**
    
    - 🔄 **整页 Suspense（如 `loading.tsx`）：** 简单统一，但如果有慢组件，整个页面都会延迟。
    - 🔹 **每个组件都用 Suspense：** 精细控制，但组件会一个个“弹出”，影响视觉连贯性。
    - 🧩 **分组分区流式加载（staggered effect）：** 需要用 wrapper 组件封装，体验更流畅，但开发更复杂。
        
- **推荐做法（但非唯一）：**
    
    - 把数据获取逻辑尽可能下沉到实际使用它的组件中；
    - 再用 Suspense 包裹这些组件，实现**局部异步加载**。


# 网络请求

## Data fetching（查）

[[Fetching data]]

## Data Mutating （增删改）

## Using forms with Server Actions


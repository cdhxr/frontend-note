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

#### 1. 在服务器组件中 (Page, Layout)

在服务器组件（如 `page.js` 或 `layout.js`）中，`searchParams` 会作为**一个 prop** 自动传递给你的组件。这是最直接、最高效的方式。

**工作原理：**
-   当用户访问一个带查询参数的 URL 时，Next.js 会在服务器上解析这些参数。
-   它将解析结果作为一个对象，通过名为 `searchParams` 的 prop 传给你的页面组件。

**示例 (`app/products/page.js`):**

```jsx
// 这是一个服务器组件
export default function ProductsPage({ searchParams }) {
  // 如果 URL 是 /products?q=laptop&page=1
  // searchParams 的值就是: { q: 'laptop', page: '1' }

  const searchQuery = searchParams.q || ''; // 提供默认值
  const currentPage = searchParams.page || '1'; // 值总是字符串

  console.log(searchParams);

  return (
    <div>
      <h1>商品列表</h1>
      <p>正在搜索: {searchQuery}</p>
      <p>当前页码: {currentPage}</p>
      {/* 在这里根据 searchParams 获取并展示数据 */}
    </div>
  );
}
```
**关键点**:
-   `searchParams` 是一个普通的对象。
-   所有的值**都是字符串类型**，即使它们看起来像数字。你需要手动转换（例如 `parseInt(searchParams.page)`）。

#### 2. 在客户端组件中 (Client Component)

在客户端组件（标记了 `'use client'` 的组件）中，你不能直接通过 props 获取 `searchParams`。你需要使用 `useSearchParams` 这个 Hook。

**工作原理：**
-   `useSearchParams` Hook (来自 `next/navigation`) 返回一个**只读**的 `URLSearchParams` 对象实例。
-   你可以使用这个对象提供的方法（如 `.get()`, `.has()`）来读取参数。

**示例 (一个独立的搜索框组件):**

```jsx
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

---

### 三、搜索与分页的实现思路 (推荐模式)

最好的实现方式是**结合服务器组件和客户端组件**：
-   **服务器组件 (`page.js`)**: 负责根据 `searchParams` **获取数据**并展示。它是数据的最终消费者。
-   **客户端组件 (`Controls.js`)**: 负责提供交互界面（搜索框、分页按钮），并**更新 URL 中的 `searchParams`**。它是状态的生产者。

这种模式利用了服务器组件的性能优势（数据获取在服务器完成）和客户端组件的交互能力。

#### **场景：实现一个可搜索、可分页的商品列表页**

**文件结构:**
```
app/
└── products/
    ├── page.js          # 服务器组件，展示列表
    └── Controls.js      # 客户端组件，提供搜索和分页按钮
```

#### **第一步：服务器组件 (`app/products/page.js`)**

这个组件读取 URL 参数，获取数据，并将数据和控制逻辑传递给客户端组件。

```jsx
import Controls from './Controls';

// 模拟一个从数据库或 API 获取数据的函数
async function fetchProducts({ query, page }) {
  // 在实际应用中，你会调用一个 API
  // const res = await fetch(`https://api.example.com/products?q=${query}&page=${page}&limit=10`);
  // const data = await res.json();
  
  // 这里我们用假数据模拟
  console.log(`Fetching data for query: "${query}", page: ${page}`);
  const allProducts = Array.from({ length: 100 }, (_, i) => `商品 ${i + 1}`);
  const filtered = allProducts.filter(p => p.includes(query));
  
  const limit = 10;
  const start = (page - 1) * limit;
  const paginated = filtered.slice(start, start + limit);

  return {
    products: paginated,
    hasNextPage: (start + limit) < filtered.length,
  };
}

export default async function ProductsPage({ searchParams }) {
  const query = searchParams.q || '';
  const page = parseInt(searchParams.page) || 1;

  const { products, hasNextPage } = await fetchProducts({ query, page });

  return (
    <div>
      <h1>商品列表</h1>
      
      {/* 客户端组件，用于控制 */}
      <Controls currentPage={page} hasNextPage={hasNextPage} />

      {/* 展示从服务器获取的数据 */}
      <ul>
        {products.map(product => <li key={product}>{product}</li>)}
      </ul>
      {products.length === 0 && <p>没有找到相关商品。</p>}
    </div>
  );
}
```

#### **第二步：客户端组件 (`app/products/Controls.js`)**

这个组件处理用户输入，并更新 URL。

```jsx
'use client';

import { useSearchParams, usePathname, useRouter } from 'next/navigation';
import { useDebouncedCallback } from 'use-debounce'; // 推荐使用 debounce 避免频繁请求

export default function Controls({ currentPage, hasNextPage }) {
  const searchParams = useSearchParams();
  const pathname = usePathname(); // 获取当前路径，如 /products
  const { replace } = useRouter(); // 使用 router 来更新 URL

  // 使用 debounce 优化搜索体验
  const handleSearch = useDebouncedCallback((term) => {
    const params = new URLSearchParams(searchParams);
    params.set('page', '1'); // 新的搜索总是从第一页开始

    if (term) {
      params.set('q', term);
    } else {
      params.delete('q');
    }
    replace(`${pathname}?${params.toString()}`);
  }, 300); // 300ms延迟

  const handlePagination = (direction) => {
    const params = new URLSearchParams(searchParams);
    const newPage = direction === 'next' ? currentPage + 1 : currentPage - 1;
    params.set('page', newPage.toString());
    replace(`${pathname}?${params.toString()}`);
  };

  return (
    <div style={{ display: 'flex', gap: '20px', marginBottom: '20px' }}>
      {/* 搜索框 */}
      <input
        type="text"
        placeholder="搜索商品..."
        onChange={(e) => handleSearch(e.target.value)}
        defaultValue={searchParams.get('q')?.toString()}
      />

      {/* 分页按钮 */}
      <div>
        <button 
          onClick={() => handlePagination('prev')} 
          disabled={currentPage <= 1}
        >
          上一页
        </button>
        <span> 第 {currentPage} 页 </span>
        <button 
          onClick={() => handlePagination('next')} 
          disabled={!hasNextPage}
        >
          下一页
        </button>
      </div>
    </div>
  );
}
```

### 完整的数据流（The Round-Trip）

1.  **初始加载**: 用户访问 `/products`。`page.js` 在服务器上运行，`searchParams` 为空，获取第一页的全部数据并渲染。
2.  **用户搜索**: 用户在搜索框输入 "手机"。
3.  **客户端操作**: `Controls.js` 中的 `handleSearch` 函数被触发。它创建了一个新的 URL `/products?q=手机&page=1`。
4.  **URL 更新**: `router.replace()` 更新了浏览器的 URL，**但不会导致页面硬刷新**。Next.js 捕获到这个变化。
5.  **服务器重新渲染**: Next.js 重新在服务器上渲染 `page.js`，这次传入的 `searchParams` 是 `{ q: '手机', page: '1' }`。
6.  **数据重新获取**: `fetchProducts` 函数根据新的参数获取过滤后的数据。
7.  **页面更新**: 服务器将更新后的 HTML 内容发送给客户端，页面无缝更新为搜索结果。
8.  **用户点击“下一页”**: 重复步骤 3-7，只是这次更新的是 `page` 参数。

这种模式完美地体现了 Next.js App Router 的设计哲学：**让 URL 成为状态的唯一真实来源**，并利用服务器的强大能力来处理数据，同时保持客户端的流畅交互。

# 渲染模式




# 网络请求


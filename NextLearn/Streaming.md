
流式传输是一种数据传输技术，它允许你将路由分解为更小的“块”，并随着它们准备好，逐步从服务器流式传输到客户端。

通过流式传输，你可以防止缓慢的数据请求阻塞整个页面。这允许用户在等待所有数据加载之前就能看到并与页面的部分内容进行交互。

流式传输与 React 的组件模型配合得很好，因为每个组件都可以被视为一个数据块。

There are two ways you implement streaming in Next.js:  
在 Next.js 中实现流式传输有两种方法：

1. At the page level, with the `loading.tsx` file (which creates `<Suspense>` for you).  
	
    在页面级别，使用 `loading.tsx` 文件（它会为你创建 `<Suspense>` ）。
2. At the component level, with `<Suspense>` for more granular control.  
	
    在组件级别，使用 `<Suspense>` 进行更细粒度的控制。


`loading.tsx` 是一个基于 React Suspense 构建的 Next.js 特殊文件。它允许你在页面内容加载时显示备用 UI 作为替代。

`loading.tsx` 和 `page.tsx` 是**具有特殊意义的保留文件名（convention-based components）**，框架会根据它们的命名来自动处理对应逻辑。
## 📂 示例目录结构和加载顺序

```
/app
  /dashboard
    loading.tsx     ← 加载中状态
    layout.tsx      ← 包裹 dashboard 下所有页面
    page.tsx        ← 实际页面内容
```

### 加载顺序：

1. 用户访问 `/dashboard`
2. Next.js 发现 `layout.tsx` + `page.tsx` 是 **异步加载的**
3. 就立即渲染 `loading.tsx`
4. 一旦真实内容加载完，`loading.tsx` 会被卸载

非常好，这是个关键问题！Next.js 中的 `loading.tsx` 和 React 的 `Suspense` 密切相关，但两者的**职责和触发机制略有不同**。

---

## 🧠 先理解 React 的 `Suspense`

### 🔹 React Suspense 是什么？

React 的 `Suspense` 允许你**为异步加载的组件或数据添加“加载中”的 fallback UI**。

```tsx
import { Suspense } from 'react'

<Suspense fallback={<div>加载中...</div>}>
  <MyAsyncComponent />
</Suspense>
```

只要 `MyAsyncComponent` 在加载中，就会先显示 fallback 的 `<div>`。

---

## ✅ Next.js 的 `loading.tsx` = App Router 中的自动化 `Suspense.fallback`

### ✅ 在 App Router 中，**Next.js 自动为每个 layout 和 page 添加了 `Suspense`**

你不需要手动写 `<Suspense>`，Next.js 帮你包好了，并通过 `loading.tsx` 实现 `fallback`。

### 👇 实际上，Next.js 的行为可以理解为这样：

```tsx
// App Router 的 layout 默认行为（概念上的简化）
<Suspense fallback={<Loading />}>
  <Page />
</Suspense>
```

而 `loading.tsx` 就是这个 `fallback` 对应的组件！

---

## 🧪 举个实际例子

### 目录结构

```
/app
  /dashboard
    layout.tsx
    page.tsx
    loading.tsx
```

### 加载流程（简化）：

1. 用户访问 `/dashboard`
2. Next.js 检查是否有 `layout.tsx` 和 `page.tsx`（这两个可能是异步的）
3. 如果这两者还在加载中 → **自动触发 Suspense fallback**
4. fallback 显示为 `loading.tsx` 渲染出的内容
5. 加载完毕 → 正常显示页面


Route groups

在（overview）文件夹下的文件会成为一个路由组
路由组允许你将文件组织成逻辑分组，而不会影响 URL 路径结构。当你使用括号 `()` 创建一个新文件夹时，其名称不会包含在 URL 路径中。
因此 `/dashboard/(overview)/page.tsx` 变成 `/dashboard` 。

这里，你使用路由组来确保 `loading.tsx` 仅适用于你的仪表板概览页面。

然而，你也可以使用路由组将应用程序分为不同部分（例如 `(marketing)` 路由和 `(shop)` 路由）或按团队进行划分，适用于更大型应用程序。

路由组的主要作用是：**方便你对不同功能页面进行逻辑上的分组管理，同时保持 URL 不变，还可以为每组加特定的 layout、loading、error。**

假设你的项目结构如下：

```
/app
  /login/page.tsx         → 路由是 /login
  /register/page.tsx      → 路由是 /register
  /dashboard/page.tsx     → 路由是 /dashboard
```

你想给登录和注册页加一个统一的 `layout.tsx`，但不希望影响 `dashboard` 页

loading与page可以被称为页面级别的streaming
是分成loading和page两个chunk进行加载的

# 组件级别的streaming

同理要使用Suspense来包裹组件

```jsx
<Suspense fallback={<RevenueChartSkeleton />}> 
	<RevenueChart /> 
</Suspense>
```

在调用子组件这一层，不应该发生阻塞，所以要将Data fetching移至<RevenueChart />内部，而不是在这个主组件fetch然后进行参数传递

# 希望同时渲染多个组件

如果几个组件一个一个的出现可能会在视觉上出现奇怪的效果

我们希望部分组件同时渲染

则可以将几个组件包装至一个组件中，一次性显示组件内部包装所有组件的骨架屏，然后并发的加载数据

```jsx
//可以将多个Card组件包装进一个Cardwrapper组件中

<Suspense fallback={<CardsSkeleton />}>
  <CardWrapper />
</Suspense>
```
# suspense边界的抉择

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
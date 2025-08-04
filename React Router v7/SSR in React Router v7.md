# 与RSC的区别

React Router v7使用的SSR与Next不同，
RSC 仅在服务器上运行，而传统的 SSR 方法在服务器和客户端都运行。
所以在React Router的组件中是可以使用钩子的，因为其实jsx是在客户端运行的

- 初次访问页面时：
    - **在服务端**：组件被 `renderToString()` 或 `renderToPipeableStream()` 渲染成 HTML
- 页面加载后：
    - **在客户端**：同样的组件代码会“hydrate”（再激活）成可交互的 React 应用

这些组件：

- 是 **客户端组件（Client Components）**
- 但 **在初次渲染时运行在服务端**，生成 HTML
- 后续交互（如点击按钮、改变状态）发生在客户端

所以说，它们本质与 Next.js 中的客户端组件相当，这些组件首先在服务器上渲染，然后在客户端上水合。“是客户端组件”，但初次渲染在服务器。

- 如果你 **不进行 hydration**（即不让 React 在客户端接管这些组件）：
    
    - 它们就只在服务端运行
    - 不再支持 `useState`、`useEffect` 这些前端 hooks
    - 但可以使用如数据库调用、文件系统、权限检查等 **服务端能力**

所以，**“不 hydrate = 真正意义上的服务端组件”**


# 对比 **SSR（服务端渲染）** 与 **有 API 层的 CSR（客户端渲染）** 这两种架构模式

---

## 1. 基本概念对比

| 特性      | SSR（服务端渲染）             | 有 API 层的 CSR（客户端渲染）        |
| ------- | ---------------------- | -------------------------- |
| 页面渲染位置  | 服务器端（每次请求服务器返回完整 HTML） | 浏览器端（首次加载后由 JS 生成 DOM）     |
| 数据获取    | 服务端 loader 直接获取数据      | 前端通过 API 请求数据              |
| HTML 返回 | 返回完整 HTML，含数据内容        | 返回基本空白 HTML，后由 JS 加载数据渲染页面 |

---

## 2. 数据流和交互流程

通过 [[Hydration]] 机制实现

| 步骤          | SSR                        | CSR + API                            |
| ----------- | -------------------------- | ------------------------------------ |
| 用户访问页面      | 发送请求 → 服务器执行 loader → 渲染页面 | 发送请求 → 服务器返回空白 shell → 前端请求 API 获取数据 |
| 页面首屏内容展示    | 服务器生成完整 HTML，快速显示内容        | 首次显示 Loading，等待数据加载                  |
| 数据更新（如表单提交） | action 在服务器执行，返回新的页面或重定向   | 前端调用 API 接口，更新状态，重新渲染 UI             |

---

## 3. 优缺点对比

| 方面     | SSR                      | CSR + API                 |
| ------ | ------------------------ | ------------------------- |
| 首屏加载速度 | 快（直接加载完整 HTML）           | 慢（先加载 JS 再请求数据）           |
| SEO    | 好，搜索引擎能直接抓取页面内容          | 差，页面内容需 JS 执行后才能获取        |
| 用户体验   | 立刻可见内容，交互顺畅              | 首屏加载时可能看到空白或加载动画          |
| 开发复杂度  | 需要服务端环境，loader/action 设计 | 前后端分离，API 设计，前端管理数据状态复杂度高 |
| 部署要求   | 需要支持服务器渲染环境（Node.js 等）   | 静态服务器即可，API 可独立部署         |
| 数据安全   | 数据请求在服务端，安全性更好           | API 接口需额外设计权限和安全保护        |
| 缓存和性能  | 可利用服务端缓存，减少重复渲染          | API 响应可独立缓存，但首屏依赖客户端处理    |

---

## 4. 代码设计差异

### SSR 示例（React Router v7）

```ts
export async function loader({ params }) {
  return fetchFromDB(params.id); // 直接访问数据库或后端资源
}

export async function action({ request }) {
  const form = await request.formData();
  await saveToDB(form);
  return redirect(`/profile/${form.get("id")}`);
}
```

- loader 和 action 在服务器执行，返回完整页面

---

### CSR + API 示例（React Query + REST API）

```ts
// 前端 React 组件
const { data, isLoading } = useQuery(['profile', userId], () =>
  fetch(`/api/profile/${userId}`).then(res => res.json())
);

const mutation = useMutation(formData => fetch('/api/profile', {
  method: 'POST',
  body: formData,
}));
```

- 前端请求 API，渲染和交互都在浏览器中完成

对于今天大多数构建的 Web 应用，你需要在整个应用或特定路由之间选择静态渲染或动态渲染。在 Next.js 中，如果你在路由中调用动态函数（如查询数据库），整个路由将变为动态。

然而，大多数路由既不是完全静态也不是完全动态。例如，考虑一个电子商务网站。你可能希望静态渲染产品信息页面的大部分内容，但可能需要动态获取用户的购物车和推荐产品，这允许你向用户展示个性化内容。

**Next.js 在 App Router 中本身就支持“页面一部分静态，一部分动态”的渲染方式** —— 比如你使用 `loading.tsx`、`<Suspense>`、`fetch` 以及 RSC 时，实际上就已经享受到“部分静态、部分动态 + Streaming” 的效果了。

那为什么还需要 **Partial Prerendering (PPR)** 呢？它解决了 **现有机制下无法完全做到的一个关键问题**：

---

## 🧩 核心区别：PPR 提供了**可缓存的静态 Shell + 动态流式注入**

|功能|App Router + Suspense (传统)|PPR (Next.js 15)|
|---|---|---|
|支持部分内容流式渲染|✅ 是|✅ 是|
|页面可部分静态、部分动态|✅ 是|✅ 是|
|✅ **HTML shell 可静态缓存**|❌ 否（有动态部分就不能 SSG）|✅ 是（静态部分会生成并缓存）|
|支持 CDN 缓存与加速|部分支持（但不灵活）|✅ 明确设计用于边缘缓存|
|可组合静态 + 动态流式加载|受限（全页面要么 SSG/SSR）|✅ 真正做到“静态 shell + 动态流”|

---

### 📌 举个例子来理解：

假设你有一个电商页面：

```tsx
// page.tsx
export default function ProductPage() {
  return (
    <>
      <StaticHeader />             // 静态导航栏
      <Suspense fallback={<LoadingDetails />}>
        <ProductDetails />        // 动态产品详情
      </Suspense>
      <StaticFooter />            // 静态页脚
    </>
  );
}
```

在 App Router 中，如果 `ProductDetails` 是动态的：

- Next.js **默认整页会变为动态渲染**（即便 Header 和 Footer 是静态的）；
- 这意味着整页无法被 CDN 缓存；
- 你不能享受 SSG 的性能优势；
- 所有渲染都需要每次请求命中服务器。

而开启 PPR 后：

- ✅ `StaticHeader` 和 `StaticFooter` 会预渲染为一个静态 HTML shell；
- ✅ `ProductDetails` 仍然可以异步服务端渲染并流式注入；
- ✅ 页面首次加载“立刻出现框架”，核心内容稍后补上；
- ✅ 静态部分可用 CDN 缓存，性能和体验大幅提升。

---

## 🔍 总结一句话：

> ✅ App Router + Suspense 已经支持“部分静态、部分动态”的渲染体验，  
> 但 ❌ 无法将静态部分缓存成 HTML 交给 CDN。  
> 而 ✅ **PPR 的关键价值在于“将静态 shell 部分**提前预渲染并缓存”，真正实现**全局性能优化与 Streaming 的结合**。


CDN?
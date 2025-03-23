---
tags:
  - http
date: 2025-03-22
---

正如我们所知道的，`fetch` 返回一个 promise。JavaScript 通常并没有“中止” promise 的概念。那么我们怎样才能取消一个正在执行的 `fetch` 呢？例如，如果用户在我们网站上的操作表明不再需要某个执行中的 `fetch`。

- `bortController` 是一个简单的对象，当 `abort()` 方法被调用时，会在自身的 `signal` 属性上生成 `abort` 事件（并将 `signal.aborted` 设置为 `true`）。
- `fetch` 与之集成：我们将 `signal` 属性作为可选参数（option）进行传递，之后 `fetch` 会监听它，因此它能够中止 `fetch`。
- 我们可以在我们的代码中使用 `AbortController`。“调用 `abort()`” → “监听 `abort` 事件”交互简单且通用。即使没有 `fetch`，我们也可以使用它。


为此有一个特殊的内建对象：`AbortController`。它不仅可以中止 `fetch`，还可以中止其他异步任务。


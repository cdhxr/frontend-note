---
date: 2025-07-20
tags:
  - nextjs
---

# page

`page.tsx` 是一个特殊的 Next.js 文件，它导出一个 React 组件，并且是路由可访问的必要条件。在您的应用程序中，您已经有一个页面文件： `/app/page.tsx` - 这是与路由 `/` 关联的主页。

Page文件的路由将会被定义成其父文件夹的名字，直到app为单纯的`/`，子文件夹中的Page文件都是嵌套路由，如app/dashboard/page的路由就是app/dashboard/

仪表板在多个页面之间共享某种导航。在 Next.js 中，你可以使用一个特殊的 `layout.tsx` 文件来创建在多个页面之间共享的 UI。

`<Layout />` 组件接收一个 `children` 属性。
这个子组件要么是一个page，要么是另一个layout。

在layout中子文件夹下变换Page，只有Page组件会更新，layout部分不会更新，这被称为部分渲染，它可以在页面之间切换时保留布局中的客户端 React 状态。

`/app/layout.tsx`

这被称为根布局，并且是每个 Next.js 应用程序所必需的。
你添加到根布局中的任何 UI 将在你的应用程序的所有页面之间共享。

你可以使用根布局来修改你的 `<html>` 和 `<body>` 标签，并添加元数据（你将在后面的章节中了解更多关于元数据的内容）。

由于你刚刚创建的新布局（ `/app/dashboard/layout.tsx` ）仅适用于仪表板页面，因此你不需要在根布局上方添加任何 UI。
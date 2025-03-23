---
tags:
  - react
date: 2025-03-23
---
## State简介

state作用类似于vue中的响应式变量（ref，reactive）

1. `import { useState } from 'react';`导入
2. 声明state变量
3. 可当做响应式变量使用

在 React 中，`useState` 以及任何其他以“`use`”开头的函数都被称为 **Hook**。
useState也是一个hook

类似于之前vue的学习，hook是一种特殊的函数，封装了一套逻辑，可以在组件中调用公开的方法和属性

`const [thing, setThing] = useState(0);`

每次你的组件渲染时，`useState` 都会给你一个包含两个值的数组：

1. **state 变量** 会保存上次渲染的值。
2. **state setter 函数** 可以更新 state 变量并触发 React 重新渲染组件。
	- 内部可以传值（表达式结果），也可以传递函数、对象作为参数

## 渲染流程

1. **组件进行第一次渲染。** 因为你将 `0` 作为 `index` 的初始值传递给 `useState`，它将返回 `[0, setIndex]`。 React 记住 `0` 是最新的 state 值。
2. **你更新了 state**。当用户点击按钮时，它会调用 `setIndex(index + 1)`。 `index` 是 `0`，所以它是 `setIndex(1)`。这告诉 React 现在记住 `index` 是 `1` 并触发下一次渲染。
3. **组件进行第二次渲染**。React 仍然看到 `useState(0)`，但是因为 React _记住_ 了你将 `index` 设置为了 `1`，它将返回 `[1, setIndex]`。

## State 是隔离且私有的

State 是屏幕上组件实例内部的状态。换句话说，**如果你渲染同一个组件两次，每个副本都会有完全隔离的 state**！改变其中一个不会影响另一个。

```js
import Gallery from './Gallery.js';

export default function Page() {
  return (
    <div className="Page">
      <Gallery />
      <Gallery />
    </div>
  );
}
```

- 两个gallery的组件State并不同步![[Pasted image 20250323155117.png]]

还要注意 `Page` 组件“不知道”关于 `Gallery` state 的任何信息，甚至不知道它是否有任何 state。与 props 不同，**state 完全私有于声明它的组件**。父组件无法更改它。这使你可以向任何组件添加或删除 state，而不会影响其他组件。

## 摘要

- 当一个组件需要在多次渲染间“记住”某些信息时使用 state 变量。
- State 变量是通过调用 `useState` Hook 来声明的。
- Hook 是以 `use` 开头的特殊函数。它们能让你 “hook” 到像 state 这样的 React 特性中。
- Hook 可能会让你想起 import：它们需要在非条件语句中调用。调用 Hook 时，包括 `useState`，仅在组件或另一个 Hook 的顶层被调用才有效。
- `useState` Hook 返回一对值：当前 state 和更新它的函数。
- 你可以拥有多个 state 变量。在内部，React 按顺序匹配它们。
- State 是组件私有的。如果你在两个地方渲染它，则每个副本都有独属于自己的 state。

## 任务的启示

1. 列表渲染时setState要处理避免越界情况
2. State变量响应式渲染，而普通变量不行
	- 重写该普通逻辑至响应式
	- 将声明let变为声明State
	- 将赋值修改改为set修改
3. 必须在条件语句外并且始终以相同的顺序调用 Hook
4. State 变量仅用于在组件重渲染时保存信息（即在tag中的数据）。在单个事件处理函数中，普通变量就足够了。当普通变量运行良好时，不要引入 state 变量。


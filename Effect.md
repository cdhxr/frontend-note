
- 与事件不同，Effect 由渲染本身引起，而非特定的交互。
- Effect 允许你将组件与某些外部系统（第三方 API、网络等）同步。
- 默认情况下，Effect 在每次渲染（包括初始渲染）后运行。
- 如果所有依赖项都与上一次渲染时相同，React 会跳过本次 Effect。
- 你不能“选择”依赖项，它们是由 Effect 内部的代码所决定的。
- 空的依赖数组（`[]`）对应于组件的“挂载”，即组件被添加到页面上时。
- 仅在严格模式下的开发环境中，React 会挂载两次组件，以对 Effect 进行压力测试。
- 如果你的 Effect 因为重新挂载而出现问题，那么你需要实现一个清理函数。
- React 会在 Effect 再次运行之前和在组件卸载时调用你的清理函数。

# Why

在 React 中，`useEffect` 是一个 Hook，它用于在函数组件中执行副作用（Side Effects）。
副作用通常指的是那些影响组件外部世界的操作，例如：

- **数据获取（Data Fetching）**：向 API 发送请求并更新状态。
- **订阅（Subscriptions）**：监听 WebSocket 或事件。
- **手动 DOM 操作**：操作 `document.title`、修改 DOM 元素等。
- **定时器（Timers）**：使用 `setTimeout` 或 `setInterval` 等。

在 React 中，“**副作用（Side Effects）**” 指的是**组件渲染过程中，除了返回 UI 之外，还会产生的额外操作**。这些操作通常会影响外部环境，比如修改 DOM、发送网络请求、使用定时器等。

- 在纯函数编程的理念中，一个函数的返回值应该 **仅由它的输入决定，不会影响外部环境**
- 而如果一个函数修改了外部变量，或者执行了异步操作，就会产生副作用。
- React 组件的 `render` 过程**应该是纯的**，即**它只能返回 JSX，不应该有副作用**。
- 如果我们在 `render` 里直接执行副作用操作，可能会导致难以预测的行为。

**Effect 允许你指定由渲染自身，而不是特定事件引起的副作用**。
记住，Effect 通常用于暂时“跳出” React 并与一些 **外部** 系统进行同步。

# 使用Effect

## 第一步：声明 Effect
先从 React 中导入 [`useEffect` Hook](https://zh-hans.react.dev/reference/react/useEffect)：

```js
import { useEffect } from 'react';
```

再在组件顶部调用, 并在其中加入一些代码：

```js
function MyComponent() {
  useEffect(() => {
    // 每次渲染后都会执行此处的代码
  });
  return <div />;
}
```

每当你的组件渲染时，React 会先更新页面，然后再运行 `useEffect` 中的代码。换句话说，**`useEffect` 会“延迟”一段代码的运行，直到渲染结果反映在页面上**

不使用useEffect时，无法指定代码的执行阶段，

```js
<VideoPlayer isPlaying={isPlaying} />;
```

```js
function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  if (isPlaying) {
    ref.current.play();  // 渲染期间不能调用 `play()`。 
  } else {
    ref.current.pause(); // 同样，调用 `pause()` 也不行。
  }

  return <video ref={ref} src={src} loop playsInline />;
}
```

当第一次调用 `VideoPlayer` 时，对应的 DOM 节点还不存在！因为 React 在你返回 JSX 之前不知道要创建什么样的 DOM，所以没有 DOM 节点可以调用 `play()` 或 `pause()` 方法。

解决办法是 **使用 `useEffect` 包裹副作用，把它分离到渲染逻辑的计算过程之外**：

```js
import { useEffect, useRef } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  });

  return <video ref={ref} src={src} loop playsInline />;
}
```

通过将 DOM 更新封装在 Effect 中，你可以让 React 先更新页面，然后再运行 Effect。

当 `VideoPlayer` 组件渲染时（无论是否为首次渲染），会发生以下几件事：首先 React 会更新页面，确保 `<video>` 标签带着正确的 props 出现在 DOM 中；接着 React 将运行 Effect；最后 Effect 将根据 `isPlaying` 的值调用 `play()` 或 `pause()`。

需要注意的是，控制视频播放器在实际应用中要复杂得多：比如调用 `play()` 可能会失败、用户可能会使用内置的浏览器控件来进行播放或暂停等操作。本例子是一个非常简化且不完整的示例。

## 第二步：指定 Effect 的依赖项 

默认情况下，Effect 会在 **每次** 渲染后运行。但往往 **这并不是你想要的**：

- 有时，它可能会很慢。与外部系统的同步并不总是即时的，所以你可能希望在不必要时跳过它。例如，你不会想在每次打字时都得重新连接聊天服务器。
- 有时，它可能会出错。例如，你不会想在每次按键时都触发组件的淡入动画。动画应该只在组件首次出现时播放。

通过在调用 `useEffect` 时指定一个 **依赖数组** 作为第二个参数，你可以让 React **跳过不必要地重新运行 Effect**。

```js
 useEffect(() => {
    if (isPlaying) { // isPlaying 在此处使用……
      // ...
    } else {
      // ...
    }
  }, [isPlaying]); // ……所以它必须在此处声明！
```

指定 `[isPlaying]` 作为依赖数组会告诉 React：如果 `isPlaying` 与上次渲染时相同，就跳过重新运行 Effect。

依赖数组可以包含多个依赖项。只有当你指定的 **所有** 依赖项的值都与上一次渲染时完全相同，React 才会跳过重新运行该 Effect。React 使用 [`Object.is`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/is) 来比较依赖项的值。有关详细信息，请参阅 [`useEffect` 参考文档](https://zh-hans.react.dev/reference/react/useEffect#reference)。

**请注意，你不能随意“选择”依赖项**。如果你指定的依赖项与 React 根据 Effect 内部代码所推断出的依赖项不匹配，你将收到来自 linter 的错误提示。

## 第三步：按需添加清理（cleanup）函数

考虑一个不同的例子。

假如你正在编写一个 `ChatRoom` 组件，该组件在显示时需要连接到聊天服务器。
- 现在为你提供了 `createConnection()` API，
- 该 API 返回一个包含 `connect()` 与 `disconnection()` 方法的对象。
如何确保组件在显示时始终保持连接？

如果每次重新渲染后都得进行连接，这会很慢，所以你需要添加依赖数组：

```js
useEffect(() => {
  const connection = createConnection();
  connection.connect();
}, []);
```

**由于 Effect 中的代码没有使用任何 props 或 state，所以依赖数组为空数组 `[]`。这告诉 React 仅在组件“挂载”（即首次显示在页面上）时运行此代码**。

假设 `ChatRoom` 组件是一个大型多页面应用中的一部分。用户最初在 `ChatRoom` 页面上。组件挂载并调用 `connection.connect()` 。接着用户可能会导航到另一个页面，比如切换到“设置”页面，于是 `ChatRoom` 组件被卸载。最后，当用户点击“返回”时，`ChatRoom` 组件再次挂载。这将建立第二个连接——但第一个连接从未被销毁！随着用户在应用中来回切换，连接将会不断累积。

这类 bug 在没有大量手动测试的情况下很容易被忽略。为了帮助你快速发现它们，在开发环境中，React 会在组件首次挂载后立即重新挂载一次。

两次出现 `"✅ 连接中……"` 能够帮助你注意到真正的问题：在代码中，组件被卸载时没有关闭连接。

为了解决这个问题，可以在 Effect 中返回一个 **清理（cleanup）函数** 。

```js
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, []);
```

React 会在每次 Effect 重新运行之前调用清理函数，并在组件卸载（被移除）时最后一次调用清理函数。

React 有意在开发环境下重新挂载你的组件，来找到类似上例中的 bug。**你需要思考的不是“如何只运行一次 Effect”，而是“如何修复我的 Effect 来让它在重新挂载后正常运行”**。

通常，答案是实现清理函数。清理函数应该停止或撤销 Effect 所做的一切。原则是用户不应该感受到 Effect 只执行一次（在生产环境中）和连续执行“挂载 → 清理 → 挂载”（在开发环境中）之间的区别。

# 具体情况：

https://zh-hans.react.dev/learn/synchronizing-with-effects

- cleanup
	- 管理非react部件
	- 订阅事件
	- 触发动画
	- 发送分析报告
	- 获取数据
- 不适用于Effect的情况
	- 初始化应用，某些逻辑应该只在应用启动时运行一次。
	- 购买商品，这类行为不允许调用两次




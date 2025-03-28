
- 如果你可以在渲染期间计算某些内容，则不需要使用 Effect。
- 想要缓存昂贵的计算，请使用 `useMemo` 而不是 `useEffect`。
- 想要重置整个组件树的 state，请传入不同的 `key`。
- 想要在 prop 变化时重置某些特定的 state，请在渲染期间处理。
- 组件 **显示** 时就需要执行的代码应该放在 Effect 中，否则应该放在事件处理函数中。
- 如果你需要更新多个组件的 state，最好在单个事件处理函数中处理。
- 当你尝试在不同组件中同步 state 变量时，请考虑状态提升。
- 你可以使用 Effect 获取数据，但你需要实现清除逻辑以避免竞态条件。


- **你不必使用 Effect 来转换渲染所需的数据**。
	- state upate => render =>effect(it maybe make state update again) => render again ( we have to do the same thing twice )
- **你不必使用 Effect 来处理用户事件**。
	- effect can't show what event happened, cus of effect can only know the prop or state change.And don' t like the event function we can know event from which button.

Some example

**如果一个值可以基于现有的 props 或 state 计算得出，[不要把它作为一个 state](https://zh-hans.react.dev/learn/choosing-the-state-structure#avoid-redundant-state)，而是在渲染期间直接计算这个值**。(fullname = firstname + lastname)

**缓存昂贵的计算**，你可以使用 [`useMemo`](https://zh-hans.react.dev/reference/react/useMemo) Hook 缓存，即将结果缓存，避免重新渲染时再次计算

**当 props 变化时重置所有 state**，Effect不适合重置State，it will use the old value first and then reset it，it need twice render for an operation.
Instead of using effect, you can just divide the component to two componnets.
Transmiting the prop to childComponent and when it changes the state will reset automaticly.

**当 prop 变化时调整部分 state**，so in this case, we must can locate the state we want to change, we can setstate before return,which means the change happend in rendering time
它执行到 `return` 语句退出后，React 将 **立即** 重新渲染 `List`。此时 React 还没有渲染 `List` 的子组件或更新 DOM，这使得 `List` 的子组件可以跳过渲染旧的 `selection` 值。

**当你不确定某些代码应该放在 Effect 中还是事件处理函数中时，先自问 为什么 要执行这些代码。Effect 只用来执行那些显示给用户时组件 需要执行 的代码**。(通知应该在用户 **按下按钮** 后出现，而不是因为页面显示出来时！)
从用户的角度来看它是 **怎样的逻辑**。如果这个逻辑是由某个特定的交互引起的，请将它保留在相应的事件处理函数中。如果是由用户在屏幕上 **看到** 组件时引起的，请将它保留在 Effect 中。

**链式计算**：避免链接多个 Effect 仅仅为了相互触发调整 state，更好的做法是：尽可能在渲染期间进行计算，以及在事件处理函数中调整 state

**初始化应用**：如果某些逻辑必须在 **每次应用加载时执行一次**，而不是在 **每次组件挂载时执行一次**，可以添加一个顶层变量来记录它是否已经执行过了
可以添加一个顶层变量来记录它是否已经执行过了（didinit true or false）
你也可以在模块初始化和应用渲染之前执行它：`if (typeof window !== 'undefined') {}`

**通知父组件有关 state 变化的信息**，往往关于多个组件，用状态提升或者用一个事件处理函数，处理两个组件的State

**将数据传递给父组件** 当子组件在 Effect 中更新其父组件的 state 时，数据流变得非常难以追踪。既然子组件和父组件都需要相同的数据，那么可以让父组件获取那些数据，并将其 **向下传递** 给子组件，这更简单，并且可以保持数据流的可预测性：数据从父组件流向子组件。

**订阅外部 store** 由于这些数据可能在 React 无法感知的情况下发变化，你需要在你的组件中手动订阅它们。尽管通常可以使用 Effect 来实现此功能，但 React 为此针对性地提供了一个 Hook 用于订阅外部 store。
与手动使用 Effect 将外部可变数据同步到 React state 相比，这种方法能减少错误。
删除 Effect 并将其替换为调用 [`useSyncExternalStore`](https://zh-hans.react.dev/reference/react/useSyncExternalStore)
通常，你可以写一个像上面的 `useOnlineStatus()` 这样的自定义 Hook，这样你就不需要在各个组件中重复写这些代码。

```js
function useOnlineStatus() {
  // ✅ 非常好：用内置的 Hook 订阅外部 store
  return useSyncExternalStore(
    subscribe, // 只要传递的是同一个函数，React 不会重新订阅
    () => navigator.onLine, // 如何在客户端获取值
    () => true // 如何在服务端获取值
  );
}
```

**获取数据**时使用useEffect的方法

假设你快速地输入 `“hello”`。那么 `query` 会从 `“h”` 变成 `“he”`，`“hel”`，`“hell”` 最后是 `“hello”`。这会触发一连串不同的数据获取请求，但无法保证对应的返回顺序。例如，`“hell”` 的响应可能在 `“hello”` 的响应 **之后** 返回。由于它的 `setResults()` 是在最后被调用的，你将会显示错误的搜索结果。这种情况被称为 “[竞态条件](https://en.wikipedia.org/wiki/Race_condition)”：两个不同的请求 “相互竞争”，并以与你预期不符的顺序返回。

**为了修复这个问题，你需要添加一个 清理函数 来忽略较早的返回结果：**

```js
  useEffect(() => {
    // 🔴 避免：没有清除逻辑的获取数据
    fetchResults(query, page).then(json => {
      setResults(json);
    });
  }, [query, page]);



  useEffect(() => {
    let ignore = false;
    fetchResults(query, page).then(json => {
      if (!ignore) {
        setResults(json);
      }
    });
    return () => {
      ignore = true;
    };
  }, [query, page]);
```

处理竞态条件并不是实现数据获取的唯一难点。你可能还需要考虑缓存响应结果（使用户点击后退按钮时可以立即看到先前的屏幕内容），如何在服务端获取数据（使服务端初始渲染的 HTML 中包含获取到的内容而不是加载动画），以及如何避免网络瀑布（使子组件不必等待每个父组件的数据获取完毕后才开始获取数据）。

如果你不使用框架（也不想开发自己的框架），但希望使从 Effect 中获取数据更符合人类直觉，请考虑将获取逻辑提取到一个自定义 Hook 中。
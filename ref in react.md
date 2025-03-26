
- Vue 的 `ref` 既可以用来创建响应式数据，也可以用来访问 DOM/组件实例。
- React 的 `useRef` **不会** 影响组件的响应式渲染，主要用于 DOM 访问。


你可以通过从 React 导入 `useRef` Hook 来为你的组件添加一个 ref：

```
import { useRef } from 'react';
```

在你的组件内，调用 `useRef` Hook 并传入你想要引用的初始值作为唯一参数。例如，这里的 ref 引用的值是“0”：

```
const ref = useRef(0);
```

`useRef` 返回一个这样的对象:

```
{   current: 0 // 你向 useRef 传入的值}
```

类似于vue中的value，访问ref的值需要用到current
这个值是有意被设置为可变的，意味着你既可以读取它也可以写入它。

与 state 不同的是，ref 是一个普通的 JavaScript 对象，具有可以被读取和修改的 `current` 属性。

请注意，**组件不会在每次递增时重新渲染。** 与 state 一样，React 会在每次重新渲染之间保留 ref。但是，设置 state 会重新渲染组件，更改 ref 不会！

## ref 和 state 的不同之处 

也许你觉得 ref 似乎没有 state 那样“严格” —— 例如，你可以改变它们而非总是必须使用 state 设置函数。但在大多数情况下，我们建议你使用 state。ref 是一种“脱围机制”，你并不会经常用到它。 以下是 state 和 ref 的对比：

| ref                                                  | state                                                                                         |
| ---------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| `useRef(initialValue)`返回 `{ current: initialValue }` | `useState(initialValue)` 返回 state 变量的当前值和一个 state 设置函数 ( `[value, setValue]`)                 |
| 更改时不会触发重新渲染                                          | 更改时触发重新渲染。                                                                                    |
| 可变 —— 你可以在渲染过程之外修改和更新 `current` 的值。                  | “不可变” —— 你必须使用 state 设置函数来修改 state 变量，从而排队重新渲染。                                               |
| 你不应在渲染期间读取（或写入） `current` 值。                         | 你可以随时读取 state。但是，每次渲染都有自己不变的 state [快照](https://zh-hans.react.dev/learn/state-as-a-snapshot)。 |

- ref 是一种脱围机制，用于保留不用于渲染的值。 你不会经常需要它们。
- ref 是一个普通的 JavaScript 对象，具有一个名为 `current` 的属性，你可以对其进行读取或设置。
- 你可以通过调用 `useRef` Hook 来让 React 给你一个 ref。
- 与 state 一样，ref 允许你在组件的重新渲染之间保留信息。
- 与 state 不同，设置 ref 的 `current` 值不会触发重新渲染。
- 不要在渲染过程中读取或写入 `ref.current`。这使你的组件难以预测。

# 使用 ref 操作 DOM

有时你可能需要访问由 React 管理的 DOM 元素 —— 例如，让一个节点获得焦点、滚动到它或测量它的尺寸和位置。

```
const myRef = useRef(null);
```

最后，将 ref 作为 `ref` 属性值传递给想要获取的 DOM 节点的 JSX 标签：

```
<div ref={myRef}>
```

当 React 为这个 `<div>` 创建一个 DOM 节点时，React 会把对该节点的引用放入 `myRef.current`。

然后，你可以从 [事件处理器](https://zh-hans.react.dev/learn/responding-to-events) 访问此 DOM 节点，并使用在其上定义的内置[浏览器 API](https://developer.mozilla.org/docs/Web/API/Element)。

```js
// 你可以使用任意浏览器 API，例如：
myRef.current.scrollIntoView();
```

只要在 **某个 DOM 元素或组件** 上设置了 `ref` 属性，该 `ref` 变量就会引用这个 DOM 或组件实例。

### 示例: 使文本输入框获得焦点

点击按钮，使输入框获得焦点

```js
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        聚焦输入框
      </button>
    </>
  );
}
```

### 示例: 滚动至一个元素

点击按钮会调用浏览器的 [`scrollIntoView()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollIntoView) 方法，在相应的 DOM 节点上将它们居中显示在视口中
```js
import { useRef } from 'react';

export default function CatFriends() {
  const firstCatRef = useRef(null);
  const secondCatRef = useRef(null);
  const thirdCatRef = useRef(null);

  function handleScrollToFirstCat() {
    firstCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function handleScrollToSecondCat() {
    secondCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function handleScrollToThirdCat() {
    thirdCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  return (
  ···
```
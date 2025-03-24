set含义是，将在下一次渲染时，将渲染为set值
每次渲染后，State会更新为当前渲染值

反之，在下次渲染前，即便经过了set，调用的State值仍为当前已渲染的值，
并且延时函数也不会影响，即时下次渲染完成了，延时函数调用时仍然是上次的State

也是因为这个特性，同一个handle内多个set往往是无效的
但有时你可能会希望在下次渲染加入队列之前对 state 的值执行多次操作。为此，了解 React 如何批量更新 state 会很有帮助。

**React 会等到事件处理函数中的** 所有 **代码都运行完毕再处理你的 state 更新。** 这就是重新渲染只会发生在所有这些 `setNumber()` 调用 **之后** 的原因。

这让你可以更新多个 state 变量——甚至来自多个组件的 state 变量——而不会触发太多的 重新渲染。但这也意味着只有在你的事件处理函数及其中任何代码执行完成 **之后**，UI 才会更新。这种特性也就是 **批处理**，它会使你的 React 应用运行得更快。

## 在下次渲染前多次更新同一个State

这是一个不常见的用例，但是如果你想在下次渲染之前多次更新同一个 state，你可以像 `setNumber(n => n + 1)` 这样传入一个根据队列中的前一个 state 计算下一个 state 的 **函数**，而不是像 `setNumber(number + 1)` 这样传入 **下一个 state 值**。这是一种告诉 React “用 state 值做某事”而不是仅仅替换它的方法。

```
setNumber(n => n + 1);
setNumber(n => n + 1);
setNumber(n => n + 1);
```

下面是 React 在执行事件处理函数时处理这几行代码的过程：

1. `setNumber(n => n + 1)`：`n => n + 1` 是一个函数。React 将它加入队列。
2. `setNumber(n => n + 1)`：`n => n + 1` 是一个函数。React 将它加入队列。
3. `setNumber(n => n + 1)`：`n => n + 1` 是一个函数。React 将它加入队列。

当你在下次渲染期间调用 `useState` 时，React 会遍历队列。之前的 `number` state 的值是 `0`，所以这就是 React 作为参数 `n` 传递给第一个更新函数的值。然后 React 会获取你上一个更新函数的返回值，并将其作为 `n` 传递给下一个更新函数

命名惯例：内部的函数变量以set后单词的首字母命名
setEnabled(e => !e);  
setLastName(ln => ln.reverse());  
setFriendCount(fc => fc * 2);

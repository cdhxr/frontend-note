---
tags:
  - react
date: 2025-03-24
---
# 声明式地考虑 UI

你已经从上面的例子看到如何去实现一个表单了，为了更好地理解如何在 React 中思考，接下来你将会学到如何用 React 重新实现这个 UI：

1. **定位**你的组件中不同的视图状态
2. **确定**是什么触发了这些 state 的改变
3. **表示**内存中的 state（需要使用 `useState`）
4. **删除**任何不必要的 state 变量
5. **连接**事件处理函数去设置 state

**idea：** 
- 存在一个status变量，status映射着不同的UI界面
- 人为输入（通常涉及事件处理函数）？计算机输入？触发状态改变（State更新）的因素，理解完整的状态流
	- ![[流程图从左到右共有 5 个节点。第一个 empty 节点通过 start typing 分支与 typing 节点相连。typing 节点则.png]]
- 先从添加足够多的 state 开始，**确保**所有可能的视图状态囊括其中，用`useState`定义每个State变量
- 重构State，删除不必要的State
	- **这个 state 是否会导致矛盾**？
		- `isTyping` 与 `isSubmitting` 的状态不能同时为 `true`。
		- 矛盾的产生通常说明了这个 state 没有足够的约束条件。
		- 两个布尔值有四种可能的组合，但是只有三种对应有效的状态。
		- 你可以将他们合并到一个 `'status'` 中，它的值必须是 `'typing'`、`'submitting'` 以及 `'success'` 这三个中的一个。
	- **相同的信息是否已经在另一个 state 变量中存在**？
		- 另一个矛盾：`isEmpty` 和 `isTyping` 不能同时为 `true`。
		- 通过使它们成为独立的 state 变量，可能会导致它们不同步并导致 bug。
		- 幸运的是，你可以移除 `isEmpty` 转而用 `message.length === 0`。
	- **你是否可以通过另一个 state 变量的相反值得到相同的信息**？
		- `isError` 是多余的，因为你可以检查 `error !== null`。

```js
const [answer, setAnswer] = useState('');
const [error, setError] = useState(null);
const [status, setStatus] = useState('typing'); // 'typing', 'submitting', or 'success'
```

- 最后，创建事件处理函数去设置 state 变量。

[详细解释](https://zh-hans.react.dev/learn/reacting-to-input-with-state#step-3-represent-the-state-in-memory-with-usestate)

[[原生 stage management hooks]]
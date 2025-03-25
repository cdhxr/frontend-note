
- 只要在相同位置渲染的是相同组件， React 就会保留状态。
	- 状态与渲染树中的位置相关
	- 相同位置的相同组件会使得state被保留下来
	- 相同位置的不同组件会使state重置
- state 不会被保存在 JSX 标签里。它与你在树中放置该 JSX 的位置相关联。
- 你可以通过为一个子树指定一个不同的 key 来重置它的 state。

```JS
{isPlayerA ? (
  <Counter key="Taylor" person="Taylor" />
) : (
  <Counter key="Sarah" person="Sarah" />
)}	
```

指定一个 `key` 能够让 React 将 `key` 本身而非它们在父组件中的顺序作为位置的一部分。这就是为什么尽管你用 JSX 将组件渲染在相同位置，但在 React 看来它们是两个不同的计数器。
因此它们永远都不会共享 state。
每当一个计数器出现在屏幕上时，它的 state 会被创建出来。
每当它被移除时，它的 state 就会被销毁。
在它们之间切换会一次又一次地使它们的 state 重置。




- 不要嵌套组件的定义，否则你会意外地导致 state 被重置。


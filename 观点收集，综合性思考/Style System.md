
现在的UI或者样式库的功能大致可以分为三类

- CSS的扩展：扩展了CSS的功能 —— taliwind
- Headless Behaviors：只处理行为逻辑，而不提供任何样式或布局。—— radix
- themselves：CSS的封装 —— bootstrap

# Taliwind

原生CSS本质上和组件系统冲突，因为你不得不在不同地方定义组件

CSS 是**全局的、无作用域隔离的**，这跟组件**局部封装、独立性强**的理念是**冲突**的。

 并且你还要确保它们能够**正确对接、重叠一致**。

比如：

- 你 JS 里写了 `<div class="card">`，CSS 就得恰好也写了 `.card`；
- 样式的命名不能冲突；
- JS 操作的 DOM 结构不能跟 CSS 的期望结构不一致。

这种“搭配好”是**人工完成的，很容易错**。

在React中taliwind CSS就是最好的方案，在Vue中则通过单文件组件解决

随着Taliwind的开放性和扩展性，他已经不再是一种CSS的写法，而可以被视作一种CSS extension，CSS++


# best practice？

三者各有各的缺陷

我们希望的是能够引入一个style Solution，可以和组件集中逻辑，可以自定义样式，用起来又像是CSS封装好的组件

这就是shadCN做的好的地方

shadCN不同于其他库引入代码至node module ，他将其放在src，这意味着他是可以随意修改的，并且他的library不是一个shadCN，而就是一些组件库，radix等等的组合，这允许了他能够有以上三个特性
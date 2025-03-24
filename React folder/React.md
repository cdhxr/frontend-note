
React 可以改变你对可见设计和应用构建的思考。当你使用 React 构建用户界面时，你首先
会把它分解成一个个 **组件**，然后，你需要把这些组件连接在一起，使数据流经它们。

# React 渲染流程

想象一下，你的组件是厨房里的厨师，把食材烹制成美味的菜肴。在这种场景下，React 就是一名服务员，他会帮客户们下单并为他们送来所点的菜品。这种请求和提供 UI 的过程总共包括三个步骤：

1. **触发** 一次渲染（把客人的点单分发到厨房）
2. **渲染** 组件（在厨房准备订单）
3. **提交** 到 DOM（将菜品放在桌子上）

详情：[渲染和提交](https://zh-hans.react.dev/learn/render-and-commit)

# React的哲学——纯函数

**传入组件的变量的值不应该是动态的**（有相应需求用State）

**渲染（Render）只是计算组件 UI 应该是什么样子，而不应该直接修改 DOM 或执行其他副作用操作**。

---

### **🔹 如何用上面的例子来理解这句话？**

在 **第一种写法**：

```jsx
export default function Clock({ time }) {
  let hours = time.getHours();
  let className = hours >= 0 && hours <= 6 ? 'night' : 'day';

  return (
    <h1 className={className}>
      {time.toLocaleTimeString()}
    </h1>
  );
}
```

#### **✅ 符合“渲染是一种计算过程”**

- **渲染时，组件只是计算 `className` 和 `h1` 的内容**，然后交给 React **决定如何更新 UI**。
- 这里的 `className` 计算是**纯函数**，不会对外部产生影响，只是**描述 UI 该是什么样子**。
- React 内部会**对比新旧虚拟 DOM**，然后**最小化真实 DOM 变更**，保证高效更新。

---

在 **第二种写法**：

```jsx
export default function Clock({ time }) {
  let hours = time.getHours();
  
  if (hours >= 0 && hours <= 6) {
    document.getElementById('time').className = 'night';
  } else {
    document.getElementById('time').className = 'day';
  }

  return (
    <h1 id="time">
      {time.toLocaleTimeString()}
    </h1>
  );
}
```

#### **❌ 违反“渲染是一种计算过程”**

- **渲染时，组件不只是计算 UI，而是试图直接修改 DOM** (`document.getElementById`)。
- 这样做会带来**副作用**，即：
    - `document.getElementById('time')` 可能为 `null`，导致错误。
    - 手动修改 `className` 可能与 React 的渲染机制**冲突**，导致 React **下一次渲染覆盖掉手动修改的 className**。
    - 直接操作 DOM 可能影响性能，因为 React 期望**通过虚拟 DOM** 计算最优更新方式，而手动操作可能绕过这个机制。

---

### **🔹 结论**

**渲染应该是一个“纯计算”过程，而不是“做事情”**：

- **计算**：React 组件的 `render` 方法（或者函数组件的 `return`）应该**仅仅计算**组件的 UI 结构，而不应该**直接修改 DOM** 或执行其他副作用操作。
- **不要做事情**：手动修改 DOM、执行网络请求、修改全局变量等操作，不应该发生在渲染过程中，而应该放到 **`useEffect`** 这样的 React 生命周期钩子里去执行。

因此，**第一种写法符合 React 设计理念**，而**第二种写法则违背了 React 渲染的核心原则**。


# 组件

React 组件是返回tag的 JavaScript 函数

```js
function MyButton() {
  return (
    <button>我是一个按钮</button>
  );
}
```

声明了 `MyButton`，现在把它嵌套到另一个组件中：

```js
export default function MyApp() {
  return (
    <div>
      <h1>欢迎来到我的应用</h1>
      <MyButton />
    </div>
  );
}
```

你可能已经注意到 `<MyButton />` 是以大写字母开头的。你可以据此识别 React 组件。**React 组件必须以大写字母开头，而 HTML 标签则必须是小写字母。**

# JSX

上面所使用的标签语法被称为 _JSX_

JSX 比 HTML 更加严格。
- 你必须闭合标签，如 `<br />`。
- 你的组件也不能返回多个 JSX 标签。
- 你必须将它们包裹到一个共享的父级中，比如 `<div>...</div>` 或使用空的 `<>...</>` 包裹
- 如果你有大量的 HTML 需要移植到 JSX 中，你可以使用 [在线转换器](https://transform.tools/html-to-jsx)

# 样式

和原生的方式一致，用link标签引入css文件，通过选择器来确定样式，React没有规定

# 数据展示语法 `{ }`

JSX 会让你把标签放到 JavaScript 中。
而大括号会让你 “回到” JavaScript 中，这样你就可以从你的代码中嵌入一些变量并展示给用户。

```js
return (
  <h1>
    {user.name}
  </h1>
);
```

大括号内可以传递对象的值，以及对象相关的表达式

- 在 JSX 的大括号内引用 JavaScript 变量
	- 当你想把一个字符串属性传递给 JSX 时，把它放到单引号或双引号中
	- **使用 JavaScript 变量** ：`src={avatar}`
- 在 JSX 的大括号内调用 JavaScript 函数
- 在 JSX 的大括号内使用 JavaScript 对象

### 可以在哪使用大括号

在 JSX 中，只能在以下两种场景中使用大括号：

1. 用作 JSX 标签内的**文本**：`<h1>{name}'s To Do List</h1>` 是有效的，但是 `<{tag}>Gregorio Y. Zara's To Do List</{tag}>` 无效。
2. 用作紧跟在 `=` 符号后的 **属性**：`src={avatar}` 会读取 `avatar` 变量，但是 `src="{avatar}"` 只会传一个字符串 `{avatar}`。

## 使用 “双大括号”：JSX 中的 CSS 和 对象

传递对象时需要双重花括号

除了字符串、数字和其它 JavaScript 表达式，你甚至可以在 JSX 中传递对象。对象也用大括号表示，例如 `{ name: "Hedy Lamarr", inventions: 5 }`。因此，为了能在 JSX 中传递，你必须用另一对额外的大括号包裹对象：`person={{ name: "Hedy Lamarr", inventions: 5 }}`。

# 条件渲染

React 没有特殊的语法来编写条件语句，因此你使用的就是普通的 JavaScript 代码。

```js
<div>
  {isLoggedIn ? (
    <AdminPanel />
  ) : (
    <LoginForm />
  )}
</div>
```

用了大括号语法以及简单的三元运算符，决定渲染方式

- 在 JSX 中，`{cond ? <A /> : <B />}` 表示 _“当 `cond` 为真值时, 渲染 `<A />`，否则 `<B />`”_。
- 在 JSX 中，`{cond && <A />}` 表示 _“当 `cond` 为真值时, 渲染 `<A />`，否则不进行渲染”_。

# 渲染列表

[[react-for detail]]

你将依赖 JavaScript 的特性，例如 [`for` 循环](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for) 和 [array 的 `map()` 函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 来渲染组件列表。

产品数组：

```js
const products = [
  { title: 'Cabbage', id: 1 },
  { title: 'Garlic', id: 2 },
  { title: 'Apple', id: 3 },
];
```

在你的组件中，使用 `map()` 函数将这个数组转换为 `<li>` 标签构成的列表:

```js
const listItems = products.map(product =>
  //将一个属性设为键，一个设为值渲染
  <li key={product.id}>
    {product.title}
  </li>
);

return (
  <ul>{listItems}</ul>
);
```

注意， `<li>` 有一个 `key` 属性。

# 响应事件

[[event in react]]

```js
function MyButton() {
  function handleClick() {
    alert('You clicked me!');
  }

  return (
    <button onClick={handleClick}>
      点我
    </button>
  );
}
```

可以在组件内声明 **事件处理** 函数来响应事件

事件处理函数有如下特点:

- 通常在你的组件 **内部** 定义。
- 名称以 `handle` 开头，后跟事件名称。

你也可以在 JSX 中定义一个内联的事件处理函数：

```js
<button onClick={function handleClick() {
  alert('你点击了我！');
}}>

<button onClick={() => {
  alert('你点击了我！');
}}>
```

以上所有方式都是等效的。当函数体较短时，内联事件处理函数会很方便。

| 传递一个函数（正确）                       | 调用一个函数（错误）                         |
| -------------------------------- | ---------------------------------- |
| `<button onClick={handleClick}>` | `<button onClick={handleClick()}>` |
|                                  |                                    |

# State

[[State in react]]

# 组件通信

## 导入和导出（import and export）

不同导出方式：[[export]]

|语法|导出语句|导入语句|
|---|---|---|
|默认|`export default function Button() {}`|`import Button from './Button.js';`|
|具名|`export function Button() {}`|`import { Button } from './Button.js';`|

**同一文件中，有且仅有一个默认导出，但可以有多个具名导出！**


## props传递

[[Props in React]]
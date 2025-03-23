
React 可以改变你对可见设计和应用构建的思考。当你使用 React 构建用户界面时，你首先会把它分解成一个个 **组件**，然后，你需要把这些组件连接在一起，使数据流经它们。

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

# 渲染列表

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

# 更新界面

作用类似于vue中的响应式变量（ref，reactive）

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
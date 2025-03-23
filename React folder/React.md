
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
## 流程

这里我们有一个列表。

```html
<ul>
  <li>凯瑟琳·约翰逊: 数学家</li>
  <li>马里奥·莫利纳: 化学家</li>
  <li>穆罕默德·阿卜杜勒·萨拉姆: 物理学家</li>
  <li>珀西·莱温·朱利亚: 化学家</li>
  <li>苏布拉马尼扬·钱德拉塞卡: 天体物理学家</li>
</ul>
```

这些列表项之间唯一的区别就是其中的内容/数据。
未来你可能会碰到很多类似的情况，在那些场景中，你想基于不同的数据渲染出相似的组件，比如评论列表或者个人资料的图库。

这里给出一个由数组生成一系列列表项的简单示例：

1. 首先，把数据 **存储** 到数组中，并让其变得更加结构化：

```js
const people = [{
  id: 0,
  name: '凯瑟琳·约翰逊',
  profession: '数学家',
}, {
  id: 1,
  name: '马里奥·莫利纳',
  profession: '化学家',
}, {
  id: 2,
  name: '穆罕默德·阿卜杜勒·萨拉姆',
  profession: '物理学家',
}, {
  id: 3,
  name: '珀西·莱温·朱利亚',
  profession: '化学家',
}, {
  id: 4,
  name: '苏布拉马尼扬·钱德拉塞卡',
  profession: '天体物理学家',
}];
```

2. 过滤得到新数组，**遍历** `people` 这个数组中的每一项，并获得一个新的 JSX 节点数组 `chemists`：

```js
const chemists = people.filter(person =>
  person.profession === '化学家'
);
```

3. 接下来 **用 map 方法遍历** `chemists` 数组，并用 `key` 保持列表项的顺序 :

```js
const listItems = chemists.map(person =>
  <li key={person.id}>
     <img
       src={getImageUrl(person)}
       alt={person.name}
     />
     <p>
       <b>{person.name}:</b>
       {' ' + person.profession + ' '}
       因{person.accomplishment}而闻名世界
     </p>
  </li>
);
```


4. 把 `listItems` 用 `<ul>` 包裹起来，然后 **返回** 它：

```js
return <ul>{listItems}</ul>;
```

## **key**

直接放在 `map()` 方法里的 JSX 元素一般都需要指定 `key` 值！

不同来源的数据往往对应不同的 key 值获取方式：

- **来自数据库的数据：** 如果你的数据是从数据库中获取的，那你可以直接使用数据表中的主键，因为它们天然具有唯一性。
- **本地产生数据：** 如果你数据的产生和保存都在本地（例如笔记软件里的笔记），那么你可以使用一个自增计数器或者一个类似 [`uuid`](https://www.npmjs.com/package/uuid) 的库来生成 key。

- **key 值在兄弟节点之间必须是唯一的。** 不过不要求全局唯一，在不同的数组中可以使用相同的 key。
- **key 值不能改变**，否则就失去了使用 key 的意义！所以千万不要在渲染时动态地生成 key。

React 里需要 key 和文件夹里的文件需要有文件名的道理是类似的。

###  注意
你可能会想直接把数组项的索引当作 key 值来用，实际上，如果你没有显式地指定 `key` 值，React 确实默认会这么做。但是数组项的顺序在插入、删除或者重新排序等操作中会发生改变，此时把索引顺序用作 key 值会产生一些微妙且令人困惑的 bug。

与之类似，请不要在运行过程中动态地产生 key，像是 `key={Math.random()}` 这种方式。这会导致每次重新渲染后的 key 值都不一样，从而使得所有的组件和 DOM 元素每次都要重新创建。这不仅会造成运行变慢的问题，更有可能导致用户输入的丢失。所以，使用能从给定数据中稳定取得的值才是明智的选择。

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
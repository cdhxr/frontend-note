---
tags:
  - react
---


- 要传递 props，请将它们添加到 JSX，就像使用 HTML 属性一样。
- 要读取 props，请使用 `function Avatar({ person, size })` 解构语法。
- 你可以指定一个默认值，如 `size = 100`，用于缺少值或值为 `undefined` 的 props 。
- 你可以使用 `<Avatar {...props} />` JSX 展开语法转发所有 props，但不要过度使用它！
- 像 `<Card><Avatar /></Card>` 这样的嵌套 JSX，将被视为 `Card` 组件的 `children` prop。
- Props 是只读的时间快照：每次渲染都会收到新版本的 props。
- 你不能改变 props。当你需要交互性时，你可以设置 state。

### **基本使用**

可以分两步给 `Avatar`组件 一些 props

#### 步骤 1: （在子组件tag传输Props）将 props 传递给子组件

首先，将一些 props 传递给 `Avatar`。例如，让我们传递两个 props：`person`（一个对象）和 `size`（一个数字）：

```js
export default function Profile() {
  return (
    <Avatar
      person={{ name: 'Lin Lanying', imageId: '1bX5QH6' }}
      size={100}
    />
  );
}
```

#### 步骤 2: （在子组件文件内部接受Props渲染）在子组件中读取 props 

你可以通过在 `function Avatar` 之后直接列出它们的名字 `person, size` 来读取这些 props。这些 props 在 `({` 和 `})` 之间，并由逗号分隔。这样，你可以在 `Avatar` 的代码中使用它们，就像使用变量一样。

```js
function Avatar({ person, size }) {  
   // 在这里 person 和 size 是可访问的
}
```

向使用 `person` 和 `size` props 渲染的 `Avatar` 添加一些逻辑，你就完成了。

Props 使你独立思考父组件和子组件。
### **使用 JSX 展开语法传递 props** 

有时候，传递 props 会变得非常重复：
```js
      <Avatar
        person={person}
        size={size}
        isSepia={isSepia}
        thickBorder={thickBorder}
      />
```

传递所有Props时可以写为`<Avatar {...props} />`，该语法为jsx展开语法

### **将 JSX 作为子组件传递，利用children Prop**

当你将组件嵌套在 JSX 标签中时，父组件将在名为 `children` 的 prop 中接收到该内容
#### **`Card` 组件**

```jsx
import Avatar from './Avatar.js';

function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

export default function Profile() {
  return (
    <Card>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi',
          imageId: 'YfeOqp2'
        }}
      />
    </Card>
  );
}
```

- 这个 `Card` 组件是一个 **通用的卡片容器**，可以包裹任何内容。
- 它的 **`children`** 属性是一个 **React 组件的特性**，代表**嵌套在 `<Card>` 组件中的内容**。

React中任何组件必须返回一个jsx，这样导致没办法去创建通用的容器型组件，因为里面装的组件是不一定的，没办法确定内部的jsx，所以需要`children` prop作为一个抽象不确定组件。

### **Props 如何随时间变化**

Props 反映了组件在任何时间点的数据，并不仅仅是在开始时。

然而，props 是 [不可变的](https://en.wikipedia.org/wiki/Immutable_object)。
当一个组件需要改变它的 props（例如，响应用户交互或新数据）时，它不得不“请求”它的父组件传递 **不同的 props** —— 一个新对象！
它的旧 props 将被丢弃，最终 JavaScript 引擎将回收它们占用的内存。

**不要尝试“更改 props”。** 当你需要响应用户输入（例如更改所选颜色）时，你可以“设置 state”
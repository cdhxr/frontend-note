## 事件处理函数与 props

由于事件处理函数声明于组件内部，因此它们可以直接访问组件的 props。

```js
function AlertButton({ message, children }) {
  return (
    <button onClick={() => alert(message)}>
      {children}
    </button>
  );
}
```

通常，我们会在父组件中定义子组件的事件处理函数。
此时，我们需要将事件处理函数本身作为Props传递。

```js
//你的 `Button` 组件接收一个名为 `onClick` 的 prop。
//它直接将这个 prop 以 `onClick={onClick}` 方式传递给浏览器内置的 `<button>`。
//当点击按钮时，React 会调用传入的函数。

function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}

function UploadButton() {
  return (
    <Button onClick={() => alert('正在上传！')}>
      上传图片
    </Button>
  );
}
```

内置组件（`<button>` 和 `<div>`）仅支持 [浏览器事件名称](https://zh-hans.react.dev/reference/react-dom/components/common#common-props)，例如 `onClick`。但是，当你构建自己的组件时，你可以按你个人喜好命名事件处理函数的 prop。

```js
function Button({ onSmash, children }) {
  return (
    <button onClick={onSmash}>
      {children}
    </button>
  );
}
```

## 事件传递

自然的，事件会冒泡

事件处理函数接收一个 **事件对象** 作为唯一的参数。按照惯例，它通常被称为 `e` ，代表 “event”（事件）。你可以使用此对象来读取有关事件的信息。

这个事件对象还允许你阻止传播。如果你想阻止一个事件到达父组件，你需要像下面 `Button` 组件那样调用 `e.stopPropagation()`

```js
function Button({ onClick, children }) {
  return (
    <button onClick={e => {
      e.stopPropagation();//调用onClick前执行了其他语句
      onClick();
    }}>
      {children}
    </button>
  );
}
```

你也可以在调用父元素 `onClick` 函数之前，向这个处理函数添加更多代码。此模式是事件传播的另一种 **替代方案** 。它让子组件处理事件，同时也让父组件指定一些额外的行为。

### 阻止默认事件

某些浏览器事件具有与事件相关联的默认行为。例如，点击 `<form>` 表单内部的按钮会触发表单提交事件，默认情况下将重新加载整个页面

你可以调用事件对象中的 `e.preventDefault()` 来阻止这种情况发生：

```js
export default function Signup() {
  return (
    <form onSubmit={e => {
      e.preventDefault();
      alert('提交表单！');
    }}>
      <input />
      <button>发送</button>
    </form>
  );
}
```

- `e.stopPropagation()` 阻止触发绑定在外层标签上的事件处理函数。
- `e.preventDefault()` 阻止少数事件的默认浏览器行为。

## 处理函数可以存在动态变量，即不是纯函数

与渲染函数不同，事件处理函数不需要是 [纯函数](https://zh-hans.react.dev/learn/keeping-components-pure)，因此它是用来 _更改_ 某些值的绝佳位置。例如，更改输入框的值以响应键入，或者更改列表以响应按钮的触发。但是，为了更改某些信息，你首先需要某种方式存储它。在 React 中，这是通过 [state（组件的记忆）](https://zh-hans.react.dev/learn/state-a-components-memory) 来完成的。

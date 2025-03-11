---
tags:
  - vue
date: 2025-03-05
---

## why we need v-model

绑定（如 `v-model`）能起到封装作用，是因为它允许父组件与子组件之间传递和同步数据，而不暴露实现细节。通过绑定，子组件的内部实现（如输入框的逻辑、格式化、验证等）被隐藏在封装组件内部，父组件只需要关心数据的输入和输出，而无需了解具体的实现方式。

即将父组件的逻辑，封装在子组件中，使父组件简单易用

## 写法

`defineModel()` 返回的值是一个 ref。
它可以像其他 ref 一样被访问以及修改，
不过它能起到在父组件和当前变量之间的双向绑定的作用：

- 它的 `.value` 和父组件的 `v-model` 的值同步；
- 当它被子组件变更了，会触发父组件绑定的值一起更新。

```vue
<!-- Child.vue -->
<script setup>
const model = defineModel()

function update() {
  model.value++
}
</script>

<template>
  <div>Parent bound v-model is: {{ model }}</div>
  <button @click="update">Increment</button>
</template>
```

```html
<!-- Parent.vue -->
<Child v-model="countModel" />
```

如该例子，父组件中countModel和model.value的值绑定了

这意味着你也可以用 `v-model` 把这个 ref 绑定到一个原生 input 元素上，在提供相同的 `v-model` 用法的同时轻松包装原生 input 元素：

```vue
<script setup>
const model = defineModel()
</script>

<template>
  <input v-model="model" />
</template>
```

|**优势**|**详细说明**|
|---|---|
|**提高复用性**|组件可以在多个地方使用，避免重复代码|
|**封装输入逻辑**|处理格式化、去除空格、只允许数字等|
|**内置验证**|组件内部可添加错误提示，避免在每个页面写验证逻辑|
|**扩展功能**|可添加清除按钮、图标、输入框样式|
|**适配 UI 组件库**|适配 Element Plus、Ant Design Vue 等|

如果你的项目有多个表单输入框，**封装 `<input>` 组件是一个非常好的实践**，可以减少重复代码，增加可维护性 🚀！

## `v-model` 的参数

### 数据流解析

- **父组件 (`App.vue` )**
    
    - `bookTitle` 是一个 `ref`
    - `bookTitle` 绑定到了 `MyComponent` 组件的 `v-model:title`，即传递 `bookTitle` 作为 `title` 并建立双向绑定。
- **子组件 (`MyComponent.vue`)**
    
    - `defineModel('title')` 定义了一个 `title` 变量，它会自动与 `v-model:title` 进行双向绑定。
    - `v-model="title"` 绑定到 `input` 元素，这意味着用户修改输入框的值时，`title` 也会随之变化。

用户在 `input` 里修改文本，数据会通过 `v-model:title` 传递回 `bookTitle`，使得父组件的 `h1` 也会实时更新。
### 
组件上的 `v-model` 也可以接受一个参数：

```html
<h1>{{ bookTitle }}</h1>
<MyComponent v-model:title="bookTitle" />
```

在子组件中，我们可以通过将字符串作为第一个参数传递给 `defineModel()` 来支持相应的参数：

```vue
<!-- MyComponent.vue -->
<script setup>
const title = defineModel('title')
</script>

<template>
  <input type="text" v-model="title" />
</template>
```

如果需要额外的 prop 选项，应该在 model 名称之后传递：

```ts
const title = defineModel('title', { required: true })
```

## 用于输入绑定

- 文本类型的 `<input>` 和 `<textarea>` 元素会绑定 `value` property 并侦听 `input` 事件；
- `<input type="checkbox">` 和 `<input type="radio">` 会绑定 `checked` property 并侦听 `change` 事件；
- `<select>` 会绑定 `value` property 并侦听 `change` 事件。

基本用法包括：文本，多行文本，复选框，单选按钮，选择器
https://cn.vuejs.org/guide/essentials/forms.html

值绑定：

有时我们可能希望将值绑定到当前组件实例上的动态数据。
这可以通过使用 `v-bind` 来实现。
此外，使用 `v-bind` 还使我们可以将选项值绑定为非字符串的数据类型。

复选框，单选按钮，选择器选项，会根据所按下的事件渲染发生变化（动态），
详情如上文档

修饰符可以用作input后的类型转换
- `.lazy`，在 "change" 事件后同步更新而不是 "input"
- `.number`，用户输入自动转换为数字
- `.trim`，自动去除用户输入内容中两端的空格

语法如下
```vue
<!-- 在 "change" 事件后同步更新而不是 "input" -->
<input v-model.lazy="msg" />
```

### 自定义处理v-model修饰符

读文档即可
https://cn.vuejs.org/guide/components/v-model.html
---
tags:
  - vue
date: 2025-03-13
---

# why we need slot

组件任意类型的 JavaScript 值作为 props，那么组件如何接受模板内容？

`<slot>` 元素是一个**插槽出口** (slot outlet)，标示了父元素提供的**插槽内容** (slot content) 将在哪里被渲染。

使用组件：
```html
<FancyButton>
  Click me! <!-- 插槽内容 -->
</FancyButton>
```

FancyButton.vue模板：
```vue
<template>
  <button class="fancy-btn">
    <slot></slot> <!-- 插槽出口 -->
  </button>
</template>
```


上方两段代码的渲染结果为

```html
<button class="fancy-btn">Click me!</button>
```

通过使用插槽，`<FancyButton>` 仅负责渲染外层的 `<button>` (以及相应的样式)，而其内部的内容由父组件提供。

类似于，函数传参的过程

# 插槽作用范围

插槽内容可以访问到父组件的数据作用域，因为插槽内容本身是在父组件模板中定义的。
（message是父组件的数据）
```html
<span>{{ message }}</span>
<FancyButton>{{ message }}</FancyButton>
```

# 具名插槽和默认插槽

可以设置为插槽默认内容：
```html
<slot> Submit <!-- 默认内容 --> </slot>
```

对于这种场景，`<slot>` 元素可以有一个特殊的 attribute `name`，用来给各个插槽分配唯一的 ID，以确定每一处要渲染的内容

```html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

要为具名插槽传入内容，我们需要使用一个含 `v-slot` 指令的 `<template>` 元素，并将目标插槽的名字传给该指令：

```html
<BaseLayout>
  <template v-slot:header>
    <!-- header 插槽的内容放这里 -->
  </template>
</BaseLayout>
```

v-slot的简写：
`<template #header>`。其意思就是“将这部分模板片段传入子组件的 header 插槽中”。

```vue
<BaseLayout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <!-- 隐式的默认插槽 -->
  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</BaseLayout>
```

如果一个组件同时用到了默认插槽和具名插槽时，所有位于顶级的非 `<template>` 节点都被隐式地视为默认插槽的内容。

# 条件插槽

在 Vue 中，**条件插槽**可以。实现条件插槽的核心在于 `$slot` 属性和 `v-if` 指令的结合使用。

---

## **1. $slots 对象的基本用法**

`$slots` 存储的是**所有传递给组件的插槽内容**，它的结构如下：

```js
this.$slots = {
  default: [VNode, VNode, ...],  // 默认插槽内容（VNode 数组）
  header: [VNode, VNode, ...],   // 具名插槽 'header'
  footer: [VNode, VNode, ...]    // 具名插槽 'footer'
}
```

- 每个插槽（如 `default`、`header`）的值是一个**VNode 数组**，即**虚拟 DOM 节点**。
- 如果某个插槽**未提供内容**，那么 `this.$slots[slotName]` 的值为 `undefined` 或 `null`

在 Vue 中，**条件插槽**可以根据某些条件渲染不同的内容。实现条件插槽的核心在于 `$slot` 属性和 `v-if` 指令的结合使用。

---
## 2. **v-if 指令**

`v-if` 用于**条件渲染**，如果条件为 `false`，Vue **不会**渲染对应的 DOM 元素。

在 `v-if="xx"` 语法中：

- `xx` 必须是**布尔值**或可转为布尔值的表达式
- 可以是 **变量**、**计算属性**、**函数返回值**、甚至 **$slots** 判断插槽是否存在
- 当 `xx` 为 `false`，Vue **不会**渲染这个元素，它不会出现在 DOM 中
- `$slot`是内置的一种特殊处理情况，根据对象的是否存在返回值

---

## 3. **条件插槽的实现**

条件插槽就是基于 `$slots` 和 `v-if` **动态决定是否渲染某个插槽**。

### **示例：实现一个条件插槽**

```vue
<template>
  <div>
    <h2>条件插槽示例</h2>
    
    <!-- 只有当 $slots.default 存在时才渲染 -->
    <slot v-if="$slots.default"></slot>
    <p v-else>没有默认插槽内容</p>
  </div>
</template>
```

### **使用**

```vue
<ConditionSlot>
  <p>我是默认插槽</p>
</ConditionSlot>

<ConditionSlot>
  <!-- 这里没有默认插槽内容 -->
</ConditionSlot>
```

**结果：**

- 第一个 `ConditionSlot` 组件会渲染 `"我是默认插槽"`。
- 第二个 `ConditionSlot` 组件会渲染 `"没有默认插槽内容"`。

---

## 4. **更灵活的条件插槽**

如果你想**按名字动态显示插槽**：

```vue
<template>
  <div>
    <slot v-if="$slots.header" name="header"></slot>
    <slot v-if="$slots.footer" name="footer"></slot>
    <p v-if="!$slots.header && !$slots.footer">没有插槽内容</p>
  </div>
</template>
```

### **使用**

```vue
<FlexibleSlot>
  <template #header><h1>我是 Header</h1></template>
  <template #footer><h3>我是 Footer</h3></template>
</FlexibleSlot>

<FlexibleSlot>
  <!-- 这里没有传入插槽 -->
</FlexibleSlot>
```

**结果：**

- 第一个 `FlexibleSlot` 组件会显示 `"我是 Header"` 和 `"我是 Footer"`。
- 第二个 `FlexibleSlot` 组件会显示 `"没有插槽内容"`。

---

### **总结**

- `$slots[slotName]` 可用于**检查插槽是否存在**。
- `v-if` 可用于**控制插槽是否渲染**。
- 结合 `$slots` 和 `v-if`，可以**动态实现条件插槽**，提升组件的灵活性。

你可以试着自己写一个支持**多种条件插槽**的组件，比如**优先显示某个插槽，若不存在则回退到默认内容**。

# 动态参数名

```vue
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>

  <!-- 缩写为 -->
  <template #[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

- `#[dynamicSlotName]` 用于动态插槽，相当于 `v-slot:[dynamicSlotName]`。
- 通过 `ref` 变量控制 `dynamicSlotName`，可以**动态切换插槽**。
- 适用于**可变插槽**的场景，比如**不同状态下显示不同内容**。

# 作用域插槽

插槽的内容无法访问到子组件的状态。

然而在某些场景下插槽的内容可能想要同时使用父组件域内和子组件域内的数据。要做到这一点，我们需要一种方法来让子组件在渲染时将一部分数据提供给插槽。

![[scoped-slots.B67tIPc5.svg]]
其中greetingMessage为Component的脚本中定义的变量，内部有定义的值，
最终显示的是该值和1

使用v-slot定义的变量名（slortprops）接收传来的Props

通过在slot tag中使用v-bind语法，实现了子传父的通信

具名的情况：v-slot:name="slotProps"，简写写法为

```vue
<template #header="headerProps"> {{ headerProps }} </template>
```

向具名插槽中传入 props：

```vue
<slot name="header" message="hello"></slot>
```

注意插槽上的 `name` 是一个 Vue 特别保留的 attribute，不会作为 props 传递给插槽。因此最终 `headerProps` 的结果是 `{ message: 'hello' }`

如果你同时使用了具名插槽与默认插槽，则需要为默认插槽使用显式的 `<template>` 标签。

```vue
<!-- <MyComponent> template -->
<div>
  <slot :message="hello"></slot>
  <slot name="footer" />
</div>
```

```vue
<MyComponent>
  <!-- 使用显式的默认插槽 -->
  <template #default="{ message }">
    <p>{{ message }}</p>
  </template>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</MyComponent>
```

# 高级列表组件示例

在某些场景中，我们可能想要为子组件传递一些模板片段，让子组件在它们的组件中渲染这些片段。

`<slot>` 元素是一个**插槽出口** (slot outlet)，标示了父元素提供的**插槽内容** (slot content) 将在哪里被渲染。
根据某些条件渲染不同的内容
`<FancyList>`组件：

它会渲染一个列表，
并同时会封装一些
- 加载远端数据的逻辑
- 使用数据进行列表渲染
- 分页或无限滚动
这样更进阶的功能。

然而我们希望它能够保留足够的灵活性，
将对单个列表元素内容和样式的控制权留给使用它的父组件。

```vue
<FancyList :api-url="url" :per-page="10">
  <template #item="{ body, username, likes }">
    <div class="item">
      <p>{{ body }}</p>
      <p>by {{ username }} | {{ likes }} likes</p>
    </div>
  </template>
</FancyList>
```

在 `<FancyList>` 之中，我们可以多次渲染 `<slot>` 并每次都提供不同的数据 (注意我们这里使用了 `v-bind` 来传递插槽的 props)：

```vue
<ul>
  <li v-for="item in items">
    <slot name="item" v-bind="item"></slot>
  </li>
</ul>
```

这里在script里写了个items表存不同数据

上面的 `<FancyList>` 案例同时封装了可重用的逻辑 (数据获取、分页等) 和视图输出，但也将部分视图输出通过作用域插槽交给了消费者组件来管理。

作用域插槽在需要**同时**封装逻辑、组合视图界面时还是很有用，就像上面的 `<FancyList>` 组件那样。


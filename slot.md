
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

插槽内容可以访问到父组件的数据作用域，因为插槽内容本身是在父组件模板中定义的。
（message是父组件的数据）
```html
<span>{{ message }}</span>
<FancyButton>{{ message }}</FancyButton>
```

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


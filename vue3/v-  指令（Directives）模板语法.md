---
tags:
  - vue
date: 2025-03-07
---

在 Vue.js（Vue 2 和 Vue 3）中，`v-` 开头的指令（**Directives**）是用于操作 DOM 的特殊属性，常见的 `v-` 语法包括以下几种：

---

### **1. v-bind**

用于动态绑定 HTML 属性。

**语法：**

```vue
<a v-bind:href="url">点击访问</a>
```

等价于：

```vue
<a :href="url">点击访问</a>
```

> **说明：** `v-bind` 也可以用于动态绑定 `class` 和 `style`，如 `:class="{ active: isActive }"` 或 `:style="{ color: textColor }"`。

---

### **2. v-model**

用于双向数据绑定，常用于表单输入元素。

**语法：**

```vue
<input v-model="message" />
<p>{{ message }}</p>
```

> **说明：** `v-model` 适用于 `<input>`, `<textarea>`, `<select>` 等，并且在 Vue 3 中可以用于组件的 `prop` 绑定（`v-model:propName="value"`）。

---

### **3. v-if / v-else-if / v-else**

条件渲染，根据表达式的真假决定是否渲染元素。

**语法：**

```vue
<p v-if="isShow">显示的内容</p>
<p v-else>隐藏的内容</p>
```

> **说明：** `v-if` 是**真正的条件渲染**，当条件为 `false` 时，元素不会被渲染到 DOM 中。

---

### **4. v-show**

用于控制元素的显示与隐藏，但不会从 DOM 中移除元素（只是修改 `display` 样式）。

**语法：**

```vue
<p v-show="isShow">显示的内容</p>
```

> **区别：** `v-if` **会移除 DOM**，`v-show` **只是修改 CSS `display` 属性**，适用于频繁切换的场景。

---

### **5. v-for**

用于遍历数组或对象。

**语法（遍历数组）：**

```vue
<ul>
  <li v-for="(item, index) in items" :key="index">
    {{ index }} - {{ item }}
  </li>
</ul>
```

**遍历对象：**

```vue
<ul>
  <li v-for="(value, key) in obj" :key="key">
    {{ key }}: {{ value }}
  </li>
</ul>
```

> **注意：** `:key` 是必须的，建议使用唯一值（如 `id`）。

---

### **6. v-on**

用于绑定事件监听器。

**语法：**

```vue
<button v-on:click="handleClick">点击</button>
```

等价于：

```vue
<button @click="handleClick">点击</button>
```

> **修饰符（Modifiers）：**

- `@click.stop` —— 阻止事件冒泡。
- `@submit.prevent` —— 阻止默认提交行为。
- `@keyup.enter` —— 监听 `Enter` 按键。

---

### **7. v-text**

用于替换元素的 `textContent`。

**语法：**

```vue
<p v-text="message"></p>
```

等价于：

```vue
<p>{{ message }}</p>
```

> **区别：** `v-text` 会**覆盖**元素的内容，而 `{{ message }}` 只是插值。

---

### **8. v-html**

用于渲染 HTML 字符串。

**语法：**

```vue
<p v-html="htmlContent"></p>
```

> **注意：** `v-html` 可能会引发 XSS（跨站脚本攻击），不推荐使用不受信任的数据。

---

### **9. v-cloak**

用于在 Vue 还未编译时隐藏插值内容，防止闪烁。

**语法：**

```vue
<p v-cloak>{{ message }}</p>
```

> **配合 CSS 使用：**

```css
[v-cloak] {
  display: none;
}
```

---

### **10. v-pre**

跳过该元素及其子元素的编译。

**语法：**

```vue
<p v-pre>{{ 这段内容不会被解析 }}</p>
```

---

### **11. v-once**

只渲染一次，不会随数据变化而更新。

**语法：**

```vue
<p v-once>{{ message }}</p>
```

> **用途：** 适用于不需要更新的静态内容，可以提升渲染性能。

---

## **总结**

| 指令                          | 作用            |
| --------------------------- | ------------- |
| `v-bind` (`:`)              | 绑定 HTML 属性    |
| `v-model`                   | 双向数据绑定        |
| `v-if / v-else-if / v-else` | 条件渲染          |
| `v-show`                    | 控制显示但不移除 DOM  |
| `v-for`                     | 列表渲染          |
| `v-on` (`@`)                | 事件绑定          |
| `v-text`                    | 更新文本内容        |
| `v-html`                    | 渲染 HTML 字符串   |
| `v-cloak`                   | 隐藏未编译的 Vue 模板 |
| `v-pre`                     | 跳过编译          |
| `v-once`                    | 只渲染一次         |

---

这些 `v-` 指令构成了 Vue.js 最常用的模板语法，帮助我们高效地开发 Vue 应用。你最感兴趣的是哪一部分？
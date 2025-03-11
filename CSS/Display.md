#css

### 常见的 `display` 值及其效果

#### 1. `display: block`

- **特性**：块级元素会独占一行，默认情况下其宽度会扩展到父容器的全部宽度。
- **常见元素**：`<div>`、`<h1>`、`<p>`、`<header>` 等元素默认都是块级元素。
- **应用场景**：适用于需要占据整行的元素，如段落、标题、分隔区域等。

  ```css
  div {
      display: block;
  }
  ```

#### 2. `display: inline`

- **特性**：行内元素不会独占一行，它们只占据内容的宽度，可以和其他元素在同一行显示。行内元素无法设置宽高，`width` 和 `height` 属性对其无效。
- **常见元素**：`<span>`、`<a>`、`<strong>`、`<em>` 等。
- **应用场景**：适用于需要在同一行显示的文本内容或小组件，如链接、按钮文字等。

  ```css
  span {
      display: inline;
  }
  ```

#### 3. `display: inline-block`

- **特性**：结合了 `inline` 和 `block` 的特点。`inline-block` 元素不会独占一行，可以和其他元素并排显示，但可以设置宽度和高度。
- **常见应用**：行内显示的小组件，适合排成一行但需要控制尺寸的元素。
- **应用场景**：适合用于并排的按钮、图标等。

  ```css
  button {
      display: inline-block;
      width: 100px;
      height: 40px;
  }
  ```

#### 4. `display: flex`

- **特性**：`flex` 布局是一种更强大的布局模式，可以在父容器（称为 “flex 容器”）中控制子元素的排列方式和对齐方式。
- **常见应用**：用于创建复杂的、响应式的布局，如横向排列的一组按钮，具有自适应排列的网格等。
- **应用场景**：适用于创建水平或垂直排列的自适应布局。

  ```css
  .container {
      display: flex;
      justify-content: space-between;
      align-items: center;
  }
  ```

#### 5. `display: grid`

- **特性**：`grid` 是一种二维布局系统，允许将元素放置在行和列的网格中，适合于复杂的布局结构。
- **常见应用**：创建页面的整体布局、图片库、表格等。
- **应用场景**：用于网格布局需求强烈的场景。

  ```css
  .grid-container {
      display: grid;
      grid-template-columns: 1fr 1fr 1fr;
      gap: 10px;
  }
  ```

#### 6. `display: none`

- **特性**：隐藏元素，元素不占据任何空间，相当于从文档流中移除。
- **应用场景**：适用于需要动态控制显示或隐藏的元素，比如切换标签、弹出框等。

  ```css
  .hidden {
      display: none;
  }
  ```

### 其他常见 `display` 值

- **`display: table`**：将元素设置为表格（类似于HTML表格），可以配合 `display: table-row` 和 `display: table-cell` 来模拟表格布局。
- **`display: list-item`**：使元素表现为列表项，通常用于带有 `::marker` 的元素（如 `<li>`）。
- **`display: contents`**：不会在文档流中生成实际盒子，只显示它的子元素。这在需要移除冗余容器元素时很有用。

### 总结

不同的 `display` 值可以帮助我们灵活地控制页面中元素的布局和显示方式。选择合适的 `display` 值可以简化布局，提升页面的美观和响应性：

- **`block`** 和 **`inline`**：基础布局。
- **`inline-block`**：内容块的行内显示。
- **`flex`** 和 **`grid`**：强大的布局工具，适合响应式设计。
- **`none`**：控制元素的显示与隐藏。
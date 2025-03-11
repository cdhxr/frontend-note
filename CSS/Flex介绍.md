#css 

**Flex 布局（Flexible Box Layout）** 是一种用于在容器中排列和对齐项目的 CSS 布局模型，旨在提供一种更强大且灵活的方式来控制网页布局，尤其是在处理复杂的响应式设计时。Flex 布局通过使容器的子项能够动态地调整其大小和顺序，实现更加灵活和高效的布局控制。

### Flex 布局的核心概念
11~14为对flex的细分

1. **Flex 容器（Flex Container）**：
   - 通过将父元素设置为 `display: flex` 或 `display: inline-flex`，将其转化为一个 Flex 容器。
   - **`display: flex;`**：创建一个块级（block-level）Flex容器。
   - **`display: inline-flex;`**：创建一个内联（inline-level）Flex容器。

2. **Flex 项目（Flex Items）**：
   - Flex 容器中的直接子元素称为 Flex 项目（items）。
   - Flex 项目会根据容器的宽度和高度动态调整其大小。

3. **主轴（Main Axis）和交叉轴（Cross Axis）**：
   - **主轴（Main Axis）**：默认情况下，主轴是横向的（从左到右），也可以通过 `flex-direction` 改变为纵向（从上到下）。
   - **交叉轴（Cross Axis）**：与主轴垂直的方向。若主轴是横向的，交叉轴就是纵向的；反之亦然。

4. **`flex-direction`**：
   - 定义 Flex 容器中项目排列的方向。
   - 值：`row`（默认，从左到右），`row-reverse`（从右到左），`column`（从上到下），`column-reverse`（从下到上）。

   示例：
   ```css
   .container {
     display: flex;
     flex-direction: row;  /* 水平方向排列 */
   }
   ```

5. **`justify-content`**：
   - 定义 Flex 容器中项目沿主轴的对齐方式。
   - 常见值：`flex-start`（默认，左对齐），`flex-end`（右对齐），`center`（居中），`space-between`（项目之间均匀分布），`space-around`（项目之间均匀分布，带有间隔）。
	- ![[justifycontent.png]]

   示例：
   ```css
   .container {
     display: flex;
     justify-content: center;  /* 水平居中 */
   }
   ```

6. **`align-items`**：
   - 定义 Flex 容器中项目沿交叉轴的对齐方式。
   - 常见值：`stretch`（默认，拉伸以适应容器），`flex-start`（顶部对齐），`flex-end`（底部对齐），`center`（居中对齐），`baseline`（基线对齐）。
	- ![[align-items.png]]
   示例：
   ```css
   .container {
     display: flex;
     align-items: center;  /* 垂直居中 */
   }
   ```

7. align-self：
   - 用于单独调整单个 Flex 项目的对齐方式，覆盖 `align-items` 的默认值。
   - 值：`auto`（默认，继承父容器的对齐方式），`flex-start`，`flex-end`，`center`，`baseline`，`stretch`。

8. flex-wrap：
   - 控制项目是否换行，默认情况下，所有项目都在一行显示。如果项目总宽度超过容器，可以使用 `flex-wrap` 让项目换行。
   - 值：`nowrap`（默认，不换行），`wrap`（换行），`wrap-reverse`（反向换行）。

   示例：
   ```css
   .container {
     display: flex;
     flex-wrap: wrap;  /* 换行 */
   }
   ```

9. `flex`：
   - 定义 Flex 项目的伸缩能力，允许项目在主轴方向上分配剩余空间。
   - 语法：`flex: <flex-grow> <flex-shrink> <flex-basis>`。
     - **`flex-grow`**：定义项目的放大比例。
     - **`flex-shrink`**：定义项目的缩小比例。
     - **`flex-basis`**：定义项目在主轴方向上的初始大小。


   示例：
   ```css
	flex: 1
	flex-grow: 1
	
	flex: 100px
	flex-basis: 100px
	
	flex: 2 1
	flex-grow: 2; flex-shrink: 1
	
	flex: 1 100px
	flex-grow: 1; flex-basis: 100px
	
	flex: 2 0 100px
	flex-grow: 2; flex-shrink: 0; flex-basis: 100px
	
	flex: auto
	flex: 1 1 auto
	
	flex: none
	flex: 0 0 auto
	```

10. order：
    - 控制项目在容器中的排列顺序，默认值为 `0`，数值越小排列越靠前。
    
    示例：
    ```css
    .item {
      order: 1;  /* 设置排列顺序 */
    }
    ```

11. `Flexibility`：

	- 可以设置子项的弹性：当容器有剩余空间时，会伸展；容器空间不够时，会收缩。
	- flex-grow 有剩余空间时的伸展能力
	- flex-shrink 容器空间不足时收缩的能力
	- flex-basis 没有伸展或收缩时的基础长度


	### 讲解：
	在 CSS 的 Flex 布局中，`flex-grow`、`flex-shrink` 和 `flex-basis` 是控制子项弹性的三个重要属性。

	1. **`flex-grow`**：定义当容器中有**剩余空间**时，子项的**伸展比例**。例如，`flex-grow: 1;` 表示子项会尝试填充多余空间。多个子项的 `flex-grow` 值可以不同，表示它们根据比例来分配剩余空间。
	
	2. **`flex-shrink`**：定义当容器**空间不足**时，子项的**收缩能力**。`flex-shrink: 1;` 表示子项会在容器空间不足时按比例收缩。不同的 `flex-shrink` 值可以决定哪些子项优先收缩，哪些次要收缩。
	
	3. **`flex-basis`**：定义子项在没有伸展或收缩时的**初始长度**。这相当于子项的默认宽度（如果是水平排列）或高度（如果是垂直排列），单位可以是 `px`、`%` 等。
	
		### 综合用法：
		通常，可以通过 `flex: <flex-grow> <flex-shrink> <flex-basis>;` 来综合设置弹性，例如 `flex: 1 1 100px;` 表示子项在有多余空间时会伸展（`flex-grow: 1`），在空间不足时会收缩（`flex-shrink: 1`），默认基础宽度为 `100px`。

### Flex 布局示例：

```html
<div class="container">
  <div class="item">项目 1</div>
  <div class="item">项目 2</div>
  <div class="item">项目 3</div>
</div>
```

```css
.container {
  display: flex;
  justify-content: space-between; /* 水平间隔均匀 */
  align-items: center; /* 垂直居中 */
  height: 200px; /* 设置容器高度 */
}

.item {
  width: 100px;
  height: 50px;
  background-color: lightblue;
}
```

### Flex 布局的优点：
- **灵活性**：可以方便地调整项目的大小、位置和顺序。
- **响应式**：特别适用于自适应布局，尤其是在屏幕尺寸变化时。
- **简化布局**：比传统的浮动布局和定位布局要简单和直观。

Flex 布局广泛应用于网页设计，尤其是在响应式设计和复杂布局的构建中，它使得内容排列和对齐更加容易和灵活。

[[Flex实例]]
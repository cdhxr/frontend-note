#css 

以下是一个使用 Flex 布局的完整示例，展示了 Flex 布局的多个特点，包括 `flex-direction`、`justify-content`、`align-items`、`flex-wrap`、`flex` 和 `order` 等属性。你可以将这段代码复制到 HTML 文件中并运行，观察不同 Flex 属性对布局的影响。

### 完整代码示例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Flex 布局示例</title>
  <style>
    /* Flex 容器 */
    .container {
      display: flex;
      flex-wrap: wrap; /* 允许换行 */
      justify-content: space-between; /* 项目之间均匀分布 */
      align-items: center; /* 项目垂直居中 */
      height: 100vh; /* 容器高度为视口高度 */
      gap: 20px; /* 项目之间的间隙 */
      border: 2px solid #ccc;
      padding: 10px;
    }

    /* Flex 项目 */
    .item {
      background-color: #4CAF50;
      color: white;
      width: 150px;
      height: 150px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 18px;
      border-radius: 10px;
      /* 让项目具有弹性 */
      flex: 1 1 200px; /* flex-grow: 1, flex-shrink: 1, flex-basis: 200px */
      text-align: center;
      box-sizing: border-box;
    }

    /* 使用 order 设置项目顺序 */
    .item:nth-child(1) {
      order: 2;
    }
    .item:nth-child(2) {
      order: 1;
    }
    .item:nth-child(3) {
      order: 3;
    }

    /* 当屏幕较小 (如手机时)，改变 flex-direction 为 column */
    @media (max-width: 600px) {
      .container {
        flex-direction: column; /* 改为纵向排列 */
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="item">项目 1</div>
    <div class="item">项目 2</div>
    <div class="item">项目 3</div>
    <div class="item">项目 4</div>
    <div class="item">项目 5</div>
  </div>
</body>
</html>
```

### 解释：
1. **Flex 容器**：
   - `display: flex;`：将容器设置为 Flex 布局。
   - `flex-wrap: wrap;`：允许项目在容器空间不足时换行。
   - `justify-content: space-between;`：项目之间均匀分布。
   - `align-items: center;`：项目沿交叉轴（垂直方向）居中对齐。
   - `gap: 20px;`：设置项目之间的间隙。
   - `height: 100vh;`：容器高度设置为视口高度，使其填充屏幕。

2. **Flex 项目**：
   - `flex: 1 1 200px;`：每个项目有弹性，默认宽度为 200px，允许根据容器大小调整（`flex-grow: 1` 和 `flex-shrink: 1`）。
   - `justify-content: center;` 和 `align-items: center;`：使每个项目中的内容水平和垂直居中。
   - `order: 2`、`order: 1` 等：改变了项目的排列顺序，`项目 1` 排在第三，`项目 2` 排在第一。

3. **响应式设计**：
   - 使用了媒体查询，在屏幕宽度小于 600px 时，容器的 `flex-direction` 属性改为 `column`，使项目从纵向排列，适应较小的屏幕。

### 运行效果：
- 在大屏幕上，项目会水平排列，使用 `space-between` 分布。
- 在小屏幕（如手机）上，项目会垂直排列，并根据设置的顺序显示。
- 通过 `flex` 属性，项目会根据容器的大小调整宽度，保证它们平衡地分配空间。

你可以在浏览器中打开此 HTML 文件并调整窗口大小，以查看响应式效果。

![[Flex.png]]
#css 
# idea: 
集中管理style属性，实现一系列属性的复用

# syntax:

由selector和property组成

```css
selector {
    property: value;
    property: value;
}
```


![CSS p 声明，其中 color 为 red](https://developer.mozilla.org/zh-CN/docs/Learn/Getting_started_with_the_web/CSS_basics/css-declaration-small.png)

整个结构称为**规则集**（_规则集_通常简称为_规则_）

## 不同类型的选择器

选择器有许多不同的类型。上面只介绍了**元素选择器**，用来选择所有指定类型的元素。但是选择操作可以更加具体。下面是一些更常用的选择器类型：

| 选择器名称              | 选择的内容                                              | 示例                                                                  |
| ------------------ | -------------------------------------------------- | ------------------------------------------------------------------- |
| 元素选择器（也称作标签或类型选择器） | 所有指定类型的 HTML 元素                                    | `p`  <br>选择 `<p>`                                                   |
| ID 选择器             | 具有特定 ID 的元素。单一 HTML 页面中，每个 ID 只对应一个元素，一个元素只对应一个 ID | `#my-id`  <br>选择 `<p id="my-id">` 或 `<a id="my-id">`                |
| 类选择器               | 具有特定类的元素。单一页面中，一个类可以有多个实例                          | `.my-class`  <br>选择 `<p class="my-class">` 和 `<a class="my-class">` |
| 属性选择器              | 拥有特定属性的元素                                          | `img[src]`  <br>选择 `<img src="myimage.png">` 但不是 `<img>`            |
| 伪类选择器              | 特定状态下的特定元素（比如鼠标指针悬停在链接上时）                          | `a:hover`  <br>选择仅在鼠标指针悬停在链接上时的 `<a>` 元素                            |
|                    |                                                    |                                                                     |

选择器的种类远不止这些，更多信息请参阅 MDN 的[选择器指南](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/Selectors)。


# Box Model

在CSS中，**盒模型（Box Model）** 是定义元素的尺寸和布局的基础。每个HTML元素在页面上被渲染时都被视为一个矩形的盒子。
详见[[Box Model]]
### 盒模型的结构图

盒模型由四个主要部分组成：**内容区域**、**内边距（padding）**、**边框（border）** 和 **外边距（margin）**

```plaintext
┌─────────────── Total Width ────────────────┐
|                                            |
|                Margin                      |
|  ┌───────────────────────────────────────┐ |
|  |              Border                   | |
|  |  ┌─────────────────────────────────┐  | |
|  |  |           Padding               |  | |
|  |  |  ┌───────────────────────────┐  |  | |
|  |  |  |       Content Area        |  |  | |
|  |  |  └───────────────────────────┘  |  | |
|  |  └─────────────────────────────────┘  | |
|  └───────────────────────────────────────┘ |
|                                            |
└────────────────────────────────────────────┘
```

Margin -> Border -> Padding -> Content Area

# Display

在CSS中，**`display`** 属性决定了一个元素如何被渲染、布局和显示在页面上。它是控制页面布局和元素外观的核心属性之一。`display` 有许多可能的值，但最常用的包括 **`block`**、**`inline`**、**`inline-block`**、和 **`flex`**、**`Grid`**。每种值都会影响元素的布局特性。


#css 

![[CSS是如何工作的.png]]

# selector的特异度

选择元素可见[[CSS]]
### 特异度的计算方式总结

1. **内联样式**的特异度总是最高。
2. **ID 选择器**的特异度次之。
3. **类选择器**、**伪类选择器**、**属性选择器** 的特异度再次之。
4. **元素选择器**和**伪元素选择器**的特异度最小。

这种写法很好的对代码进行了复用，一个基础button之上再设置优先级更高的特殊属性

![[CSS类的覆盖.png]]

如图，

这段代码定义了两个按钮样式，分别应用于“普通按钮”和“主要按钮”。

- `<button class="btn">普通按钮</button>`：这是一个带有 `btn` 类的按钮，会应用 `.btn` 样式。
- `<button class="btn primary">主要按钮</button>`：这是一个同时带有 `btn` 和 `primary` 类的按钮，会应用 `.btn` 和 `.btn.primary` 样式。

样式说明：

- `.btn` 类设置了按钮的基础样式，包括显示方式、内外边距、对齐、光标、圆角、背景色等。
- `.btn.primary` 类是一个更具体的选择器，给“主要按钮”设置了白色文字颜色（`#fff`）和蓝色背景（`#218de6`），覆盖了 `.btn` 中的默认颜色。
  
# CSS的继承

某些标签会自动继承其父元素的计算值，除非显式指定

![[继承示例.png]]
如图，strong没有指定color但继承了p标签的color

通常，文字相关标签可以继承，盒模型相关则不行

对于不会自动继承的标签，我们希望其继承父级标签时可以**显式继承**

在 CSS 中，“显式继承”是指通过指定属性的值为 `inherit`，让一个元素明确地继承其父元素的某个样式属性。这是一种强制性的继承方法，因为并不是所有的 CSS 属性默认都会继承父元素的样式，尤其是像边距、填充、边框等。

```css
.child {
    color: inherit;  /* 子元素将继承父元素的文本颜色 */
}
```

在CSS中每个属性都有初始值

# 布局相关技术

## 常规流Normal flow

行级，块级，表格布局，Flex Box，Grid

- 盒模型
	- width
		- 指定 content box 宽度
		- 取值为长度、百分数、auto
		- auto 由浏览器根据其它属性确定
		- 百分数相对于容器的 content box 宽度
	- height
		- 指定 content box 高度
		- 取值为长度、百分数、auto
		- auto 取值由内容计算得来
		- 百分数相对于容器的 content box 高度
		- 容器有指定的高度时，百分数才生效
	- padding
		- 指定元素四个方向的内边距
		- 百分数相对于容器宽度
	- border
		- 指定容器边框样式，粗细和颜色
		- 三种属性
			- border-width
			- border-style
			- border-color

		- 四个方向：
			- border-top
			- border-right
			- border-bottom
			- border-left	
		
		- border示例代码：
		```css
		border: 1px solid #ccc;

		border-left: 1px solid #ccc;
		border-right: 2px dotted red;

		border-width: 1px 2px 3px 4px;
		border-style: solid;
		border-color: green blue;

		border-left-width: 3px;
		border-top-color: #f00;
		```
		
	- margin
		- 指定元素四个方向的外边距
		- 取值可以是长度、百分数、auto
		- 百分比相对于容器宽度
		- margin:auto可用于水平居中，`margin-left:auto,margin-right:auto`
		- **Margin Collapse（外边距折叠）：**

			- 在垂直方向上，如果两个相邻的块级元素都有 `margin` 值，那么它们之间的距离不会是两个 `margin` 的总和，而是取两个 `margin` 值中的较大值，这个现象叫 **Margin Collapse**（外边距折叠）。
			- 例如:在这种情况下，两个元素之间的实际距离是 `30px`，而不是 `20px + 30px = 50px`。
		```css
        <div class="box1" style="margin-bottom: 20px;"></div>
        <div class="box2" style="margin-top: 30px;"></div>
		```
	- Borderbox模式
			- 没有指定 `box-sizing`，因此默认是 `content-box`。在 `content-box` 模式下，`width: 100%` 表示内容区域的宽度是 100%，而**边框（border）和内边距（padding）会额外增加元素的总宽度**。因此，`.a` 的总宽度是 `100% + 1em（左右各 1em 的 padding）+ 6px（左右各 3px 的 border）`
			- ![[boxsizing.png]]
	- overflow
			- 文字内容如果到到框外用overflow来控制文字的显示模式
			- visiable,hidden,scroll

- CSS 盒模型 - 行级
- CSS 盒模型 - 块级

- Flex布局：[[Flex介绍]]
- Grid布局：(https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Grids)

- 默认是行级上下文或者块级上下文布局，指定display后，Flex是一维的布局，Grid是二维的布局，是通过划分网格，再通过指定网格线坐标定位确定实际的布局的（3×3的网格，可以确定一个宽1格，长2格的块，放在分好的网格上）

## 浮动

为了实现文字环绕
![[Float实例.png]]

## 绝对定位

position属性
- **static**
    
    - 默认值，表示元素是非定位的。
    - 元素会按照常规流中的正常位置排列，不会受 `top`、`right`、`bottom` 或 `left` 属性影响。
- **relative**
    
    - 在常规流里面布局
	- 相对于自己本应该在的位置进行偏移
	- 使用 top、left、bottom、right 设置偏移长度
	- 流内其它元素当它没有偏移一样布局
- **absolute**
    
    - 元素被绝对定位，相对于最近的非 `static` 的祖先元素定位（即被设置为 `relative`, `absolute`, 或 `fixed`）
    - （如果没有，则相对于初始包含块 `<html>` 元素）。
    - 元素会脱离常规流，不会为其他元素预留空间。
- **fixed**
    
    - 元素相对于视口（浏览器窗口）进行定位。
    - 它脱离常规流，即使页面滚动，它的位置也不会改变
- sticky

- **static** 是最基础的布局模式，适用于不需要任何特殊定位的场景。
- **relative** 适合在当前文档流的基础上做微调，常用于局部偏移。
- **absolute** 和 **fixed** 都脱离文档流，适合用来创建独立的、不会影响其他布局的元素，比如悬浮窗口。
- **fixed** 比 **absolute** 更特殊，常用在创建固定导航栏或悬浮按钮时
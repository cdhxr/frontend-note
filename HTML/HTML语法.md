#html 

- 标签和属性不区分大小写，推荐小写
- 空标签可以不闭合，比如 input、meta
- 属性值推荐用双引号包裹
- 某些属性值可以省略，比如 required、readonly

# 标题h1~h6

一级标题至六级标题
`<h1>...</h1>

# 列表 list

分为order list（有序列表），unordered list (无序列表），definition list (定义列表)
---

```html
<h2>世界电影票房排行榜</h2>
<ol>
  <li>阿凡达</li>
  <li>泰坦尼克号</li>
  <li>复仇者联盟</li>
<ol>

<h2>购物清单</h2>
<ul>
  <li>🍇</li>
  <li>🍉</li>
  <li>🍎</li>
</ul>

<h2>霸王别姬</h2>
<dl>
  <dt>导演：</dt>
  <dd>陈凯歌</dd>
  <dt>主演：</dt>
  <dd>张国荣</dd>
  <dd>张丰毅</dd>
  <dd>巩俐</dd>
  <dt>上映日期：</dt>
  <dd>1993-01-01</dd>
</dl>
```

其中，定义列表由 definition title 和 definition description 组成

---


- **世界电影票房排行榜**
  1. 阿凡达
  2. 泰坦尼克号
  3. 复仇者联盟

- **购物清单**
  - 🍇
  - 🍉
  - 🍎

- **霸王别姬**
  - 导演: 陈凯歌
  - 主演:
    - 张国荣
    - 张丰毅
    - 巩俐
  - 上映日期: 1993-01-01

# 链接

- 
  ```html
  <a href="https://www.bytedance.com/">
    字节跳动官网
  </a>
  ```
  点击链接会在**当前页面**中打开字节跳动官网。

-   
  ```html
  <a href="https://www.bytedance.com/" target="_blank">
    字节跳动官网
  </a>
  ```
  添加了 `target="_blank"` 属性后，点击链接会在**新标签页**中打开字节跳动官网。

总结：`target="_blank"` 的作用是让链接在新标签页中打开，提升用户体验，不打断当前页面的浏览。

# 多媒体

`<img>`、`<audio>` 和 `<video>`。
它们分别用于在网页中插入图片、音频和视频。下面是对每个标签的语法讲解：

### 1. `<img>` 标签
```html
<img 
  src="https://lf3-static.bytednsdoc.com/obj/eden-cn/ubqnuhbnuho/movable_type.jpg" 
  alt="Metal movable type" 
  width="400" 
/>
```
- **`<img>` 标签**：用于插入图片，是一个**自闭合标签**（不需要闭合标签）。
- **`src` 属性**：指定图片的URL，即图片的路径。这个属性是**必需的**。
- **`alt` 属性**：定义图片的替代文本，如果图片无法加载会显示此文字，也有助于屏幕阅读器描述图片内容，增强网页的可访问性。
- **`width` 属性**：定义图片的宽度，这里设置为`400`，单位为像素(px)。

### 2. `<audio>` 标签
```html
<audio 
  src="/assets/music.ogg" 
  controls
></audio>
```
- **`<audio>` 标签**：用于插入音频文件，**需要闭合标签**。
- **`src` 属性**：指定音频文件的路径。
- **`controls` 属性**：显示音频控制界面（播放、暂停、音量等）。`controls` 是一个布尔属性，**不需要赋值**，直接写出即可。

### 3. `<video>` 标签
```html
<video 
  src="/assets/video.mp4" 
  controls
></video>
```
- **`<video>` 标签**：用于插入视频文件，**需要闭合标签**。
- **`src` 属性**：指定视频文件的路径。
- **`controls` 属性**：显示视频控制界面，功能与 `<audio>` 标签中的 `controls` 一样。

### 总结
- `<img>` 标签用于显示图片，不需要闭合标签。
- `<audio>` 和 `<video>` 标签分别用于嵌入音频和视频文件，且都需要闭合标签。
- `src` 属性是 `<img>`、`<audio>`、`<video>` 的必需属性，表示文件的路径。
- `controls` 属性为音频和视频添加播放控制，不需要赋值。

# 输入

这段代码展示了 HTML 中几种不同类型的 `<input>` 标签和一个 `<textarea>` 标签的使用。下面是对每个部分的解释：

1. **`<input placeholder="请输入用户名">`**
   - 这是一个文本输入框（默认情况下，`type` 是 `"text"`）。
   - `placeholder` 属性用于显示占位符文本，当输入框为空时，会显示“请输入用户名”以提示用户。
   
2. **`<input type="range">`**
   - 这是一个范围选择输入框，允许用户选择一个范围内的值（通常显示为滑块）。
   - 该标签没有设置 `min` 和 `max` 属性，因此范围默认值会根据浏览器的设定。可以通过设置 `min` 和 `max` 属性来控制范围，例如：`<input type="range" min="1" max="100">`。
   
3. **`<input type="number" min="1" max="10">`**
   - 这是一个数字输入框，允许用户输入一个数值。
   - `min="1"` 和 `max="10"` 属性设置了输入值的范围，用户只能输入 1 到 10 之间的数值。

4. **`<input type="date" min="2018-02-10">`**
   - 这是一个日期选择输入框，允许用户选择一个日期。
   - `min="2018-02-10"` 属性限制了最早的可选日期为 2018 年 2 月 10 日。用户只能选择这个日期之后的日期。

5. **`<textarea>Hey</textarea>`**
   - 这是一个多行文本输入区域，用于输入较长的文本。
   - `<textarea>` 标签之间的文本（这里是 “Hey”）将作为默认内容显示在文本区域内。

这些标签的组合可以在 HTML 表单中提供多种不同的输入方式，用于实现丰富的用户交互体验。[[more inputs]]

# 选择

### 1. 复选框（Checkbox）

复选框允许用户多选，可以选择多个选项。在 HTML 中使用 `<input type="checkbox">` 来创建。

```html
<label><input type="checkbox"> 🍎</label>
<label><input type="checkbox"> 🍏</label>
```

- `<input type="checkbox">` 表示复选框。
- 使用 `<label>` 标签包裹复选框和对应的文本（或图标），这样点击图标或文字时也可以选择复选框，提升用户体验。

### 2. 单选按钮（Radio Button）

单选按钮允许用户在多个选项中选择一个。在 HTML 中使用 `<input type="radio">` 来创建单选按钮。

```html
<label><input type="radio" name="sport"> ⚽</label>
<label><input type="radio" name="sport"> 🏀</label>
<label><input type="radio" name="sport"> 🏐</label>
```

- `<input type="radio">` 表示单选按钮。
- `name="sport"` 属性用于将单选按钮分组。相同的 `name` 值表示同一组中的按钮，只能选择一个。
- 通过 `<label>` 标签包裹可以提高可点击范围。

### 3. 下拉选择菜单（Dropdown）

下拉菜单允许用户从多个选项中选择一个。可以使用 `<select>` 和 `<option>` 标签来创建。

```html
<select>
  <option>🥎</option>
  <option>🏐</option>
  <option>⚾</option>
</select>
```

- `<select>` 标签用于创建下拉菜单。
- `<option>` 标签表示菜单中的一个选项。可以添加任意多个 `<option>` 标签作为选项。

### 4. 多选下拉菜单（Multiple Select Dropdown）

类似于下拉菜单，但可以通过设置 `multiple` 属性实现多选。

```html
<select multiple>
  <option>🍓</option>
  <option>🍇</option>
  <option>🍉</option>
</select>
```

- `multiple` 属性允许用户选择多个选项。
- 使用 Ctrl（或 Command）键来多选。

### 5.
这段代码展示了一个基于 `<datalist>` 元素的输入框实现，用户可以输入或从预设的选项列表中选择。这种方法为用户提供了建议列表，但也允许用户输入自定义内容。下面是这段代码的解析和语法讲解：

```html
<input list="countries" />
<datalist id="countries">
    <option>Greece</option>
    <option>United Kingdom</option>
    <option>United States</option>
</datalist>
```

### 代码解析

1. **`<input list="countries" />`**
   - 这是一个普通的输入框，但通过 `list` 属性与 `<datalist>` 关联。
   - `list="countries"` 表示这个输入框关联了 `id="countries"` 的 `<datalist>`，这样用户在输入框中输入时会看到建议的列表。

2. **`<datalist id="countries">`**
   - `<datalist>` 定义了一个建议列表，包含多个 `<option>` 元素。
   - `id="countries"` 是这个 `<datalist>` 的唯一标识，用于关联输入框。

3. **`<option>`**
   - `<option>` 标签在 `<datalist>` 中表示一个建议项。
   - 在这个例子中，提供了 "Greece"、"United Kingdom" 和 "United States" 作为选项，用户可以从这些预设值中选择。

### 作用

使用 `<datalist>` 和 `<input>` 的组合，用户在输入框中输入内容时会看到建议的列表。适用于需要提供可选建议但又允许自由输入的情况，比如国家、城市等字段。

### 注意事项

- `<datalist>` 只是提供建议，用户可以选择列表中的选项，也可以输入其他未在列表中的内容。
- `<datalist>` 不支持多选功能。

# 页面划分

![[HTML标签布局.png]]

这个图显示了一个典型的HTML布局结构，包括`header`、`nav`、`main`、`article`、`aside`和`footer`标签的排列。以下是用Markdown和HTML代码描述的布局：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTML Layout</title>
    <style>
        body { display: flex; flex-direction: column; align-items: center; }
        header, footer { background: #f8d7da; padding: 20px; width: 100%; text-align: center; }
        nav { background: #f5c6cb; padding: 10px; margin-top: 10px; }
        main { background: #cfe2f3; display: flex; width: 100%; padding: 20px; }
        article { background: #d4edda; margin: 10px; flex: 1; padding: 20px; }
        aside { background: #e2e3e5; width: 200px; padding: 20px; }
    </style>
</head>
<body>
    <header>Header</header>
    <nav>Nav</nav>
    <main>
        <div style="flex: 3;">
            <article>Article 1</article>
            <article>Article 2</article>
        </div>
        <aside>Aside</aside>
    </main>
    <footer>Footer</footer>
</body>
</html>
```

### 布局说明
1. **Header**：顶部的`header`区域，用于显示网站的标题或导航条上方的内容。
2. **Nav**：位于`header`下方的`nav`区域，通常用于放置导航链接。
3. **Main**：包含主要内容的`main`区域，在该区域中包含两个`article`块，通常用于显示文章内容。
4. **Article**：两个`article`块是主内容的主体部分，每个`article`代表一篇文章或内容块。
5. **Aside**：`aside`区域位于右侧，通常用于放置侧边栏内容，例如广告、相关链接等。
6. **Footer**：位于页面底部的`footer`区域，用于显示版权信息、联系方式等。

# 
在 **global CSS** 中，通常会设置一些全局样式，这些样式会影响整个网站的外观和布局。它们可以包括：

### 1. **全局字体设置**
   设置网站的基础字体、字体大小、字体颜色等，确保整个网站的字体一致。

   ```css
   body {
       font-family: Arial, sans-serif;  /* 设置全站的字体 */
       font-size: 16px;  /* 设置默认字体大小 */
       color: #333;  /* 设置默认文字颜色 */
       line-height: 1.5;  /* 设置行高，增加可读性 */
   }
   ```

### 2. **背景颜色和背景图像**
   设置页面的背景颜色或背景图像。

   ```css
   body {
       background-color: #f4f4f4;  /* 设置背景颜色 */
       background-image: url('images/background.jpg');  /* 设置背景图像 */
       background-size: cover;  /* 背景图像覆盖整个页面 */
   }
   ```

### 3. **页面边距和填充（Margin & Padding）**
   设置页面元素的外边距和内边距，以确保布局的一致性。

   ```css
   * {
       margin: 0;  /* 重置所有元素的外边距 */
       padding: 0;  /* 重置所有元素的内边距 */
       box-sizing: border-box;  /* 使元素的宽高包含内边距和边框 */
   }
   ```

### 4. **链接样式**
   设置全站链接的样式，包括鼠标悬停、点击等状态的样式。

   ```css
   a {
       color: #007bff;  /* 设置链接的颜色 */
       text-decoration: none;  /* 去除下划线 */
   }

   a:hover {
       text-decoration: underline;  /* 悬停时加下划线 */
   }
   ```

### 5. **通用布局样式（如容器、对齐）**
   设置通用容器、对齐方式等，确保布局一致。

   ```css
   .container {
       width: 80%;
       margin: 0 auto;  /* 居中对齐容器 */
       padding: 20px;  /* 设置容器内边距 */
   }
   ```

### 6. **全局字体大小和行高设置**
   为网页的不同部分定义统一的字体大小、行高等，保持页面一致性。

   ```css
   h1, h2, h3, h4, h5, h6 {
       margin-top: 0;
       margin-bottom: 0.5em;  /* 设置标题的上下外边距 */
   }

   p {
       margin-bottom: 1em;  /* 设置段落的底部外边距 */
   }
   ```

### 7. **表单元素样式**
   设置表单元素的样式，以保持表单控件的统一外观。

   ```css
   input, textarea, select, button {
       font-family: inherit;
       font-size: inherit;
       padding: 8px;  /* 设置表单元素内边距 */
   }

   input[type="submit"], button {
       background-color: #007bff;
       color: white;
       border: none;
       cursor: pointer;
   }

   input[type="submit"]:hover, button:hover {
       background-color: #0056b3;
   }
   ```

### 8. **响应式设计基础**
   使用媒体查询设置不同设备尺寸下的样式，确保页面在手机、平板和桌面设备上都有良好的展示效果。

   ```css
   @media (max-width: 768px) {
       body {
           font-size: 14px;  /* 在小屏设备上减小字体大小 */
       }

       .container {
           width: 100%;  /* 在小屏设备上使容器占满全宽 */
       }
   }
   ```

### 9. **动画和过渡（可选）**
   设置全局动画或过渡效果，让页面更具互动性。

   ```css
   * {
       transition: all 0.3s ease;  /* 所有元素的过渡效果 */
   }

   a:hover {
       color: #0056b3;  /* 修改链接颜色时的过渡效果 */
   }
   ```

### 总结：
- **全局CSS** 应包括一些通用的样式设置，确保页面的外观和布局一致。
- **重要内容**：字体、背景颜色、外边距和内边距、链接样式、容器样式、表单样式、响应式设计等。
- **目的**：通过统一的全局设置，保证整个网站的统一性和一致性。
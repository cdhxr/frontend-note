
使用 `@font-face` 导入自定义字体后，你可以在页面中的任意选择器内通过 `font-family` 来应用该字体。
### 使用步骤：

1. **导入字体**：  
    使用 `@font-face` 定义自定义字体的源。例如：
    
    ```css
    @font-face {
      font-family: 'MyCustomFont';
      src: url('path/to/font.woff2') format('woff2'),
           url('path/to/font.woff') format('woff');
      font-weight: normal;
      font-style: normal;
    }
    ```
    
2. **应用字体**：  
    在任何 CSS 选择器中，可以通过 `font-family` 属性应用这个字体：
    
    ```css
    body {
      font-family: 'MyCustomFont', sans-serif;
    }
    
    h1 {
      font-family: 'MyCustomFont', serif;
    }
    ```
    
    这样，你就可以在页面中的任何元素（如 `body`, `h1`, `p`, 等等）上使用自定义字体了。
    


你可以通过使用 CSS 的 `@font-face` 规则来导入字体 A 和字体 B，并在相应的 HTML 元素上应用这两个字体。以下是具体步骤：

### 1. 导入字体
假设你已经将字体文件存储在项目目录下的 `fonts/` 文件夹中。使用 `@font-face` 规则为每个字体定义名称和路径。

```css
@font-face {
    font-family: 'FontA';
    src: url('fonts/FontA.woff2') format('woff2'),
         url('fonts/FontA.woff') format('woff');
    font-weight: normal;
    font-style: normal;
    font-display: swap;
}

@font-face {
    font-family: 'FontB';
    src: url('fonts/FontB.woff2') format('woff2'),
         url('fonts/FontB.woff') format('woff');
    font-weight: normal;
    font-style: normal;
    font-display: swap;
}
```

### 2. 应用字体到特定元素
在 CSS 中，可以为标题和正文分别指定不同的字体。

```css
h1 {
    font-family: 'FontA', sans-serif; /* 为标题使用字体A */
}

p {
    font-family: 'FontB', sans-serif; /* 为正文使用字体B */
}
```

### 3. 将 CSS 链接到 HTML 文件
可以将 CSS 直接嵌入到 `<style>` 标签中，也可以创建单独的 CSS 文件（例如 `styles.css`），然后在 HTML 中引用：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Life System</title>
    <link rel="stylesheet" href="styles.css"> <!-- 引入CSS文件 -->
</head>
<body>
    <h1>Life System</h1>
    <p>This is a simple life system simulation.</p>
</body>
</html>
```

### 完整的 `styles.css` 文件内容
```css
@font-face {
    font-family: 'FontA';
    src: url('fonts/FontA.woff2') format('woff2'),
         url('fonts/FontA.woff') format('woff');
    font-weight: normal;
    font-style: normal;
    font-display: swap;
}

@font-face {
    font-family: 'FontB';
    src: url('fonts/FontB.woff2') format('woff2'),
         url('fonts/FontB.woff') format('woff');
    font-weight: normal;
    font-style: normal;
    font-display: swap;
}

h1 {
    font-family: 'FontA', sans-serif;
}

p {
    font-family: 'FontB', sans-serif;
}
```

这样，标题会使用字体 A，而正文会使用字体 B。

[[CSS字体设置相关属性]]


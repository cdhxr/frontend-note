#css 

在HTML中，`<link>`标签的`rel`属性用于定义当前文档与链接资源之间的关系（relationship）。`rel`属性是`<link>`标签的核心属性之一，通常与`href`属性一起使用，指定链接资源的位置。

### `rel`属性的常见取值：
1. **stylesheet**  
   - 表示链接的资源是一个外部样式表（CSS文件）。  
   - 示例：  
     ```html
     <link rel="stylesheet" href="styles.css">
     ```

2. **icon**  
   - 表示链接的资源是页面的图标（如favicon）。  
   - 示例：  
     ```html
     <link rel="icon" href="favicon.ico" type="image/x-icon">
     ```

3. **preload**  
   - 表示链接的资源需要提前加载，通常用于优化性能。  
   - 示例：  
     ```html
     <link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin>
     ```

4. **prefetch**  
   - 表示链接的资源可能会在后续页面中使用，浏览器可以提前缓存。  
   - 示例：  
     ```html
     <link rel="prefetch" href="next-page.html">
     ```

5. **canonical**  
   - 用于指定当前页面的规范URL，帮助搜索引擎避免重复内容。  
   - 示例：  
     ```html
     <link rel="canonical" href="https://example.com/page">
     ```

6. **alternate**  
   - 表示链接的资源是当前页面的替代版本（如不同语言或格式）。  
   - 示例：  
     ```html
     <link rel="alternate" hreflang="es" href="https://example.com/es/page">
     ```

7. **dns-prefetch**  
   - 表示需要提前解析链接资源的域名，以加快加载速度。  
   - 示例：  
     ```html
     <link rel="dns-prefetch" href="https://cdn.example.com">
     ```

8. **manifest**  
   - 表示链接的资源是一个Web应用的清单文件（通常用于PWA）。  
   - 示例：  
     ```html
     <link rel="manifest" href="manifest.json">
     ```

### 总结：
`rel`属性定义了当前文档与链接资源之间的关系，浏览器会根据`rel`的值对资源进行不同的处理。常见的用途包括加载样式表、图标、预加载资源、指定规范URL等。
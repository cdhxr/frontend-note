---
tags:
  - http
date: 2025-03-22
---
# FormData方法

它是表示 HTML 表单数据的对象。

如果提供了 HTML `form` 元素，它会自动捕获 `form` 元素字段。

`FormData` 的特殊之处在于网络方法（network methods），例如 `fetch` 可以接受一个 `FormData` 对象作为 body。它会被编码并发送出去，带有 `Content-Type: multipart/form-data`。

从服务器角度来看，它就像是一个普通的表单提交。

```js
let response = await fetch('/article/formdata/post/user', { 
  method: 'POST', 
  body: new FormData(formElem) //它几乎就是一行代码
  });
```

我们可以从 HTML 表单创建 `new FormData(form)`，也可以创建一个完全没有表单的对象，然后使用以下方法附加字段：

- `formData.append(name, value)` —— 添加具有给定 `name` 和 `value` 的表单字段，
- `formData.append(name, blob, fileName)`—— 添加一个字段，就像它是 `<input type="file">`，第三个参数 `fileName` 设置文件名（而不是表单字段名），因为它是用户文件系统中文件的名称，
- `formData.set(name, value)`
- `formData.set(name, blob, fileName)`

`set` 方法，语法与 `append` 相同。不同之处在于 `.set` 移除所有具有给定 `name` 的字段，然后附加一个新字段，相当于**设置**

让我们在这里注意两个特点：

1. `set` 方法会移除具有相同名称（name）的字段，而 `append` 不会。
2. 要发送文件，需要使用三个参数的语法，最后一个参数是文件名，一般是通过 `<input type="file">` 从用户文件系统中获取的。

其他方法是：

- `formData.delete(name)`—— 移除带有给定 `name` 的字段，
- `formData.get(name)`—— 获取带有给定 `name` 的字段值，
- `formData.has(name)`—— 如果存在带有给定 `name` 的字段，则返回 `true`，否则返回 `false`。

这就是它的全貌！


# 发送带有文件的表单

表单始终以 `Content-Type: multipart/form-data` 来发送数据，这个编码允许发送文件。因此 `<input type="file">` 字段也能被发送，类似于普通的表单提交。

```js
<!-- 表单元素，id 为 formElem -->
<form id="formElem">
  <!-- 文本输入框，用户可以输入名字，默认值为 "John" -->
  <input type="text" name="firstName" value="John">
  
  <!-- 文件输入框，用户可以选择上传图片，限制文件类型为图片 -->
  Picture: <input type="file" name="picture" accept="image/*">
  
  <!-- 提交按钮 -->
  <input type="submit">
</form>

<script>
  // 监听表单提交事件
  formElem.onsubmit = async (e) => {
    e.preventDefault(); // 阻止默认表单提交，改用 JavaScript 处理

    // 使用 FormData 获取表单数据（包括文本和文件）
    let formData = new FormData(formElem);

    // 发送 POST 请求，将 FormData 作为请求体提交
    let response = await fetch('/article/formdata/post/user-avatar', {
      method: 'POST',
      body: formData // 直接传递 formData，浏览器会自动设置合适的 Content-Type
    });

    // 解析服务器返回的 JSON 数据
    let result = await response.json();

    // 弹出服务器返回的消息
    alert(result.message);
  };
</script>

```

# 发送图片

在实际中，通常更方便的发送图片的方式，是将其作为表单的一部分，并带有附加字段（例如 “name” 和其他 metadata）一起发送。

并且，服务器通常更适合接收多部分编码的表单（multipart-encoded form），而不是原始的二进制数据。

下面这个例子使用 `FormData` 将一个来自 `<canvas>` 的图片和一些其他字段一起作为一个表单提交
formData.append("image", imageBlob, "image.png");

```js
<body style="margin:0">
  <!-- 画布元素，设置宽高，并带有边框 -->
  <canvas id="canvasElem" width="100" height="80" style="border:1px solid"></canvas>

  <!-- 提交按钮，点击后调用 submit() 方法 -->
  <input type="button" value="Submit" onclick="submit()">

  <script>
    // 监听鼠标在 canvas 上移动的事件
    canvasElem.onmousemove = function(e) {
      let ctx = canvasElem.getContext('2d'); // 获取 2D 绘图上下文
      ctx.lineTo(e.clientX, e.clientY); // 画线到鼠标位置
      ctx.stroke(); // 绘制路径
    };

    // 提交画布内容的函数
    async function submit() {
      // 将 canvas 转换为 Blob 格式的图片
      let imageBlob = await new Promise(resolve => canvasElem.toBlob(resolve, 'image/png'));

      // 创建 FormData 对象并附加数据
      let formData = new FormData();
      formData.append("firstName", "John"); // 添加文本字段
      formData.append("image", imageBlob, "image.png"); // 添加图片文件

      // 发送 POST 请求
      let response = await fetch('/article/formdata/post/image-form', {
        method: 'POST',
        body: formData
      });

      // 解析服务器返回的 JSON 并弹出消息
      let result = await response.json();
      alert(result.message);
    }
  </script>
</body>
```
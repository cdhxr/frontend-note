---
tags:
  - http
date: 2025-03-22
---
它是表示 HTML 表单数据的对象。

如果提供了 HTML `form` 元素，它会自动捕获 `form` 元素字段。

`FormData` 的特殊之处在于网络方法（network methods），例如 `fetch` 可以接受一个 `FormData` 对象作为 body。它会被编码并发送出去，带有 `Content-Type: multipart/form-data`。

从服务器角度来看，它就像是一个普通的表单提交。
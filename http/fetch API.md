---
tags:
  - http
date: 2025-03-22
---

Fetch API 是一种现代 JavaScript 接口，用于在网络浏览器中发出 HTTP 请求

- Fetch 使用 Promises，允许编写更简洁的异步代码
- 它支持各种数据格式、自定义标头和不同类型的请求（GET、POST 等）
- API 的设计具有可扩展性，并能与其他网络技术很好地集成
- 虽然 Fetch 对于基本用例来说比较简单，但它也能处理复杂的情况，如请求取消和读取流式响应

它在现代浏览器中得到广泛支持，并已成为客户端 JavaScript 应用程序中网络请求的标准

# 基本语法：

`let promise = fetch(url, [options])`

- **`url`** —— 要访问的 URL。
- **`options`** —— 可选参数：method，header 等。

没有 `options`，这就是一个简单的 GET 请求，下载 `url` 的内容。
浏览器立即启动请求，并返回一个该调用代码应该用来获取结果的 `promise`

获取响应通常需要经过两个阶段。

## **第一阶段，当服务器发送了响应头（response header），`fetch` 返回的 `promise` 就使用内建的 [Response](https://fetch.spec.whatwg.org/#response-class) class 对象来对响应头进行解析。**

在这个阶段，我们可以通过检查响应头，来检查 HTTP 状态以确定请求是否成功，当前还没有响应体（response body）。

如果 `fetch` 无法建立一个 HTTP 请求，例如网络问题，亦或是请求的网址不存在，那么 promise 就会 reject。异常的 HTTP 状态，例如 404 或 500，不会导致出现 error。

我们可以在 response 的属性中看到 HTTP 状态：

- **`status`** —— HTTP 状态码，例如 200。
- **`ok`** —— 布尔值，如果 HTTP 状态码为 200-299，则为 `true`。

## **第二阶段，为了获取 response body，我们需要使用一个其他的方法调用。**

`Response` 提供了多种基于 promise 的方法，来以不同的格式访问 body

我们只能选择一种读取 body 的方法。

如果我们已经使用了 `response.text()` 方法来获取 response，那么如果再用 `response.json()`，则不会生效，因为 body 内容已经被处理过了。

例如，我们从 GitHub 获取最新 commits 的 JSON 对象：

```js
let url = 'https://api.github.com/repos/javascript-tutorial/en.javascript.info/commits';

let response = await fetch(url);

let commits = await response.json(); // 读取 response body，并将其解析为 JSON 格式

alert(commits[0].author.login);
```

# Response header and Request header

## Response header

Response header 位于 `response.headers` 中的一个类似于 Map 的 header 对象。它不是真正的 Map，但是它具有类似的方法，我们可以按名称（name）获取各个 header，或迭代它们

```js
let response = await fetch('https://api.github.com/repos/javascript-tutorial/en.javascript.info/commits');

// 获取一个 header
alert(response.headers.get('Content-Type')); // application/json; charset=utf-8

// 迭代所有 header
for (let [key, value] of response.headers) {
  alert(`${key} = ${value}`);
}
```

## Request header

要在 `fetch` 中设置 request header，我们可以使用 `headers` 选项。它有一个带有输出 header 的对象，如下所示：

```js
let response = fetch(protectedUrl, {
  headers: {
    Authentication: 'secret'
  }
});
```

……但是有一些我们无法设置的 header（详见 [forbidden HTTP headers](https://fetch.spec.whatwg.org/#forbidden-header-name)）
这些 header 保证了 HTTP 的正确性和安全性，所以它们仅由浏览器控制。

## Post请求

要创建一个 `POST` 请求，或者其他方法的请求，我们需要使用 `fetch` 选项：

- **`method`** —— HTTP 方法，例如 `POST`，
- **`body`** —— request body，其中之一：
    - 字符串（例如 JSON 编码的），
    - `FormData` 对象，以 `multipart/form-data` 形式发送数据，
    - `Blob`/`BufferSource` 发送二进制数据，

JSON 形式是最常用的。

例如，下面这段代码以 JSON 形式发送 `user` 对象：

```js
let user = {
  name: 'John',
  surname: 'Smith'
};

let response = await fetch('/article/fetch/post/user', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json;charset=utf-8'
  },
  body: JSON.stringify(user)
});

let result = await response.json();
alert(result.message);

```

我们同样可以使用 `Blob` 或 `BufferSource` 对象通过 `fetch` 提交二进制数据。

```js
async function submit() {

  let blob = await new Promise(resolve => canvasElem.toBlob(resolve, 'image/png'));

  let response = await fetch('/article/fetch/post/image', {
    method: 'POST',
	body: blob
  });

  // 服务器给出确认信息和图片大小作为响应
  let result = await response.json();
  alert(result.message);
}
```

# 总结

典型的 fetch 请求由两个 `await` 调用组成：

```js
let response = await fetch(url, options); // 解析 response header
let result = await response.json(); // 将 body 读取为 json
```

或者以 promise 形式：

```js
fetch(url, options)
.then(response => response.json())
.then(result => /* process result */)
```

响应的属性：

- `response.status` —— response 的 HTTP 状态码，
- `response.ok` —— HTTP 状态码为 200-299，则为 `true`。
- `response.headers` —— 类似于 Map 的带有 HTTP header 的对象。

获取 response body 的方法：

- **`response.text()`** —— 读取 response，并以文本形式返回 response，
- **`response.json()`** —— 将 response 解析为 JSON 对象形式，
- **`response.formData()`** —— 以 `FormData` 对象（`multipart/form-data` 编码，参见下一章）的形式返回 response，
- **`response.blob()`** —— 以 [Blob](https://zh.javascript.info/blob)（具有类型的二进制数据）形式返回 response，
- **`response.arrayBuffer()`** —— 以 [ArrayBuffer](https://zh.javascript.info/arraybuffer-binary-arrays)（低级别的二进制数据）形式返回 response。

到目前为止我们了解到的 fetch 选项：

- `method` —— HTTP 方法，
- `headers` —— 具有 request header 的对象（不是所有 header 都是被允许的）
- `body` —— 要以 `string`，`FormData`，`BufferSource`，`Blob` 或 `UrlSearchParams` 对象的形式发送的数据（request body）。
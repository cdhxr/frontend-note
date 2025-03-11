
| promise                                                                | callback                                                                                                  |
| ---------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| promise 允许我们按照自然顺序进行编码。首先，我们运行 `loadScript`，之后，用 `.then` 来处理结果。        | 在调用 `loadScript(script, callback)` 时，我们必须有一个 `callback` 函数可供使用。换句话说，在调用 `loadScript` **之前**，我们必须知道如何处理结果。 |
| 我们可以根据需要，在 promise 上多次调用 `.then`。每次调用，我们都会在“订阅列表”中添加一个新的“粉丝”，一个新的订阅函数。 | 只能有一个回调。                                                                                                  |

相当于，
callback是在 **完成** 后直接执行函数，
promise是在 **完成** 后承诺其已完成，提供了一个 **完成状态** 可供外部访问，在外部确认状态后，执行函数

好处是，对于先前执行的函数来说，
如果需要直接执行函数，它必须知道这个函数，即必须传入函数，会使程序更加耦合复杂，程序编写的顺序也十分反逻辑，因为要传入函数，意味着要预先定义好未来的函数

而定义状态，不涉及外部，可以专注于自身的逻辑，解决了上述缺点

### 1. **Promise 的定义**

- **Promise** 是一种表示异步操作最终完成或失败的机制。它有三种状态：
    - **Pending**（待定）：初始状态，操作未完成。
    - **Fulfilled**（已完成）：操作成功完成。
    - **Rejected**（已拒绝）：操作失败。

### 2. **创建 Promise**

- 使用 `new Promise` 构造函数创建 Promise 对象，需要传入一个执行器（executor）函数，执行器函数有两个参数：`resolve` 和 `reject`

当 executor 获得了结果，无论是早还是晚都没关系，它应该调用以下回调之一：
- `resolve(value)` —— 如果任务成功完成并带有结果 `value`。 用于将 Promise 状态从 **Pending** 变为 **Fulfilled**。
- `reject(error)` —— 如果出现了 error，`error` 即为 error 对象。用于将 Promise 状态从 **Pending** 变为 **Rejected**。
- `resolve` 和 `reject` 这些函数由 JavaScript 引擎预先定义，因此我们不需要创建它们

```js
let promise = new Promise(function(resolve, reject) { 
	// 当 promise 被构造完成时，自动执行此函数 

	// 1 秒后发出工作已经被完成的信号，并带有结果 "done" 
	setTimeout(() => resolve("done"),1000);
});
```

### 3. **使用 Promise**

- **`then()`** 方法用于处理 **Fulfilled** 状态的 Promise。
    - `.then(onFulfilled, onRejected)` 接受两个参数，分别是当 Promise 完成或拒绝时执行的回调函数。
- **`catch()`** 方法用于处理 **Rejected** 状态的 Promise。
    - `.catch(onRejected)` 是 `.then(null, onRejected)` 的简写，专门用来处理错误。

```js
// resolve运行.then中的第一个函数
promise.then(
  result=>alert(result),//1秒后显示"done!"
  error=>alert(error)//不运行
})

// reject 运行 .then 中的第二个函数
promise.then(
  result=>alert(result),//不运行
  error=>alert(error)//1秒后显示"Error:Whoops!"
);
```

### 4. **Promise 语法糖**

- **`finally()`** 方法用于无论 Promise 是完成还是失败后都执行的代码（类似于传统编程中的 "finally"）。
- 可以用于清理操作、日志记录，确保不管结果如何都执行特定的代码。
- `finally()` 方法并不改变 Promise 的值或状态，它只是在 Promise 完成时执行一个清理。但是，`finally()` 返回的值并不会影响 Promise 的最终值。

```javascript
promise.finally(() => {
  // 执行一些清理操作
});
```

### 5. **Promise 的用途**

- Promise 使得异步操作的处理更加简洁，避免了回调地狱（callback hell）。
- 可以通过链式调用来处理多个异步操作，并且更容易进行错误处理。

### 6. **Promise.all() 和 Promise.race()**

- **`Promise.all()`**：接受一个 Promise 数组，返回一个新的 Promise，只有当所有的 Promise 都成功时，它才会被 **Fulfilled**，否则会被 **Rejected**。
- **`Promise.race()`**：接受一个 Promise 数组，返回一个新的 Promise，最先完成的 Promise 状态决定了返回 Promise 的状态。


```javascript
let promise = new Promise((resolve, reject) => {
  setTimeout(resolve, 1000, "操作完成");
});

promise
  .then((result) => {
    console.log(result);  // "操作完成"
  })
  .catch((error) => {
    console.log(error);   // 如果有错误会执行此部分
  })
  .finally(() => {
    console.log("无论如何都会执行");
  });
```


### 8. **链式调用**

- `then()` 方法返回一个新的 Promise，因此可以链式调用多个 `then()` 方法。
- 每个 `then()` 返回的 Promise 会继承上一个 `then()` 的返回值，或者如果发生错误，会跳到下一个 `catch()`。

```js
promise.then(f).then(f).then(f)...
```

- 如果 `.then` 内部的回调函数正常执行且没有抛出错误，返回的 Promise 会变为 `fulfilled`（已解决）状态，且结果是回调函数的返回值。
- 如果回调函数抛出错误，返回的 Promise 会变为 `rejected`（已拒绝）状态，且错误是抛出的异常。

```js
//链式调用示例
new Promise(function(resolve, reject) {
	setTimeout(() => resolve(1), 1000); // (*)

}).then(function(result) { // (**)

	alert(result); // 1
	return result * 2;

}).then(function(result) { // (***)

	alert(result); // 2
	return result * 2;

}).then(function(result) {

	alert(result); // 4
	return result * 2;

});
```


`.then(handler)` 中所使用的处理程序（handler）可以创建并返回一个 promise。
在这种情况下，其他的处理程序将等待它 settled 后再获得其结果。

```javascript
new Promise(function(resolve, reject) {
  setTimeout(() => resolve(1), 1000);
}).then(function(result) {
  alert(result); // 1
  return new Promise((resolve, reject) => { // (*)
    setTimeout(() => resolve(result * 2), 1000);
  });
}).then(function(result) { // (**)
  alert(result); // 2
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve(result * 2), 1000);
  });
}).then(function(result) {
  alert(result); // 4
});
```

返回 promise 使我们能够构建异步行为链。

确切地说，处理程序返回的不完全是一个 promise，而是返回的被称为 “thenable” 对象 —— 一个具有方法 `.then` 的任意对象。它会被当做一个 promise 来对待。

### 7. **示例loadScript**

callback写法

```js
function loadScript(src, callback) {
    let script = document.createElement('script');
    script.src = src;
    
    script.onload = () => callback(null, script);
    script.onerror = () => callback(new Error(`error for ${src}`));
    
    document.head.append(script);
}
```

利用promise重构的loadScript函数

```js
function loadScript(src) {
	return new Promise(function(resolve, reject) {
		let script = document.createElement('script');
		script.src = src;
	
		script.onload = () => resolve(script);
		script.onerror = () => reject(new Error(`error for ${src}`));
		
		document.head.append(script);
	});
}
```

用法

```js
let promise = loadScript("https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.11/lodash.js");

promise.then(
	script => alert(`${script.src} is loaded!`),
	error => alert(`Error: ${error.message}`)
);

promise.then(script => alert('Another handler...'));
```

**第二个 `then()`** 会在第一个 `then()` 之后继续执行。它会在脚本加载成功时弹出另一个提示框，显示 "Another handler..."，这表明 Promise 是可以多次链式调用的。

链式调用，按顺序依次加载脚本（因为loadScript返回promise对象所以可以这么写）

```javascript
loadScript("/article/promise-chaining/one.js")
  .then(script => loadScript("/article/promise-chaining/two.js"))
  .then(script => loadScript("/article/promise-chaining/three.js"))
  .then(script => {
    // 脚本加载完成，我们可以在这儿使用脚本中声明的函数
    one();
    two();
    three();
  });
```


### 示例，fetch

发起请求，返回响应，解析响应，操作数据

```javascript
// 发送一个对 user.json 的请求
fetch('/article/promise-chaining/user.json')
  // 将其加载为 JSON
  .then(response => response.json())
  // 发送一个到 GitHub 的请求
  .then(user => fetch(`https://api.github.com/users/${user.name}`))
  // 将响应加载为 JSON
  .then(response => response.json())
  // 显示头像图片（githubUser.avatar_url）3 秒（也可以加上动画效果）
  .then(githubUser => {
    let img = document.createElement('img');
    img.src = githubUser.avatar_url;
    img.className = "promise-avatar-example";
    document.body.append(img);

    setTimeout(() => img.remove(), 3000); // (*)
  });
```

为了使链可扩展，我们需要返回一个在头像显示结束时进行 resolve 的 promise。

最后一个then修改如下：

```javascript
.then(githubUser => new Promise(function(resolve, reject) { // (*)
    let img = document.createElement('img');
    img.src = githubUser.avatar_url;
    img.className = "promise-avatar-example";
    document.body.append(img);

    setTimeout(() => {
      img.remove();
      resolve(githubUser); // (**)
    }, 3000);
}))
```

作为一个好的做法，异步行为应该始终返回一个 promise。这样就可以使得之后我们计划后续的行为成为可能。即使我们现在不打算对链进行扩展，但我们之后可能会需要。

最后，我们可以将代码拆分为可重用的函数：

```js
// 加载 JSON 数据的函数，代表fetch(URL)得到response解析为JSON的过程
function loadJson(url) {
  return fetch(url)
    .then(response => response.json());
}

//以下是两个特定的函数，实现特定的功能

// 加载 GitHub 用户信息的函数
function loadGithubUser(name) {
  return loadJson(`https://api.github.com/users/${name}`);
}

// 显示 GitHub 用户头像的函数
function showAvatar(githubUser) {
  return new Promise(function(resolve, reject) {
    let img = document.createElement('img');
    img.src = githubUser.avatar_url;
    img.className = "promise-avatar-example";
    document.body.append(img);

    setTimeout(() => {
      img.remove();
      resolve(githubUser); // 3 秒后移除图片并返回用户信息
    }, 3000);
  });
}

// 使用这些函数
loadJson('/article/promise-chaining/user.json')
  .then(user => loadGithubUser(user.name)) // 加载 GitHub 用户信息
  .then(showAvatar) // 显示头像
  .then(githubUser => alert(`Finished showing ${githubUser.name}`)); // 显示完成提示
  // ...
```

用async，await重构版本

```javascript
async function showAvatar() {

  // 读取我们的 JSON
  let response = await fetch('/article/promise-chaining/user.json');
  let user = await response.json();

  // 读取 github 用户信息
  let githubResponse = await fetch(`https://api.github.com/users/${user.name}`);
  let githubUser = await githubResponse.json();

  // 显示头像
  let img = document.createElement('img');
  img.src = githubUser.avatar_url;
  img.className = "promise-avatar-example";
  document.body.append(img);

  // 等待 3 秒
  await new Promise((resolve, reject) => setTimeout(resolve, 3000));

  img.remove();

  return githubUser;
}

showAvatar();
```

## Promisification

将已有的callback实现的函数，转换为return promise的形式的 函数

如果原始的 `f` 期望一个带有更多参数的回调 `callback(err, res1, res2, ...)`，该怎么办呢？

我们可以继续改进我们的辅助函数。让我们写一个更高阶版本的 `promisify`。

- 当它被以 `promisify(f)` 的形式调用时，它应该以与上面那个版本的实现的工作方式类似。
- 当它被以 `promisify(f, true)` 的形式调用时，它应该返回以回调函数数组为结果 resolve 的 promise。这就是具有很多个参数的回调的结果。

```js
function promisify(f, manyArgs = false) {
  return function (...args) {
    return new Promise((resolve, reject) => {
      function callback(err, ...results) {  // 我们自定义的 f 的回调
        if (err) {
          reject(err);
        } else {
          // 如果 manyArgs 被指定，则使用所有回调的结果 resolve
          resolve(manyArgs ? results : results[0]);
        }
      }
      args.push(callback);
      f.call(this, ...args);
    });
  };
}

// 用法：
f = promisify(f, true);
f(...).then(arrayOfResults => ..., err => ...);

```


正如你所看到的，它与上面那个实现基本相同，只是根据 `manyArgs` 是否为真来决定仅使用一个还是所有参数调用 `resolve`。

对于一些更奇特的回调格式，例如根本没有 `err` 的格式：`callback(result)`，我们可以手动 promise 化这样的函数，而不使用 helper。

也有一些具有更灵活一点的 promisification 函数的模块（module），例如 [es6-promisify](https://github.com/digitaldesignlabs/es6-promisify)。在 Node.js 中，有一个内建的 promise 化函数 `util.promisify`。
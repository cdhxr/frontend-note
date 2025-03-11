
捕获所有 error 的最简单的方法是，将 `.catch` 附加到链的末尾：

```js
fetch('/article/promise-chaining/user.json')
.then(response => response.json()) 
.then(user => fetch(`https://api.github.com/users/${user.name}`))
.then(response => response.json())
.then(githubUser => new Promise((resolve, reject) => {
	let img = document.createElement('img');
	img.src = githubUser.avatar_url;
	img.className = "promise-avatar-example";
	document.body.append(img);
	
	setTimeout(() => {
		img.remove();
		resolve(githubUser);
	}, 3000);
}))
.catch(error => alert(error.message));
```

通常情况下，这样的 `.catch` 根本不会被触发。但是如果上述任意一个 promise rejected（网络问题或者无效的 json 或其他），`.catch` 就会捕获它。

### 隐式 try…catch

promise 的执行者（executor）和 promise 的处理程序周围有一个“隐式的 `try..catch`”。如果发生异常，它就会被捕获，并被视为 rejection 进行处理。

```javascript
new Promise((resolve, reject) => {
  throw new Error("Whoops!");
}).catch(alert); // Error: Whoops!
```

……与下面这段代码工作上完全相同：

```javascript
new Promise((resolve, reject) => {
  reject(new Error("Whoops!"));
}).catch(alert); // Error: Whoops!
```

这不仅仅发生在 executor 函数中，同样也发生在其处理程序中。如果我们在 `.then` 处理程序中 `throw`，这意味着 promise rejected，因此控制权移交至最近的 error 处理程序。

最后的 `.catch` 不仅会捕获显式的 rejection，还会捕获它上面的处理程序中意外出现的 error。

### 再次抛出（Rethrowing）

这里 `.catch` 块正常完成。所以下一个成功的 `.then` 处理程序就会被调用。

在下面的例子中，我们可以看到 `.catch` 的另一种情况。`(*)` 行的处理程序捕获了 error，但无法处理它（例如，它只知道如何处理 `URIError`），所以它将其再次抛出：

```js
// 执行流：catch -> catch

new Promise((resolve, reject) => {

	throw new Error("Whoops!");

}).catch(function(error) { // (*)

	if (error instanceof URIError) {
		// 处理它
	} else {
		alert("Can't handle such error");

		throw error; // 再次抛出此 error 或另外一个 error，执行将跳转至下一个 catch
	}

}).then(function() {
/* 不在这里运行 */
}).catch(error => { // (**)

alert(`The unknown error has occurred: ${error}`);
// 不会返回任何内容 => 执行正常进行

});
```
执行从第一个 `.catch` `(*)` 沿着链跳转至下一个 `(**)`。

### 未处理的rejection

当一个 error 没有被处理会发生什么？例如，我们忘了在链的尾端附加 `.catch`
如果出现 error，promise 的状态将变为 “rejected”，然后执行应该跳转至最近的 rejection 处理程序。但上面这个例子中并没有这样的处理程序。因此 error 会“卡住”。没有代码来处理它。

在实际开发中，就像代码中常规的未处理的 error 一样，这意味着某些东西出了问题。

当发生一个常规的 error 并且未被 `try..catch` 捕获时会发生什么？脚本死了，并在控制台中留下了一个信息。对于在 promise 中未被处理的 rejection，也会发生类似的事。

JavaScript 引擎会跟踪此类 rejection，在这种情况下会生成一个全局的 error。如果你运行上面这个代码，你可以在控制台中看到。
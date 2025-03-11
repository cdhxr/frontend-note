
async/await 是以更舒适的方式使用 promise 的一种特殊语法，同时它也非常易于理解和使用。

```js
async function f() {
	return 1;
}

f().then(alert); // 1
```

在函数前面的 “async” 这个单词表达了一个简单的事情：即这个函数总是返回一个 promise。其他值将自动被包装在一个 resolved 的 promise 中。

Promise 前的关键字 `await` 让 JavaScript 引擎等待直到 promise 完成（settle）并返回结果

```js
// 只在 async 函数内工作 
async function f() {
	let promise = new Promise((resolve, reject) => {
	  setTimeout(() => resolve("done!"), 1000)
	});

	let result = await promise; // 等待，直到 promise resolve (*)
	
	alert(result); // "done!"
}

f();
```

相比于 `promise.then`，它只是获取 promise 的结果的一个更优雅的语法。并且也更易于读写。


```js
//await与then写法上的差异

let result = await f(a); 
// 处理结果

f(a).then(result => { 
// 处理结果 
})

```

await的逻辑：

提前定义了处理的结果变量，
再处理变量

then的逻辑：

创建一个函数，
将返回值作为结果

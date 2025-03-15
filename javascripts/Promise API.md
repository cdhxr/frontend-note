---
tags:
  - js
---

`Promise` 类有 6 种静态方法：

1. `Promise.all(promises)` —— 等待所有 promise 都 resolve 时，返回存放它们结果的数组。如果给定的任意一个 promise 为 reject，那么它就会变成 `Promise.all` 的 error，所有其他 promise 的结果都会被忽略。
2. `Promise.allSettled(promises)`（ES2020 新增方法）—— 等待所有 promise 都 settle 时，并以包含以下内容的对象数组的形式返回它们的结果：
    - `status`: `"fulfilled"` 或 `"rejected"`
    - `value`（如果 fulfilled）或 `reason`（如果 rejected）。
3. `Promise.race(promises)` —— 等待第一个 settle 的 promise，并将其 result/error 作为结果返回。
4. `Promise.any(promises)`（ES2021 新增方法）—— 等待第一个 fulfilled 的 promise，并将其结果作为结果返回。如果所有 promise 都 rejected，`Promise.any` 则会抛出 [`AggregateError`](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/AggregateError) 错误类型的 error 实例。
5. `Promise.resolve(value)` —— 使用给定 value 创建一个 resolved 的 promise。
6. `Promise.reject(error)` —— 使用给定 error 创建一个 rejected 的 promise。


在JavaScript中，`Promise` 是一种用于处理异步操作的机制。`Promise` API 提供了几个重要的方法，可以帮助你更高效地处理异步操作。下面是 `Promise` API 中最常用的几个方法：

### 3. **`Promise.all(iterable)`**

- 接受一个可迭代对象（如数组），返回一个新的 `Promise`，当所有传入的 `Promise` 都成功时，它会被解决，返回一个数组包含所有 `Promise` 的解决值。如果其中一个 `Promise` 被拒绝，`Promise.all()` 会立即拒绝并返回第一个拒绝的 `reason`。

**示例**：

```javascript
const p1 = Promise.resolve(3);
const p2 = new Promise(resolve => setTimeout(resolve, 100, 'foo'));
const p3 = 42;

Promise.all([p1, p2, p3]).then(values => {
  console.log(values); // [3, 'foo', 42]
});
```

### 4. **`Promise.allSettled(iterable)`**

- 接受一个可迭代对象，返回一个新的 `Promise`，该 `Promise` 在所有传入的 `Promise` 完成时（无论是解决还是拒绝）都会完成。返回的数组会包含每个 `Promise` 的状态和相应的值或拒绝原因。

**示例**：

```javascript
const p1 = Promise.resolve(3);
const p2 = Promise.reject('Error');
const p3 = Promise.resolve(42);

Promise.allSettled([p1, p2, p3]).then(results => {
  console.log(results);
  // [
  //   { status: 'fulfilled', value: 3 },
  //   { status: 'rejected', reason: 'Error' },
  //   { status: 'fulfilled', value: 42 }
  // ]
});
```

### 5. **`Promise.any(iterable)`**

- 接受一个可迭代对象，返回一个新的 `Promise`。当其中任意一个 `Promise` 成功时，返回该 `Promise` 的结果。如果所有 `Promise` 都失败（即都被拒绝），它会返回一个拒绝的 `Promise`，并包含所有 `Promise` 的拒绝原因。

**示例**：

```javascript
const p1 = Promise.reject('Error1');
const p2 = Promise.resolve('Success');
const p3 = Promise.reject('Error2');

Promise.any([p1, p2, p3]).then(value => {
  console.log(value); // Success
});
```

### 6. **`Promise.race(iterable)`**

- 接受一个可迭代对象，返回一个新的 `Promise`。该 `Promise` 在第一个完成（无论是成功还是失败）的 `Promise` 完成时就完成。它会采用第一个 `Promise` 的结果，忽略其他 `Promise` 的状态。

**示例**：

```javascript
const p1 = new Promise(resolve => setTimeout(resolve, 500, 'p1'));
const p2 = new Promise(resolve => setTimeout(resolve, 100, 'p2'));

Promise.race([p1, p2]).then(value => {
  console.log(value); // p2
});
```

### 7. **`Promise.finally(onFinally)`**

- 当 `Promise` 完成（无论是解决还是拒绝）时，总是会执行 `onFinally` 中的回调函数。它不会改变 `Promise` 的结果，但可以用于清理操作。

**示例**：

```javascript
const p = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, 'Done');
});

p.finally(() => {
  console.log('Clean up!');
}).then(value => {
  console.log(value); // Done
});
```


### 总结

`Promise` API 提供了一些强大的方法来处理异步操作，例如并行执行、失败回调、按顺序执行等。熟练掌握这些方法能够帮助你编写更清晰、更高效的异步代码。你可以根据具体的需求，选择合适的 `Promise` 方法来组织异步流程。
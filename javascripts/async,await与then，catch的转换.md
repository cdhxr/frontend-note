



使用 `async/await` 重写 `then/catch` 可以让异步代码更简洁、易读。以下是关键技巧和示例：

---

### **1. 基础转换：`then` → `await`，`catch` → `try/catch`**
**原代码（Promise 链）：**
```javascript
fetchData()
  .then(response => console.log(response))
  .catch(error => console.error(error));
```

**重写为 `async/await`：**
```javascript
async function fetchDataAsync() {
  try {
    const response = await fetchData();
    console.log(response);
  } catch (error) {
    console.error(error);
  }
}
```

---

### **2. 链式 `then` 转换为顺序 `await`**
**原代码（链式调用）：**
```javascript
fetchData()
  .then(processData)
  .then(saveData)
  .catch(handleError);
```

**重写为 `async/await`：**
```javascript
async function processAsync() {
  try {
    const raw = await fetchData();
    const processed = await processData(raw);
    await saveData(processed);
  } catch (error) {
    handleError(error);
  }
}
```

---

### **3. 并行请求：`Promise.all` → `await Promise.all`**
**原代码（并行处理）：**
```javascript
Promise.all([fetchUser(), fetchPosts()])
  .then(([user, posts]) => console.log(user, posts))
  .catch(error => console.error(error));
```

**重写为 `async/await`：**
```javascript
async function fetchParallel() {
  try {
    const [user, posts] = await Promise.all([fetchUser(), fetchPosts()]);
    console.log(user, posts);
  } catch (error) {
    console.error(error);
  }
}
```

---

### **4. 错误处理技巧**
#### **4.1 单独处理某个步骤的错误**
```javascript
async function handleSpecificError() {
  try {
    const data = await fetchData();
    try {
      const processed = await processData(data);
    } catch (processError) {
      console.error("处理数据失败:", processError);
    }
    await saveData(data); // 即使 processData 失败，继续执行
  } catch (error) {
    console.error("全局错误:", error);
  }
}
```

#### **4.2 在 `finally` 中执行清理**
**原代码：**
```javascript
fetchData()
  .then(data => console.log(data))
  .catch(error => console.error(error))
  .finally(() => stopLoading());
```

**重写为 `async/await`：**
```javascript
async function fetchWithFinally() {
  try {
    const data = await fetchData();
    console.log(data);
  } catch (error) {
    console.error(error);
  } finally {
    stopLoading();
  }
}
```

---

### **5. 返回值的处理**
`async` 函数默认返回 `Promise`，无需显式包装：
```javascript
// 原代码
function getData() {
  return fetchData().then(data => data.id);
}

// 重写为 async/await
async function getDataAsync() {
  const data = await fetchData();
  return data.id; // 自动包装为 Promise
}
```

---

### **6. 循环中的异步操作**
#### **顺序执行：**
```javascript
async function processSequentially(items) {
  for (const item of items) {
    await processItem(item); // 逐个处理
  }
}
```

#### **并行执行：**
```javascript
async function processInParallel(items) {
  await Promise.all(items.map(item => processItem(item))); // 同时处理所有项
}
```

---

### **7. 高级技巧：避免不必要的 `try/catch`**
通过包裹函数简化错误处理：
```javascript
async function safeAwait(promise) {
  try {
    return await promise;
  } catch (error) {
    console.error(error);
    return null; // 或自定义默认值
  }
}

// 使用示例
async function example() {
  const data = await safeAwait(fetchData());
  if (data) console.log(data);
}
```

---

### **总结**
- 用 `await` 替换 `.then()`，用 `try/catch` 替换 `.catch()`。
- 需要并行操作时，优先使用 `Promise.all`。
- 通过嵌套 `try/catch` 实现细粒度错误控制。
- 循环中的异步操作根据需求选择顺序或并行执行。

这些技巧能显著提升代码可读性，同时保留 Promise 的灵活性！
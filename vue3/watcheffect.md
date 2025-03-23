`watchEffect` 是 Vue 3 中的一个 API，用于自动追踪所有响应式数据的变化并执行副作用。与 `watch` 不同，`watchEffect` 无需明确指定要监视的数据源，它会自动追踪回调函数中使用的所有响应式数据。当这些数据发生变化时，`watchEffect` 会自动重新执行。

### 主要特点：

- **自动追踪依赖**：`watchEffect` 会自动收集回调中使用的响应式数据。
- **立即执行**：`watchEffect` 在创建时会立即执行一次回调，并根据数据变化自动触发后续执行。
- **没有旧值对比**：不像 `watch`，`watchEffect` 只会执行副作用，无法获得新旧值对比。

### 使用场景：

- 用于执行副作用操作，如 DOM 更新、异步请求等。
- 适合不需要显式列出依赖、自动处理多个响应式数据的场景。

### 示例：

```js
import { ref, watchEffect } from 'vue';

const count = ref(0);
const name = ref('Vue');

watchEffect(() => {
  console.log(`count: ${count.value}, name: ${name.value}`);
});

count.value = 10;  // 会触发回调：count: 10, name: Vue
name.value = 'Vue 3';  // 会触发回调：count: 10, name: Vue 3
```

### 总结：

`watchEffect` 适用于那些不需要明确指定依赖、自动追踪响应式数据变化的副作用操作。它简单易用，但在复杂的场景中可能不如 `watch` 精细控制。
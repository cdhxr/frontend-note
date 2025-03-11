
```js
import { reactive } from 'vue';

const state = reactive({
  count: 0,
  name: 'Vue3'
});

// 解构时失去响应性
const { count, name } = state;
count = 10;  // 这里的 count 不是响应式的
```

**解构操作** 就是从 `state` 对象中提取出 `count` 和 `name` 这两个属性

这相当于
```js
const count = state.count; // 从 state 中提取 count 属性的值并赋给 count 变量
const name = state.name; // 从 state 中提取 name 属性的值并赋给 name 变量
```

`count` 和 `name` 只是普通的变量，它们会丧失响应性

如果你希望在解构后仍然保持响应性，Vue 提供了 `toRefs` 和 `toRef` 方法来帮助你将 `reactive` 对象的属性转换为 `ref`，这样解构后的属性仍然是响应式的。

```js
import { reactive, toRefs } from 'vue';

const state = reactive({
  count: 0,
  name: 'Vue3'
});

// 使用 toRefs 保证解构后的属性仍然是响应式的
const { count, name } = toRefs(state);

// count 和 name 仍然是 ref 对象，保持响应式
```
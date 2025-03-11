---
tags:
  - vue
date: 2025-03-03
completed: true
---
# 大致理解

是一个状态管理工具，这里的状态其实就是指数据

![[piniaExample.jpg]]
如图，当项目复杂时会出现十分复杂的组件关系
所以我们需要一个管理工具，以方便的管理数据
Pinia主要是用于对共享数据的管理，或者说全局数据的管理，会被多个组件使用的数据

# 核心概念

## **Store**

每个 **Store** 是一个独立的模块，正如其名字，承担着仓库的作用，存储全局状态
它由三部分组成：

- state
- getter
- action

可以理解为组件的data，computed和methods

## 环境搭建

```ts
npm i pinia // 在终端中

// 在main.ts中
//引入，创键，安装
import {createPinia} from 'pinia'
const pinia = createPinia()
app.use(pinia)
```

## pinia存储读取数据

按照规范，应该在src下创建store文件夹，内部存储ts文件命名，应该和对应的组件名相同保持语义化

```ts
import {'ref'} from 'vue'
import {definestore} from 'pinia'
//defineStore，
//第一个参数是模块名，
//第二个参数传如一个箭头函数，函数内部定义数据，并确定其修改数据的方法
//这也和需要接受对象的返回值自洽
export const useCountStore = defineStore('counter',()=>{
	const count = ref<number>(0)
	const increment = () =>{
		const count++
	}
	
	return{
		count,
		increment
	}
})
```

在vue组件中使用：

```vue
<template>
	<div>
		<button @click="counterStore.increment">
			加一{{counterStore.count}}
		</button>
	</div>
</template>

<script setup lang="ts">

import {useCountStore} from './store/counter'
const counterStore = useCountStore()//用变量接受即可渲染

</script>
```

## Store

### 简介

一个Store是代表一个功能相关的ts文件，类似于class随用随取，是一个独立的全局模块

Store对象（instance）
`store.xxx`
- state确定的数据变量名
- action确定的方法名
- getter确定的computed方法名

### 定义：

参数为，商店名，以及内部的三大属性的定义，此为选项式写法，分三个部分

```ts
export const useCounterStore = defineStore('counter', {
  state: () => ({ count: 0, name: 'Eduardo' }),
  getters: {
    doubleCount: (state) => state.count * 2,
  },
  actions: {
    increment() {
      this.count++
    },
  },
})
```

**组合式写法**：
类似setup的思想，将语法简化

```ts
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  const doubleCount = computed(() => count.value * 2)
  function increment() {
    count.value++
  }

  return { count, doubleCount, increment }
})
```

在 _Setup Store_ 中：

- `ref()` 就是 `state` 属性
- `computed()` 就是 `getters`
- `function()` 就是 `actions`

### 从 Store 解构 :`storeToRefs()`

为了从 store 中提取属性时保持其响应性，你需要使用 `storeToRefs()`，类似于torefs思想

```ts
<script setup>
import { storeToRefs } from 'pinia'
const store = useCounterStore()
// `name` 和 `doubleCount` 是响应式的 ref
// 同时通过插件添加的属性也会被提取为 ref
// 并且会跳过所有的 action 或非响应式 (不是 ref 或 reactive) 的属性
const { name, doubleCount } = storeToRefs(store)
// 作为 action 的 increment 可以直接解构
const { increment } = store
</script>
```

## State

### ts类型限制

```ts
interface State {
  userList: UserInfo[]
  user: UserInfo | null
}

const useStore = defineStore('storeId', {
  state: (): State => {
    return {
      userList: [],
      user: null,
    }
  },
})

interface UserInfo {
  name: string
  age: number
}
```

### 重置state：`$reset()`

创建自己的 `$reset()` 方法：

```ts
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)

  function $reset() {
    count.value = 0
  }

  return { count, $reset }
})
```

### 修改state：`$patch`

除了用 `store.count++` 直接改变 store，你还可以调用 `$patch` 方法。它允许你用一个 `state` 的补丁对象在同一时间更改多个属性：

```ts
store.$patch({
  count: store.count + 1,
  age: 120,
  name: 'DIO',
})
```

不过，用这种语法的话，有些变更真的很难实现或者很耗时：任何集合的修改（例如，向数组中添加、移除一个元素或是做 `splice` 操作）都需要你创建一个新的集合。因此，`$patch` 方法也接受一个函数来组合这种难以用补丁对象实现的变更。

### 监听state：`$subscribe()`

mutation和state是内部对象，下面是一些mutation属性的解释
最后一行， 每当状态发生变化时，将整个 state 持久化到本地存储。（刷新不会丢失）

```ts
cartStore.$subscribe((mutation, state) => {
  // import { MutationType } from 'pinia'
  mutation.type // 'direct' | 'patch object' | 'patch function'
  // 和 cartStore.$id 一样
  mutation.storeId // 'cart'
  // 只有 mutation.type === 'patch object'的情况下才可用
  mutation.payload // 传递给 cartStore.$patch() 的补丁对象。

  // 每当状态发生变化时，将整个 state 持久化到本地存储。
  localStorage.setItem('cart', JSON.stringify(state))
})
```

默认情况下，_state subscription_ 会被绑定到添加它们的组件上 (如果 store 在组件的 `setup()` 里面)。
这意味着，当该组件被卸载时，它们将被自动删除。
如果你想在组件卸载后依旧保留它们，请将 `{ detached: true }` 作为第二个参数，以将 _state subscription_ 从当前组件中 _分离_：

```vue
<script setup>
const someStore = useSomeStore()
// 此订阅器即便在组件卸载之后仍会被保留
someStore.$subscribe(callback, { detached: true })
</script>
```

## getter

大多数时候，getter 仅依赖 state。不过，有时它们也可能会使用其他 getter。因此，即使在使用常规函数定义 getter 时，我们也可以通过 `this` 访问到**整个 store 实例**，**但(在 TypeScript 中)必须定义返回类型**。
这是为了避免 TypeScript 的已知缺陷，**不过这不影响用箭头函数定义的 getter，也不会影响不使用 `this` 的 getter**。

### 用箭头函数写getter

选项式写法需要显式返回类型

```ts
export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0,
  }),
  getters: {
    // 自动推断出返回类型是一个 number
    doubleCount(state) {
      return state.count * 2
    },
    // 返回类型**必须**明确设置
    doublePlusOne(): number {
      // 整个 store 的 自动补全和类型标注 ✨
      return this.doubleCount + 1
    },
  },
})
```

箭头函数写法

```ts
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)

  const doubleCount = computed(() => count.value * 2)
  const doubleCountPlusOne = computed(() => doubleCount.value + 1)

  return { count, doubleCount, doubleCountPlusOne }
})
```

### getter的复用

访问其他 getter 与计算属性一样，你也可以组合多个 getter。

```typescript
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)

  const doubleCount = computed(() => count.value * 2)

  const doubleCountPlusOne = computed((): number => doubleCount.value + 1)

  return { count, doubleCount, doubleCountPlusOne }
})
```

### 通过返回箭头函数向getter传参

向 getter 传递参数 Getter 只是幕后的计算属性，所以不可以向它们传递任何参数。不过，你可以从 getter 返回一个函数，该函数可以接受任意参数：

```typescript
import { ref, computed } from 'vue'
import { defineStore } from 'pinia'

export const useUserListStore = defineStore('userList', () => {
  const users = ref([
    { id: 1, name: 'Alice', active: true },
    { id: 2, name: 'Bob', active: false },
    { id: 3, name: 'Charlie', active: true }
  ])

  const getUserById = computed(() => {
    return (userId: number) => users.value.find(user => user.id === userId)
  })

  const getActiveUserById = computed(() => {
    const activeUsers = users.value.filter(user => user.active)
    return (userId: number) => activeUsers.find(user => user.id === userId)
  })

  return { users, getUserById, getActiveUserById }
})
```
### 跨Store的getter复用

访问其他 store 的 getter 想要使用另一个 store 的 getter 的话，那就直接在 getter 内使用就好：

```typescript
import { ref, computed } from 'vue'
import { defineStore } from 'pinia'
	import { useOtherStore } from './other-store'

export const useStore = defineStore('main', () => {
  const localData = ref(10)
  const otherStore = useOtherStore()

  const otherGetter = computed(() => localData.value + otherStore.data)

  return { localData, otherGetter }
})
```


## action

Action 相当于组件中的 [method](https://cn.vuejs.org/api/options-state.html#methods)。它们可以通过 `defineStore()` 中的 `actions` 属性来定义，**并且它们也是定义业务逻辑的完美选择。**

### 访问其他 store 的 action[​](https://pinia.vuejs.org/zh/core-concepts/actions.html#accessing-other-stores-actions)

逻辑和getter相同想要使用另一个 store 的话，那你直接在 _action_ 中调用就好了：

```ts
import { useAuthStore } from './auth-store'

export const useSettingsStore = defineStore('settings', {
  state: () => ({
    preferences: null,
    // ...
  }),
  actions: {
    async fetchUserPreferences() {
      const auth = useAuthStore()
      if (auth.isAuthenticated) {
        this.preferences = await fetchPreferences()
      } else {
        throw new Error('User must be authenticated')
      }
    },
  },
})
```

### 订阅 action

你可以通过 `store.$onAction()` 来监听 action 和它们的结果。传递给它的回调函数会在 action 本身之前执行。
`after` 表示在 promise 解决之后，允许你在 action 解决后执行一个回调函数。
同样地，`onError` 允许你在 action 抛出错误或 reject 时执行一个回调函数。

这里有一个例子，在运行 action 之前以及 action resolve/reject 之后打印日志记录。

```ts
const unsubscribe = someStore.$onAction(
  ({
    name, // action 名称
    store, // store 实例，类似 `someStore`
    args, // 传递给 action 的参数数组
    after, // 在 action 返回或解决后的钩子
    onError, // action 抛出或拒绝的钩子
  }) => {
    // 为这个特定的 action 调用提供一个共享变量
    const startTime = Date.now()
    // 这将在执行 "store "的 action 之前触发。
    console.log(`Start "${name}" with params [${args.join(', ')}].`)

    // 这将在 action 成功并完全运行后触发。
    // 它等待着任何返回的 promise
    after((result) => {
      console.log(
        `Finished "${name}" after ${
          Date.now() - startTime
        }ms.\nResult: ${result}.`
      )
    })

    // 如果 action 抛出或返回一个拒绝的 promise，这将触发
    onError((error) => {
      console.warn(
        `Failed "${name}" after ${Date.now() - startTime}ms.\nError: ${error}.`
      )
    })
  }
)

// 手动删除监听器
unsubscribe()
```

默认情况下，_action 订阅器_ 会被绑定到添加它们的组件上(如果 store 在组件的 `setup()` 内)。
这意味着，当该组件被卸载时，它们将被自动删除。
如果你想在组件卸载后依旧保留它们，请将 `true` 作为第二个参数传递给 _action 订阅器_，以便将其从当前组件中分离：

```vue
<script setup>
const someStore = useSomeStore()
// 此订阅器即便在组件卸载之后仍会被保留
someStore.$onAction(callback, true)
</script>
```

## 组合式改写

### 关键点：

✔ **监听 Action** → `store.$onAction()` 在 `onMounted` 里调用  
✔ **清理监听器** → 在 `onUnmounted` 里手动 `unsubscribe()`  
✔ **捕获 Action 生命周期** → 监听 `name`、`args`、`after()`、`onError()`  
✔ **返回值支持** → `increment()` 仍然返回 `count.value`

✔ **状态 (state)** → `ref()`  
✔ **getter** → `computed()`  
✔ **访问 getter** → `computed()` 内直接访问  
✔ **getter 传递参数** → `computed` 返回一个函数  
✔ **访问其他 store** → `useOtherStore()` 放在 setup 内  
✔ **修改状态 (action)** → 直接定义函数，使用 `state.value` 修改值  
✔ **异步 action** → 定义 `async` 函数，支持 `await` 操作  
✔ **访问其他 store 的 action** → `useOtherStore()` 后直接调用 `otherStore.someAction()`

# 其他内容

插件
https://pinia.vuejs.org/zh/core-concepts/plugins.html
组件外的Store
https://pinia.vuejs.org/zh/core-concepts/outside-component-usage.html
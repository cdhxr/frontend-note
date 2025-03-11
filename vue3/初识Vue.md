
# 整体理解：

vue组件的作用，就是实现了原本HTML，CSS，JS的模块化，可以分文件编写了
可以以将一个**组件**理解为一个 `.vue` 文件，一个专注于模块的三件套。

# 项目创建

启动项目的方法，在package.json中寻找短命令——dev

要在package.json，终端中输入，npm run dev

项目结构

- public——favico.ico是页标签
- src文件夹，源代码，js,css,vue...
- .git忽略文件
- env.d.ts声明，使ts文件理解常用文件格式
- 两个package，JSON文件，包的管理文件（依赖声明文件）
- vite.config配置文件，安装插件，设置代理等等...


src

- assets folder
- components folder
- App.vue
- main.js / .ts

```js
import './assets/main.css'
import { createApp } from 'vue'//按需导入函数
import App from './App.vue'//Vue应用的入口组件
createApp(App).mount('#app')//使用mount函数来指定容器
//用mount函数将组件应用到id为APP的元素上
```
  
 
## 代码解释
 ___
这段代码是Vue.js应用程序的入口文件，它是用来启动和渲染Vue应用的。每行代码的作用如下：

4. **`import './assets/main.css'`**
    
	- 引入项目中的CSS样式文件（`main.css`)
     
5. **`import { createApp } from 'vue'`**
    
    - 从Vue库中导入了`createApp`函数。
    - `createApp`是Vue 3.x中用来创建应用实例的函数。
    
6. **`import App from './App.vue'`**
    
    - 导入了`App.vue`文件，这是Vue项目的根组件。
    - `App.vue`通常包含应用程序的主要布局和逻辑，作为整个Vue应用的入口组件。
    
7. **`createApp(App).mount('#app')`**
    
    - 使用`createApp`创建一个Vue实例，并传入根组件`App`。
    - `.mount('#app')`会将这个Vue应用实例挂载到页面中一个具有`id="app"`的HTML元素上。
    - 这样，Vue应用就会被渲染到该元素内，通常这个元素会在`index.html`文件中定义。

总结：

- 启动一个Vue应用，
- 先加载样式表，
- 再加载根组件，
- 并将其挂载到页面上的指定元素上


# .vue文件

Vue 3 的代码布局:
```
<template>
  <header></header>
  <main></main>
</template>
<script></script>
<style></style>
```


# 体验组合式API

写一个计数器

选项式API的写法

```vue
<script>
export default {
  // 定义组件的数据，返回一个包含count属性的对象
  data() {
    return {
      count: 0,  // 初始化count值为0
    };
  },
  // 定义组件的方法
  methods: {
    // 增加count值的方法
    addCount() {
      this.count++;  // 每次调用时，count的值增加1
    },
  },
};
</script>
```

类似于面向对象的思想，有属性，对象，方法等概念

组合式API

要按需导入

声明一个响应式的数据要用reactive或者ref

```javascript
<script>
import { ref } from "vue";  // 从Vue库中导入ref，用于创建响应式数据

export default {
  setup() {  // Vue 3 Composition API的setup函数，用于定义组件的状态和逻辑
    const count = ref(0);  // 使用ref创建响应式数据，初始化count为0
    const addCount = () => {  // 定义增加count值的方法
      count.value++  // 通过count.value访问和修改响应式数据的值
    }

    return {  // 返回要暴露给模板的数据和方法
      count, addCount
    }
  }
}
</script>
```

更像是一个函数，类似于函数式编程的思想

优势: 
逻辑都集中在一个setup内，不像vue2的写法分在两个模块中
所有业务逻辑都写在一个地方, 不用在`data`,`method`内找了, 逻辑集中带来了极好的提升。


将上述代码填充至下方script中即可运行

```vue
<script></script>
<template>
  <div class="box">
    <button class="my-button" @click="addCount">result {{ count }}</button>
  </div>
</template>

<style scoped>
.box {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  padding: 20px;
}

.my-button {
  display: inline-block;
  text-align: center;
  vertical-align: middle;
  padding: 15px 32px;
  text-decoration: none;
  font-size: 16px;
  font-weight: 500;
  border-radius: 4px;
  border: none;
  cursor: pointer;

  background-color: #4caf50;
  color: white;

  transition: background-color 0.3s ease;
  &:hover {
    background-color: #45a049;
  }

  &:active {
    background-color: #388e3c;
  }
}
</style>
```

# setup

tips: setup是执行最早的函数，在beforecreate函数之前

体验 Setup 语法糖

```vue
<script setup>
import { ref } from "vue";
const count = ref(0);
const printCount = () => {
  count.value++;
};
</script>
```

简化了return，export default，setup一些硬性编写时间

# reactive与ref的对比

```vue
<script setup>
import { reative } from "vue";
const count = reative ({
  count: 0
})
const printCount = () => {
  count.count++
};
```

```vue
<script setup>
import { ref } from "vue";
const count = ref ({
  count: 0
})
const addCount = () => {
  count.value.count++
}
</script>
```

传入的是响应式对象，要定义一个属性，以接受值

ref的底层是一个reactive，所以自带了一个value的属性，简化了一些写法

# compute语法

```js
const homoCount = computed(() => {
  return objCount.value.count * 114514;
});
```

# 监视数据

**监听**（Watching）是指在数据发生变化时执行特定操作的过程。

监视数据- Watch

```js
watch(objCount, (newVal, oldVal) => {
  console.log(`值从${oldVal}变成了${newVal}`);
});
```

监听多个内容

利用 **数组** 的方式监听多个对象的值

```js
const objCount = ref(0);
const age = ref(114);

watch(
  [objCount, age],
  (newVal, oldVal) => {
    console.log(`值从${oldVal}变成了${newVal}`);
  },
  { immediate: true }//加上此行，则会在进入页面立即触发，而非等待事件触发
);
```

深度监听

当监听的对象不是基本类型而是复杂对象时，需要用`{ deep: true }`来查看其属性的值

```js
import { ref, watch } from "vue";
const objCount = ref({
  count: 0,
});

watch(
  objCount,
  (newVal, oldVal) => {
    console.log(oldVal);
    console.log(newVal);
  },
  { deep: true }
);
```

这里运行代码会发现，oldVal和newVal的值相同，

原因是这里，oldValue和newValue指向的是，对象的地址
而基本类型的情况，会直接存储数值

但事实上，大多数情况下不需要用到oldVal，所以无伤大雅

用深度克隆解决问题(供参考)

```js
import { ref, watch } from "vue";
const objCount = ref({
  count: 0,
});

let oldState = JSON.parse(JSON.stringify(objCount.value));

watch(
  objCount,
  (newVal) => {
    console.log(oldState);
    console.log(newVal);
    oldState = JSON.parse(JSON.stringify(newVal));
  },
  { deep: true }
);
```


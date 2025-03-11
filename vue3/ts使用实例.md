
TS 的作用主要体现在**类型安全**、**提升开发体验** 和 **增强代码可维护性**。它通过静态类型检查，避免运行时错误，提高 Vue 组件的可靠性；在 IDE 中提供更好的自动补全和智能提示，让开发更高效；同时，它支持泛型、接口等高级特性，使代码更加清晰、可复用，适合长期维护的项目。

## 让每个文件都是一个单独的模块，避免命名重复问题

```ts
export {};
```

## 体验 TS 接口, 接口如何处理对象多个层级的情况。

例子:

```ts
interface Info{
	title:string
	lyrics:string
}
interface Uta{
	id:number
	producer:string
	info:Info
}


const uta = {
  id: 1,
  producer: "harumakigohan"
  info: {
    title: "メルティランドナイトメア",
    lyrics: "Welcome to the MeltyLand",
  },
};

```

## 用断言帮助智能提示:

```ts
const TestId = document.getElementById("#test") as HTMLImageElement;
```

# TS的作用就是为vue中，各种代码，提供静态类型检查

## 1. ref

```ts
const year = ref<string | number>('1145')


//法1
type ListItem = {
  id: number
  name: string
}
//法2
interface ListItem { 
  id: number; 
  name: string; 
}

const list = ref<ListItem[]>([])

list.value = [
  {
    id: 100,
    name: 'inkka'
  }
]
```

**使用 `interface` 或 `type`**：适用于结构化对象
**直接在 `ref<>` 后写**：适用于**简单类型**


## 2. reactive

```js
type Kyoky{
   name:string
   producer:string
   language?:string//？表示可有可无
}

const sukinakyoku reactive({
   name:'humming',
   producer:'ikura'
})

sukinakyoku.langauge ='jp'
```

reactive都是复杂对象，即便它会自动识别内部的属性创建对象，
但定义了类型限制后，便可以随意添加属性，更加灵活，也有更好的可读性

## 3. 触发事件时的类型

需求: 请输入文本同时在控制台输出输入的内容。

```vue
<input type="text" placeholder="文本测试" @change="inputChange">
```

```js
const inputChange = (e: Event) => {
  console.log((e.target as HTMLInputElement).value);// 使用 `as` 进行类型断言
}
```

类型断言的目的是让 TypeScript 知道你确信 `e.target` 是一个 `HTMLInputElement`，这样你就可以安全地访问 `value` 属性。
如果没有类型断言，TypeScript 将无法推断出 `e.target` 是一个 `HTMLInputElement`，并且会报错提示你 `EventTarget` 类型没有 `value` 属性。

在跨越继承关系出现调用属性时，需要用类型断言

## 4. 模板引用的类型

用ref传递组件的方法和属性时，需要实例化组件的对象

这个对象的类型是什么？

1. 使用typeof得到组件的类型
2. 用instanceType获取实例类型
3. 组件未加载时的类型应该为null

`<InstanceType<typeof ComponentsTest01>|null>`

```vue
<template>
	<div>
		<ComponentsTesto1 ref="componentsTest"></ComponentsTest01>
		<button@click="callLogMsg">打印-下！</button>
	</div>
</template>

<script setup lang="ts">

import ComponentsTesto1 from "./components/ComponentsTest01.vue";
import ref from 'vue'
const componentsTest = ref<InstanceType<typeof ComponentsTest01>|null>(null)

//注意用if或者？，保证其非空类型
const callLogMsg = () => {
	if(componentsTest.value){
		   componentsTest.value.LogMessage()
	   }
	}
</script>

```


ref传DOM元素，非空值处理

知道HTML DOM元素对应的类型即可

```vue
<template>
	<input ref="autoFocusInput"v-model="inputValue"placeholder="请输入内容"/>
</template>

<script setup lang="ts">

import ref,onMounted,nextTick from "vue";
const inputValue ref<string>("");

//为了使它不是never类型，ref使用后加上类型约束
//其状态分为挂载前和挂载后，对应着不同的类型
const autoFocusInput = ref<null|HTMLInputElement>(null);

onMounted(async() =>
	await nextTick();
	if (autoFocusInput.value)//保证了组件挂载后才调用focus
		autoFocusInput.value.focus();
	})
</script>
```

元素和组件都是需要时间渲染的，这也使他们必须要加上 |null，确保其为被渲染完时不被误操作

## 父传子，props的类型

```ts
//子组件

//传值部分
//定义接受值的类型
type Currency = {
	name:string
	value?:number
}

//const props = defineprops<Currency>(),
//因为value?属性的存在，value需要一个默认值
//但defineProps不能设置默认值，所以需要withDefaults函数

const props = withDefaults(defineProps<Currency>(),{
	value:5000
})

```

父组件中就是v-bind传响应式变量

## 子传父，emits（event,param）

```typescript
//子组件
type Emits = {
  (e: "msg", msg: string): void;//最后的是返回值的类型
};

const Emit = defineEmits<Emits>();
const handleClick = () => {
  Emit("msg", "testVal");
};

```

父组件中`@` 语法（即 `v-on` 绑定）可以用来监听 `defineEmits` 定义的事件

## 第三方库的类型声明文件引入

### 有的第三方库会自带类型声明文件

```
npm i axios
```

如果用axios，会生成一个.d.ts文件，即类型声明文件，故使用不会有问题
### 有的库需要我们去引入

用到jQuery库时，相同的操作不会生成类型声明文件
可以通过一个第三方库，内部包含了大量类型声明文件

```
npm i --save-dev @types/jquery
```

### App.vue的类型声明文件

复制文件shims-vue.d.ts

```ts
declare module "*.vue" {
  import { DefineComponent } from "vue";
  const component: DefineComponent<{}, {}, any>;
  export default component;
}
```

### 自己写类型声明文件

```ts
export interface Currency = {
  name: string;
  ...
};
```


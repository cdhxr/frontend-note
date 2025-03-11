

# 响应式对象与创建：

## 创建语法

创建responsive Object就是创建一个property变化，DOM也会rerender的Object

`const 响应式对象= reactive(源对象)`
`const xxx = ref(初始值)`

## 为什么用const

- const为了**保持引用不变**：虽然对象的内容是可变的，但它的引用不应该改变

## 传值差异

- reactive往往需要在（）内传入{property：初值}
- ref只需要传入（初值），因为某认自带了一个value属性，简化了写法
- 在ref变量在template内不用.value，在script内需要.value

## 使用选择

- 因为一定会带有一层value属性，对于多属性的对象ref并不合适
- ref应该用作创建基本类型的对象（String, Number, Boolean）和单独追踪的元素
- reactive用作创建复杂对象和数组

## 其他

[[toRef and toRefs  Method]]

# Computed Method

**计算属性（`computed`）** 本质上返回的是一个 **`ref` 对象**。这意味着你可以通过 `.value` 来访问计算属性的值。

## 基本用法，只传入一个参数

```js
import { reactive, computed } from 'vue';

const state = reactive({
  count: 0
});

// 定义一个计算属性
const doubleCount = computed(() => state.count * 2);

// 访问计算属性
console.log(doubleCount.value); // 输出：0
state.count = 2;
console.log(doubleCount.value); // 输出：4
```

`computed` 是通过 `ref` 和 `reactive` 的组合来实现的，主要用于根据已有数据动态计算出新的值。

1. 通过 `ref` 和 `reactive`创建响应式对象
2. `const 计算属性 = computed(函数，以响应式对象的属性作为参数);`
3. `responsiveObject.computedProperty`调用数据

`computedProperty`根据(响应式对象的)属性值，以特定的方式修改，响应式变化，可以随时被响应式对象调用

## 计算属性的 getter 和 setter

最常见的用法是只使用getter,设置只读的计算属性，
事实上可以再传入一个函数作为setter

```js
import { reactive, computed } from 'vue';

const state = reactive({
  count: 0
});

// 计算属性包含 getter 和 setter
const doubleCount = computed({
  // getter: 获取计算值
  get: () => state.count * 2,
  
  // setter: 设置值时自动触发
  set: (newVal) => {
    state.count = newVal / 2;  // 将传入的新值除以 2，更新 count
  }
});

console.log(doubleCount.value); // 输出：0
doubleCount.value = 10;  // 设置新值
console.log(state.count);  // 输出：5，count 被更新为 5
```

# watch

## 基本介绍

- 作用：是指在数据发生变化时执行特定操作的过程。
- 特点：`Vue3`中的`watch`只能监视以下**四种数据**：

> 1. `ref`定义的数据。
> 2. `reactive`定义的数据。
> 3. 函数返回一个值（`getter`函数）。
> 4. 一个包含上述内容的数组。

`watch(source, callback, options);`

- **`source`**: 要监视的数据源。可以是 `ref`、`reactive`、getter 函数，或者它们的数组。
- **`callback`**: 数据变化时执行的回调函数。该函数接受两个参数：
	- `newValue`（新值）和 `oldValue`（旧值），第一个参数是new
- **`options`**: 
	- **`immediate`**: 是否在侦听开始时立即执行回调（默认为 `false`）。
	- **`deep`**: 是否深度监听对象的内部属性。对于 `reactive` 创建的对象，默认已经开启深度监听，但对于 `ref` 对象，需要显式指定 `deep: true`。

oldValue和newValue指向的是，对象的地址
而基本类型的情况，会直接存储数值，不会有使用问题
但复杂对象，由于指向同一个引用，old与new的值一样

但事实上，大多数情况下不需要用到oldVal，所以无伤大雅
## 监视`ref`定义的【基本类型】数据

```js
// objCount 是由 ref 创建的基本类型数据
const objCount = ref(0);
watch(objCount, (newVal, oldVal) => {
  console.log(`值从${oldVal}变成了${newVal}`);
});
objCount.value = 10; // 会触发 watch 的回调，输出：值从 0 变成了 10
```

## 监视`ref`定义的【对象类型】数据——深度监听

```js
// objState 是由 ref 创建的对象类型数据
const objState = ref({ count: 0, name: 'Vue' });

watch(objState, (newVal, oldVal) => {
  console.log(`对象发生变化，从 ${JSON.stringify(oldVal)} 变成了 ${JSON.stringify(newVal)}`);
}, { deep: true });  // 开启深度监听
```

## 监视`reactive`定义的【对象类型】数据，且默认开启了深度监视。

```js
// reactive 定义的对象数据，自动深度监听
const state = reactive({ count: 0, name: 'Vue' });

watch(state, (newVal, oldVal) => {
  console.log(`对象发生变化，从 ${JSON.stringify(oldVal)} 变成了 ${JSON.stringify(newVal)}`);
});
```

## 监视`ref`或`reactive`定义的【对象类型】数据中的**某个属性**

```js
// 监视 reactive 对象中的某个属性
const state = reactive({ count: 0, name: 'Vue' });

watch(() => state.count, (newVal, oldVal) => {
  console.log(`count 从 ${oldVal} 变成了 ${newVal}`);
});

```

## 监视上述的多个数据

利用 **数组** 的方式监听多个对象的值

```js
import { ref, reactive, watch } from 'vue';

const count = ref(0);
const state = reactive({ name: 'Vue' });

watch([count, () => state.name], ([newCount, newName], [oldCount, oldName]) => {
  console.log(`count 从 ${oldCount} 变成了 ${newCount}`);
  console.log(`name 从 ${oldName} 变成了 ${newName}`);
});

count.value = 5;   // 会触发 watch 的回调，输出：count 从 0 变成了 5
state.name = 'Vue 3';  // 会触发 watch 的回调，输出：name 从 Vue 变成了 Vue 3

```

## watcheffect
[[watcheffect]]

# ref Attribute 模板引用

作用：用于注册模板引用。

- 用在组件标签上，获取的是组件实例对象。
- 用在普通`DOM`标签上，获取的是`DOM`节点。 

1. 父组件调用子组件中的属性和方法

	- 对象在子组件标签中用ref属性声明，则其将被识别为子组件对象
	- 由于vue3默认属性和方法封闭，需要在子组件中，用 **defineExpose** 暴露方法和属性


2. 获取DOM元素或者组件实例

	- 在目标获得的标签中定义ref属性
	- 利用`ref('null')`创建同名对象
	- 在渲染后即可用，对象名.value得到

```vue
<template>
	<componentsTest01 ref="component"></componentsTest01>
	<p ref="test">测试</p>
<template>

<script>
	
	//1.父组件调用子组件中的属性和方法
	//导入了子组件，其实就是导入了子组件的属性和方法
	//子组件中的属性和方法需，通ref属性创建，需要defineExpose
	import componentsTest01 from './components/componentsTest01.vue';
	const component = ref('null')//ref内部随意
	console.log(component.value.property)  
	component.value.method()
	
	//2.获取DOM元素或者组件实例
	//test.value就是DOM元素（带有<>的那个）
	const test = ref('null') //ref内部必须为null
	console.log(test.value)
	
</script>
```

DOM例子初始值为null的原因：

- test是指向DOM元素或者组件的
- 在未挂载时，如果被访问，将无法返回正确值，因为渲染后才有值
- 页面加载好后，其值一定为组件
- 设置为null，可以避免上述情况，也保证了其返回值为指定DOM元素或组件
- 由此可知，该对象在钩子中可应用的时机为onmounted

# props，父组件向子组件传值

父组件向子组件中传值，前提是要使用子组件

父组件中在 子组件标签 中使用v-bind语法，传值
子组件中使用defineProps语法接收

父组件v-bind语法

`<ComponentsTest01 :子组件属性名 ="父组件变量名"></ComponentsTest01>` 

```vue
<script setup>
import ComponentsTest01 from './components/componentsTest01.vue'
import { ref } from "vue";  // 引入 Vue 的 ref 函数，用于创建响应式数据
const data = ref("父组件的数据");  //创建一个响应式数据 txt，初始值为 "父组件的数据"
</script>

<template>
    <h2>父组件</h2>
    <!-- 向子组件传递 txt 数据,将父组件的 txt 数据绑定到子组件的 `msg` 属性上 -->
    <ComponentsTest01 :msg="data"></ComponentsTest01> 
</template>
```

子组件defineprops语法

模板内：`<tag> {{上方的子组件属性名}} </tag>`
脚本内如下：

```vue
<template>
  <div class="component01">
    <h2>子组件</h2>
    <p>接受的内容:{{ msg }}</p>
  </div>
</template>

<!--接受值的两种方式-->

<!--1. 校验式写法 -->
<script setup>
defineProps({ msg: { type: String, required: true } }
)</script>
<!--2. vue3支持的简写-->
<script setup> defineProps(['msg']); // 接收名为 msg 的属性 </script>
```

# 生命周期钩子

[[Vue生命周期钩子]]

## 自定义hook

自定义 Hook 的想法就是JAVA中类的思想。
希望将特定功能的数据和方法一起封装，进行模块化

不同于类的是，它不仅把属性和方法写在一起，
还可以把使用这些的行为一同封装了（即使用钩子,computed属性等等）

hooks就是.js和.ts文件，以useXX命名，是一种使用逻辑的封装

将数据和方法封装在一个ts文件中的一个暴露的函数中

```ts
import xxxx
export default function (){
	let xx = ...
	function method(){...}
	return {public的东西，供外界调用}
}
onMounted(()=>{method})
```

```vue
<script lang="ts" setup name="Person">
import 上方的ts文件useXX
//因为上方的ts文件，写法导致其是一个函数，所以加个括号调用函数，就是让数据方法跑起来
//同样因为是函数，可以直接用变量接受返回值
const {上方文件return的变量和方法们} = useXX()
</script>
```

JAVA用类封装，vue用函数封装
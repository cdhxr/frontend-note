
# 基本理解

ruote就是类似于key：value，键值对的 **对应关系**
多组路由，需要路由的管理

前端中的路由，是为了实现单页面应用

即一个HTML文件，实现切换效果
如，导航栏每一项对应，页面呈现不同的组件，而不用跳转链接
如下图

![[Pasted image 20250301134633.png]]

# Vue中的使用

在 Vue 中，**Vue Router** 是官方提供的路由管理库，它用于管理单页面应用（SPA）的不同视图。Vue Router 允许你在不同的 URL 之间切换，同时保持应用的状态，而无需刷新页面。

---
## 2. **基本使用**

1. 准备好不同导航对应的组件（.vue文件）
2. 在ruoter.ts中创建定义路由
3. 在main.ts中注册路由
4. 在vue文件中使用路由，
	- `<router-link>` 用于创建链接，它会自动渲染为 `<a>` 标签，并且不会触发页面刷新
	- **使用 `<router-view>` 渲染路由组件**，刷新页面

注意：
- 路由组件通常存放在`pages` 或 `views`文件夹，一般组件通常存放在`components`文件夹。
- 通过点击导航，视觉效果上“消失” 了的路由组件，默认是被**卸载**掉的，需要的时候再去**挂载**。

首先，在 Vue 项目中创建 `router.js` 文件，并定义路由：

```js
// src/router.js
import { createRouter, createWebHistory } from 'vue-router'
//注意要引入history模式和createruoter所需的方法
import Home from '@/views/Home.vue'
import About from '@/views/About.vue' 

const routes = [
  { 
	  (name:'xxx'),//可以进行路由命名，简化to的写法 
	  path: '/', 
	  component: Home 
  },
	  { 
	  (name:'xxx'), 
	  path: '/about', 
	  component: About 
  },
	  //利用重定向，确定一个默认页面，解决单'/'报错
	  {
		    path:'/',
		    redirect:'/about'
	  }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

export default router 
```

然后，在 `main.js` 中注册路由：

```js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

const app = createApp(App)
app.use(router)
app.mount('#app')
```

---



## 3. **在 Vue 组件中使用路由**

### 1）**使用 `<router-link>` 进行导航**

`<router-link>` 用于创建链接，它会自动渲染为 `<a>` 标签，并且不会触发页面刷新：

```vue
<template>
  <nav>
    <router-link to="/">首页</router-link>
    <router-link to="/about">关于</router-link>
  </nav>
  <router-view></router-view>
</template>
```

### 2）**使用 `<router-view>` 渲染路由组件**

`<router-view>` 组件会根据当前的 URL 动态渲染对应的组件。

### 3) to的两种写法 

```vue
<!-- 第一种：to的字符串写法 -->
<router-link active-class="active" to="/home">主页</router-link>

<!-- 第二种：to的对象写法 -->
<router-link active-class="active" :to="{path:'/home'}">Home</router-link>

<!-- 采用路由命名的好处 -->

<!--简化前：需要写完整的路径（to的字符串写法） -->
<router-link to="/news/detail">跳转</router-link>

<!--简化后：直接通过名字跳转（to的对象写法配合name属性） -->
<router-link :to="{name:'guanyu'}">跳转</router-link>
```

## 4. **路由传参**

传参发生的事：

涉及三个部分

- App.vue，这里代表主界面的vue文件
- 路由组件们.vue，即不同导航对应的组件
- router.ts

`<ruoter-link>`和`<ruoter-view>`写在App.vue中，
render 路由组件们.vue

传递参数，

要传的参数在App.vue（主界面）中：
query：所要传的参数写在`<ruoter-link>`的 to 内部
params：在ruoter.ts中定义，再将所要传的参数写在`<ruoter-link>`的 to 内部

获取参数，

在路由组件们.vue中：
导入useRuote hook
创建ruote对象
通过ruote.query（params）.变量名调用参数

### **1. 使用 `query`（查询参数）**

**适用于可选参数，格式：`?key=value`**
#### **（1）传递 `query`**，在to中传递

① **字符串写法**

```vue
<router-link to="/search?keyword=vue">搜索 Vue</router-link> 
```

② **对象写法**

```vue
<router-link :to="{ 
	path: '/search', 
	query: { keyword: 'vue' } 
}">
搜索 Vue
</router-link>
```

#### **（2）获取 `query`**

在 `Search.vue` 组件中获取：

```vue
<script setup>
 import { useRoute } from 'vue-router'

const route = useRoute()
console.log(route.query.keyword) // "vue"
</script>
```

### **2. 使用 `params`（动态路由参数）**

**适用于路径参数，必须在路由定义时声明**

#### **（1）配置路由**

在 `router.js` 中：

```js
	const routes = [
	  { path: '/user/:id', name: 'user', component: User }
	]
```

#### **（2）传递 `params`**

① **字符串写法（❌ 不支持）**

```vue
<router-link to="/user/123">用户 123</router-link>
```

可以使用，但**不推荐**，因为 `params` 只能在对象写法中使用。

② **对象写法（✅ 推荐）**

```vue
<router-link :to="{ name: 'user', params: { id: 123 } }">用户 123</router-link>
```

#### **（3）获取 `params`**

在 `User.vue` 组件中：

```vue
<script setup>
import { useRoute } from 'vue-router'

const route = useRoute()
console.log(route.params.id) // "123"
</script>
```








## 5. **编程式导航**

	脱离ruoterlink进行页面跳转

可以使用 `useRouter` 进行路由跳转：

字符串写法

```vue
<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()

const goToAbout = () => {
  router.push('/about') // 跳转到 about 页面
}
</script>

<template>
  <button @click="goToAbout">去关于页</button>
</template>
```

对象式写法

```ts
const router = useRouter()

interface NewsInter {
    id: string,
    title: string,
    content: string
} 

function showNewsDetail(news: NewsInter) {
    router.push({
        name: 'xiang',
        query: {
            id: news.id,
            title: news.title,
            content: news.content
        }
    })
}

```

使用场景

1. 符合条件跳转
2. 鼠标滑过时跳转
3. ... 

## 6. **路由规则中的props**

在ruoter.ts中
添加一个属性props：true

```ts
const routes = [
  { 
	  (name:'xxx'), 
	  path: '/about', 
	  component: About 
	  ///第一种写法：将路由收到的所有params参数作为props传给路由组件
	  props: true
	  //第二种写法：通过ruote对象，用来处理query的情况
	  props (route){
		return route.query
	  }
	  //一般情况下，组件的props直接在标签中传递，
	  //但是路由组件是特殊的,我们不写路由组件,而是通过ruoter-view直接渲染
	  //所以我们在这里，要用这些特殊的语法传props
  }
]
```

实际运用中

数据流：
在主界面的vue中点击`<ruoter-link :to ...>`组件
通过to中的参数定位到ruoter.ts中的对应属性
写完Component后，相当于写了`<Component/>`
写完props,相当于写了`<Component property01 = '', property02 = '' .../>`

于是，可以在  **路由组件们.vue**  中，
使用`defineProps(['','','',...])`，一行代码，完成对所有参数的获取
不需要调用ruoter对象

## 7. **路由模式**

Vue Router 提供了两种模式：

1. **`createWebHistory()`**：基于浏览器 `History API`（推荐）。
2. **`createWebHashHistory()`**：使用 `#` 符号的 hash 模式。

例如：

```js
const router = createRouter({
  history: createWebHistory(), // 使用 history 模式
  routes
})
```

如果你的应用需要兼容旧浏览器，可以使用 `createWebHashHistory()`。

## 8. replace属性

1. 作用：控制路由跳转时操作浏览器历史记录的模式。
    
2. 浏览器的历史记录有两种写入方式：分别为`push`和`replace`：
    - `push`是追加历史记录（默认值）。
    - `replace`是替换当前记录。
3. 开启`replace`模式：
    
    ```vue
    <RouterLink replace .......>News</RouterLink>
    ```

## 9. **组件嵌套**

有一个news路由的界面，其中有新闻列表，在这个展示界面中又有一个对应关系，此时要用到路由嵌套

1. 编写`News`的子路由：`Detail.vue`
    
2. 配置路由规则，使用`children`配置项：
    
    ```ts
    const router = createRouter({
      history:createWebHistory(),
    	routes:[
    		{
    			name:'zhuye',
    			path:'/home',
    			component:Home
    		},
    		{
    			name:'xinwen',
    			path:'/news',
    			component:News,
	    		//在其中一个路由中嵌套写一个路由规则
				children:[
    				{
    					name:'xiang',
    					path:'detail',
    					component:Detail
    				}
    			]
    		},
    		{
    			name:'guanyu',
    			path:'/about',
    			component:About
    		}
    	]
    })
    export default router
    ```
    
3. 跳转路由（记得要加完整路径）：
    
    ```vue
    <router-link to="/news/detail">xxxx</router-link>
    <!-- 或 -->
    <router-link :to="{path:'/news/detail'}">xxxx</router-link>
    ```
    
4. 记得去`Home`组件中预留一个`<router-view>`
    
    ```vue
    <template>
      <div class="news">
        <nav class="news-list">
          <RouterLink v-for="news in newsList" :key="news.id" :to="{path:'/news/detail'}">
            {{news.name}}
          </RouterLink>
        </nav>
        
        <div class="news-detail">
          <!--每个路由都要用到ruoterview，子路由也不例外-->
          <RouterView/>
        </div>
      </div>
    </template>
    ```
    

 
# 生命周期钩子

G:\first.library\images\M779FK5RPM4AF.info\

可以将**生命周期钩子**理解为**在特定阶段执行的代码段**
本质上是一些预定义的回调函数

### 1. **生命周期的主要阶段**

Vue 组件的生命周期大致可以分为以下几个阶段：

- **创建阶段**：初始化组件，进行数据设置、属性接收等。
- **挂载阶段**：指的是 Vue 组件被成功渲染并插入到 DOM 后的状态。
- **更新阶段**：当组件的响应式数据变化时，组件重新渲染。
- **销毁阶段**：组件从 DOM 中移除，释放资源。


- `Vue3`的生命周期
	- 创建阶段：`setup
	- 挂载阶段：`onBeforeMount`、`onMounted`
	- 更新阶段：`onBeforeUpdate`、`onUpdated`
	- 卸载阶段：`onBeforeUnmount`、`onUnmounted`
- 常用的钩子：`onMounted`(挂载完毕)、`onUpdated`(更新完毕)、`onBeforeUnmount`(卸载之前)


渲染器遇到组件，

执行setup

- beforeCreate钩子

初始化选定的API

- created钩子

是否被预渲染
（编译template）

- beforeMount钩子

初次渲染，创建并插入 DOM 节点

- mounted钩子

进入挂载状态，当组件的响应式数据变化时

- beforeUpdated钩子

重新渲染

- Updated 钩子

回到挂载状态，当组件要被卸下

- beforeUnmount钩子
- unmounted钩子


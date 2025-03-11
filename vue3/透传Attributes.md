
### 透传 Attribute 和 v-on 事件监听器

在 Vue 中，**透传的 attribute** 指的是那些传递给一个组件但没有在该组件的 `props` 或 `emits` 中声明的属性或事件监听器。最常见的例子包括 `class`、`style` 和 `id`。

### 透传机制

1. **透传到根元素**  
    当一个组件渲染单个元素作为根元素时，传递给该组件的未声明的属性（例如 `class`、`style`）会自动透传到组件的根元素。
    
    例如，父组件传入 `class="large"`：
    
    ```vue
    <template>
      <MyButton class="large" />
    </template>
    ```
    
    子组件的根元素会自动接收这个 `class`，并最终渲染为：
    
    ```html
    <button class="large">Click Me</button>
    ```
    
2. **`class` 和 `style` 的合并**  
    如果子组件的根元素已经有了 `class` 或 `style`，它会和父组件传递的值进行合并。例如：
    
    ```vue
    <template>
      <button class="btn">Click Me</button>
    </template>
    ```
    
    父组件传入 `class="large"`，最终渲染为：
    
    ```html
    <button class="btn large">Click Me</button>
    ```
    
    这里 `btn` 和 `large` 会合并成一个 `class` 属性。
    
3. **透传 `v-on` 事件监听器**  
    同样，父组件传入的事件监听器（如 `@click="onClick"`）也会被透传到根元素。多个监听器（父组件的和根元素自身的）会共同触发。
    
    例如：
    
    ```vue
    <template>
      <MyButton @click="onClick" />
    </template>
    ```
    
    如果 `MyButton` 组件的根元素是 `<button>`，`@click` 事件会被绑定到这个 `button` 上，点击时触发 `onClick` 方法。
    
4. **深层组件的透传**  
    如果一个组件的根元素是另一个子组件，透传的属性会继续传递下去。例如：
    
    ```vue
    <template>
      <BaseButton />
    </template>
    ```
    
    父组件传递给 `MyButton` 的属性（例如 `class`、`style`）会被透传到 `BaseButton` 组件中。
    

### 注意事项：

- 透传的 attribute 不会覆盖已在子组件中声明的 `props`，也不会影响 `v-on` 事件监听器。
- 如果透传的属性符合 `BaseButton` 的 `props` 声明，它们可以作为 `props` 传递给 `BaseButton`。

### 总结

Vue 中的透传机制使得父组件可以通过属性和事件监听器向子组件传递数据，子组件可以选择性地使用 `props` 或 `v-on` 处理这些数据。如果这些属性未显式声明，Vue 会自动将它们透传到组件的根元素，或者进一步传递给子组件。

## 禁用Attributes继承

最常见的需要禁用 attribute 继承的场景就是 attribute 需要应用在根节点以外的其他元素上。
通过设置 `inheritAttrs` 选项为 `false`，你可以完全控制透传进来的 attribute 被如何使用。

这些透传进来的 attribute 可以在模板的表达式中直接用 `$attrs` 访问到。

`<span> Fallthrough attribute: {{ $attrs }} </span>`

这个 `$attrs` 对象包含了除组件所声明的 `props` 和 `emits` 之外的所有其他 attribute，例如 `class`，`style`，`v-on` 监听器等等。

- 和 props 有所不同，透传 attributes 在 JavaScript 中保留了它们原始的大小写，所以像 `foo-bar` 这样的一个 attribute 需要通过 `$attrs['foo-bar']` 来访问。
    
- 像 `@click` 这样的一个 `v-on` 事件监听器将在此对象下被暴露为一个函数 `$attrs.onClick`。

## 利用Attributes实现，父组件向孙组件传值

前置知识
```vue
<!--简单的父向子传值,用v-bind即可-->
<Child :a="a" :b="b" :c="c" :d="d" v-bind="{x:100, y:200}" />
```

defineProps接受，则变量成为子组件的props，不接受，则会成为attribute，要通过`$attrs`对象调用

在这段代码中，父组件 (`Father`)、子组件 (`Child`) 和孙组件 (`GrandChild`) 之间的数据流通过 `props` 和 `$attrs` 进行传递。以下是详细的数据流分析：

### **1. 父组件 (Father)**

```vue
<template>
  <div class="father">
    <h3>父组件</h3>
    <Child :a="a" :b="b" :c="c" :d="d" v-bind="{x:100, y:200}" :updateA="updateA"/>
  </div>
</template>

<script setup lang="ts" name="Father">
  import { ref } from "vue";
  import Child from './Child.vue';

  let a = ref(1);
  let b = ref(2);
  let c = ref(3);
  let d = ref(4);

  function updateA(value) {
    a.value = value;
  }
</script>
```

- 在父组件中，`a`、`b`、`c`、`d` 都是通过 `ref` 定义的响应式数据。
- `updateA` 方法被传递给子组件，允许子组件通过调用该方法来更新父组件中的 `a` 的值。
- 父组件通过 `v-bind="{x:100, y:200}"` 向子组件传递了两个额外的属性 `x` 和 `y`，这会以 `props` 的形式传递给子组件。

### **2. 子组件 (Child)**

```vue
<template>
  <div class="child">
    <h3>子组件</h3>
    <GrandChild v-bind="$attrs"/>
  </div>
</template>

<script setup lang="ts" name="Child">
  import GrandChild from './GrandChild.vue'
</script>
```

- 在子组件中，`v-bind="$attrs"` 会将所有父组件透传的属性（不在子组件的 `props` 中声明的属性）传递给 `GrandChild` 组件。
- `$attrs` 包含了父组件传递给子组件的所有属性，即 `a`、`b`、`c`、`d`、`x`、`y` 和 `updateA`，这些属性会继续传递给孙组件。

### **3. 孙组件 (GrandChild)**

```vue
<template>
  <div class="grand-child">
    <h3>孙组件</h3>
    <h4>a：{{ a }}</h4>
    <h4>b：{{ b }}</h4>
    <h4>c：{{ c }}</h4>
    <h4>d：{{ d }}</h4>
    <h4>x：{{ x }}</h4>
    <h4>y：{{ y }}</h4>
    <button @click="updateA(666)">点我更新A</button>
  </div>
</template>

<script setup lang="ts" name="GrandChild">
  defineProps(['a', 'b', 'c', 'd', 'x', 'y', 'updateA'])
</script>
```

- 孙组件通过 `defineProps` 声明接收的属性，接收了从父组件传递过来的 `a`、`b`、`c`、`d`、`x`、`y` 和 `updateA`。
- 这些 `props` 会显示在孙组件的模板中。
- `button` 元素绑定了一个点击事件，点击时会调用 `updateA(666)`，并传递一个新值 `666`，从而触发父组件中的 `updateA` 方法，更新父组件中 `a` 的值。

### **数据流总结**

1. **父组件 → 子组件**
    
    - 父组件通过 `:a="a"`、`:b="b"`、`:c="c"`、`:d="d"` 将 `a`、`b`、`c`、`d` 作为 `props` 传递给子组件 `Child`。
    - 父组件通过 `v-bind="{x:100, y:200}"` 将 `x` 和 `y` 作为属性传递给子组件 `Child`。
2. **子组件 → 孙组件**
    
    - 子组件通过 `v-bind="$attrs"` 将父组件传递的所有属性（包括 `a`、`b`、`c`、`d`、`x`、`y` 和 `updateA`）透传给孙组件 `GrandChild`。
3. **孙组件**
    
    - 孙组件接收并使用从父组件传递来的 `props`（`a`、`b`、`c`、`d`、`x`、`y` 和 `updateA`）。
    - 孙组件可以通过调用 `updateA(666)` 更新父组件中的 `a`，触发数据的双向流动。

### **交互总结**

- 当用户点击孙组件中的按钮时，`updateA(666)` 会被调用，触发父组件中的 `updateA` 方法，更新父组件中的 `a` 的值，进而影响整个组件树的更新。
- 由于 `a` 是响应式的，因此父组件和孙组件中的 `a` 会保持同步。

父到孙，自然的用v-bind传给子，子再用v-bind将$attrs传给孙

孙到父，将修改数据的操作，包装到函数中，函数可以一路传到孙子，
再在孙子组件中监听事件，事件触发函数，修改父组件中的数据。


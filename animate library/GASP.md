# Grammer

- Creating an animation.
	
	- `gsap.to(".box", { x: 200 })`
	- 像这样的单个动画被称为 "tween"，这个 tween 的意思是 "嘿，GSAP，把类为 '.box' 的元素做成 x 为 200px 的动画（如 transform: translateX(200px)) "。![[tween-diagram-39f72740ce8fddcca921341f8a4bd007.webp]]
此语句由三部分组成：method，target，vars
## 1. method

`gsap.to()` 这是最常见的过渡类型。`.to()` tween 将从元素的当前状态开始，并 "到 "tween 中定义的值进行动画。

`gsap.from()` - 类似于反向 `.to()`，从tween中定义的状态到当前状态的动画

`gsap.fromTo()` - 您可以定义起始值和结束值。

`gsap.set()` 立即设置属性（无动画）。它本质上是一个零持续时间的 `.to()` tween。

## 2. 如何确定动画的对象：The Target

```js
// use a class or ID
gsap.to(".box", { x: 200 });

// a complex CSS selector
gsap.to("section > .box", { x: 200 });

// a variable
let box = document.querySelector(".box");
gsap.to(box, { x: 200 })

// or even an Array of elements
let square = document.querySelector(".square");
let circle = document.querySelector(".circle");
     
gsap.to([square, circle], { x: 200 })
```

各种对象的确定方法，包括数组，svg，HTML，变量等等

## 3. The variables

vars 对象包含动画的所有信息。

- 这些信息可以是您想制作动画的任意属性，
- 也可以是影响动画行为的特殊属性，like `duration`, `onComplete` or `repeat`.

```js
gsap.to(target, {
  // this is the vars object
  // it contains properties to animate
  x: 200,
  rotation: 360,
  // and special properties
  duration: 2
})
```

# 使用GASP我们可以操作哪些属性

GSAP 几乎可以为任何东西制作动画，没有预先确定的列表。这包括 CSS 属性、自定义对象属性，甚至 CSS 变量和复杂字符串！最常见的动画属性是变换和不透明度。

## GASP为transform定义了简写形式


Here's a list of the shorthand transforms and some other commonly used properties.

| GSAP                          | Description or equivalent CSS       |
| ----------------------------- | ----------------------------------- |
| x: 100                        | transform: translateX(100px)        |
| y: 100                        | transform: translateY(100px)        |
| xPercent: 50                  | transform: translateX(50%)          |
| yPercent: 50                  | transform: translateY(50%)          |
| scale: 2                      | transform: scale(2)                 |
| scaleX: 2                     | transform: scaleX(2)                |
| scaleY: 2                     | transform: scaleY(2)                |
| rotation: 90                  | transform: rotate(90deg)            |
| rotation: "1.25rad"           | Using Radians - no CSS alternative  |
| skew: 30                      | transform: skew(30deg)              |
| skewX: 30                     | transform: skewX(30deg)             |
| skewY: "1.23rad"              | Using Radians - no CSS alternative  |
| transformOrigin: "center 40%" | transform-origin: center 40%        |
| opacity: 0                    | adjust the elements opacity         |
| autoAlpha: 0                  | shorthand for opacity & visibility  |
| duration: 1                   | animation-duration: 1s              |
| repeat: -1                    | animation-iteration-count: infinite |
| repeat: 2                     | animation-iteration-count: 2        |
| delay: 2                      | animation-delay: 2                  |
| yoyo: true                    | animation-direction: alternate      |

 ##  除了transform
 
**CSS properties**
Transforms, colors, padding, border radius, GSAP can animate it all!
只要记得用驼峰式命名替代`-` ， e.g. `background-color` becomes `backgroundColor`.

 **SVG attributes** like `width`, `height`, `fill`, `stroke`, `cx`, `opacity` and even the SVG `viewBox` itself using an `attr` object.


任何数值、颜色或包含数字的复杂字符串
当我们说任何东西时，我们指的是任何东西。GSAP 甚至不需要 DOM 元素就能为属性制作动画。您可以将任何对象的任何属性作为目标，甚至可以像这样任意创建对象：

```js
//create an object
let obj = { myNum: 10, myColor: "red" };
//将对象的数值变化至200，颜色变为蓝色
gsap.to(obj, {
  myNum: 200,
  myColor: "blue",
  onUpdate: () => console.log(obj.myNum, obj.myColor)
});
```

 Canvas
我们使用 HTML 画布绘制了一个方框。我们对存储在位置对象中的 x 和 y 值进行动画处理，然后在动画的每帧上更新画布。
**_GSAP is often used this way to animate in Three.js, HTML canvas and Pixi.js_**


## 特殊属性

You can check them all out in [our documentation](https://gsap.com/docs/v3/GSAP/Tween), but here are some of most common ones.

| Property   | Description                                                                                                      |
| ---------- | ---------------------------------------------------------------------------------------------------------------- |
| duration   | 动画持续时间（秒） 默认： 0.5                                                                                                |
| delay      | Amount of delay before the animation should begin (seconds)                                                      |
| repeat     | How many times the animation should repeat.                                                                      |
| yoyo       | 如果为 "true"，则每重复一次 tween 就会向相反方向运行一次。(就像悠悠球一样） 默认值：false                                                          |
| stagger    | Time (in seconds) between the start of each target's animation (if multiple targets are provided)                |
| ease       | Controls the rate of change during the animation, like the motion's "personality" or feel. Default: "power1.out" |
| onComplete | 动画完成时运行的函数                                                                                                       |

# repeat

Do you want your animation to repeat infinitely? No problem! Use `repeat: -1`
You can `delay` the start of an animation by a certain number of seconds. You can also use `repeatDelay` to add a delay to the start of any repeat iterations.

# Ease曲线种类

用法：
`gsap.to(".purple", { rotation: 360, duration: 2, ease: "bounce.out" });`

![[Pasted image 20250422165325.png]]

# Staggers

类似motion tools里的延迟效果，多个图层的彼此间延时

## Simple Configuration[​](https://gsap.com/resources/getting-started/Staggers#simple-configuration "Direct link to Simple Configuration")

```
gsap.to('.box', {    y: 100,    stagger: 0.1 // 0.1 seconds between when each ".box" element starts animating});
```

![[Pasted image 20250422165838.png]]
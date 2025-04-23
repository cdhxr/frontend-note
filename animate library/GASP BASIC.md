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

## repeat

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

### Property Description

- #### amount 延迟完成的总时间
    
    [Number]: The total amount of time (in seconds) that gets split among all the staggers. So if `amount` is `1` and there are 100 elements that stagger linearly, there would be 0.01 seconds between each sub-tween's start time. If you prefer to specify a certain amount of time between each tween, use the `each` property _instead_.
    
- #### each 每个动画之间的单位延迟时间
    
    [Number]: The amount of time (in seconds) between each sub-tween's start time. So if `each` is `1` (regardless of how many elements there are), there would be 1 second between each sub-tween's start time. If you prefer to specify a **total** amount of time to split up among the staggers, use the `amount` property _instead_.
    
- #### from 当你对多个元素使用 `stagger` 进行错位动画时，`from` 决定**动画的起点**
    
    [String | Integer | Array]: The 
    
	- 值                含义 
	- | `"start"`   | 从第一个元素开始向后依次动画（默认值）
	- | `"end"`       | 从最后一个元素开始向前依次动画
	- | `"center"` | 从中间元素开始，向两边扩散动画
	- | `"edges"`   | 从两端元素开始，向中间收拢动画 
	- | `"random"` | 以随机顺序播放动画
	
	- 可以结合grid使用
	
- #### grid
    
    [Array | "auto"]: If 
    作用是告诉gasp这个界面上的元素是几行几列的，设置为auto可以自动判断，如果是网格布局的话，结合from使用有必要设置grid，否则就会按顺序判断，效果会出现异常
    
    如果你的元素不是规则网格排列（比如散落在页面上），GSAP 还提供一个高级方法：
    
	```js
	stagger: {
	  each: 0.1,
	  from: gsap.utils.distribute({
	    amount: 1,
	    position: "center"
	  })
	}
	```
	
	用 `distributeByPosition()` 或 `gsap.utils.distribute()` 可以让动画按像素距离来判断“谁离中心最近”。
	
- #### axis
    
	当你使用 `stagger` + `grid` 时，GSAP 默认会根据元素**与起点的 x 和 y 轴距离综合计算**动画顺序。
	但有时候你想让动画只在某一个方向传播，比如：
	- ❌ 不要从中心扩散成“圈圈”，
	- ✅ 而是只从左到右、或者从上到下错开出现
	这时候就可以用下面这个参数：✅ `axis: "x"` 或 `axis: "y"`
    
- #### ease 这个很好理解就是曲线


# sequencing animations

## Timelines

```js
// create a timeline  
let tl = gsap.timeline()  
  
// add the tweens to the timeline - Note we're using tl.to not gsap.to  
tl.to(".green", { x: 600, duration: 2 });  
tl.to(".purple", { x: 600, duration: 1 });  
tl.to(".orange", { x: 600, duration: 1 });
```

相当于创建了一个公用的时间轴，类似于AE中一个合成

但如果我们想在某些过渡效果之间添加间隙或延迟，该怎么办呢？一种方法是为过渡效果添加延迟，以抵消它的开始时间。

```js
let tl = gsap.timeline()  
  
tl.to(".green", { x: 600, duration: 2 });  
tl.to(".purple", { x: 600, duration: 1, delay: 1 });  
tl.to(".orange", { x: 600, duration: 1 });
```

但这并不十分灵活。如果我们想让微调重叠或同时开始呢？

## Position Parameter

位置参数时可以精细的控制动画在各种的地方实现

```js
let tl = gsap.timeline()  
  
// start at exactly 1 second into the timeline (absolute)  
tl.to(".green", { x: 600, duration: 2 }, 1);  
  
// insert at the start of the previous animation  
tl.to(".purple", { x: 600, duration: 1 }, "<");  
  
// insert 1 second after the end of the timeline (a gap)  
tl.to(".orange", { x: 600, duration: 1 }, "+=1");
```

The most commonly used position parameters are the following -

1. **Absolute time** (in seconds) measured from the start of the timeline.
    
    ```js
    // insert exactly 3 seconds from the start of the timeline
    tl.to(".class", {x: 100}, 3);
    ```
    
2. **A Gap**
    
    ```js
    //  1 second after the end of the timeline (usually the previously inserted animation)
    tl.to(".class", {x: 100}, "+=1");
    // beyond the end of the timeline by 50% of the inserting animation's total duration
    tl.to(".class", {x: 100}, "+=50%");
    ```
    
3. **An Overlap**
    
    ```js
    //  1 second before the end of the timeline (this is usually the previously inserted animation)
    tl.to(".class", {x: 100}, "-=1");
    //  overlap with the end of the timeline by 25% of the inserting animation's total duration
    tl.to(".class", {x: 100}, "-=25%");
    ```

## Special Properties

Timelines share most of the same special properties that tweens have like `repeat` and `delay` which allow you to control the entire sequence of animations as a whole!

大多数特殊属性都有，可以用于控制整个序列的动画

`let tl = gsap.timeline({repeat: -1, repeatDelay: 1, yoyo: true})`

如果你发现自己一遍又一遍地输入一个属性，那么可能是时候使用默认值了。添加到时间线默认值对象中的任何属性都将被所有使用 to()、from() 和 fromTo() 等便捷方法创建的子对象继承。这是保持代码简洁的好方法。

```js
var tl = gsap.timeline({defaults: {duration: 1}});

//no more repetition of duration: 1!
tl.to(".green", {x: 200})
  .to(".purple", {x: 200, scale: 0.2})
  .to(".orange", {x: 200, scale: 2, y: 20});
```

# Control and Callbacks


```js
// store the tween or timeline in a variable  
let tween = gsap.to("#logo", {duration: 1, x: 100});  
  
//pause  
tween.pause();  
  
//resume (honors direction - reversed or not)  恢复方向
tween.resume();  
  
//reverse (always goes back towards the beginning)  
tween.reverse();  
  
//jump to exactly 0.5 seconds into the tween  
tween.seek(0.5);  
  
//jump to exacty 1/4th into the tween 's progress:  
tween.progress(0.25);  
  
//make the tween go half-speed  
tween.timeScale(0.5);  
  
//make the tween go double-speed  
tween.timeScale(2);  
  
//immediately kill the tween and make it eligible for garbage collection  
tween.kill();  
  
// You can even chain control methods  
// Play the timeline at double speed - in reverse.  
tween.timeScale(2).reverse();
```


## Callback回调函数

If you need to know when an animation starts, or maybe run some JS when an animation comes to an end, you can use **Callbacks**. All tweens and timelines have these callbacks:

- **onComplete**: invoked when the animation has completed.
- **onStart**: invoked when the animation begins
- **onUpdate**: invoked every time the animation updates (on every frame while the animation is active).
- **onRepeat**: invoked each time the animation repeats.
- **onReverseComplete**: invoked when the animation has reached its beginning again when reversed.


在用户交互事件的事件监听器中，我们可以使用控制方法来精细控制动画的播放状态。

在下面的示例中，我们为每个元素创建了一个时间线（这样就不会在所有实例中都触发相同的动画），将时间线的引用附加到元素本身，然后在元素悬停时播放相关的时间线，当鼠标离开时则反向播放。我们还调整了速度，使其在反向时更快，在进入时更慢。这是一种很好的用户体验模式。

# Overwrite Mode

```js
// Set overwrite on a tween
gsap.to(".line", { x: 200, overwrite: true });

// Set overwrite globally for all tweens
gsap.defaults({ overwrite: true });

// Set overwrite for all tweens in a timeline
const tl = gsap.timeline({ defaults: { overwrite: true } });
```

|模式|行为描述|
|---|---|
|`false`（默认）|不干涉，多个动画可能会冲突并“打架”|
|`true`|彻底终止同目标的所有旧动画|
|`"auto"`|**只终止冲突的属性动画**，其他的保留|
只有那些**和当前动画冲突的部分**会被终止。 举例：如果 `tween1` 在动画 `x` 和 `rotation` 属性，然后 `tween2` 开始对同一个目标的 `x` 进行动画，并且设置了 `overwrite: "auto"`，那么：

- `tween1` 中关于 `rotation` 的动画会继续，
- 但 `tween1` 中对 `x` 的动画会被停止。


# Avoiding FOUC

Have you ever noticed an annoying _"flash of unstyled content"_ (FOUC) when a web page first loads? That happens because browsers render things as quickly as possible, often **BEFORE** your JavaScript executes the first time. So what if some of your initial styles are set via JavaScript...like with GSAP?

解决方案：在 CSS 中对元素应用 visibility: hidden;，然后使用 GSAP 的 autoAlpha 属性在页面加载时显示它（或以动画形式显示）！

# immediateRender

from() 和 fromTo() tweens 比较特殊，因为它们会在创建后立即将 immediateRender 设置为 true。这在创建交错构建时尤其有帮助，因为在交错构建时您不希望元素在计划之前出现。

在同一对象的同一属性上有多个 from()/fromTo() 微调器时，更改默认行为就显得尤为重要。

the default of `immediateRender: true` however can cause problems when creating multiple from() tweens on the same properties of the same element.

Notice in the demo below that you don't see green animate a second time.






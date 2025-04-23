# Creating from() logic issues

使用 .to() 和 .from() tweens 而不是 .fromTo() tweens 通常是明智之举，因为它们更具动态性--它们会从 tweens 首次渲染时的当前值中提取起始值或结束值。这也是文章 "高效动画 "中的提示之一。但要小心，因为动态特性会在一些情况下让你吃亏。

## 1. 首先，请记住 .from() tweens 从提供的值到当前值。请看这个示例：

```js
<div class="box gradient-green"></div>
<button>Restart</button>
```

现在试着连续多次点击它。 方框会停止显示，因为它会使用当前的不透明度作为终点，如果动画尚未完成，终点的值会小于 1。

解决方法很简单：使用 .fromTo()。
或者，你也可以事先创建动画，然后使用一个控制方法。

## 2. 其次，请记住，.from() 和 .fromTo() tween 的默认情况下 immediateRender 为 true，因为这通常是最直观的行为（如果从某个值开始制作动画，则应立即从该值开始）。

但是，如果在影响同一对象相同属性的 .to() tween 之后创建 .from() tween，试想一下会发生什么情况。

```js
const tl = gsap.timeline()
tl.to(".box", {x: 100});
tl.from(".box", {x: 100});
```

默认情况下，.to() tweens 会等到播放头实际移动后才开始渲染（在时间为 0 时渲染会浪费 CPU 循环，因为什么都没有改变）。
但由于 from() 的 immediateRender 值为 true，因此 x 会在当前时间点立即跳转到 100！
然后，它会在下一个刻度运行 .to() tween（因为它在时间线中处于第一位），并记录当前的起始值 100！因此，它会在 0.5 秒内将 100 变为 100。
然后，它运行 .from() tween，将缓存值 0 作为结束值。

如果有多条时间线影响同一元素，类似这种情况就很难捕捉。因此，在使用 .to() 和 .from() tweens 时，请注意事情是如何运作的。它们非常强大，但强大的同时也意味着责任。一个简单的解决方案是在 .to() tween 上设置 immediateRender: true，或在 .from() tween 上设置 immediateRender: false。

## 3.  第三种情况与之类似，但涉及 repeatRefresh 和重复。


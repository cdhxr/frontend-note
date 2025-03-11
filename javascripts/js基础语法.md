#grammer #js
- 变量声明
	- let 用于声明变量
	- const 用于声明常量
- 原生数据类型
	- String, Number, Boolean, 
	- null, 用作表示空对象
	- undefined，往往用作表示刚刚声明的变量，没确定类型
	- object ，类似Java中的Object类，联想到Python的万物皆对象
	- 不用声明具体的类型，类比python
	- `typeof 变量名`，可以得到变量类型
	- String
		- 连接
			- String之间用＋连接（同python）
			- 新方法：模版字符串——esc下方的反引号取代引号，括起所有输出，${变量}
				- 新老对比：``My name is ${username}``--`“my name is ”+username`
		- 内置方法
			- `s.length`长度
			- `s.toUperCase()`,`s.toLowerCase()`大小写
			- `s.substring(0,5)`切片
			- `s.split(',')`根据特定分割内容
- 数组
	- `const fruit =["apples","lemons"]`可以看做py中的列表
	- `fruits.push("mangos")`在末尾加入元素
	- `fruits.pop()`在末尾删去元素
	- `fruits.unshift("strawberry")`在开头加入元素
	- `Array.isArray(fruits)`将判断变量是否为数组，返回布尔	
	- `fruits.indexOf("apples")`查询元素的索引
- 对象
	- 基本语法
			- ![[js对象基本语法.jpg]]
	- `const {属性名}=person`可以把属性从类内提取出来
	- `person.email='john@email'`可以通过赋值添加属性
	- 对象数组
			- ![[js对象数组.jpg]]
- JSON
	- 一种数据格式
	- 和对象数组的唯一的区别是每个属性都打上“属性”（不是‘属性’）
	- `const todoJSON = JSON.stringfy(数组名)` 将对象数组转换为JSON，需将其上传至服务器？
- if/switch
	- 语法和C一致
	- `==`只考虑值，`===`考虑值和类型
	- &&用于多条件
- for/while
	- 与C系语法相同，只需注意声明变量用let
	- `for(let 变量 of 数组)`则变量将是数组中的每一项

25/02/18补充：

# use strict模式
在代码开头加上"use strict"则会进行类型检查
# 反引号与简易函数
反引号是 **功能扩展** 引号。
它们允许我们通过将变量和表达式包装在 `${…}` 中
`${…}` 内的表达式会被计算
类似于一个简易函数

```js
name = John
alert( `Hello,${name}!`); // Hello,John!
```

# 交互alert，prompt和confirm

- alert
	- 它会显示一条信息，并等待用户按下 “OK”。
	- `alert("hello!")`
- promt
	- `result = prompt(title, [default]);`
	- 浏览器会显示一个带有文本消息的模态窗口，还有 input 框和确定/取消按钮
	- `title`显示给用户的文本
	- `default`可选的第二个参数，指定 input 框的初始值。
	- ![[Pasted image 20250218201246.png]]
- `confirm`
	- 跳出问题，没有对话框，只有一个参数用于描述问题
	- 显示信息等待用户点击确定或取消。点击确定返回 `true`，点击取消或按下 Esc 键返回 `false`。

这些方法都是模态的：它们暂停脚本的执行，并且不允许用户与该页面的其余部分进行交互，直到窗口被解除。

# 类型转换

String(value)/Number(value)/Boolean(value)
需要一个字符串形式的值时，就会 **自动** 进行字符串转换。
在算术函数和表达式中，会自动进行 number 类型转换。
一些编程语言视 `"0"` 为 `false`。但在 JavaScript 中，非空的字符串总是 `true`。

# 严格相等
普通的相等性检查 `==` 存在一个问题，它不能区分出 `0` 和 `false`：
严格相等运算符 `===` 在进行比较时不会做任何的类型转换。

```js
alert(0 == false);// true
alert('' == false);// true
alert(0 === false);// false
```

# 空值合并运算符

 `a ?? b` 的结果是：

- 如果 `a` 是已定义的，则结果为 `a`，
- 如果 `a` 不是已定义的，则结果为 `b`。

换句话说，如果第一个参数不是 `null/undefined`，则 `??` 返回第一个参数。否则，返回第二个参数。

- `||` 返回第一个 **真** 值。
- `??` 返回第一个 **已定义的** 值。


# 对象 —— 原始值转换

这节就是讲，把对象转换为基本数据类型。(“string”，或"number")  
可以根据自己指定的规则，定义转换为哪种类型。

https://zh.javascript.info/object-toprimitive

尚未完全理解


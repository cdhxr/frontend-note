在js中
可以利用document对象，
操作dom树中的元素

document对象是是DOM的主入口点

`<html> = document.documentElement`

最顶层的 document 节点是 document.documentElement。
这是对应 `<html>` 标签的 DOM 节点。

`<body> = document.body`

另一个被广泛使用的 DOM 节点是 `<body>` 元素 —— document.body。

`<head> = document.head`
`<head> `标签可以通过 document.head 访问。

childNodes 集合列出了所有子节点，包括文本节点。

```js
for (let i = 0; i < document.body.childNodes.length; i++) {
      alert( document.body.childNodes[i] );
       // Text, DIV, Text, UL, ..., SCRIPT
    }
```

firstChild 和 lastChild 属性是访问第一个和最后一个子元素的快捷方式

正如我们看到的那样，childNodes 看起来就像一个数组。但实际上它并不是一个数组，而是一个 集合 —— 一个类数组的可迭代对象。

不要使用 for..in 来遍历集合

### 1. **DOM 树的结构**

- HTML 页面被解析为一个DOM树，包含多个节点：`Document`、`Element`、`Text`、`Attribute` 等。
- 每个元素都是一个节点，可以包含子元素、文本内容等。

### 2. **通过 DOM API 导航**

- `parentNode`：返回当前节点的父节点。
- `childNodes`：返回一个包含所有子节点的集合（包括文本节点、注释节点等）。
- `firstChild` 和 `lastChild`：返回第一个和最后一个子节点。
- `nextSibling` 和 `previousSibling`：返回当前节点的前一个或下一个兄弟节点。

### 3. **获取元素节点**

- `getElementById(id)`：返回具有指定 `id` 的元素。
- `getElementsByClassName(className)`：返回具有指定类名的所有元素集合。
- `getElementsByTagName(tagName)`：返回所有具有指定标签名的元素集合。
- `querySelector(selector)`：返回匹配指定CSS选择器的第一个元素。
- `querySelectorAll(selector)`：返回所有匹配指定CSS选择器的元素集合
- `elem.matches(css)` 不会查找任何内容，它只会检查 `elem` 是否与给定的 CSS 选择器匹配。它返回 `true` 或 `false`。
- `elem.closest(css)` 方法会查找与 CSS 选择器匹配的最近的祖先
- 目前为止，最常用的是 `querySelector` 和 `querySelectorAll`

### 4. **DOM 属性**

每个 DOM 节点都属于一个特定的类。这些类形成层次结构（hierarchy）。完整的属性和方法集是继承的结果。

- `nodeType`：返回节点的类型（如元素节点、文本节点等）。
	- 在现代脚本中，我们可以使用 `instanceof` 和其他基于类的检查方法来查看节点类型，但有时 `nodeType` 可能更简单。我们只能读取 `nodeType` 而不能修改它。
- `nodeName`：返回节点的名称（如 `div`、`p` 等）。
	- `tagName` 属性仅适用于 `Element` 节点。
	- `nodeName` 是为任意 `Node` 定义的：
	    - 对于元素，它的意义与 `tagName` 相同。
	    - 对于其他节点类型（text，comment 等），它拥有一个对应节点类型的字符串。
- `innerHTML`：允许将元素中的 HTML 获取为字符串形式。
	- 我们也可以修改它。因此，它是更改页面最有效的方法之一。
	- `document.body.innerHTML = 'The new BODY!'; // 替换它`
	- `outerHTML` 属性包含了元素的完整 HTML。就像 `innerHTML` 加上元素本身一样。
- `nodeValue`：返回节点的值（如文本内容或属性值）
	- 其他节点类型，例如文本节点，具有它们的对应项：`nodeValue` 和 `data` 属性。这两者在实际使用中几乎相同，只有细微规范上的差异。因此，我们将使用 `data`，因为它更短。
- `textContent`元素内的文本：提供了对元素内的 **文本** 的访问权限：仅文本，去掉所有 `<tags>`
	- 写入文本会将文本放入元素内，所有特殊字符和标签均被视为文本。可以安全地插入用户生成的文本，并防止不必要的 HTML 插入。
	- ![[Pasted image 20250225134209.png]]
- `hidden`：当被设置为 `true` 时，执行与 CSS `display:none` 相同的事。


### 5. **元素节点的常用方法**

HTML属性（attribute）和DOM属性（property）
前者写在HTML标签内部，后者在js中定义
大多数情况下，它们描述同一件事物，attribute定义初值，property用作修改

- `getAttribute(attr)`：获取元素的属性值。
- `setAttribute(attr, value)`：设置元素的属性值。
- `hasAttribute(attr)`：检查元素是否具有指定的属性。
- `removeAttribute(attr)`：删除元素的指定属性。

### 6. **动态遍历和修改 DOM**


- 创建新节点的方法：
    
    - `document.createElement(tag)` —— 用给定的标签创建一个元素节点，
    - `document.createTextNode(value)` —— 创建一个文本节点（很少使用），
    - `elem.cloneNode(deep)` —— 克隆元素，如果 `deep==true` 则与其后代一起克隆。
- 插入和移除节点的方法：
    
    - `node.append(...nodes or strings)` —— 在 `node` 末尾插入，
    - `node.prepend(...nodes or strings)` —— 在 `node` 开头插入，
    - `node.before(...nodes or strings)` —— 在 `node` 之前插入，
    - `node.after(...nodes or strings)` —— 在 `node` 之后插入，
    - `node.replaceWith(...nodes or strings)` —— 替换 `node`。
    - `node.remove()` —— 移除 `node`。
    
    文本字符串被“作为文本”插入。
    
- 在 `html` 中给定一些 HTML，`elem.insertAdjacentHTML(where, html)` 会根据 `where` 的值来插入它：
    
    - `"beforebegin"` —— 将 `html` 插入到 `elem` 前面，
    - `"afterbegin"` —— 将 `html` 插入到 `elem` 的开头，
    - `"beforeend"` —— 将 `html` 插入到 `elem` 的末尾，
    - `"afterend"` —— 将 `html` 插入到 `elem` 后面。



要管理 class，有两个 DOM 属性：

- `className` —— 字符串值，可以很好地管理整个类的集合。
- `classList` —— 具有 `add/remove/toggle/contains` 方法的对象，可以很好地支持单个类。
	- `elem.classList.add/remove(class)` —— 添加/移除类。
	- `elem.classList.toggle(class)` —— 如果类不存在就添加类，存在就移除它。
	- `elem.classList.contains(class)` —— 检查给定类，返回 `true/false`。- 

要改变样式：

- `style` 属性是具有驼峰（camelCased）样式的对象。对其进行读取和修改与修改 `"style"` 特性（attribute）中的各个属性具有相同的效果。要了解如何应用 `important` 和其他特殊内容 —— 在 [MDN](https://developer.mozilla.org/zh/docs/Web/API/CSSStyleDeclaration) 中有一个方法列表。
	
	- `elem.style.width="100px"` 的效果等价于我们在 `style` 特性中有一个 `width:100px` 字符串
	
- `style.cssText` 属性对应于整个 `"style"` 特性（attribute），即完整的样式字符串。
    
	要读取已解析的（resolved）样式（对于所有类，在应用所有 CSS 并计算最终值之后）：
- `getComputedStyle(elem, [pseudo])` 返回与 `style` 对象类似的，且包含了所有类的对象。只读。（像 ::before 和 ::after 这样的伪元素并不是元素的常规 DOM 的一部分，但它们仍会被浏览器渲染和样式化。 要获取这些伪元素的计算样式（例如，应用于 ::before 伪元素的内容、宽度、高度或颜色），需要将相应的伪元素作为第二个参数传递。）


元素大小和滚动，Windows大小和滚动，坐标等等参考网页，都是用法相关东西
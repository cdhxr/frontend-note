#js 

应用程序接口（API）是基于编程语言构建的结构，让开发者更容易地创建复杂的功能。它们抽象了复杂的代码，并提供一些简单的接口规则直接使用。

# JavaScript、API 和其他 JavaScript 工具之间的关系

- JavaScript——一种内置于浏览器的高级脚本语言，你可以用来实现 Web 页面/应用中的功能。注意 JavaScript 也可用于其他像 [Node](https://developer.mozilla.org/zh-CN/docs/Learn/Server-side/Express_Nodejs/Introduction) 这样的编程环境。
- 客户端 API——内置于浏览器的结构程序，位于 JavaScript 语言之上，使你可以更容易的实现功能。
- 第三方 API——置于第三方普通的结构程序（例如 Disqus、Facebook），使你可以在自己的 Web 页面中使用这些平台的某些功能（例如在你的 Web 页面显示 Disqus 评论）。
- JavaScript 库——通常是包含具有[自定义函数](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Building_blocks/Functions)的一个或多个 JavaScript 文件，把这些文件关联到你的 Web 页以加快或启用常见的功能的编码。示例包括 jQuery、Mootools 和 React。
- JavaScript 框架——从库开始的下一步，JavaScript 框架（例如 Angular 和 Ember）视图把 HTML、CSS、JavaScript 和其他安装的技术打包在一起，然后用来从头编写一个完整的 Web 应用。库和框架的主要区别在于“控制反转”。调用库中的方法时，开发人员处于控制地位。而框架的控制权则相反：框架调用开发人员的代码。


使用API时，实际上是在利用预先定义好的类、对象和方法来与服务器进行交互。

# Browser API

1. **DOM APIs**：用于操作文档对象模型（DOM）的接口，例如 `document.xxx` 和 `window.xxx`，可以用来访问和修改页面的结构、内容和样式。
   
2. **Data Fetch APIs**：用于获取数据的接口，包括 `XMLHttpRequest`、`fetch` 和 `WebRTC`。它们提供了从服务器获取数据、进行异步请求和点对点数据传输的能力。
   
3. **Graphic APIs**：用于处理图形和渲染的接口，例如 `<canvas>` 元素、Canvas API 和 WebGL API。可以用于绘制图形、处理动画和实现3D渲染。

4. **Video/Audio APIs**：用于音视频处理的接口，比如 `<video>` 和 `<audio>` 元素，以及 `HTMLMediaElements.xxx`，可以实现视频和音频的播放和控制。

5. **Storage APIs**：用于本地存储数据的接口，包括 Web Storage API（如 `localStorage` 和 `sessionStorage`）、IndexedDB API 和 Cache API。它们允许在用户设备上存储数据，支持离线访问。

这些 API 为前端开发者提供了丰富的功能，帮助构建更互动和功能丰富的网页应用。


## 常见浏览器 API

特别地，你将使用的最常见的浏览器 API 类别（以及我们将在本模块中更详细地介绍的）有：

- **操作文档的 API** 内置于浏览器中。最明显的例子是 [DOM（文档对象模型）API](https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model)，它允许你操作 HTML 和 CSS——创建、移除以及修改 HTML，动态地将新样式应用到你的页面，等等。每当你看到一个弹出窗口出现在一个页面上，或者显示一些新的内容时，这都是 DOM 的行为。你可以在[操控文档](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Client-side_web_APIs/Manipulating_documents)一文中找到关于这些类型的 API 的更多信息。
- **从服务器获取数据的 API** 用于更新网页的一小部分，其非常常用。这个看似很小的细节可以对网站的性能和行为产生巨大的影响——如果你只是更新一个股票列表或者一些可用的新故事而不需要从服务器重新加载整个页面将使网站或应用程序的响应感觉更快、更“敏捷”。主要用于此目的的 API 是 [Fetch API](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)，虽然旧的代码可能仍然会使用 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) API。你也可能会同时遇到描述这项技术的术语 **Ajax**）。你可以在[从服务器中获取数据](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data)一文找到关于此类 API 的更多信息。
- **用于绘制和操作图形的 API** 目前已被浏览器广泛支持。最流行的是允许你以编程方式更新包含在 HTML [`<canvas>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/canvas) 元素中的像素数据以创建 2D 和 3D 场景的 [Canvas](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API) 和 [WebGL](https://developer.mozilla.org/zh-CN/docs/Web/API/WebGL_API)。例如，你可以绘制矩形或圆形等形状，将图像导入到画布上，然后使用 Canvas API 对其应用滤镜（如棕褐色滤镜或灰度滤镜），或使用 WebGL 创建具有光照和纹理的复杂 3D 场景。这些 API 经常与用于创建动画循环的 API（例如 [`window.requestAnimationFrame()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)）和其他 API 一起不断更新诸如动画和游戏之类的场景。
- **[音频和视频 API](https://developer.mozilla.org/en-US/docs/Web/Media/Audio_and_video_delivery "此页面目前仅提供英文版本")** 例如 [`HTMLMediaElement`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLMediaElement)、[Web 音频 API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Audio_API) 和 [WebRTC](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API) 允许你使用多媒体来做一些非常有趣的事情，比如创建用于播放音频和视频的自定义 UI 控件、显示字幕字幕和你的视频、从网络摄像机抓取视频、通过画布操纵（见上），或在网络会议中显示在别人的电脑上，或者添加效果到音轨（如增益、失真、平移等） 。
- **设备 API** 使得你可以与设备的硬件进行交互：例如，使用[地理位置 API](https://developer.mozilla.org/zh-CN/docs/Web/API/Geolocation_API)来访问设备的 GPS，从而查找用户的位置。
- **客户端存储 API** 使得你可以在客户端存储数据，因此你可以创建一个保存页面加载状态的应用，甚至让设备在处于离线状态时可用。有多种选项可供选择，例如使用 [Web 存储 API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API) 的简单的键/值存储以及使用 [IndexedDB API](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API) 的更复杂的数据库存储。

## 常见第三方 API

第三方 API 种类繁多; 下列是一些比较流行的你可能迟早会用到的第三方 API:

- 地图 API（例如 [Mapquest](https://developer.mapquest.com/) 和 [Google Maps API](https://developers.google.com/maps/)），允许你在网页上对地图执行多种操作。
- [Facebook API 套件](https://developers.facebook.com/docs/)，允许你将 Facebook 生态系统中的各个部分应用到你的应用并使之受益，比如说它提供了通过 Facebook 账户登录、接受应用内支付、推送有针对性的广告活动等功能。
- [Telegram API](https://core.telegram.org/api)，允许你在网站中潜入来自 Telegram 频道的内容，此外还提供了对机器人的支持。
- [YouTube API](https://developers.google.com/youtube/)，允许你将 Youtube 上的视频嵌入到网站中去，同时提供搜索 Youtube、创建播放列表等众多功能。
- [Pinterest API](https://developers.pinterest.com/)，提供了管理 Pinterest 图板和图钉的工具，以便将它们纳入你的网站。
- [Twilio API](https://www.twilio.com/docs)，为你的应用提供了针对语音通话和视频聊天的框架，以及从你的 app 发送短信息或多媒体信息等诸多功能。
- [Disqus API](https://disqus.com/api/docs/)，提供了一个可集成到网站中的评论平台。
- [Mastodon API](https://docs.joinmastodon.org/api/)，让你以编程方式操作 Mastodon 社交网络的特性。
- [IFTTT API](https://ifttt.com/developers)，允许你通过一个平台集成多个 API。
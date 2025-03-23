
https://satelab.spcsft.com/en

# 随着scroll，始终悬浮在界面上的信息

- 内容包括，左侧的标题SPACESHIFT，语言选择器，右侧的菜单
- ![[Pasted image 20250207182030.png]]

```html
<header class="section fixed sd">
  <!-- 页面头部区域 -->
  <div class="section-inner sd">
    <!-- 内部容器，可能用于调整布局 -->
    <div class="sd appear">
      <!-- 可见区域（可能与动画效果相关） -->
      <div class="sd appear">
        <!-- 可见区域，用于存放图片 -->
        <div class="image sd">
          <!-- 内嵌样式，用于设置图片背景 -->
          <style>
            .sd[data-r-0_0_0_0_2_7097286c-3349-4330-9e7f-fc736e8722c1]:before {
              background-image: url("https://storage.googleapis.com/studio-design-asset-files/projects/BmqM5xdYqX/s-541x85_27e55c22-7926-4bcf-b250-1967d79b44ff.svg")
            }
          </style>
        </div>
        <!-- 语言切换区域 -->
        <div class="sd appear">
          <!-- "JP" 语言选择按钮，链接到主页 -->
          <a href="/" class="link sd appear">
            <p class="text sd appear">JP</p>
          </a>
          <!-- "/" 分隔符 -->
          <p class="text sd appear">/</p>
          <!-- "EN" 语言选择按钮 -->
          <div class="sd appear">
            <p class="text sd appear">EN</p>
          </div>
        </div>
      </div>
      <!-- 导航链接区域 -->
      <div class="sd appear">
        <!-- "Top" 链接，指向页面顶部 -->
        <a href="#top" class="text sd appear">Top</a>
        <!-- "Concept" 链接，指向概念介绍部分 -->
        <a href="#concept" class="text sd appear">Concept</a>
        <!-- "What is SateLab" 链接，指向关于 SateLab 的介绍 -->
        <a href="#about" class="text sd appear">What is SateLab</a>
        <!-- "Case" 链接，指向案例部分 -->
        <a href="#case" class="text sd appear">Case</a>
        <!-- "Technology" 链接，指向技术部分 -->
        <a href="#technology" class="text sd appear">Technology</a>
        <!-- "Step" 链接，指向步骤部分 -->
        <a href="#step" class="text sd appear">Step</a>
        <!-- "Partners" 链接，指向合作伙伴部分 -->
        <a href="#partners" class="text sd appear">Partners</a>
        <!-- 外部链接，指向 SPACE SHIFT 网站 -->
        <a href="https://www.spcsft.com/en/" rel="noopener" target="_blank" class="sd appear">
          <!-- SPACE SHIFT 文本链接 -->
          <p class="text sd appear">SPACE SHIFT</p>
          <!-- 外部链接图标 -->
          <i class="icon material-icons sd appear">call_made</i>
        </a>
      </div>
    </div>
  </div>
</header>
```

分析：
CSS文件中，根据div的类名，利用flex进行布局，
将header的标签类，在CSS文件中设定位fixed，使其可以浮在页面顶部，不受滚动影响
内容默认显示在左上角
布局时，像在进行”切块“，通过div间的挤压，使创建的div可以在想要的位置上，以实现内容的布局

三个header以适应三种屏幕尺寸

HTML结构

![[Pasted image 20250207210046.png]]

![[Pasted image 20250208222225.png]]
三个header，适应三种尺寸
接下来每个div代表一整个分页面

- head
- body
	- 一层div,id是nuxt
		- 一层div
			- 一层div，类名为container
				- 一堆styles标签
				- （三个div，一个进入页面时显示标题，一个加载页面图标，一个真正的网页内容）
				- 标题div
				- 加载div
				- 内容div
					- modals,div，空的块
					- StudioCanvus，div（真正的内容div）
						- div，appear展示内容
							- img脏灰色背景
							- 链接标签用于contact
							- 三种header用于不同屏幕尺寸的顶栏
							- 多个div，每个div一个展示区域，随着scroll逐一展示，也是真正的主要内容区域
							- 一个div划分底部区域
								- footer底栏
						- div font
							- style标签
	- scripts
	-  `<deepI-input-controller></deepI-input-controller>` 这看起来是一个自定义元素，可能与输入控制或表单相关，可能是通过某个框架或库生成的。
	- `<div id="eagle-drag-images" style="position: fixed; top: -100000px;"></div>`这是一个固定定位的 `div` 元素，它的 `top` 样式被设置为 `-100000px`，这通常意味着该元素在页面上不可见。这种隐藏的方式可能用于后续的动态操作或交互。

![[Pasted image 20250208220133.png]]

以下是格式化后的代码，并带有注释，帮助理解每个部分的功能：

```html
<!-- 定义一个按钮元素，type="button" 表示它是一个普通按钮，不提交表单 -->
<button type="button" 
        data-s-*="" 
        data-r-3_1_1_8_*="" 
        class="image link sd">
  
  <!-- 外层容器 div，可能用于包裹多个元素和动画效果 -->
  <div data-s-*="" class="sd appear">
    
    <!-- 第二层 div，进一步包裹图片和文本 -->
    <div data-s-*="" class="sd appear">
      
      <!-- 图片元素，显示 SVG 格式的图标 -->
      <img data-s-*="" 
           class="sd" 
           alt="" 
           src="https://storage.googleapis.com/studio-design-asset-files/projects/BmqM5xdYqX/s-91x57_7075ea71-a6df-4ed4-a280-bc724d069b1d.svg">
      
      <!-- 该 div 内包含一段描述文本 -->
      <div data-s-*="" class="sd appear">
        <!-- 文本元素 "Insurance" -->
        <p data-s-*="" 
           data-r-0_1_0_0_3_1_1_8_*="" 
           class="text sd appear">Insurance</p>
        <div data-s-*="" class="sd"></div>
      </div>
    </div>
    
    <!-- 另一个包含文本和图标的 div -->
    <div data-s-*="" class="sd appear">
      
      <!-- 文本 "SPACE SHIFT" -->
      <div data-s-*="" class="sd appear">
        <p data-s-*="" 
           data-r-0_0_1_0_3_1_1_8_*="" 
           class="text sd appear">SPACE SHIFT</p>
        <div data-s-*="" class="sd"></div>
      </div>
      
      <!-- 图片和文本 "Insurance Company" -->
      <div data-s-*="" class="sd appear">
        <div data-s-*="" class="sd appear">
          <img data-s-*="" 
               class="sd" 
               alt="" 
               src="https://storage.googleapis.com/studio-design-asset-files/projects/BmqM5xdYqX/s-14x15_6af5dc7f-5faa-4735-b4f9-8cd550ef0b84.svg">
          <p data-s-*="" 
             data-r-1_0_1_1_0_3_1_1_8_*="" 
             class="text sd appear">Insurance Company</p>
        </div>
        <div data-s-*="" class="sd"></div>
      </div>
    </div>
  </div>

  <!-- 额外的一层文本描述 -->
  <div data-s-*="" class="sd appear">
    <div data-s-*="" class="sd appear">
      <!-- 文本描述 "Revolutionize the insurance system with detecting disaster situations from space" -->
      <p data-s-*="" 
         data-r-0_0_1_3_1_1_8_*="" 
         class="text sd appear">Revolutionize the insurance system with detecting disaster situations from space</p>
      <div data-s-*="" class="sd"></div>
    </div>
  </div>

  <!-- 内联样式，定义按钮的背景图像 -->
  <style>
    .sd[data-r-3_1_1_8_*]:before { 
      background-image: url("https://storage.googleapis.com/studio-design-asset-files/projects/BmqM5xdYqX/s-2400x1485_v-frms_webp_3671fc63-888a-4949-b819-c7f7a2cd816c_middle.webp");
    }
  </style>
</button>
```

### 注释解释：

1. **按钮元素**：使用了 `type="button"` 来标识它是一个普通按钮，没有提交表单的功能。`class="image link sd"` 给按钮添加了样式控制。
2. **嵌套 div 元素**：这些 `<div>` 元素作为容器，可能用于定位、布局和控制显示的动画效果（如 `appear` 类）。
3. **图片元素**：显示了两个图标图片，使用 `src` 属性指定图片的 URL，并通过 `class="sd"` 来控制它们的样式。
4. **文本元素**：包含了多个 `<p>` 元素，分别显示“Insurance”、“SPACE SHIFT”和“Insurance Company”，这些文本可以用于描述或标题。
5. **内联样式**：通过 `:before` 伪元素，按钮在加载时会显示一个背景图片，该图片URL指定了一个WebP格式的图像。

这种结构可以用于创建一个带有图片、文本和动画效果的按钮，可能在用户交互时有一些视觉上的动态效果。


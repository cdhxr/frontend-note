
构建一个轮播图组件

1. HTML结构化，将图片作为列表结构中的元素

```html
<div id="my-slider" class="slider-list">
  <ul>
    <li class="slider-list__item--selected">
      <img src="https://p5.ssl.qhimg.com/t019c74624763dd070.png"/>
    </li>
    <li class="slider-list__item">
      <img src="https://p4.ssl.qhimg.com/t01adbe335db853eb3.jpg"/>
    </li>
    <li class="slider-list__item">
      <img src="https://p2.ssl.qhimg.com/t0164cd5ba0c3b60cb.jpg"/>
    </li>
    <li class="slider-list__item">
      <img src="https://p4.ssl.qhimg.com/t0133ac159b58f5478.jpg"/>
    </li>
  </ul>
</div>
```

2. CSS定义样式，利用绝对定位将，列表中的图片，重合固定在一个位置，并定义两个类为*选中的元素* 和 *未选中的元素* ，为其分别定义样式

```css
#my-slider {
  position: relative;
  width: 790px;
}

.slider-list ul {
  list-style-type: none;
  position: relative;
  padding: 0;
  margin: 0;
}

.slider-list__item,
.slider-list__item--selected {
  position: absolute;
  transition: opacity 1s;
  text-align: center;
}

.slider-list__item--selected {
  opacity: 1;
}
```

3. js调用编写行为，核心逻辑是通过更改元素的HTML类名，来更换其对应的CSS样式，以实现切换

```javascript
class Slider {
    constructor(id) {
        this.container = document.getElementById(id);
        this.items = this.container.querySelectorAll('.slider-list__item');
    }

    getSelectedItem() {
        return this.container.querySelector('.slider-list__item--selected');
    }

    getSelectedItemIndex() {
        const selected = this.getSelectedItem();
        if (selected) {
            return Array.from(this.items).indexOf(selected);
        }
        return null;
    }

    slideTo(idx) {
        const selected = this.getSelectedItem();
        if (selected) {
            selected.className = 'slider-list__item'; // 移除选中状态
        }
        const item = this.items[idx];
        if (item) {
            item.className = 'slider-list__item slider-list__item--selected'; // 设置新选中状态
        }
    }

    slideNext() {
        const currentIdx = this.getSelectedItemIndex();
        const nextIdx = (currentIdx + 1) % this.items.length;
        this.slideTo(nextIdx);
    }

    slidePrevious() {
        const currentIdx = this.getSelectedItemIndex();
        const previousIdx = (this.items.length + currentIdx - 1) % this.items.length;
        this.slideTo(previousIdx);
    }
}
```

建立控制流，需要利用Doms API的event相关方法

- 自定义事件

下方四个球和图片状态绑定，为了分别控制组件状态和图片的状态，可以采用自定义事件

- 在构造函数中增加controller，在controller中监听mouthover和mouthout事件，在对应的事件中做出对应的变化
- 自定义slide事件，并监听该事件，以保证自动播放时，红点与图片匹配

1. **创建事件对象**:
    - 使用 `CustomEvent` 构造函数创建自定义事件对象。
    - 可以传递事件名称和选项对象，包括是否冒泡、是否可取消以及自定义数据。
2. **分发事件**:
    - 使用 `dispatchEvent` 方法在目标元素上触发事件。
3. **监听事件**:
    - 使用 `addEventListener` 方法在目标元素或其他祖先元素上监听事件，并定义事件处理函数

如此这般，组件就实现了，但是组件很难维护

```javascript
class Slider{
  constructor(id, cycle = 3000){
    this.container = document.getElementById(id);
    this.items = this.container.querySelectorAll('.slider-list__item, .slider-list__item--selected');
    this.cycle = cycle;
    const controller = this.container.querySelector('.slide-list__control');
    if(controller){
      const buttons = controller.querySelectorAll('.slide-list__control-buttons, .slide-list__control-buttons--selected');
      controller.addEventListener('mouseover', evt=>{
        const idx = Array.from(buttons).indexOf(evt.target);
        if(idx >= 0){
          this.slideTo(idx);
          this.stop();
        }
      });
      controller.addEventListener('mouseout', evt=>{
        this.start();
      });
      this.container.addEventListener('slide', evt => {
        const idx = evt.detail.index
        const selected = controller.querySelector('.slide-list__control-buttons--selected');
        if(selected) selected.className = 'slide-list__control-buttons';
        buttons[idx].className = 'slide-list__control-buttons--selected';
      })
    }
    const previous = this.container.querySelector('.slide-list__previous');
    if(previous){
      previous.addEventListener('click', evt => {
        this.stop();
        this.slidePrevious();
        this.start();
        evt.preventDefault();
      });
    }
    const next = this.container.querySelector('.slide-list__next');
    if(next){
      next.addEventListener('click', evt => {
        this.stop();
        this.slideNext();
        this.start();
        evt.preventDefault();
      });
    }
  }
  getSelectedItem(){
    let selected = this.container.querySelector('.slider-list__item--selected');
    return selected
  }
  getSelectedItemIndex(){
    return Array.from(this.items).indexOf(this.getSelectedItem());
  }
  slideTo(idx){
    let selected = this.getSelectedItem();
    if(selected){
      selected.className = 'slider-list__item';
    }
    let item = this.items[idx];
    if(item){
      item.className = 'slider-list__item--selected';
    }
    const detail = {index: idx}
    const event = new CustomEvent('slide', {bubbles:true, detail})
    this.container.dispatchEvent(event)
  }
  slideNext(){
    let currentIdx = this.getSelectedItemIndex();
    let nextIdx = (currentIdx + 1) % this.items.length;
    this.slideTo(nextIdx);
  }
  slidePrevious(){
    let currentIdx = this.getSelectedItemIndex();
    let previousIdx = (this.items.length + currentIdx - 1) % this.items.length;
    this.slideTo(previousIdx);  
  }
  start(){
    this.stop();
    this._timer = setInterval(()=>this.slideNext(), this.cycle);
  }
  stop(){
    clearInterval(this._timer);
  }
}
const slider = new Slider('my-slider');
slider.start();
```

- [[插件化]]

- HTML模块化

简单地说，就是为了避免删除插件功能时，还要手动到HTML文件上删除插件图案，
就将插件对应的HTML代码写在插件功能函数中，
这样不调用功能就和不显示结构绑定了

这段代码通过将 **HTML 模块化** 主要体现在以下几个方面：

---

### **1. 插件的分离设计**

每个插件（如 `pluginController`、`pluginPrevious`、`pluginNext`）都有各自的 `render` 和 `action` 方法，分别负责以下内容：

- **`render`**：负责生成插件相关的 HTML 模块。
- **`action`**：负责为插件绑定特定的交互行为。

这种设计实现了 **HTML 渲染逻辑与功能逻辑的分离**，让每个插件更加独立。

#### 示例：

**插件 `pluginController` 的 `render` 方法：**

```javascript
render(images) {
  return `
    <div class="slide-list__control">
      ${images.map((image, i) => `
        <span class="slide-list__control-buttons${i===0 ? '--selected' : ''}"></span>
      `).join('')}
    </div>    
  `.trim();
}
```

- **模块化**：
  - HTML 模板使用字符串模板拼接。
  - 根据传入的 `images` 动态生成控制按钮。

**插件 `pluginController` 的 `action` 方法：**

```javascript
action(slider) {
  const controller = slider.container.querySelector('.slide-list__control');
  if (controller) {
    const buttons = controller.querySelectorAll('.slide-list__control-buttons, .slide-list__control-buttons--selected');
    
    // 添加交互逻辑
    controller.addEventListener('mouseover', evt => {
      const idx = Array.from(buttons).indexOf(evt.target);
      if (idx >= 0) {
        slider.slideTo(idx);
        slider.stop();
      }
    });

    controller.addEventListener('mouseout', evt => {
      slider.start();
    });
  }
}
```

- **功能化**：
  - 在 `action` 方法中完成事件绑定，实现与用户交互的逻辑。
  - 不直接与 `HTML` 模板耦合，增强了代码的可扩展性。

---

### **2. 主类 `Slider` 中的模块化设计**

`Slider` 的 `registerPlugins` 方法使得插件可以以模块化方式被注入，同时 HTML 的渲染逻辑也得以解耦：

#### 插件注入和 HTML 渲染：

```javascript
registerPlugins(...plugins) {
  plugins.forEach(plugin => {
    const pluginContainer = document.createElement('div');
    pluginContainer.className = '.slider-list__plugin';
    pluginContainer.innerHTML = plugin.render(this.options.images); // 调用插件的渲染方法
    this.container.appendChild(pluginContainer); // 插入到容器中

    plugin.action(this); // 调用插件的行为绑定方法
  });
}
```

- **动态渲染**：  
  - 每个插件的 `render` 方法动态生成相应的 HTML 片段，并插入到 `Slider` 容器中。
  - 插件的行为（通过 `action`）在 HTML 渲染完成后绑定。

- 抽象化
	- 基于模版化代码
	- 创建Component类，内有组件的方法：构造，插件注册，渲染render
	- 具体组件extends 组件类
	- 写插件类
	- 创建组件实例，注册插件


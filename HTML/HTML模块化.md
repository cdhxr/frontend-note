#modularity 

利用render方法，将HTML代码写入js文件中

```javascript
// Slider类：实现图片轮播功能，支持插件注册和定时轮播
class Slider {
  /**
   * 构造函数，初始化容器、选项、渲染内容并设置默认状态
   * @param {string} id - 容器的ID
   * @param {Object} opts - 配置选项，包含图片数组和轮播周期
   */
  constructor(id, opts = { images: [], cycle: 3000 }) {
    this.container = document.getElementById(id); // 获取轮播容器
    this.options = opts; // 存储配置选项
    this.container.innerHTML = this.render(); // 渲染图片列表
    this.items = this.container.querySelectorAll('.slider-list__item, .slider-list__item--selected'); // 获取图片项
    this.cycle = opts.cycle || 3000; // 设置默认轮播周期
    this.slideTo(0); // 默认显示第一张图片
  }

  /**
   * 渲染图片列表为HTML字符串
   * @returns {string} - 生成的HTML内容
   */
  render() {
    const images = this.options.images;
    const content = images.map(image => `
      <li class="slider-list__item">
        <img src="${image}" />
      </li>
    `.trim());
    return `<ul>${content.join('')}</ul>`;
  }

  /**
   * 注册插件，动态加载功能模块
   * @param {...Object} plugins - 插件列表
   */
  registerPlugins(...plugins) {
    plugins.forEach(plugin => {
      const pluginContainer = document.createElement('div'); // 创建插件容器
      pluginContainer.className = '.slider-list__plugin';
      pluginContainer.innerHTML = plugin.render(this.options.images); // 渲染插件内容
      this.container.appendChild(pluginContainer); // 插入插件到轮播容器
      plugin.action(this); // 执行插件行为逻辑
    });
  }

  /**
   * 获取当前选中的图片项
   * @returns {HTMLElement} - 当前选中的图片项
   */
  getSelectedItem() {
    return this.container.querySelector('.slider-list__item--selected');
  }

  /**
   * 获取当前选中图片的索引
   * @returns {number} - 当前选中图片的索引
   */
  getSelectedItemIndex() {
    return Array.from(this.items).indexOf(this.getSelectedItem());
  }

  /**
   * 切换到指定索引的图片
   * @param {number} idx - 要切换到的图片索引
   */
  slideTo(idx) {
    const selected = this.getSelectedItem();
    if (selected) {
      selected.className = 'slider-list__item'; // 取消当前选中样式
    }
    let item = this.items[idx];
    if (item) {
      item.className = 'slider-list__item--selected'; // 设置新选中样式
    }

    // 触发自定义事件
    const detail = { index: idx };
    const event = new CustomEvent('slide', { bubbles: true, detail });
    this.container.dispatchEvent(event);
  }

  /**
   * 切换到下一张图片
   */
  slideNext() {
    const currentIdx = this.getSelectedItemIndex();
    const nextIdx = (currentIdx + 1) % this.items.length; // 循环索引
    this.slideTo(nextIdx);
  }

  /**
   * 切换到上一张图片
   */
  slidePrevious() {
    const currentIdx = this.getSelectedItemIndex();
    const previousIdx = (this.items.length + currentIdx - 1) % this.items.length;
    this.slideTo(previousIdx);
  }

  /**
   * 添加事件监听器
   * @param {string} type - 事件类型
   * @param {Function} handler - 事件处理函数
   */
  addEventListener(type, handler) {
    this.container.addEventListener(type, handler);
  }

  /**
   * 开始自动轮播
   */
  start() {
    this.stop();
    this._timer = setInterval(() => this.slideNext(), this.cycle);
  }

  /**
   * 停止自动轮播
   */
  stop() {
    clearInterval(this._timer);
  }
}

// 插件：控制按钮
const pluginController = {
  render(images) {
    return `
      <div class="slide-list__control">
        ${images.map((_, i) => `
          <span class="slide-list__control-buttons${i === 0 ? '--selected' : ''}"></span>
        `).join('')}
      </div>
    `.trim();
  },
  action(slider) {
    const controller = slider.container.querySelector('.slide-list__control');
    if (controller) {
      const buttons = controller.querySelectorAll('.slide-list__control-buttons, .slide-list__control-buttons--selected');
      controller.addEventListener('mouseover', evt => {
        const idx = Array.from(buttons).indexOf(evt.target);
        if (idx >= 0) {
          slider.slideTo(idx);
          slider.stop();
        }
      });

      controller.addEventListener('mouseout', () => slider.start());
      slider.addEventListener('slide', evt => {
        const idx = evt.detail.index;
        const selected = controller.querySelector('.slide-list__control-buttons--selected');
        if (selected) selected.className = 'slide-list__control-buttons';
        buttons[idx].className = 'slide-list__control-buttons--selected';
      });
    }
  }
};

// 插件：上一页按钮
const pluginPrevious = {
  render() {
    return `<a class="slide-list__previous"></a>`;
  },
  action(slider) {
    const previous = slider.container.querySelector('.slide-list__previous');
    if (previous) {
      previous.addEventListener('click', evt => {
        slider.stop();
        slider.slidePrevious();
        slider.start();
        evt.preventDefault();
      });
    }
  }
};

// 插件：下一页按钮
const pluginNext = {
  render() {
    return `<a class="slide-list__next"></a>`;
  },
  action(slider) {
    const next = slider.container.querySelector('.slide-list__next');
    if (next) {
      next.addEventListener('click', evt => {
        slider.stop();
        slider.slideNext();
        slider.start();
        evt.preventDefault();
      });
    }
  }
};

// 初始化Slider实例并注册插件
const slider = new Slider('my-slider', {
  images: [
    'https://p5.ssl.qhimg.com/t0119c74624763dd070.png',
    'https://p4.ssl.qhimg.com/t01adbe3351db853eb3.jpg',
    'https://p2.ssl.qhimg.com/t01645cd5ba0c3b60cb.jpg',
    'https://p4.ssl.qhimg.com/t01331ac159b58f5478.jpg'
  ],
  cycle: 3000
});

slider.registerPlugins(pluginController, pluginPrevious, pluginNext);
slider.start();
```
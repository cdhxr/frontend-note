
```js
import { useState } from 'react';

export default function Picture() {
  const [isActive, setIsActive] = useState(false);

  let backgroundClassName = 'background';
  let pictureClassName = 'picture';
  if (isActive) {
    pictureClassName += ' picture--active';
  } else {
    backgroundClassName += ' background--active';
  }

  return (
    <div
      className={backgroundClassName}
      onClick={() => setIsActive(!isActive)}
    >
      <img
        className={pictureClassName}
        alt="Rainbow houses in Kampung Pelangi, Indonesia"
        src="https://i.imgur.com/5qwVYb1.jpeg"
      />
    </div>
  );
}
```

几个要点
- `+=`运算符：会同时运用多种样式，当样式冲突时，越后面优先级越高
- 每当set函数触发，组件函数会重新跑一遍，所以可以用`{}`语法配合if语句更新类名，所以if-else语句可以写在外层


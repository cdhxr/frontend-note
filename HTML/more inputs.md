#html 

HTML 中有多种用于获取用户输入的标签，最常见的是 `<input>`、`<textarea>` 和 `<select>` 标签。下面是对这些标签的详细说明：

### 1. `<input>` 标签
`<input>` 是最常用的输入标签，用于创建各种类型的输入框。`<input>` 标签的 `type` 属性可以指定不同的输入类型，如文本框、密码框、复选框等。以下是一些常用的 `type` 类型：

- **文本输入**
  ```html
  <input type="text" name="username" placeholder="输入用户名">
  ```
- **密码输入**
  ```html
  <input type="password" name="password" placeholder="输入密码">
  ```
- **电子邮件输入**
  ```html
  <input type="email" name="email" placeholder="输入电子邮件">
  ```
- **电话号码输入**
  ```html
  <input type="tel" name="phone" placeholder="输入电话号码">
  ```
- **数字输入**
  ```html
  <input type="number" name="age" min="1" max="100">
  ```
- **日期选择**
  ```html
  <input type="date" name="birthday">
  ```
- **复选框**
  ```html
  <input type="checkbox" name="agree"> 我同意条款
  ```
- **单选按钮**
  ```html
  <input type="radio" name="gender" value="male"> 男
  <input type="radio" name="gender" value="female"> 女
  ```
- **文件选择**
  ```html
  <input type="file" name="profile_picture">
  ```
- **提交按钮**
  ```html
  <input type="submit" value="提交">
  ```
- **重置按钮**
  ```html
  <input type="reset" value="重置">
  ```

### 2. `<textarea>` 标签
用于多行文本输入，例如留言或评论框。
```html
<textarea name="message" rows="4" cols="50" placeholder="输入您的留言"></textarea>
```

### 3. `<select>` 标签
创建下拉菜单，可以包含多个 `<option>` 选项。
```html
<select name="country">
  <option value="china">中国</option>
  <option value="usa">美国</option>
  <option value="japan">日本</option>
</select>
```

### 4. `<button>` 标签
用于创建按钮，可以指定 `type` 属性来定义按钮类型：
- **普通按钮**
  ```html
  <button type="button">普通按钮</button>
  ```
- **提交按钮**
  ```html
  <button type="submit">提交</button>
  ```
- **重置按钮**
  ```html
  <button type="reset">重置</button>
  ```

### 5. `<label>` 标签
`<label>` 用于描述输入字段，可以通过 `for` 属性关联到具体的 `<input>`。
```html
<label for="username">用户名：</label>
<input type="text" id="username" name="username">
```

### 6. `<fieldset>` 和 `<legend>` 标签
`<fieldset>` 用于将相关表单元素分组，`<legend>` 为分组提供标题。
```html
<fieldset>
  <legend>个人信息</legend>
  <label for="name">姓名：</label>
  <input type="text" id="name" name="name">
</fieldset>
```

### 总结
HTML 提供了多种标签和属性来满足不同类型的用户输入需求。常用的标签包括 `<input>`、`<textarea>`、`<select>` 和 `<button>` 等，这些标签可以组合使用来创建功能齐全的表单。

React 有两种不同的方法来处理表单输入。

其值由 React 控制的输入表单元素称为受控组件。

当用户向受控组件输入数据时，
- 会触发一个更改事件处理程序，
- 然后由您的代码决定输入是否有效（通过使用更新后的值重新渲染）。
- 如果不重新渲染，表单元素将保持不变。

非受控组件的工作原理与 React 之外的表单元素类似。

- 当用户在表单字段（输入框、下拉菜单等）中输入数据时，更新的信息将被反映出来，而 React 无需做任何操作。
- 不过，这也意味着您不能强制字段具有特定的值。

在大多数情况下，您应该使用受控组件。


之后，您可能会开始使用第三方抽象，如 Formik 或 React Hook Form，它们采用了可控或不可控技术 under the hood。

无论哪种方式，最终目的都是收集表单数据。

对于受控表单，您的数据就是您的状态。
对于非受控表单，您需要自己收集表单值，通常开发人员会为此选择ref：

```js
function onSubmit(event: React.FormEvent) {
  event.preventDefault()

  // Collect uncontrolled form fields with refs. The refs are
  // giving us direct access to the input fields in the DOM
  const formValues = {
    name: nameRef.current.value
    email: emailRef.current.value
  }
}
```

[[ref in react]]








https://reacttraining.com/blog/react-and-form-data
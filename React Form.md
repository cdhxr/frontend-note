
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

React 中的所有表单字段要么是受控的，要么是不受控的，因为您要么添加了`value` prop，要么没有。FormData 是 2010 年制定的 JavaScript 标准，它是访问表单数据的一种方式，无论数据是受控还是不受控，但大多数人更倾向于不受控。

### FormData

使用 FormData 时，你无需使用 `ref` 获取非受控表单的值。你可以直接从 `event.target` 中读取表单值：

```js
function onSubmit(event: React.FormEvent) {
  event.preventDefault()
  const formData = new FormData(event.target)

  const formValues = {
    name: formData.get('name'),
    email: formData.get('email')
  }
}

```

由于某些原因，TypeScript 不喜欢你使用 `event.target`，而是推荐你使用 `event.currentTarget`。不过你需要知道，它们在大多数情况下其实是指向同一个对象。尽管如此，我们还是会使用 `event.currentTarget`，因为许多使用 TypeScript 的 React 开发者也是这么做的。

记得给你的 `<input>` 元素添加 `name` 属性，否则 FormData 是无法正常获取到值的：

```js
// ✅ 可以获取到，因为有 name 属性
const email = formData.get('email')
<input type="text" name="email" />
```

我们能不能不用 getter，直接像这样解构出所有表单数据呢？

```js
// ❌ 不行
const formValues = { ...formData }
```

不行，FormData 是一个更“隐式”的对象，不能像普通对象那样与字面量合并。如果你用 `console.log` 打印它，也看不到内容。


你可以使用 `Object.fromEntries()` 来避免调用多个 `get` 方法，并将 FormData 转换成普通对象：

```js
const formValues = Object.fromEntries(formData) 
console.log(formValues) // 输出：{ name: 'my name', email: 'name@someemail.com' }
```

但在 TypeScript 中这么做不会得到你期望的类型信息。

---

### FormData 与 TypeScript 的问题

即使输入框的值总是字符串，用户不输入时就是空字符串，但 TypeScript 依然会认为 `formData.get()` 的返回值类型是 `FormDataEntryValue | null`。

```ts
const quantity = formData.get('quantity')
// 类型是：FormDataEntryValue | null
```

因此就连把用户输入的值转换成数字这种简单操作也会变得啰嗦：

```ts
// 断言为 string 或 null
const quantity = formData.get('quantity') as string | null

// 防止 null 值，设置默认值
const quantity = (formData.get('quantity') as string | null) || 0

// 转换为整数
const quantity = parseInt((formData.get('quantity') as string | null) || 0)
```

就算是使用 `Object.fromEntries()` 也没有更好，TS 只知道这是一个 key 为 string、值为 FormDataEntryValue 的对象：

```ts
const formValues = Object.fromEntries(formData)
// 类型是：{ [k: string]: FormDataEntryValue }
```

---

### 用 Zod 解决问题

Zod 是一个基于 schema 的 JS 验证库，和 Yup、Joi 类似，但它从一开始就是为 TypeScript 设计的。如果你需要验证表单数据，何不顺便用 Zod 获得更好的类型呢？

我们可以把 `formValues` 这个“类型模糊”的对象传给 Zod 的 schema 进行验证，然后 Zod 不仅会验证，还会返回一个符合你 schema 的类型安全对象：

```ts
const formValues = Object.fromEntries(formData) // ❌ 类型不明确

const results = myFormSchema.safeParse(formValues)
if (results.success) {
  results.data // ✅ 类型明确：{ email: string, quantity: number }
  console.log(results.data.email) // name@someemail.com
  console.log(results.data.quantity) // 5
} else {
  // 处理 results.errors
}
```

---

### FormData 与 React 19

现代 React API 也开始鼓励你学习和使用 FormData。在 React 19 中，你可以不写 `onSubmit`，而直接用 `action` 函数来接收 FormData：

```tsx
function MyForm() {
  function formAction(formData: FormData) {
    // 直接获取到 formData，不再需要 event
  }

  return <form action={formAction}>...</form>
}
```

React 会在提交表单时自动调用你的 `formAction` 函数，并传入一个 `FormData` 实例。React 19 的一些 hooks（比如 `useActionState`）也有类似的用法。

---

### 在框架中使用

像 Remix 这样的框架（它即将合并入 React Router 7）非常推崇 Web 标准，比如 `FormData`、`Request`、`Response` 等。当表单被提交时，其数据会通过标准的 `Request` 实例在服务端可用。根据 MDN，你可以这样获取 FormData：

```ts
// 在 Remix 中，action 用于处理 POST/PUT/DELETE 请求
export async function action(request: Request) {
  const formData = await request.formData()
  // ...
}
```

这种 Remix 的表单处理方式完全符合 JavaScript 的 Web 标准做法。


https://reacttraining.com/blog/react-and-form-data



# Why we need React hook form

## 原生使用：

state定义：

```js
const [email,setEmail]useState("")
const [password,setPassword]usestate("");
const [errors,setErrors]useState<{email:string;password:string }>(
  email:""
  password:""
}):
```

事件处理函数

```js
const handleSubmit (e:React.FormEvent)=>{
  e.preventDefault();
  setErrors({email:""password:"})
  //Manual validation
  if (!email.includes("@")){
    setErrors({...errors,email:"Email must include @})
    return;
  }
  if (password.length 8){
    setErrors({...errors,password:"Password must be at least 8 chars"})
    return;
  }
  //Form submission
  console.log("Form submitted");
};
```

JSX

```js
<form className="tutorial gap-2"onSubmit={handleSubmit}>
  <input
    .type="text"
    placeholder="Email"
	value={email}
	onChange={(e)=>setEmail(e.target.value)}
  />
  {errors.email.&&<div.className="text-red-500">{errors.email}</div>}
  <input
	type="password"
	placeholder="Password"
	value={password}
	onChange={(e)=>setPassword(e.target.value)}
  />
  {errors.password &<div className="text-red-500">{errors.password}</div>}
  <button type="submit">Submit</button>
</form>
```


这样实现的问题是，表单的扩展性不足，即便只是增加一个字段，也需要做很多额外的操作，需要再定义一个State，然后再传入onChange中，放在Error状态中，对其清空，再对处理函数中添加相应的处理，结合网络请求时的异步处理等等。

如果有十个这样的字段，编写代码将会十分困难

## 使用 react hook form

```js
import { SubmitHandler, useForm } from "react-hook-form";
import z from "zod";
const schema z.object({
  email:z.string().email(), //这是zod内置的校验，相当于为我们写了一个更全面的邮箱校验validate
  password:z.string().min(8),
)};

//使用zod转换了原本的类型定义
type FormFields z.infer<typeof schema>;

//type FormFields = {
  //email:string;
  //password:string;
//};
```

```js
const { 
  register,
  handleSubmit,  //与提交处理函数绑定
  setError,    //设置错误信息，可以与后端结合
  formState:{ errors, isSubmitting }//类似于Query的异步状态管理简化
}=useForm<FormFields>({
  //设置默认值的方法
  defaultValues:{
    email:"test@email.com"
  },
  resolver:zodResovler(schema)//使用zod做解析器与react hook连接
});

const onSubmit:SubmitHandler<FormFields>= async (data) => {
  try {
    await newPromise((resolve) => setTimeout(resolve,1000));
    console.log(data);
  }catch(error){
    //将第一个字段设置为root，则消息属于整个表单
    setError("email",{
      message:"this email is already taken",
    }) 
  }
}
```

```js
<form className="tutorial gap-2">
  //邮箱表单
  <input
    {...register("email",
      // 返回字符串，而不是布尔值，作为err
      // 使用zod后，这也不是required:"Email is required",
	  //可以用正则表达式，也可以加其他的合法性检测
	  //pattern:/^[a-z0-9._6+-]+@[a-z0-9.-]+\.[a-z]{2,4}$/,

	  //使用zod简化validate验证,
	  //这里还需要安装hookform/resolvers 库, Zod库
	  

	  //validate:(value)=
	    //if (!value.includes("@")){
		  //return "Email must-include @";
	    //}
	    //return true;
	  //},
	})}
	type="text"
	placeholder="Email"
  />
  
  {errors.email &&
    <div className="text-red-500">errors.email.message}</div>
  )}
  //密码表单  
  <input
	{...register("password",
	  //同理，zod会为我们检验
	  //required:"Password is required",
	  //minLength:{
	    //value:8,
	    //message:"Password must have at least 8 characters"
	  //}
	)}
	type="password"
	placeholder="Password"
  />
  
  {errors.password &&
    <div className="text-red-500">{errors.password.message}</div>
  )}
  
  <button disabled={isSubmitting} type="submit">
    {isSubmitting ? "Loading...":"Submit}
  </button>

  {errors.root &&
	<div className="text-red-500">{errors.roo.message}</div>
  }
</form>
```




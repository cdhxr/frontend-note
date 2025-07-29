React Server Actions 允许您直接在服务器上运行异步代码。

它们消除了创建 API 端点以修改数据的需要。

相反，您编写在服务器上执行的异步函数，并且可以从您的客户端或服务器组件中调用这些函数。

安全是网络应用程序的首要任务，因为它们可能容易受到各种威胁。这就是服务器操作发挥作用的地方。它们包括加密闭包、严格输入检查、错误消息哈希、主机限制等功能——所有这些协同工作，显著提升您的应用程序安全性。

# Creating an invoice  创建发票
## server action

在 React 中，您可以在 `<form>` 元素中使用 `action` 属性来调用操作。
操作将自动接收原生 FormData 对象，其中包含捕获的数据。

在server componet中调用server action的一个优势是渐进增强
——例如在没有较慢的互联网连接的情况下，
即使客户端尚未加载 JavaScript，表单也能正常工作。

server action也与 Next.js 缓存深度集成。

当通过服务器操作提交表单时，不仅可以使用该操作来修改数据，还可以使用 `revalidatePath` 和 `revalidateTag` 等 API 重新验证相关缓存。

## use server

通过添加 `'use server'` ，你将文件中所有导出的函数标记为服务器操作。

这些服务器函数随后可以导入并在客户端和服务器组件中使用。
此文件中未使用的任何函数将自动从最终应用程序包中移除。

你也可以通过在操作内部添加 `"use server"` 直接在服务器组件中编写服务器操作。但对于本课程，我们将它们全部组织在一个单独的文件中。我们建议为你的操作创建一个单独的文件。

## form action

```tsx
// app/invoices/Form.tsx
import { createInvoice } from './actions';

export default function Form() {
  return (
    <form action={createInvoice}>
      <input name="customer" type="text" placeholder="客户名称" />
      <input name="amount" type="number" placeholder="金额" />
      <button type="submit">提交</button>
    </form>
  );
}

```

### ✅ 这行代码的关键点：

`<form action={createInvoice}>`

它的意思是：

> 当用户点击提交时，浏览器会自动将表单数据（作为 `FormData` 对象）提交给 `createInvoice()` 函数，由它在服务器上处理逻辑。


值得了解：在 HTML 中，你会将一个 URL 传递给 `action` 属性。这个 URL 将是你表单数据应该提交的目的地（通常是 API 端点）。

然而，在 React 中， `action` 属性被视为一个特殊 prop——这意味着 React 在其上构建以允许调用操作。

在幕后，Server Actions 创建一个 `POST` API 端点。这就是为什么在使用 Server Actions 时无需手动创建 API 端点。

## 类型校验与数据库数据获取

```tsx
const sql = postgres(process.env.POSTGRES_URL!, { ssl: 'require' });

const FormSchema = z.object({
  id: z.string(),
  customerId: z.string(),
  amount: z.coerce.number(),
  status: z.enum(['pending', 'paid']),
  date: z.string(),
});

const CreateInvoice = FormSchema.omit({ id: true, date: true });

export async function createInvoice(formData: FormData) {
  // 从表单数据中使用get方法提取原始值
  // 提示：如果你正在处理包含许多字段的表单，你可能需要考虑使用 JavaScript 的 entries() 方法

  const { customerId, amount, status } = CreateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
  const amountInCents = amount * 100;// 单位统一
  
  const date = new Date().toISOString().split('T')[0];// 类型的转化
  
  await sql`
    INSERT INTO invoices (customer_id, amount, status, date)
    VALUES (${customerId}, ${amountInCents}, ${status}, ${date})
  `;

  revalidatePath('/dashboard/invoices');// 使页面重新获取数据
  redirect('/dashboard/invoices');
}
```

## 重定向

|比较项|`redirect()`（服务端跳转）|`router.push()`（客户端跳转）|
|---|---|---|
|用法位置|只能在 Server Action / Server Component 中|只能在 Client Component 中|
|跳转时机|提交时就跳转，**浏览器不会加载当前页面**|当前页面先加载，再跳转|
|传输机制|HTTP 302 重定向 / Next.js 内部跳转|使用 JS 修改浏览器地址栏|
|支持无 JS|✅ 支持（因为运行在服务器）|❌ 需要启用 JS|
|SEO|✅ 更友好（URL 本身重定向）|❌ 不利于爬虫索引跳转|
|页面刷新|✅ 相当于用户访问新页面|❌ 保持 SPA 体验，不刷新页面|

 ✅ 适合用 `redirect()`（服务端跳转）：

- 表单提交后，**立即导航到其他页面**；
- 权限检查后，不满足就重定向；
- 登录成功后跳转；
- ❗️你希望跳转时 **不要渲染当前页面**，直接转向目标。

✅ 适合用 `router.push()`（客户端跳转）：
- 用户点击按钮导航；
- 客户端逻辑判断后跳转；
- 在客户端组件中处理切页或 tab。

# Updating an invoice  更新账单

1. Create a new dynamic route segment with the invoice `id`.  
    创建一个包含发票号 `id` 的新动态路由段。
2. Read the invoice `id` from the page params.  
    从页面参数中读取发票 `id` 。
3. Fetch the specific invoice from your database.  
    从你的数据库中获取特定发票。
4. Pre-populate the form with the invoice data.  
    用发票数据预填充表单。
5. Update the invoice data in your database.  
    在您的数据库中更新发票数据。


## 动态路由

Next.js 允许您在不知道确切片段名称且希望基于数据创建路由时创建动态路由片段。


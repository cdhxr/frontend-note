
# fetch:

---

Make sure your `queryFn` always has a return type specified. Otherwise, `data` will default to `any`.
`async function fetchRepos(): Promise<Array<RepoData>>`
或者可以使用类型断言
`return response.json() as Array<RepoData>`
Please **do not** provide 泛型 to the useQuery call-side.


---

# fetching on demand

---

`enabled` 选项本身并不会进行类型缩小。在这个例子中，我们会遇到类型错误，因为我们尝试将 `number | undefined` 赋值给一个期望 `number` 类型的地方：

```typescript
import { useQuery } from '@tanstack/react-query'

declare function fetchIssue(id: number): Promise<any>

function useIssue(id: number | undefined) {
  return useQuery({
    queryKey: ['issues', id],
    queryFn: () => fetchIssue(id),
    enabled: id !== undefined
  })
}
```

如果我们不想使用 `!` 操作符，我们可以传递一个 `skipToken`，它可以从 React Query 中导入，而不是直接使用 `queryFn`：

```typescript
import { skipToken, useQuery } from '@tanstack/react-query'

declare function fetchIssue(id: number): Promise<any>

function useIssue(id: number | undefined) {
  return useQuery({
    queryKey: ['issues', id],
    queryFn: id === undefined
      ? skipToken
      : () => fetchIssue(id)
  })
}
```

当 React Query 遇到 `skipToken` 时，它会将 `enabled` 设置为 `false`。然而，TypeScript 会因为条件检查而正确地缩小 `id` 的类型为 `number`，从而解决类型问题。

---
# useQueries

`useQueries` 的类型定义相当复杂。如果传入的是一个静态数组，返回的将是一个元组（tuple），这样可以安全地解构，即使每个查询返回的数据结构不同也不会有问题。而对于动态数组，返回的则是一个 `QueryResult` 数组。

在这里，尽可能利用 TypeScript 的类型推导非常重要。如果手动指定类型参数，很可能会导致类型不匹配的问题。

---

# avoiding loading

## function not tied to reactQuery

Since the `getPostQueryOptions` function is not tied to anything from React Query, it's not type safe. For example, if we misspelled `staleTime` to `staletime`, nothing would complain – the excess property would just be ignored by TypeScript.

For this situation, React Query exposes a `queryOptions` function that will restore the type-safety you're used to.

```ts
import { queryOptions } from '@tanstack/react-query'

function getPostQueryOptions(path: string) {
  return queryOptions({
    queryKey: ['posts', path],
    queryFn: () => fetchPost(path),
    staletime: 5000,
  })
}
```

Now this would error nicely, as you'd expect.

---
## queryClient

Per default, `queryClient.getQueryData` will return `unknown` since React Query can't know what lives under which `queryKey`. The Query definitions are done **ad hoc** - when you call `useQuery` for the first time, and not **upfront** e.g. via a schema.

However, if you pass in a `queryKey` that was created via the `queryOptions` function, you can get back that type-safety since that key is tied to the `queryFn`, which is properly typed:

```ts
import { queryOptions } from '@tanstack/react-query'

const postQueryOptions = queryOptions({
  queryKey: ['posts'],
  queryFn: fetchPosts,
  staleTime: 5000
})

const data = queryClient.getQueryData(
  postQueryOptions.queryKey
)
```

---

## `initialData`和`placeholderData`

无论是 `initialData` 还是 `placeholderData`，它们的类型都必须与 `queryFn` 返回的数据类型保持一致。

如果你希望 `placeholderData` 仅包含部分数据，你需要**在类型定义时确保它符合这一点**。
### **示例**

在我们的应用中，详细帖子的类型应该如下：

```ts
type PostDetail = {
  id: string,
  title: string,
  body_markdown?: string  // 注意 `body_markdown` 需要是可选的
}
```

**为什么 `body_markdown` 需要是可选的？**

- 因为 **列表查询（list Query）** 并不会返回 `body_markdown`，
- 而我们**使用列表查询的数据作为 `placeholderData`**。
- 如果 `body_markdown` 不是可选的，TypeScript 会报错。

### **如果我们希望 `body_markdown` 始终存在**

- 我们可以在 `placeholderData` 中**手动添加 `body_markdown: ''`**，保证类型一致：

```ts
placeholderData: () => {
  const post = queryClient
    .getQueryData(['posts'])
    ?.find((post) => post.path === path)
  
  return post
    ? { ...post, body_markdown: '' } // 确保 `body_markdown` 存在
    : undefined
}
```

- **这样做不会改变 UI 的显示逻辑**，因为：
    
	- **在 `isPlaceholderData` 为 `true` 时，UI 仍然会显示加载指示器**。
    - 但**它能让 TypeScript 通过类型检查**，避免报错。
### **额外提示**

使用 `placeholderData` **比在对象解构时提供默认值更安全**，因为它可以确保数据符合 `queryFn` 返回的类型，同时不会影响 `useQuery` 的数据更新逻辑。



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
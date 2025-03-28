
我们有时希望不是在组件挂载后立刻fetch数据，而是根据某些条件达成后再fetch

例如，如果我们需要先获取用户输入后再发起请求，这种情况该如何使用 `useQuery` 处理呢？

我们首先无法使用if语句，因为react不允许在if语句下调用hook

```js{5}
function useIssues(search) {
  return useQuery({
    queryKey: ['issues', search],
    queryFn: () =>  fetchIssues(search),
    enabled: search !== ''
  })
}
```

为此可以利用内置的enabled option只有在enabled为true时才能使用useQuery

但这仍然不完美：
在 `enabled: search !== ''` 的情况下，`useQuery` 只有在 `search` 不为空时才会执行 `queryFn`。
然而，在 `search` 为空时，`useQuery` 仍然会返回 `status === 'pending'`，因为它还没有数据可用。
enabled只限制了fn的调用
**即使用户还没有输入搜索词，组件仍然会显示加载状态**。

### **解决方案：使用 `fetchStatus`**

React Query 提供了 `fetchStatus`，它能告诉我们**查询函数是否正在执行**。  
我们可以用 `fetchStatus === 'fetching'` 进一步判断是否真的在加载数据：

```js
const { data, status, fetchStatus } = useIssues(search)

if (status === 'pending' && fetchStatus === 'fetching') {
  return <div>...</div>
}
```

- **`status === 'pending'`** → 数据未缓存（可能未请求或正在请求）。
- **`fetchStatus === 'fetching'`** → `queryFn` 正在执行。

```js
if (status === 'pending') {
  if (fetchStatus === 'fetching') {
    return <div>...</div>
  }
}
```

### **React Query 的 `isLoading`**

由于这个逻辑很常见，React Query 提供了 `isLoading`，它等价于 `status === 'pending' && fetchStatus === 'fetching'`，我们可以简化代码：

```js
const { data, status, isLoading } = useIssues(search)

if (isLoading) {
  return <div>...</div>
}
```

虽然 `loading` 逻辑修正了，但 **当 `search` 为空时，`useQuery` 仍然会返回 `pending`，且 `data` 也是 `undefined`**。如果不加处理，`IssueList` 可能会因为 `data.items` 访问 `undefined` 而报错。

The solution to both is to never assume we have `data` without explicitly checking if the `status` of the query is `success`. Again, a `status` of `success` means there **is** data in the cache.

```js {12}
function IssueList ({ search }) {
  const { data, status, isLoading } = useIssues(search)

  if (isLoading) {
    // there is no data in the cache 
    // and the queryFn is currently being executed
    return <div>...</div>
  }

  if (status === 'error') {
    // there was an error fetching the data to put in the cache
    return <div>There was an error fetching the issues</div>
  }

  if (status === "success") {     //显式检查success
    // there is data in the cache
    return (
      <p>
        <ul>
          {data.items.map((issue) => 
            <li key={issue.id}>{issue.title}</li>
          )}
        </ul>
      </p>
    )
  }

  // otherwise
  return <div>Please enter a search term</div>
}
```
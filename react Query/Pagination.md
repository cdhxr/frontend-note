
将一个很大的数据进行分页的技术

1. API 提供了一种方式，可以在请求参数中**指定页码（page number）和每页记录数（per_page）**。
2. 当你向 API 发送请求时，**需要包含这些分页参数**，以指明你希望获取哪一页的数据。
3. API 处理你的请求后，会**根据指定的页码和每页记录数，返回数据的一个子集**。
4. 除了分页数据，响应通常还包含额外的**元数据（metadata）**，例如：

	- **总记录数**（total number of records）
	- **当前页码**（current page number）
	- **总页数**（total number of pages available）

要获取**下一页数据**，你只需在后续请求中**增加页码**，同时保持 `per_page` 不变。
你可以不断调整 `page` 参数来遍历整个数据集，每次获取一页数据。

是的，正如你现在可能已经猜到的，**React Query 内置了对分页查询（paginated queries）的支持**。为了演示这一点，我们将**扩展之前在课程中看到的 GitHub Repos 应用**，为其**添加分页功能**。接下来，我们将从**一个基础版本**（未添加分页功能）开始……

```js
import * as React from "react"
import { useQuery } from '@tanstack/react-query'
import { fetchRepos } from "./api"
import Sort from './Sort'

function useRepos(sort) {
  return useQuery({
    queryKey: ['repos', { sort }],
    queryFn: () => fetchRepos(sort),
    staleTime: 10 * 1000,
  })
}

function RepoList({ sort }) {
  const { data, status } = useRepos(sort)

  if (status === 'pending') {
    return <div>...</div>
  }

  if (status === 'error') {
    return <div>There was an error fetching the repos.</div>
  }

  return (
    <div>
      <ul>
        {data.map((repo) => 
          <li key={repo.id}>{repo.full_name}</li>
        )}
      </ul>
    </div>
  )
}

export default function Repos() {
  const [selection, setSelection] = React.useState('created')

  const handleSort = (sort) => {
    setSelection(sort)
  }

  return (
    <div>
      <Sort value={selection} onSort={handleSort} />
      <RepoList sort={selection} />
    </div>
  )
}

```

The way GitHub's pagination API works is you can give it two parameters, `page` and `per_page`.

```js
//分页获取数据的方法实现
export async function fetchRepos(sort, page) {
  const response = await fetch(
    `https://api.github.com/orgs/TanStack/repos
      ?sort=${sort}
      &per_page=4
      &page=${page}`
  )
  
  if (!response.ok) {
    throw new Error(`Request failed with status: ${response.status}`)
  }

  return response.json()
}
```

我们应该通过react State跟踪track参数，以使用户可以进行翻页操作

让我们将它放在父组件`Repos`内部，这样，通过 props，我们就可以从`Sort` 和 `RepoList`内部访问并修改它。

```js {3,7,13}
export default function Repos() {
  const [selection, setSelection] = React.useState('created')
  const [page, setPage] = React.useState(1)
  
  const handleSort = (sort) => {
    setSelection(sort)
    setPage(1)
  }

  return (
    <div>
      <Sort value={selection} onSort={handleSort} />
      <RepoList sort={selection} page={page} setPage={setPage} />
    </div>
  )
}
```

Now inside of `RepoList`, let's add our buttons and pass along the `page` prop as an argument to `useRepos`, so that we can then add it to the `queryKey` and pass it along to `fetchRepos` in the `queryFn`.

继续优化：减少用户看到的loading次数——在新列表准备好之前保留旧列表

placeholderData

我们之前没有讨论过 `placeholderData` 的一个细节：
你传递给 `placeholderData` 的**函数**，其**第一个参数**将会是**查询的上一次状态（previous state）**。

```js
useQuery({
  queryKey,
  queryFn,
  placeholderData: (previousData) => {

  }
})
```

当用户**切换页面**时，我们可以将 `placeholderData` 设置为**前一页的数据**。
这样，用户在等待新数据加载时，**仍然可以看到旧的数据**，直到新数据被添加到缓存中。

```js
function useRepos(sort, page) {
  return useQuery({
    queryKey: ['repos', { sort, page }],
    queryFn: () => fetchRepos(sort, page),
    staleTime: 10 * 1000,
    placeholderData: (previousData) => previousData
  })
}
```

为了避免**用户混淆**，我们还可以**在旧数据上增加透明度**，**提供视觉反馈**。
可以利用 `useQuery` 提供的 **`isPlaceholderData`** 变量**动态设置列表的不透明度**，这样用户可以**直观地感受到数据正在更新**。

Next quetion:  在合适的时候禁用按钮

- 获取新数据时禁用按钮
- 到达列表末尾时禁用按钮

在button tag 的disabled Attribute内传入表达式，返回布尔值即可
`<button disabled={isPlaceholderData || page === 1}>

Now inside of `RepoList`, all we have to do is import `PAGE_SIZE` and disable the "Next" button if the length of the data we get back is less than it.

至此，我们已经实现了**完整的分页体验**！

得益于 **React Query 的缓存机制**，在页面之间来回切换时，加载**几乎是瞬时的**。  
此外，在切换到新页面时，用户会**先看到上一页的数据**，直到新数据加载完毕，从而**避免页面布局突然变化（jarring layout shift）**。

### **但还能做得更好吗？**

作为**额外优化**，我们可以**让体验更上一层楼**！

在上一节课中，我们学习了**预取（prefetching）** 的概念。  
如果我们在这里也用上它，能否**进一步提升用户体验**呢？

### **改进方案：预取下一页**

以往，我们通过 `onMouseEnter` 事件监听用户**悬停链接时**触发预取。

但这次，我们可以**更进一步**——  
**每次加载当前页时，就在后台自动预取下一页**，这样：

- 当用户点击“下一页”时，数据**已经在缓存中**，页面加载会**立即完成**！

### **实现步骤**

为了做到这一点，我们可以**先将 `useRepos` 的查询配置提取出来**，封装成一个**独立的函数**，这样我们可以**复用它**，并在其中添加预取逻辑。

```js
function getReposQueryOptions(sort, page) {
  return {
    queryKey: ['repos', { sort, page }],
    queryFn: () => fetchRepos(sort, page),
    staleTime: 10 * 1000
  }
}

function useRepos(sort, page) {
  return useQuery({
    ...getReposQueryOptions(sort, page),
    placeholderData: (previousData) => previousData
  })
}
```

Now, inside of `useRepos`, let's add a `useEffect` hook that will prefetch the data for the next page.

```js
function useRepos(sort, page) {
  const queryClient = useQueryClient()

  React.useEffect(() => {
    queryClient.prefetchQuery(getReposQueryOptions(sort, page + 1))
  }, [sort, page, queryClient])

  return useQuery({
    ...getReposQueryOptions(sort, page),
    placeholderData: (previousData) => previousData
  })
}
```

```js
function RepoList({ sort, page, setPage }) {
  const { data, status, isPlaceholderData } = useRepos(sort, page)

  if (status === 'pending') {
    return <div>...</div>
  }

  if (status === 'error') {
    return <div>There was an error fetching the repos.</div>
  }

  return (
    <div>
      <ul style={{ opacity: isPlaceholderData ? 0.5 : 1 }}>
        {data.map((repo) => 
          <li key={repo.id}>{repo.full_name}</li>
        )}
      </ul>
      <div>
        <button
          onClick={() => setPage((p) => p - 1)}
          disabled={isPlaceholderData || page === 1}
        >
          Previous
        </button>
        <span>Page {page}</span>
        <button
          disabled={isPlaceholderData || data?.length < PAGE_SIZE}
          onClick={() => setPage((p) => p + 1)}
        >
          Next
        </button>
      </div>
    </div>
  )
}

export default function Repos() {
  const [selection, setSelection] = React.useState('created')
  const [page, setPage] = React.useState(1)

  const handleSort = (sort) => {
    setSelection(sort)
    setPage(1)
  }

  return (
    <div>
      <Sort value={selection} onSort={handleSort} />
      <RepoList sort={selection} page={page} setPage={setPage} />
    </div>
  )
}

```

完成！！！
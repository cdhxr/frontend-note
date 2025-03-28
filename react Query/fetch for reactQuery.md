
- 如何使用 React Query 读取响应标头？
- 如何将 GraphQL 与 React Query 结合使用？
- 如何使用 React Query 向我的请求添加身份验证令牌？

所有这些问题的答案都是相同的——您可以像平常一样在没有 React Query 的情况下进行操作。

事实上，由于 React Query 本身并不触发请求，它甚至不知道这些事情。它所关心的只是 Promise 的状态和它解析的数据。

当你获取数据时，最常见的方法是使用浏览器内置的[Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)。
`const promise = fetch ( url ，options )`

Once invoked, the browser will start the request immediately and return you a Promise.

这里得到实际data的过程分为两步

1. 获得response对象
	- 一旦服务器响应headers，fetch返回的promise对象就会被解析为一个response对象
	- This object contains information about the response (like those headers, the HTTP `status` code, etc.), but does not contain the actual data.
	- 注意，fetch在请求失败时也不会触发promise的reject，即使状态码为400或者500，promise也会resolve
	- 为了解决这个问题，您通常会遵循一种模式，检查是否`response.ok`是`true`（如果响应`status`在`2xx`范围内，则是），如果不在，则会引发错误。

```js
const fetchRepos = async () => {
  try {
    const response = await fetch('https://api.github.com/orgs/TanStack/repos')

    if (response.ok) {

    } else {
      throw new Error(`Request failed with status: ${response.status}`)
    }
  } catch (error) {
    // handle network errors
  }
}
```

2. Next, you'll want to get the actual data from the response body.

如果要拿JSON数据：
```js {6,7}
const fetchRepos = async () => {
  try {
    const response = await fetch('https://api.github.com/orgs/TanStack/repos')

    if (response.ok) {
      const data = await response.json()
      return data
    } else {
      throw new Error(`Request failed with status: ${response.status}`)
    }
  } catch (error) {
    // handle network errors
  }
}
```

Now, if we combine this with our knowledge of `useQuery` (and specifically the `queryFn`), we get something like this.

```js
function useRepos() {
  return useQuery({
    queryKey: ['repos'],
    queryFn: async () => {
      const response = await fetch('https://api.github.com/orgs/TanStack/repos')
      
      if (!response.ok) {
        throw new Error(`Request failed with status: ${response.status}`)
      }

      return response.json()
    },
  })
}
```

There are a couple things to notice here.

First, we were able to get rid of our `try/catch` code.
为了告诉react Query错误的发生，应该在response不OK时手动抛出错误

Second, we were able to return `response.json()` directly.
since `response.json()` returns a promise that resolves with the parsed JSON data.

And if we throw our `useRepos` hook into a real app, it behaves just as expected.用钩子封装逻辑

```js
import * as React from "react"
import { QueryClient, QueryClientProvider, useQuery } from '@tanstack/react-query'

const queryClient = new QueryClient()

function useRepos() {
  return useQuery({
    queryKey: ['repos'],
    queryFn: async () => {
      const response = await fetch('https://api.github.com/orgs/TanStack/repos')
      
      if (!response.ok) {
        throw new Error(`Request failed with status: ${response.status}`)
      }

      return response.json()
    },
  })
}

function Repos() {
  const { data, status } = useRepos()

  if (status === 'pending') {
    return <div>...</div>
  }

  if (status === 'error') {
    return <div>Error fetching data 😔</div>
  }

  return (
    <ul>
      { data.map(repo => <li key={repo.id}>{repo.full_name}</li>) }
    </ul>
  )
}

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Repos />
    </QueryClientProvider>
  )
}
```
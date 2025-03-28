
之前已经学过了如何处理静态资源的获取

```js
function useRepos() {
  return useQuery({
    queryKey: ['repos'],
    queryFn: async () => {
      const response = await fetch(
        'https://api.github.com/orgs/TanStack/repos'
      )
      
      if (!response.ok) {
        throw new Error(`Request failed with status: ${response.status}`)
      }

      return response.json()
    },
  })
}
```

但事实是，很多时候数据不是静态的

以使用Github API为例，可以对结果进行排序，
The way you do this is by passing it a `sort` parameter which can be either `created`, `updated`, `pushed`, or `full_name`.

您将如何更新`useRepos`钩子以接受一个`sort`参数，然后将其作为 URL 传递`fetch`？

因为组件重新渲染时（组件会因为各种原因重新渲染），不会每次都重新fetch，所以通过传递prop传参是不可行的

Now you could do something like this, where you tell React Query to `refetch` the data whenever the `sort` changes.

```js
const { data, status, refetch } = useRepos(selection)

...

onChange={(event) => {
  const sort = event.target.value
  setSelection(sort)
  refetch()
}}
```

但我们更希望用声明式而不是命令式的方案解决问题

用queryKey是更好的办法

Whenever a value in the `queryKey` array changes, React Query will re-run the `queryFn`.
这也意味着，在QueryFn中的所有用到的变量都应该包含在Querykey数组中

```
现在我知道您在想什么 - 这听起来与`useEffect`的依赖项数组非常相似，并且您之前已经让我相信的`useEffect`依赖项数组是坏的。

公平，但`queryKey`没有那么多缺点`useEffect`。

特别是，你不必担心`queryKey`“引用稳定”的问题。你可以将数组和对象放入其中，React Query 将哈希确定性地。
```

考虑到这一点，让我们更新`queryKey`现在以包含该`sort`参数。

```js
import { useQuery } from '@tanstack/react-query'

export default function useRepos(sort) {
  return useQuery({
    queryKey: ['repos', { sort }],
    queryFn: async () => {
      const response = await fetch(
        `https://api.github.com/orgs/TanStack/repos?sort=${sort}`
      )
      
      if (!response.ok) {
        throw new Error(`Request failed with status: ${response.status}`)
      }

      return response.json()
    },
  })
}
```

在cache中发生的事情是：

每当有一个新的参数传入时，cache中会创建一个新的key，对新的key从头开始走一遍渲染流程，而切换回已经存在过的key时，会直接读取cache中的数据，即不用pending直接完成

不同的参数，实质上是不同的变量，这也能解决race condition(不同的数据抢一个变量)

唯一需要确保的是其中必须包括所有Fn中的数据
`@tanstack/eslint-plugin-query`
可以用该插件进行辅助检查
Once installed, you'll get a nice error message if you try to use something inside the `queryFn` that is not part of the `queryKey`, and it'll even offer suggestions for fixing the problem.

# queryClient

```js
import { QueryClient } from '@tanstack/react-query'

...

const queryClient = new QueryClient(options)
```

QueryClient manage the `querycache`, this is why it so important
`QueryCache` - the location where all of the data lives.本质是js中的map

注意：它需要在最父级组件之外创建（APP文件的外部），就像下面这样，在react外部可以使cache在re-Render时也能保持稳定

```js
import { QueryClient } from '@tanstack/react-query'

const queryClient = new QueryClient()

export default function App () {
  ...
}
```

However, because the `QueryClient` is created and located outside of React,  you'll then need `QueryClientProvider`： a way to distribute it throughout your application so that you can interact with it from any component.

```js {10,12}
import { 
  QueryClient, 
  QueryClientProvider 
} from '@tanstack/react-query'

const queryClient = new QueryClient()

export default function App () {
  return (
    <QueryClientProvider client={queryClient}>
      <App />
    </QueryClientProvider>
  )
}
```

通过包裹父组件，将`QueryClientProvider`创建的queryClient实例作为Props传递，
React Query will make the query cache available to you anywhere in your component tree.

底层是react context，但react query don't use for stage management, use it solely for dependency injection(依赖注入，就是传入queryClient依赖).

传下来的clientquery对象是永远不会改变的（static）, 这利于拿数据而不触发渲染

# `useQuery`

与缓存交互的主要方式是通过`useQuery` hook - the workhorse( 主力 ) of React Query.

它会订阅QueryCache中的数据，当数据变化时会触发re-render

1. How does it know which data it cares about?
2. How does it know where to get that data from?

When you invoke `useQuery`, you'll almost always give it two things: 
a `queryKey` and a `queryFn`.

```js
const { data } = useQuery({
  queryKey: ['luckyNumber'],  //要什么数据
  queryFn: () => Promise.resolve(7),  //数据来源
})
```

默认，如果key中有数据，`useQuery` will return that data immediately.
Otherwise, it will invoke the `queryFn`,获取fn中解析到的数据，存入Querykey并返回数据

1. the `queryKey`must be globally unique.
2. `queryFn` must return a promise that resolves with the data you want to cache.
3. `{ data }`是内置对象的属性，可以这样修改变量名`{ data: bookTitle }`（对象解构）

将这段代码写在需要直接获取它的数据的组件内部
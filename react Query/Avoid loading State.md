
总是在loading会带来体验上的糟糕体验
 
react Query有一些API能使loading State变的易于管理或者Avoid it

To demonstrate these options, let's have a simple app that fetches some blog posts from the dev.

用户可以根据链接来改变渲染内容，比起用户点击了再加载，我们可以提前fetch

prefetch: 在用户鼠标悬浮在链上时就可以开始fetch
prenfetching 最难的地方在于，搞清楚fetching的时机

```js
<a
  onClick={() => setPath(post.path)}
  href="#"
  onMouseEnter={() => {
    queryClient.prefetchQuery({
      queryKey: ['posts', post.path],
      queryFn: () => fetchPost(post.path),
      staleTime: 5000
    })
  }}
>
  {post.title}
</a>
```

queryClient.prefertch（）是react Query中的API，为了强制预加载
它的唯一目的是将数据放入cache，而不返回UI

这里的QueryClient Object和在根部创建的是一样的，
1. 导入queryclient hook
2. 利用useQueryClient（）在该API所在组件实例化

提取抽象可复用的Query项：

```js
function getPostQueryOptions(path) {
  return {
    queryKey: ['posts', path],
    queryFn: () => fetchPost(path),
    staleTime: 5000
  }
}

...

function usePost(path) {
  return useQuery(getPostQueryOptions(path))
}
```

staletime的设置可以避免其过于频繁的更新cache

显然的，prefetching需要时间，loading依旧可能出现

在这个例子中，即便没有fetch，我们也有了一定的数据，像是ID和标题

可以让他们作为一个占位符UI以避免loading

为了做到这一点，React Query 有`initialData`的概念。

如果传递`initialData`给`useQuery`，
React Query 将使用从`useQuery`返回的数据来初始化该`Query`的`Cache`条目。

因此，就我们的示例而言，我们需要弄清楚
- 如何从缓存中获取特定的帖子数据，
- 以便我们可以使用它来初始化我们的帖子查询。

请记住，`queryClient`是保存缓存的地方。要直接访问缓存数据，可以使用`queryClient.getQueryData`。它以`queryKey`作为参数，并返回该条目在缓存中的所有内容。

```js
function usePost(path) {
  const queryClient = useQueryClient(); // 获取React Query的 queryClient 实例

  return useQuery({
    ...getPostQueryOptions(path), // 展开对象,获取Query参数
    initialData: () => {
      return queryClient.getQueryData(['posts']) // 获取缓存中的 posts 数据
        ?.find((post) => post.path === path); // 查找匹配指定 path 的帖子
    }
  });
}
```

但事实上，这没有起到理想的占位符作用

React Query **将通过 `initialData` 存入缓存的数据视为普通数据**。这意味着：

- 当我们使用 `initialData` 并设置 `staleTime: 5000` 时，我们实际上是在告诉 React Query：**这份数据在 5 秒内是有效的，不需要重新调用 `queryFn` 进行数据请求**。
- **问题在于：这份数据是不完整的！** 我们只有 `title` 和 `id`，但 `body_markdown` 还未获取到。
- **这暴露了 `initialData` 的一个局限性**：它适用于**你已经获取到了完整数据**的情况（但现实中这种情况很少见）。

那么，我们真正需要的是什么呢？

- **我们希望在获取完整数据之前，能先展示一个合适的占位内容。**
- **幸运的是，React Query 提供了一个专门解决这个问题的选项 —— `placeholderData`！**

- **`placeholderData`** 类似于 `initialData`，但**不会被存入缓存**。
- **`placeholderData` 只是临时占位数据**，React Query 仍然会调用 `queryFn` 获取真正的数据，并在数据到达时更新 UI

只需要用 **`placeholderData`** 替代 `initialData` 就能实现效果

但是我们还能优化：在存在UI占位符时，同时为文章的加载loading的UI

当你调用 `useQuery` 并传入 `placeholderData` 时，它会返回一个 **`isPlaceholderData`** 布尔值。
如果用户当前看到的数据是占位数据，那么 `isPlaceholderData` 的值将为 `true`。
我们可以利用这个值来判断**何时应该显示加载指示器**。

为了让整个逻辑更加完善，我们可以**重新加入预取（prefetching）逻辑**。

这样，如果请求足够快，**用户将直接看到完整的帖子内容**；如果请求较慢，**用户会先看到帖子标题，并显示加载指示器，直到完整数据加载完成**。

```js
import * as React from 'react'
import markdownit from 'markdown-it'
import { useQuery, useQueryClient } from "@tanstack/react-query"
import { fetchPost, fetchPosts } from './api'

function getPostQueryOptions(path) {
  return {
    queryKey: ['posts', path],
    queryFn: () => fetchPost(path),
    staleTime: 5000
  }
}

function usePostList() {
  return useQuery({
    queryKey: ['posts'],
    queryFn: fetchPosts,
    staleTime: 5000
  })
}

function usePost(path) {
  const queryClient = useQueryClient()

  return useQuery({
    ...getPostQueryOptions(path),
    placeholderData: () => {
      return queryClient.getQueryData(['posts'])
        ?.find((post) => post.path === path)
    }
  })
}

function PostList({ setPath }) {
  const { status, data } = usePostList()
  const queryClient = useQueryClient()

  if (status === 'pending') {
    return <div>...</div>
  }

  if (status === 'error') {
    return <div>Error fetching posts</div>
  }

  return (
    <div>
      {data.map((post) => (
        <p key={post.id}>
          <a
            onClick={() => setPath(post.path)}
            href="#"
            onMouseEnter={() => {
              queryClient.prefetchQuery(getPostQueryOptions(post.path))
            }}
          >
            {post.title}
          </a>
          <br />
          {post.description}
        </p>
      ))}
    </div>
  )
}

function PostDetail({ path, setPath }) {
  const { status, data, isPlaceholderData } = usePost(path)

  const back = (
    <div>
      <a onClick={() => setPath(undefined)} href="#">
        Back
      </a>
    </div>
  )

  if (status === 'pending') {
    return <div>...</div>
  }
  
  if (status === 'error') {
    return (
      <div>
        {back}
        Error fetching {path}
      </div>
    )
  }

  const html = markdownit().render(data?.body_markdown || "")

  return (
    <div>
      {back}
      <h1>{data.title}</h1>
      {isPlaceholderData 
        ? <div>...</div> 
        : <div dangerouslySetInnerHTML={{__html: html}} />}
    </div>
  )
}

export default function Blog() {
  const [path, setPath] = React.useState()

  return (
    <div>
      {path
        ? <PostDetail path={path} setPath={setPath} />
        : <PostList setPath={setPath} />
      }
    </div>
  )
}
```
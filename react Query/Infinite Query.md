
用react Query实现无限滚动

我们真正想要的是拥有一个**单一的缓存条目**，每次获取到新数据时，我们都可以将新数据**追加**到这个条目中。

This is exactly what React Query's `useInfiniteQuery` hook allows you to do.

在获取无限列表（infinite lists）和分页列表（paginated lists）数据时，数据是分批次地**逐步获取**的。
为了实现这一点，你需要一种方式来**确定已经获取的数据**和**下一步需要获取的数据**。

通常，正如我们在 Repos 示例中看到的，**通过页码**来实现这一功能。

在我们的分页示例中，我们使用了**React 状态**来表示页码，允许用户通过 UI 来增加或减少页码，然后将这个页码传递给我们自定义的 Hook，在 `queryKey` 和 `queryFn` 中使用它。

想法是一样的，只是实现方法不同：
Instead of needing to manage the `page` in React state yourself, `useInfiniteQuery` will manage it for you.

```jsx
export async function fetchPosts(page) {
  const url = `https://dev.to/api/articles?per_page=6&page=${page}`
  const response = await fetch(url)

  if (!response.ok) {
    throw new Error(`Failed to fetch posts for page #${page}`)
  }

  return response.json()
}

function usePosts() {
  return useInfiniteQuery({
    queryKey: ['posts'],
    queryFn: ({ pageParam }) => fetchPosts(pageParam),
    //哪一页开始
    initialPageParam: 1,//初始页码，会在被Fn第一次调用时启用
    //下一页在哪
    getNextPageParam: (lastPage, allPages, lastPageParam) => {
       //如果没有页数可以获取
       if (lastPage.length === 0) {
        return undefined
      }    
      return lastPageParam + 1 
    }
  })
}

```

When invoked, React Query will pass the `getNextPageParam` method three arguments, `lastPage`, `allPages`, and `lastPageParam`.

- `lastPage` is the data from the last page fetched
- `allPages` is an array of all the pages fetched so far
- `lastPageParam` is the `pageParam` that was used to fetch the last page


这就引出了 `useQuery` 和 `useInfiniteQuery` 之间的另一个主要区别——**它们返回的数据结构**。

- **`useQuery`**：返回的是根据 `queryKey` 在缓存中找到的数据。
- **`useInfiniteQuery`**：为了方便使用，通常会返回数据以及与这些数据相关联的页面信息。

为了实现这一点，`useInfiniteQuery` 返回的数据通常是这样的结构：

- 数据被拆分成**多维数组**，每一项表示**特定页面的数据**。

这种结构允许你在使用无限查询时，能够轻松地访问每一页的数据，以及有关页面的信息。

```js
{
 "data": {
   "pages": [
     [ {}, {}, {} ],
     [ {}, {}, {} ],
     [ {}, {}, {} ]
   ],
   "pageParams": [1, 2, 3]
 }
}
```

这是目前的代码

```jsx
import * as React from "react"
import { useInfiniteQuery } from '@tanstack/react-query'
import { fetchPosts } from './api'

function usePosts() {
  return useInfiniteQuery({
    queryKey: ['posts'],
    queryFn: ({ pageParam }) => fetchPosts(pageParam),
    staleTime: 5000,
    initialPageParam: 1,
    getNextPageParam: (lastPage, allPages, lastPageParam) => {
      if (lastPage.length === 0) {
        return undefined
      }

      return lastPageParam + 1
    }
  })
}

export default function Blog() {
  const { status, data } = usePosts()

  if (status === 'pending') {
    return <div>...</div>
  }

  if (status === 'error') {
    return <div>Error fetching posts</div>
  }

  return (
    <div>
      {data.pages.flat().map((post) => (
        <p key={post.id}>
          <b>{post.title}</b>
          <br />
          {post.description}
        </p>
      ))}
    </div>
  )
}
```

当然，直到现在，我们还没有做任何特别“无限”的事情。让我们来解决这个问题。

在分页示例中，因为我们使用了 React 状态来管理页码，所以只需在点击按钮时**递增页码**，就能获取下一页的数据：

```jsx
<button onClick={() => setPage((p) => p + 1)}>
  下一页
</button>
```

但现在，`useInfiniteQuery` 已经为我们管理页码。
由于这一点，它给我们提供了一个 `fetchNextPage` 函数，当调用时，它会通过调用 `getNextPageParam` 获取新的 `pageParam`，然后用这个参数调用 `queryFn`。

```js
const { status, data, fetchNextPage } = usePosts()
```

因此，如果我们现在在列表底部添加一个按钮，并触发 `fetchNextPage`，我们就能实现第一个**无限滚动列表**。

```js
import * as React from "react"
import { useInfiniteQuery } from '@tanstack/react-query'
import { fetchPosts } from './api'

function usePosts() {
  return useInfiniteQuery({
    queryKey: ['posts'],
    queryFn: ({ pageParam }) => fetchPosts(pageParam),
    staleTime: 5000,
    initialPageParam: 1,
    getNextPageParam: (lastPage, allPages, lastPageParam) => {
      if (lastPage.length === 0) {
        return undefined
      }

      return lastPageParam + 1
    }
  })
}

export default function Blog() {
  const { status, data, fetchNextPage } = usePosts()

  if (status === 'pending') {
    return <div>...</div>
  }

  if (status === 'error') {
    return <div>Error fetching posts</div>
  }

  return (
    <div>
      {data.pages.flat().map((post) => (
        <p key={post.id}>
          <b>{post.title}</b>
          <br />
          {post.description}
        </p>
      ))}

      <button onClick={() => fetchNextPage()}>
        More
      </button>
    </div>
  )
}
```

如果我们想让按钮更智能一些，可以为它提供一些关于查询状态的元信息。具体来说，

- `isFetchingNextPage` 当下一页的数据正在请求时为 `true`
- `hasNextPage` 如果还有下一页可以获取，它为 `true`。这通过调用 `getNextPageParam` 来判断，如果返回 `undefined`，就说明没有下一页了。

我们可以利用这两个值来有条件地禁用“更多”按钮，并且在 React Query 获取下一页数据时给它加上加载指示器。

```jsx
<button
  onClick={() => fetchNextPage()}
  disabled={!hasNextPage || isFetchingNextPage}
>
  { isFetchingNextPage ? '...' : '更多' }
</button>
```

而且，你不必只在一个方向上进行无限查询。到目前为止，我们只看了从开始获取并向前加载更多页的查询——但并不总是这样。

例如，假设你正在构建一个支持深度链接到任何消息的消息应用程序。在这种情况下，用户可能会在对话的中间，需要向前和向后获取消息，以便获取完整的上下文。

幸运的是，向后获取数据的模式与向前获取数据类似，只是使用了更合适的命名值。

例如，`getNextPageParam` 使用 `lastPage`、`allPages` 和 `lastPageParam`，而你将使用 `getPreviousPageParam`，它接受 `firstPage`、`allPages` 和 `firstPageParam`。

```js
useInfiniteQuery({
  queryKey,
  queryFn,
  initialPageParam,
  getNextPageParam: (lastPage, allPages, lastPageParam) => {
    if (lastPage.length === 0) {
      return undefined;
    }
    return lastPageParam + 1;
  },
  getPreviousPageParam: (firstPage, allPages, firstPageParam) => {
    if (firstPageParam <= 1) {
      return undefined;
    }
    return firstPageParam - 1;
  }
});
```

现在，我知道你在想什么：“这些都不错，但还不够上瘾。我希望我的用户大脑变成糊状物，他们不由自主地在我的应用上滚动，这样我可以最大化广告收入。”

不用担心，这个功能也不难实现。
对于这个功能，你并不需要了解与 React Query 相关的任何新知识。
相反，它只需要在用户滚动到列表底部时触发 `fetchNextPage`。

为此，我们可以利用 `useHooks` 的 `useIntersectionObserver` 钩子。

```jsx
import { useIntersectionObserver } from "@uidotdev/usehooks";

...

const [ref, entry] = useIntersectionObserver();
```

当与 `ref` 关联的元素进入视图时，`entry.isIntersecting` 将变为 `true`。
结合一些 `useEffect` 的魔法，我们可以在用户滚动到列表底部时触发 `fetchNextPage`。

```js
export default function Blog() {
  const { status, data, fetchNextPage, hasNextPage, isFetchingNextPage } = usePosts()

  const [ref, entry] = useIntersectionObserver();

  React.useEffect(() => {
    if (entry?.isIntersecting && hasNextPage && !isFetchingNextPage) {
      fetchNextPage()
    }
  }, [entry?.isIntersecting, hasNextPage, isFetchingNextPage])

  if (status === 'pending') {
    return <div>...</div>
  }

  if (status === 'error') {
    return <div>Error fetching posts</div>
  }

  return (
    <div>
      {data.pages.flat().map((post, index, pages) => (
        <p key={post.id}>
          <b>{post.title}</b>
          <br />
          {post.description}
          {index === pages.length - 3
              ? <div ref={ref} />
              : null}
        </p>
      ))}
    </div>
  )
}
```